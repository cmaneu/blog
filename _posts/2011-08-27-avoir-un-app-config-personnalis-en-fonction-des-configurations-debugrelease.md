---
layout: post
title: Avoir un app.config personnalisé en fonction des configurations (debug/release…)
slug: avoir-un-app-config-personnalis-en-fonction-des-configurations-debugrelease
date: 2011-08-27 15:58:43Z
---

<p>L’utilisation du fichier app.config dans les applications .net est tout à fait commun. Que ce soit pour stocker des variables “personnalisées” (appSettings, sections spécifiques) ou des variables utilisées par les différents outils/framework (chaînes de connexions aux bases de données, bindings WCF, …), on en retrouve dans une grande majorité de projets .net.</p>  <p>Il y a également une question récurrente qui vient avec l’utilisation de l’app.config : <strong>Comment faire pour avoir un fichier pour mon poste de dev, et un fichier pour la prod, <u>simplement ?</u></strong></p>  <p>Il y a désormais une solution simple et efficace avec <strong><a href="http://visualstudiogallery.msdn.microsoft.com/69023d00-a4f9-4a34-a6cd-7e854ba318b5/">SlowCheetah – XML Transforms</a></strong>, un plugin Visual Studio 2010 gratuit, développé par un Microsoftee, qui permet de gérer simplement des XML différents pour chacune de vos configurations.</p>  <h2>Go Cheetaa ! </h2>  <p>Une fois <a href="http://visualstudiogallery.msdn.microsoft.com/69023d00-a4f9-4a34-a6cd-7e854ba318b5/">le plugin installé</a>, ouvrez votre projet avec un fichier app.config (qui peut donc déjà exister ;), le menu contextuel laisse apparaitre une nouvelle option <em>Add Transform. </em></p>  <p><a href="http://blog.maneu.org/wp-content/uploads/2011/08/image1.png"><img style="background-image: none; border-right-width: 0px; padding-left: 0px; padding-right: 0px; display: block; float: none; border-top-width: 0px; border-bottom-width: 0px; margin-left: auto; border-left-width: 0px; margin-right: auto; padding-top: 0px" title="image" border="0" alt="image" src="http://blog.maneu.org/wp-content/uploads/2011/08/image_thumb1.png" width="317" height="252" /></a></p>  <p>Après avoir accepté l’avertissement, vous verrez votre fichier App.config, plus un fichier par configuration existante App.<em>Configuration</em>.config.</p>  <p><a href="http://blog.maneu.org/wp-content/uploads/2011/08/image2.png"><img style="background-image: none; border-right-width: 0px; padding-left: 0px; padding-right: 0px; display: block; float: none; border-top-width: 0px; border-bottom-width: 0px; margin-left: auto; border-left-width: 0px; margin-right: auto; padding-top: 0px" title="image" border="0" alt="image" src="http://blog.maneu.org/wp-content/uploads/2011/08/image_thumb2.png" width="318" height="143" /></a></p>  <p>A ce stade, si vous ouvrez les fichiers App.Debug.config ou App.Release.config, leur contenu peut vous décevoir. En effet, ils sont (quasi) vides. Ce qu’il faut comprendre, c’est que ces fichiers utilisent en réalité <a href="http://msdn.microsoft.com/en-us/library/dd465326(VS.100).aspx">la syntaxe de transformation déjà existante des projets web.config</a>. En d’autres termes, vos fichiers App.<em>configuration</em>.config sont des fichiers de transformations XML (XDT). Ils vous permettent de transformer des éléments précis du fichier XML app.config. Voyons cela avec un exemple. Nous avons ici un fichier assez classique, avec deux settings et une chaîne de connexion.</p>  <pre><code>
&lt;?xml version=&quot;1.0&quot; encoding=&quot;utf-8&quot;?&gt;
&lt;configuration&gt;
  &lt;appSettings&gt;
    &lt;add key=&quot;appName&quot; value=&quot;Mon logiciel&quot; /&gt;
    &lt;add key=&quot;version&quot;  value=&quot;1.2.3.0&quot;/&gt;
  &lt;/appSettings&gt;
&lt;connectionStrings&gt;
  &lt;add name=&quot;BeehiveEntities&quot; 
       connectionString=&quot;metadata=res://*/MonModele.csdl|res://*/MonModele.ssdl|res://*/MonModele.msl;provider=System.Data.SqlClient;provider connection string=&quot;data source=.;initial catalog=MaBase;integrated security=True;App=MonAppli&quot;&quot; providerName=&quot;System.Data.EntityClient&quot; /&gt;
&lt;/connectionStrings&gt;&lt;/configuration&gt;
</code></pre>

