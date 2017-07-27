---
layout: post
title: Les index spatiaux dans SQL Server 2012 / SQL Azure
slug: les-index-spatiaux-dans-sql-server-2012-sql-azure
date: 2012-07-30 05:22:00Z
---

Depuis SQL Server 2008, les données spatiales sont des types de premier ordre dans SQL Server. J’ai eu l’occasion de manipuler ces types sur plusieurs projets. Plus récemment, j’ai rencontré un problème de performance sur des requêtes faisant intervenir des données spatiales. L’occasion de creuser un peu plus les index spatiaux :).

<!--more-->
<h1>A quoi sert un index spatial ?</h1>
Les opérations sur la géométrie ou la géographie peuvent s’avérer complexes, et nécessiter une importante puissance de calcul (imaginez calculer l’ensemble des routes, départementales, nationales qui sont en intersection avec un nouveau tracé de ligne ferrioviaire qui traverse la France). Parmi toutes les méthodes qui existent pour manipuler les types spatiaux, certaines sont très coûteuses, tel que STDistance(), STContains() ou encore STIntersects().

Prenons un exemple, nous voulons connaître l’ensemble des routes qui croisent la ligne du nouveau TGV Nice-Brest (oui, c’est bien une ligne de TGV fictive :) ) :
<pre><code lang="sql">SELECT  NomRoute
FROM    Routes
WHERE   RouteGeometry.STIntersects(@TraceLigneTGV)</code></pre>
&nbsp;

Quand nous exécutons cette requête, SQL Server va devoir parcourir l’ensemble des lignes de la table Routes et, pour chacune des lignes, appeler la méthode STIntersects() pour savoir si il y a bien une intersection entre l’objet courant et @TraceLigneTGV.
<blockquote><strong>Au fait, on fait comment pour calculer une intersection ?
</strong>Bonne question ! C’est un des problèmes de base en géométrie algorithmique. On le retrouve par exemple très fréquemment dans les jeux vidéos afin de calculer la collision entre deux objets.  Prenons un exemple simple : nous avons deux carrés, chacun représentés par une série de 4 coordonnées (les coins). Pour valider si oui ou non ces rectangles sont en intersection, il suffit de voir si les intervalles X du carré A sont en intersection avec les intervalles X du carré B, et si les intervalles Y du carré A sont en intersection avec les intervalles Y du carré B. L’intervalle est la plage {minimum,maximum} qu’occupe le rectangle sur un axe.

<a href="http://blog.maneu.net/wp-content/uploads/2012/07/image1.png"><img style="background-image: none; padding-top: 0px; padding-left: 0px; display: inline; padding-right: 0px; border-width: 0px;" title="image" src="http://blog.maneu.net/wp-content/uploads/2012/07/image_thumb.png" alt="image" width="386" height="328" border="0" /></a>
<table width="200" border="0" cellspacing="0" cellpadding="2">
<tbody>
<tr>
<td valign="top" width="66">Rectangle</td>
<td valign="top" width="66">Intervalle X</td>
<td valign="top" width="66">Intervalle Y</td>
</tr>
<tr>
<td valign="top" width="66">A</td>
<td valign="top" width="66">{2,7}</td>
<td valign="top" width="66">{1,6}</td>
</tr>
<tr>
<td valign="top" width="66">B</td>
<td valign="top" width="66">{5,9}</td>
<td valign="top" width="66">{7,11}</td>
</tr>
</tbody>
</table>
Si on transforme cela en pseudo code, on obtient quelque chose comme :
<pre><code lang="sql">rectA.IntervalleX.Intersects(rectB.IntervalleX) &amp;&amp; rectA.IntervalleY.Intersects(rectB.IntervalleY).</code></pre>
Maintenant, comment fonctionne l’intersection d’une intervalle ? Et bien, il faut effectuer 4 comparaisons :
<ul>
	<li>Intervalle1 comprend entièrement Intervalle 2 : Intervalle1.Min &lt; Intervalle2.Min &amp;&amp; Intervalle1.Max &gt; Intervalle2.Max,</li>
	<li>Intervalle 1 est entièrement compris dans Intervalle 2 : Intervalle1.Min &gt; Intervalle2.Min &amp;&amp; Intervalle1.Max &lt; Intervalle2.Max,</li>
	<li>Intervalle1 est à cheval, côté mini, de Intervalle 2 : Intervalle1.Min &lt; Intervalle2.Min &amp;&amp; Intervalle1.Max &gt; Intervalle2.Min,</li>
	<li>Intervalle1 est à cheval, côté maxi, de Intervalle 2 : Intervalle1.min &lt; Intervalle2.Max &amp;&amp; Intervalle1.Max &gt; Intervalle2.Max</li>
