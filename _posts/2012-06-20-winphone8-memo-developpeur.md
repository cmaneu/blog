---
layout: post
title: Windows Phone 8–Mémo développeur–Juin
slug: winphone8-memo-developpeur
date: 2012-06-20 19:00:52Z
---

Microsoft vient d’annoncer – lors du premier Windows Phone Summit – une partie des grandes nouveautés qui arrivent sur la prochaine version de Windows Phone. Ce post est destiné aux développeurs existants pour Windows Phone, avec les éclairages et visions personnelles.L’organisation de ce post reprend les 8 catégories d’annonces.
<h1></h1>
<h1>Une première annonce : cœur Windows 8 / Windows Phone 8</h1>
Cette première annonce – qui ne fait pas partie des 8 mais qui est de taille – <strong>Windows Phone 8 partage le même kernel que Windows 8.</strong> Cette nouvelle est, à ce stade, une très bonne chose. C’est déjà le cas sur iOs depuis la première version, en partie le cas sur Android. Cela ne changera pas la vie d’un développeur “classique” (pour le moment), mais donne une plus grande flexibilité dans la création de nouveaux devices.
<h1>1. “Largest &amp; greatest hardware”</h1>
<h2>Multicore support</h2>
Windows Phone 8 supportera des téléphones multi-cœurs (2, 4, plus…). Ce que l’on peut présager pour le développeur :
<ul>
	<li>Levée des limitations pour les backgrounds agents et tasks,</li>
	<li>L’arrivée d’async,</li>
	<li>La possibilité d’avoir du code “qui tourne” pour changer les tuiles/tiles en temps réel (les coder en XAML ?)</li>
</ul>
<strong>Support de la VoIP et de la géolocation</strong>

Cela n’a pas été annoncé au même moment, mais les applications en background auront désormais accès à la position GPS en background. Si vous développez une application VoIP, l’intégration total est désormais possible.
<h2>Support du MicroSD</h2>
Des cartes SD sur un téléphone, c’est forcément plus d’espace disque. Mais comment la gérer d’un point de vue d’un développeur ? Pour cela, on peut regarder ce qui est fait du côté d’Android, et penser que quelque chose du même type sera réalisé :
<ul>
	<li>Une manière différente d’accéder au storage du téléphone et au storage externe (microsd),</li>
	<li>On devra valider que le fichier que l’on souhaite est toujours sur la carte SD (elle a pu changer),</li>
</ul>
<h2>De nouvelles résolutions</h2>
Windows Phone 8 supportera au total 3 résolutions :
<div align="center">
<table width="222" border="0" cellspacing="0" cellpadding="2" align="center">
<tbody>
<tr>
<td valign="top" width="95"><span style="font-size: large;">WVGA</span></td>
<td valign="top" width="125"><span style="font-size: large;">800x480</span></td>
</tr>
<tr>
<td valign="top" width="95"><span style="font-size: large;">WXGA</span></td>
<td valign="top" width="125"><span style="font-size: large;">1280x768</span></td>
</tr>
<tr>
<td valign="top" width="95"><span style="font-size: large;">720p</span></td>
<td valign="top" width="125"><span style="font-size: large;">1280x720</span></td>
</tr>
</tbody>
</table>
</div>
Les impacts sur votre applications ne sont pas encore détaillés, mais il y a plusieurs choses que l’on peut déjà dire :
<ul>
	<li>Images des tuiles : entre une nouvelle taille de tuile (4 fois plus petit que ce qui existe déjà) et des nouvelles résolutions, on peut facilement imaginer que de nouvelles images seront nécessaires,</li>
	<li>Images : Là aussi, des résolution 30% plus importantes peuvent nécessiter des mises à jour de vos images,</li>
	<li>Applications Silverlight : Vous le savez, Silverlight sait parfaitement s’adapter aux changements de résolutions…tant que rien n’est codé en dur ! <strong>Faites donc un tour du code de vos applications </strong>pour vérifier cela (code behind, animations, …)</li>
	<li>Applications XNA : La première chose à laquelle on peut penser, ce sont les textures. Pour le reste, cela dépend si vous avez codé votre jeu proprement ou pas :). Dans tous les cas, un petit tour de votre code dès maintenant peut être une bonne idée.</li>
</ul>
<h1>2. Internet Explorer 10</h1>
C’est – pour moi – une très bonne nouvelle pour le web. On commence à avoir un set de moteurs de rendering communs à l’ensemble des plateformes : safari-chrome-webkit, et internet explorer. Cela permettra de réduire notre panel de test :).