<p>Pour la version debug, nous voulons changer le nom du logiciel et faire apparaître une nouvelle clée pour activer le débug (ceci est pour l’exemple d’ajout de balises, il y a d’autres méthodes pour gérer le “debug” ;)). Nous allons donc reproduire les “parties” – à savoir hiérarchie de balises et la balise elle-même- de ce que nous voulons modifier. Pour que la magie opère, nous devons rajouter quelques attributs supplémentaires (en jaune) permettant de déterminer le type d’opération que nous souhaitons réaliser (remplacer, insérer, supprimer), et la manière de trouver quelle ligne correspond dans le fichier d’origine (via des attributs, une requête XPath, …).</p>

<pre><code>
&lt;?xml version=&quot;1.0&quot; encoding=&quot;utf-8&quot; ?&gt;
&lt;configuration xmlns:xdt=&quot;http://schemas.microsoft.com/XML-Document-Transform&quot;&gt;
  &lt;appSettings&gt;
    &lt;add key=&quot;appName&quot; value=&quot;Mon Logiciel - Bewise Internal&quot; <font style="background-color: #ffff00">xdt:Transform=&quot;Replace&quot; xdt:Locator=&quot;Match(key)&quot;</font></code><code>/</code><code><font style="background-color: #ffff00"></font>&gt;
    &lt;add key=&quot;debug&quot; value=&quot;true&quot; <font style="background-color: #ffff00">xdt:Transform=&quot;Insert&quot;</font> /&gt;
  &lt;/appSettings&gt;
&lt;/configuration&gt;

</code></pre>

<p>Il ne vous reste plus qu’à faire F5 pour voir le résultat….ou de faire un clic droit !</p>

<p><a href="http://blog.maneu.org/wp-content/uploads/2011/08/image3.png"><img style="background-image: none; border-right-width: 0px; padding-left: 0px; padding-right: 0px; display: block; float: none; border-top-width: 0px; border-bottom-width: 0px; margin-left: auto; border-left-width: 0px; margin-right: auto; padding-top: 0px" title="image" border="0" alt="image" src="http://blog.maneu.org/wp-content/uploads/2011/08/image_thumb3.png" width="342" height="172" /></a></p>

<p>L’option <strong>Preview Transform </strong>va réaliser l’opération de transformation et va vous afficher le résultat en parallèle avec le fichier App.config d’origine.</p>

<p><a href="http://blog.maneu.org/wp-content/uploads/2011/08/image4.png"><img style="background-image: none; border-right-width: 0px; padding-left: 0px; padding-right: 0px; display: inline; border-top-width: 0px; border-bottom-width: 0px; border-left-width: 0px; padding-top: 0px" title="image" border="0" alt="image" src="http://blog.maneu.org/wp-content/uploads/2011/08/image_thumb4.png" width="643" height="276" /></a></p>

<p>&#160;</p>

<h3>Un de plus ?</h3>

<p>Si par la suite, vous ajoutez des configurations à votre projet, il vous suffit de recommencer l’opération et les nouveaux fichiers seront ajoutés automatiquement, sans écraser vos fichiers existants.</p>

<h2>Ca fonctionne avec tous les projets ?</h2>

<p>Presque ! Les projets Web ne fonctionnement pas tel quel, mais ils supportent déjà cette fonctionnalité nativement. Mes tests ont montrés que les types de projets suivants fonctionnent nativement (en C#):</p>

<ul>
  <li>Console </li>

  <li>Windows Forms </li>

  <li>WPF </li>

  <li>VSTO (testé sur plugin Outlook 2010) </li>

  <li>Service Windows </li>

  <li>Solutions Sharepoint 2010 </li>

  <li>XNA (Windows uniquement) </li>

  <li>Addin Visual Studio </li>

  <li>Projets de test VS </li>

  <li>Azure Worker Role</li>
</ul>

<h2>Et le travail en équipe / l’intégration continue ?</h2>

<p>L’une des parties cool de ce projet est d’utiliser des fonctionnalités standard des outils, en l’occurence des targets MSBuild. Cela veut dire que, pour le coût de quelques modifications de votre fichier de projet et de la copie de quelques fichiers dans votre repository, cela <strong>peut fonctionner à la fois pour votre équipe – et quelque soit votre contrôleur de source (TFS, SVN, Git, ..) – et pour l’intégration continue</strong> (avec TFS, je n’ai pas testé avec d’autres outils tels que TeamCity).</p>

<p>Pour cela, il vous faudra : </p>

<ul>
  <li>Copier le contenu du répertoire $(LOCALAPPDATA)MicrosoftMSBuildSlowCheetahv1 dans votre solution, </li>

  <li>Modifier les fichiers projets concernés pour modifier la ligne ci-dessus avec le nouvel emplacement. </li>
</ul>

<p>Il ne reste plus qu’à espérer que tout ce fonctionnement sera intégré à Visual Studio vNext.</p>