</ul>
Si on ne compte pas le fait que l’on puisse avoir un résultat avec la première comparaison sans effectuer les autres, on effectuera au maximum 8 comparaisons de nombres et 8 comparaisons de booléens (deux par comparaisons, un entre chaque comparaison). Tout cela pour une seule intervalle.

<strong>Pour comparer l’intersection de deux rectangles, cela nécessite au maximum 32 comparaisons</strong>. Je n’ai parlé ici que de deux rectangles. Si on prend trois rectangles, on passe à 96 (32 x 3) comparaisons. Imaginez le nombre de comparaisons pour l’ensemble des segments de droites composant l’ensemble des routes de France…

A noter que cet algorithme est un des plus simples pour l’intersection de rectangles simples. Il existe également un autre <a href="http://rbrundritt.wordpress.com/2009/10/03/determining-if-two-bounding-boxes-overlap/">algorithme basé sur les centres et les rayons des rectangles</a>. Lignes, polygones et les autres opérations géométriques ont leur propres algorithmes, un peu plus complexes, mais beaucoup plus efficaces. Cet exemple n’est là que pour montrer la complexité de telles opérations…et ainsi l’intérêt du système d’index.</blockquote>
Si on reprend notre exemple, on se doute qu’effectuer toutes ces comparaisons pour l’ensemble des routes ne peut pas être efficace. De plus, n’importe quel humain se doute que la route Toulouse-Bayonne ne traversera pas la ligne de TGV Nice-Brest. La route Paris-Strasbourg non plus. <span style="text-decoration: underline;">On sait donc intuitivement qu’il y a des zones qui ne peuvent contenir que des lignes à exclure de notre résultat</span>. Il faudrait donc arriver à créer un découpage suffisamment efficace et pertinent pour nous aider à exclure les objets géométriques que l’on sait par avance non pertinent pour notre requête.

On entrevoit alors une solution intéressante :
<ul>
	<li>On commence par exécuter un <strong>filtre primaire,</strong>qui va retourner un ensemble de possibilités répondant à deux caractéristiques : l’ensemble des résultats corrects est obligatoirement inclus, mais certains éléments ne répondant pas aux critères peuvent-être inclus (des faux-positifs). Ce filtre doit être très rapide d’exécution. On peut le baser sur un découpage en zones pour exclure tous les objets appartenant aux zones qui ne correspondent pas à notre critère de recherche.</li>
	<li>Puis on exécute un <strong>filtre secondaire</strong> : plus lent, il va réellement valider les résultats un par un (par exemple, calculer l’intersection entre le résultat courant et le tracé du TGV). Il retournera exactement les résultats.</li>
</ul>
Ce mode de <em>présélection </em>des résultats nous permet de limiter le nombre d’enregistrements sur lequel exécuter le traitement lent de sélection, tout en étant sur de ne pas <em>louper</em> des résultats. C’est exactement comme cela que fonctionne SQL Server.

Avec l’arrivé des types <code>geometry</code> et <code>geography</code>, un nouveau type d’index est arrivé, le <strong>spatial index. </strong>Quand cet index existe sur une colonne de type spatial, c’est cet index qui est utilisé comme filtre primaire. Il retourne donc un set d’enregistrements plus affiné au filtre secondaire, qui – bien qu’il soit lent- retourne le résultat plus rapidement, ayant moins de résultats en entrée à analyser. Si il n’y a pas d’index, le filtre primaire est bypassé, et le filtre secondaire est exécuté sur l’ensemble du data set.
<h1></h1>
<h1>Cool, mais comment ca peut marcher ?</h1>
Vous le savez déjà : un index doit avoir un ordre de tri pour pouvoir être créé et organiser les données qu’il référence. Sur un type int, ou même varchar, c’est simple : on trie par ordre croissant nombre, alphabétique, chronologique. Cela se complique pour les types géographiques et géométriques : il n’existe pas d’ordre naturel. Cela dépend de vos données, de leur espace de représentation et de leur répartition dans cet espace.