Cette annonce permet également d’amener tout ce qui fait Internet Explorer 10 aujourd’hui : la performance JavaScript et HTML, le touch support de HTML 5.
<h1>3. Développement Natif</h1>
Développeurs C/C++, vous êtes désormais les bienvenus !. Le développement natif sur Windows Phone, peut être vu de deux points de vue :
<ul>
	<li><strong>Jeux vidéos </strong>:  Avec le support de C/C++ et DirectX, la création/portabilité d’un jeu entre Windows Phone/ Windows 8/ XBox sera aisée.</li>
	<li><strong>Portabilité : </strong>Cette annonce va peut-être permettre d’avoir un modèle (en dehors de Mono) de développement iOS/Android/Windows Phone performant. Ces trois plateformes permettant de développer en natif, le partage de code sera certainement un axe fort.</li>
</ul>
A noter qu’<strong>il est possible de partager du code C/C++, XAML/C#/VB et HTML5</strong> (via le browser control) au sein de la même application.
<h1></h1>
<h1>4. NFC</h1>
L’arrivée de NFC mérite un article entier – que je vais rajouter dans ma Todo –. Pour le moment, je vais simplement indiquer quelques arguments en faveur de NFC :
<ul>
	<li>Plus rapide et moins d’erreurs que les autres technologies de tag (QRCode, Microsoft Tag, …)</li>
	<li>Des scénarios plus complexes (“reconnaissance”, sharing entre devices, etc…)</li>
</ul>
<h1></h1>
<h1>5. Wallet</h1>
Le principe de Wallet est simple :  remplacer à terme votre portefeuille. Cartes de crédit, cartes de fidélité, d’accès, etc… Cela adresse plus l’utilisateur que le développeur (du moins une petite partie des développeurs).
<h1></h1>
<h1>6. Nokia Maps</h1>
C’était dans l’air depuis un moment – et désormais officiel – Nokia maps fera partie intégrante de Windows Phone 8. De bonnes choses pour les utilisateurs, tel que les maps hors lignes, une vraie navigation turn-by-turn. Pour les développeurs, nous allons voir arriver un nouveau contrôle Nokia Maps. Je vous invite à aller voir <a href="http://www.developer.nokia.com/Develop/Maps/">http://www.developer.nokia.com/Develop/Maps/</a> pour le moment (et à demander une API key).
<h1></h1>
<h1>7. Gestion du parc Windows Phone pour les ITs</h1>
J’ai déjà présenté deux sessions sur Windows Phone en entreprise aux deux dernière éditions des Microsoft TechDays…et il y a désormais un nouveau chapitre à ouvrir :
<ul>
	<li>Déploiement d’application LOB <strong>sans passer par le store,</strong></li>
	<li>Cryptage et boot sécurisé avec Bitlocker</li>
	<li>Gestion des devices centralisée – par System Center</li>
	<li>L’arrivée d’un <strong>Hub société</strong>. Les démonstrations ne montrent pas clairement si c’est juste une application custom, ou quelque chose d’un peu plus automatisé, mais c’est quelque chose d’intéressant.</li>
</ul>
<h1></h1>
<h1>8. L’écran d’accueil</h1>
L’écran d’accueil offre une nouvelle taille de tuile pour l’utilisateur, mais en tout deux pour le développeur. Je supute également que nous allons pouvoir modifier nos tuiles encore plus dynamiquement qu’avant.
<h1>Speech API</h1>
Il me tarde de mettre la main là dessus. Les développeurs vont pouvoir faire parler (text to speech) leurs application, et utiliser la plateforme de reconnaissance vocale. Pour un tas d’applications (cookbook ?), cela peut être un vrai plus.
<h1>Big news : SQLite sur Windows 8 et Windows Phone</h1>
Microsoft a annoncé avoir porté SQLite sur Windows 8 et Windows Phone. D’un point de vue technique, cela nous apporte enfin une base de données embarquée complète, et existante sur d’autres plateformes (un + pour le portage). Quid de la synchronisation dans le cloud ?

D’un point de vue stratégique…que deviens SQL Ce ?
<h1>Autres nouveautés</h1>
<ul>
	<li><strong>Mises à jour du téléphone over-the-air : </strong>plus besoin de le connecter à Zune (et donc la fin de zune ?)</li>
	<li><strong>InApp Purshasing </strong>via le wallet ?</li>
	<li><strong>App To App communication</strong> : C’était dans la vidéo avec la petite famille</li>
	<li><strong>Bluetooth &amp; IPV6</strong></li>
	<li><strong>Compile in the cloud :</strong> C’est une bonne et une mauvaise chose.</li>
	<li><strong>L’émulateur sur HyperV : </strong>Validez que votre machine supporte Hyper V pour Windows 8…</li>
</ul>
