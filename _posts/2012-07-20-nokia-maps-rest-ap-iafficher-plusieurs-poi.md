---
layout: post
title: Nokia Maps REST API - Afficher plusieurs points d'intérêt (POI)
slug: nokia-maps-rest-ap-iafficher-plusieurs-poi
date: 2012-07-20 07:00:00Z
---

J’ai écrit cette semaine un article pour le blog des développeurs Nokia France. Le sujet : <a href="http://www.nokiadevblog.fr/2012/07/windows-phone-utiliser-lapi-rest-nokia-maps-dans-vos-applications-windows-phone/">utiliser l’API REST Nokia Maps dans vos applications Windows Phone</a>. J’ai eu quelques heures après la publication une question sur twitter : <strong>Comment faire pour afficher plusieurs points sur la carte ?</strong>

La réponse est assez simple ;)
<h1><!--more--></h1>
<h1>Afficher plusieurs points d’intérêts</h1>
Il suffit pour cela d’utiliser le paramètre <strong>poi</strong> en combinant l’ensemble des positions des marqueurs, chaque position était séparé par une virgule. Cela peut être un peu confus car la latitude/longitude est séparée par une virgule, et chaque groupe latitude+longitude également. Ainsi, l’URL suivante nous place bien deux marqueurs.
<pre>http://m.nok.it/?app_id=%appId%&amp;c=48.858489,2.293600&amp;z=15&amp;nord&amp;<strong>poi=48.858487,2.2936005,48.853965,2.300777</strong>&amp;w=500&amp;h=500</pre>
<a href="http://blog.maneu.net/wp-content/uploads/2012/07/m_nok_it1.jpg"><img style="background-image: none; float: none; padding-top: 0px; padding-left: 0px; margin-left: auto; display: block; padding-right: 0px; margin-right: auto; border-width: 0px;" title="m_nok_it[1]" src="http://blog.maneu.net/wp-content/uploads/2012/07/m_nok_it1_thumb.jpg" alt="m_nok_it[1]" width="561" height="561" border="0" /></a>
<h1>Personnaliser l’affichage des marqueurs</h1>
Il existe un certain nombre de paramètres :

&nbsp;
<table width="693" border="0" cellspacing="0" cellpadding="2">
<tbody>
<tr>
<td valign="top" width="104"><strong><span style="font-size: medium;">Nom du paramètre</span></strong></td>
<td valign="top" width="172"><strong><span style="font-size: medium;">Description</span></strong></td>
<td valign="top" width="415"><strong><span style="font-size: medium;">Exemple</span></strong></td>
</tr>
<tr>
<td valign="top" width="120"><span style="font-size: small;">poitxs</span></td>
<td valign="top" width="175"><span style="font-size: small;">Taille du texte</span></td>
<td valign="top" width="403"><a href="http://blog.maneu.net/wp-content/uploads/2012/07/m_nok_it2.jpg"><span style="font-size: small;"><img style="background-image: none; padding-top: 0px; padding-left: 0px; display: inline; padding-right: 0px; border: 0px;" title="m_nok_it[2]" src="http://blog.maneu.net/wp-content/uploads/2012/07/m_nok_it2_thumb.jpg" alt="m_nok_it[2]" width="240" height="240" border="0" /></span></a></td>
</tr>
<tr>
<td valign="top" width="132"><span style="font-size: small;">poifc</span></td>
<td valign="top" width="176"><span style="font-size: small;">Couleur de fond, exprimé en couleurs hexa (avec ou sans transparence)</span></td>
<td valign="top" width="395"><a href="http://blog.maneu.net/wp-content/uploads/2012/07/m_nok_it21.jpg"><span style="font-size: small;"><img style="background-image: none; padding-top: 0px; padding-left: 0px; display: inline; padding-right: 0px; border: 0px;" title="m_nok_it[2]" src="http://blog.maneu.net/wp-content/uploads/2012/07/m_nok_it2_thumb1.jpg" alt="m_nok_it[2]" width="240" height="240" border="0" /></span></a></td>
</tr>
<tr>
<td valign="top" width="132"><span style="font-size: small;">poitxc</span></td>
<td valign="top" width="176">Couleur du texte</td>
<td valign="top" width="395"><a href="http://blog.maneu.net/wp-content/uploads/2012/07/m_nok_it4.jpg"><img style="background-image: none; padding-top: 0px; padding-left: 0px; display: inline; padding-right: 0px; border: 0px;" title="m_nok_it[4]" src="http://blog.maneu.net/wp-content/uploads/2012/07/m_nok_it4_thumb.jpg" alt="m_nok_it[4]" width="240" height="240" border="0" /></a></td>
</tr>
</tbody>
</table>
<h1>Aller plus loin avec les POI Extended</h1>
Les possibilités sont déjà intéressantes, mais comment faire si on souhaite aller plus loin, en personnalisant par exemple le texte ? La première chose qu’il est possible de réaliser est d’ajouter le paramètre <strong>poilbl=1</strong>. Cela va remplacer la numérotation chiffrée par une numérotation alphabétique.