Vous savez peut-être que SQL Server organise ses index sous forme d’arbre B (B-tree). C’est un stockage de données <a href="http://fr.wikipedia.org/wiki/Arbre_B"><em>sous une forme triée et permettant une exécution des opérations d’insertion et de suppression en temps amorti logarithmique</em></a><em>… </em>En clair, les données sont stockées sous une forme triée spécifique qui permet à chaque noeud de contenir plusieurs clés, et de limiter les opérations d’équilibrage de l’arbre à l’ajout ou la suppression d’éléments.

Il faut donc, pour cela pouvoir organiser l’ensemble de nos éléments en plages à plusieurs niveaux. Là aussi, avoir ce principe est très simple avec des nombres (1-200, contenant des sous-groupes 1-50,51-100,101-150,151-200), mais bien plus complexe avec des formes géométriques/géographiques.
<h3></h3>
<h2>Structure de l’index</h2>
On a donc du trouver un moyen de découper l’espace de manière prédictible, tout en pouvant s’adapter au mieux à l’ensemble des usages possibles. Il a donc été décidé de créer un système de grilles. L’ensemble de l’espace est découpé dans une première grille, puis chacune des cellules de cette grille est à nouveau découpé dans une grille complète.

<a href="http://blog.maneu.net/wp-content/uploads/2012/07/image3.png"><img style="background-image: none; float: none; padding-top: 0px; padding-left: 0px; margin-left: auto; display: block; padding-right: 0px; margin-right: auto; border-width: 0px;" title="image" src="http://blog.maneu.net/wp-content/uploads/2012/07/image_thumb2.png" alt="image" width="342" height="342" border="0" /></a>

Ainsi, l’objet en orange est situé dans les cellules 2-Niveau1, 3-Niveau2. Si l’on souhaite répondre à la question “Est-ce que l’objet vert est en intersection avec l’objet orange?” Il suffit de lire le premier niveau d’index pour obtenir la réponse.
<blockquote><strong>Pour l’expert
</strong>En réalité, le système de numérotation des grilles est beaucoup plus complexe que cela. Il est basé sur le <a href="http://en.wikipedia.org/wiki/Hilbert_curve">modèle de courbes de Hilbert</a>. Ce modèle est une “courbe fractale continue d’occupation de plan”. A part pour impressionner en soirée (mais l’occasion de le placer n’est pas fréquente :), ce modèle de courbes à la particularité de pouvoir remplir complètement un espace. C’est une courbe (donc en 1 dimension) qui peut remplir un espace en 2 dimensions “d’un seul trait”. Le modèle de courbes de Hilbert peut également servir de base à des labyrinthes. Fun, non ?
<p align="center"><img src="http://www.mathcurve.com/fractals/lebesgue/hilbertquad1.gif" alt="" width="178" height="174" /><img src="http://www.mathcurve.com/fractals/lebesgue/hilbertquad2.gif" alt="" width="185" height="169" />
<em><span style="background-color: #ffffff; color: #555555;"><span style="background-color: #e9e9e9; color: #777777;">Images provenant de <a title="http://www.mathcurve.com/fractals/lebesgue/lebesgue.shtml" href="http://www.mathcurve.com/fractals/lebesgue/lebesgue.shtml">http://www.mathcurve.com/fractals/lebesgue/lebesgue.shtml</a></span></span></em><em></em></p>
</blockquote>
&nbsp;
<h2>Distribution de l’index</h2>
Cette approche est intéressante, cependant, si nos données spatiales sont très étendues ou très regroupées dans des “pôles géographique”, la précision de cette grille 4x4 n’est pas forcément adéquate. Comme on peut le voir sur l’image suivante, nos données sont principalement réparties sur 5 zones. Cependant, sur la zone nord-ouest, nos nuages de points se chevauchent sur deux zones de niveau 2.
<p align="center"><a href="http://blog.maneu.net/wp-content/uploads/2012/07/image4.png"><img style="background-image: none; padding-top: 0px; padding-left: 0px; display: inline; padding-right: 0px; border-width: 0px;" title="image" src="http://blog.maneu.net/wp-content/uploads/2012/07/image_thumb3.png" alt="image" width="396" height="388" border="0" /></a></p>
Dans ce cas, si notre requête nécessite que l’on passe au niveau 2, notre index ne sera pas très pertinent.en effet, il retournera au moins une partie de l’autre groupe de points. Cependant, si on change la taille de la grille de niveau 2, pour passer à une grille 3x3, les groupes de points ne dépendent plus que d’une case. Dans cet exemple, c’est idéal. Cela ne sera pas toujours le cas avec vos données, mais l’objectif est d’optimiser au mieux, et ce type d’ajustement peut grandement aider :).

