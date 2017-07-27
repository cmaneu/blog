---
layout: post
title: Windows Phone 7 – Les changements à apporter à votre code
slug: windows-phone-7-les-changements-apporter-votre-code
date: 2010-04-29 21:27:25Z
---

<html xmlns="">Microsoft <a href="http://blog.christophermaneu.fr/2010/04/windows-phone-7-nouvelle-version-des-outils-de-dveloppement/">a publié aujourd’hui une</a> nouvelle version du SQK Windows Phone 7. Cette version apporte quelques nouveautés intéressantes, mais peut également poser quelques problèmes avec votre code existant. Les <em><a href="http://download.microsoft.com/download/D/9/A/D9A6B6ED-D1CF-4FB3-86BD-62A55959175F/ReleaseNotes.htm">releases notes</a></em> détaillent la liste complète de ces changements, et voici ma sélection des plus importants.
<blockquote><strong><em>Note : N’oubliez pas, cette nouvelle version est toujours une CTP, ce qui veut dire que nous sommes bien loin de la qualité et de la stabilité d’une version finale.</em></strong></blockquote>
<p$1$2$3$4$5$6>
<h2>Installation et outils</h2>
Cette version est compatible avec les versions RTM de Visual Studio 2010 en version anglaises (et uniquement les versions RTM), et Expression Blend 4 preview, si vous téléchargez les outils suivants :
<ul>
	<li><a href="http://www.microsoft.com/downloads/details.aspx?FamilyID=47f5c718-9dec-4557-9687-619c0fdd3d4f&amp;displaylang=en">Expression Blend Add-in Preview for Windows Phone (April Refresh)</a></li>
	<li><a href="http://www.microsoft.com/downloads/details.aspx?FamilyID=86370108-4c14-42ee-8855-226e5dd9b85b&amp;displaylang=en">Expression Blend SDK Preview for Windows Phone (April Refresh)</a></li>
</ul>
<p$1$2$3$4$5$6>
<h2>Votre code</h2>
Il y a plusieurs impacts importants sur votre code avec l’arrivée de cette nouvelle version. J’en ai relevées quelques unes que je considère comme importantes, cependant, n’hésitez pas à lire intégralement les <a href="http://download.microsoft.com/download/D/9/A/D9A6B6ED-D1CF-4FB3-86BD-62A55959175F/ReleaseNotes.htm">releases notes</a> (en anglais)
<ul>
	<li><strong>Les assemblies signées </strong>ne se chargent plus, y compris des librairies Microsoft telles que WCF Data Services ou le Silverlight Toolkit. Tim heuer <a href="http://timheuer.com/blog/archive/2010/04/29/windows-phone-tools-update-april-2010-silverlight-xna.aspx">détaille la procédure, certes un peu barbare, sur son blog</a>. Tout ceci sera corrigé dans une prochaine version,</li>
	<li><strong>Pour les projets XNA? on tourne a 90°</strong>. Par défaut, a partir de maintenant, les Jeux XNA sont exécutés en paysage et plus en portrait. Cependant, l’émulateur n’est pas parfait sur ce point là, et vous devez donc forcer le backbuffer aux bonnes tailles.</li>
<pre>graphics.PreferredBackBufferWidth = 480;
graphics.PreferredBackBufferHeight = 800;</pre>
</ul>
<ul>
	<li><strong>Le clavier ne fonctionne pas en XML</strong> : c’est quelque chose que je ne comprends pas (car cela fonctionnait correctement dans l’ancienne version), mais pas de clavier pour cette version…</li>
	<li><strong>Si vous utilisez le Push</strong>, <a href="http://msdn.microsoft.com/en-us/library/ff402545(v=VS.92).aspx">http://msdn.microsoft.com/en-us/library/ff402545(v=VS.92).aspx</a>le format de notification change et vous devez ajoutermodifier le fichier wpappmanifest.xml pour préciser un <em>Publisher</em></li>
</ul>
Vous pouvez rétourner à vos développements :)