<a href="http://blog.maneu.net/wp-content/uploads/2012/07/m_nok_itCAZX89UO.jpg"><img style="background-image: none; float: none; padding-top: 0px; padding-left: 0px; margin-left: auto; display: block; padding-right: 0px; margin-right: auto; border: 0px;" title="m_nok_itCAZX89UO" src="http://blog.maneu.net/wp-content/uploads/2012/07/m_nok_itCAZX89UO_thumb.jpg" alt="m_nok_itCAZX89UO" width="456" height="367" border="0" /></a>

Vous voulez aller plus loin de chez plus loin ? Ok, pas de soucis !
<h2>Les poi “bulles” étendus</h2>
Ces POIs vous permettent de personnaliser le contenu de la bulle, la taille et la couleur du texte, ainsi que la couleur de fond. La syntaxe est légèrement différente. Vous devez utiliser un paramètre par point d’intérêt. Le paramètre est poixA, oú A est un nombre de 0 à votre maximum de points. la valeur de ce paramètre est comme suit : <strong>position;texte;couleurfond;couleurtexte;tailletexte</strong>

<a title="http://m.nok.it/?app_id==_peU-uCkp-j8ovkzFGNU&amp;z=15&amp;nord&amp;w=400&amp;h=400&amp;poix0=48.858487,2.2936005;cyan;white;18;hello&amp;poix1=48.853965,2.300777;cyan;white;24;world" href="http://m.nok.it/?app_id==_peU-uCkp-j8ovkzFGNU&amp;z=15&amp;nord&amp;w=400&amp;h=400&amp;poix0=48.858487,2.2936005;cyan;white;18;hello&amp;poix1=48.853965,2.300777;cyan;white;24;world">http://m.nok.it/?app_id==_peU-uCkp-j8ovkzFGNU&amp;z=15&amp;nord&amp;w=400&amp;h=400&amp;poix0=48.858487,2.2936005;cyan;white;18;hello&amp;poix1=48.853965,2.300777;cyan;white;24;world</a>

<a href="http://blog.maneu.net/wp-content/uploads/2012/07/m_nok_it22.jpg"><img style="background-image: none; float: none; padding-top: 0px; padding-left: 0px; margin-left: auto; display: block; padding-right: 0px; margin-right: auto; border: 0px;" title="m_nok_it[2]" src="http://blog.maneu.net/wp-content/uploads/2012/07/m_nok_it2_thumb2.jpg" alt="m_nok_it[2]" width="483" height="483" border="0" /></a>
<h2>Les poi “texte brut” étendus</h2>
Ces autres types de POI vous permettent d’afficher directement sur la carte le texte, sans punaise. Le principe est identique aux poi bulles étendus, sauf que le paramètre est de la forme <strong>txA</strong>.

La forme d’un marqueur est également différente : <strong>position;texte;couleurfond;couleurtexte;tailletexte</strong>

<strong></strong><a href="http://blog.maneu.net/wp-content/uploads/2012/07/m_nok_it23.jpg"><img style="background-image: none; float: none; padding-top: 0px; padding-left: 0px; margin-left: auto; display: block; padding-right: 0px; margin-right: auto; border: 0px;" title="m_nok_it[2]" src="http://blog.maneu.net/wp-content/uploads/2012/07/m_nok_it2_thumb3.jpg" alt="m_nok_it[2]" width="448" height="448" border="0" /></a>

A vous de jouer !