<a href="http://blog.maneu.net/wp-content/uploads/2012/07/image5.png"><img style="background-image: none; float: none; padding-top: 0px; padding-left: 0px; margin-left: auto; display: block; padding-right: 0px; margin-right: auto; border-width: 0px;" title="image" src="http://blog.maneu.net/wp-content/uploads/2012/07/image_thumb4.png" alt="image" width="418" height="435" border="0" /></a>

L’index spatial dans SQL Server peut avoir 3 différentes densités : (4x4 – 16 cases), Medium (8x8 – 64 cases) ou High (16x16 – 256 cases). Il est donc possible d’augmenter la résolution de la grille. Ce faisant, plus la résolution sera importante, plus la taille de l’index – et donc son coût en création et maintenance – sera important.

En dehors de la précision, l’index géographique a une autre caractéristique essentielle : il est composé de 4 niveaux. Ce nombre est fixe, mais il est possible d’ajuster la finesse de la grille séparément pour chacun des niveaux : par défaut à Medium. Si on reprend notre réflexion sur les routes et le trajet TGV, on pourrait imaginer avoir la France découpée tout d’abord à 16 cases, puis en 64 cases (grosso  modo équivalentes à un demi-département). En ajustant ainsi l’index, cela nous permet d’exclure plus de la moitié des routes seulement en lisant l’index de premier niveau. A noter que si les 4 niveaux sont avec une résolution de grille à Medium, cela représente tout de même 64 puissance 4 : 16,7 Million de cellules.

Ce nombre est important, mais la répartition l’est tout autant. Ainsi un index défini avec les résolutions HMMM et MMMH produisent le même nombre de cellules, mais que le résultat ainsi que leurs performances sont sensiblement différents. C’est à vous – en fonction de vos données et de leur répartition – de choisir et d’ajuster ces paramètres.
<h2></h2>
<h2>Construction de l’index</h2>
Une fois que votre index a été défini (avec ces grilles), faut indexer les données contenues dans votre table. Pour cela, la table est parcourue ligne par ligne. Une fois l’objet lu, le processus de pavage (<em>tesselation</em> en anglais) commence. Il permet de placer l’objet dans la hiérarchie de grilles, partant de la première grille, et si besoin, poursuivre à travers les 4 niveaux. A l’issue de ce processus, le jeu de cellules touchés est enregistré dans l’index spatial.

Ce processus peut être complexe, et là également, des règles ont été posées pour limiter le temps d’exécution de ce processus :
<ul>
	<li><strong>La règle de couverture</strong>: si l’objet couvre entièrement une cellule, elle est comptabilisée comme couverte, et le processus de pavage s’arrête (on ne descend pas au niveau inférieur),</li>
	<li><strong>La règle de cellules par objet</strong>: Cette règle permet de limiter le nombre de cellules qui sont comptabilisées pour un objet (hormis au niveau 1).</li>
	<li><strong>La règle de cellule la plus profonde</strong> : On enregistre uniquement les cellules les plus profondes, sauf si une cellule de niveau supérieur est entièrement couverte.</li>
</ul>
Une partie de ces règles sont ajustables, mais cela dépasse le cadre de cet article.
<blockquote>Il y a des trucs super intéressants sur la tesselation, qui peut également s’appliquer à plein de domaines, dont la 3 dimension et les jeux vidéos (avec des nouveautés à ce sujet sur DirectX 11). Mais ce post est déjà assez grand <img class="wlEmoticon wlEmoticon-smile" style="border-style: none;" src="http://blog.maneu.net/wp-content/uploads/2012/07/wlEmoticon-smile.png" alt="Sourire" />. Si vous souhaitez en savoir plus  sur la tesselation dans SQL Server, je vous recommande la <a href="http://technet.microsoft.com/fr-fr/library/bb964712%28SQL.105%29.aspx">lecture de technet</a>.</blockquote>
<em><strong>Contenu schématique d’un index spatial</strong></em>
<table width="400" border="0" cellspacing="0" cellpadding="2">
<tbody>
<tr>
<td valign="top" width="100">ID Geom figure</td>
<td valign="top" width="100">Grid level</td>
<td valign="top" width="100">Cell number</td>
<td valign="top" width="100">“Covering” type</td>
</tr>
<tr>
<td valign="top" width="100">TraitBleu</td>
<td valign="top" width="100">1,2</td>
<td valign="top" width="100">2,3</td>
<td valign="top" width="100">touched</td>
</tr>
<tr>
<td valign="top" width="100">TraitVert</td>
<td valign="top" width="100">1</td>
<td valign="top" width="100">3</td>
<td valign="top" width="100">Partially covered</td>
</tr>
<tr>
<td valign="top" width="100">TraitViolet</td>
<td valign="top" width="100">1</td>
<td valign="top" width="100">4</td>
<td valign="top" width="100">fully covered</td>
</tr>
</tbody>
</table>
<span>Bien évidemment, quasiment tout ce que l’on a vu jusqu’à présent ne s’applique que si l’on parle de type gemoetry, et donc dans un plan. Cela devient beaucoup plus complexe dans le cas de la représentation de la te</span><span>rre. Pour pouvoir traiter les informations, appliquer nos grilles multiniveaux, calculer nos distances et intersections, nous avons besoin d’une représentation de la terre sur un plan. C’est ce que l’on appelle une projection.</span>
<blockquote><span><strong>La terre n’est pas ronde !
</strong>Non, sans déconner ! C’est ce que l’on peut voir grâce à l’une des missions de l’agence spatiale européenne (ESA) avec le projet <a href="http://www.esa.int/esaMI/GOCE/SEM3Q2VHJCF_0.html">GOCE</a> (Gravity field and steady-state Oecean Circulation Explorer).  Si on observe la <a href="http://fr.wikipedia.org/wiki/G%C3%A9o%C3%AFde">géoïde</a>, c’est à dire la représentation de la surface terrestre basée sur son champ de gravité et plus précises que les autres représentations.</span>

<a href="http://blog.maneu.net/wp-content/uploads/2012/07/image6.png"><img style="background-image: none; float: none; padding-top: 0px; padding-left: 0px; margin-left: auto; display: block; padding-right: 0px; margin-right: auto; border: 0px;" title="image" src="http://blog.maneu.net/wp-content/uploads/2012/07/image_thumb5.png" alt="image" width="244" height="204" border="0" /></a><span>
</span><span>Hormis l’anecdote, cela a une importance plus ou moins majeure par rapport à SQL Server. Est-ce que vos données et traitement utilisent l’altitude ? Avez-vous besoin d’une précision inférieure à 1 mètre ? Si oui, alors, vous devrez certainement vous soucier du SRID. En effet, il existe de nombreux référentiels spatiaux (plus de 4000), qui apportent de précisions différentes, soit sur la terre entière, soit sur des portions spécifiques de la Terre.</span>

&nbsp;</blockquote>
<span>Les projections sont donc indispensables pour effectuer des calculs et des recherches sur les type géographiques. SQL Server réalise pour nous cette projection, au besoin. Celle-ci est déterminée par le SRID des objets géographiques. </span>Si le sujet des projections vous intéresse, je vous conseille la lecture de l’article <strong><a href="http://msdn.microsoft.com/en-us/library/cc749633.aspx">Flat maps for a Round planet</a></strong> sur MSDN.
<h2></h2>
<h1></h1>
<h1>Créer des requêtes utilisant les index spatiaux</h1>
Les index sont désormais en place, vos données également: c’est une très bonne chose, Vous allez désormais pouvoir requêter tous azimut. Mais avant cela, vous devez vous assurer que les requêtes que vous allez écrire vont réellement utiliser les index spatiaux (tout du moins, les requêtes qui se doivent d’être performantes. En effet, seules certaines méthodes sont supportées.

Pour les types géographiques, seules trois méthodes sont supportées : STIntersects(), STEquals() et STDistance(). Comme l’explique <a href="http://technet.microsoft.com/en-us/library/bb933796%28v=sql.105%29">la documentation</a>, ces méthodes doivent être utilisées dans la clause WHERE pour pouvoir utiliser l’index.

Les types géométriques nous laissent un peu plus de possibilités, en supportant STContains(), STDistance(), STEquals(), STIntersects(), STOverlaps(), STTouches() et STWithin(). En plus de l’utilisation dans une clause WHERE, vous pouvez également utiliser ces méthodes dans les clauses JOIN ON.
<blockquote><em>Note : Filter et STIntersects fonctionnent de la même manière sans index. Lorsqu’un index est utilisé, filter retourne uniquement l’équivalent du premier filtre. Il peut donc y avoir des faux positifs.</em></blockquote>
<h2>Limites des index spatiaux</h2>
Les index spatiaux ne peuvent être appliqués que sur des colonnes de type geometry ou geography. Vous pouvez créer un maximum de 249 index spatiaux sur une même colonne d’une même table. Enfin le processus de création des index ne permet pas d’utiliser la parallélisation sur votre serveur est multi-coeur.

Les limites des index spatiaux sont également très liés à leur configuration : résolution des grilles, zone englobante, etc…

La <a href="http://technet.microsoft.com/en-us/library/bb964740(v=sql.105)">documentation officielle</a> n’explicite pas beaucoup plus de restrictions sur les index spatiaux.
<blockquote>Beaucoup d’opérations géographiques font de la projection. Hors, les deux hémisphères doivent être projetés séparément, sur deux surfaces planes, rendant ainsi impossible qu’un objet géographique s’étende sur deux hémisphères.</blockquote>
<h2>Optimiser les performances des requêtes spatiales</h2>
<ul>
	<li>sp_help_spatial_geography_index</li>
	<li>sp_help_spatial_geometry_index</li>
</ul>
L’article sur le <a href="http://boomphisto.blogspot.fr/2011/04/black-art-of-spatial-index-tuning-in.html">blog de Todd</a> Jackson présente une étude de cas intéressante. Il a testé plusieurs paramétrages des index avec trois data sets différentes, entre 14 000 et 2.5 millions d’enregistrements.
<h3>Zone englobante et résolution de grille</h3>
Les index spatiaux sont toujours définis sur une colonne spatiale, et pour un espace fini. Dans le cas d’un index spatial géographique, l’espace maximum sera l’ensemble de la Terre (-180/180 longitude et –90/90 latitude). Il est possible de le réduire, par exemple si vos données ne couvrent que la France. Dans le cas d’une donnée de type géométrique, il faudra définir un espace rectangulaire. Il est également possible de créer de multiples index –un par région- et préciser au moment de votre requête quel index est à utiliser. Si vos requêtes ne couvrent qu’une zone par exécution, cela peut être intéressant.

Comme nous l’avons déjà vu, la résolution des différentes grilles est également un point très important, et souvent négligé. Je n’ai pas de recettes magique pour trouver automatiquement les résolutions adaptées à vos données et à vos requêtes…à part du travail et de la réflexion :).
<h3>Spatial index hints</h3>
Ca serait peut-être la première consigne à donner pour optimiser les requêtes spatiales : <strong><a href="http://www.sqlskills.com/BLOGS/BOBB/post/How-to-ensure-your-spatial-index-is-being-used.aspx">Assurez-vous que votre index est réellement utilisé</a></strong> ! Testez de forcer l’utilisation de votre index en rajoutant WITH (INDEX(xsHigh)) à la fin de votre requête et regardez le plan d’exécution de votre requête.

Et n’oubliez pas que les requêtes spatiales sont avant tout des requêtes. Vous savez déjà comment les optimiser ;).
<h1>On continue la lecture ?</h1>
Cet article est (enfin) terminé.
<ul>
	<li><a href="http://technet.microsoft.com/en-us/library/bb964712%28SQL.105%29.aspx">Technet : Spatial indexing overview</a>,</li>
	<li><a href="http://msdn.microsoft.com/en-us/library/cc749633.aspx">Flat Maps for a Round Planet</a>,</li>
	<li><a href="http://www.sharpgis.net">Le blog de Morten Nielsen, consacré aux GIS sous .net</a></li>
</ul>
