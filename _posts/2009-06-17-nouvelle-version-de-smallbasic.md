---
layout: post
title: Nouvelle version de SmallBasic
slug: nouvelle-version-de-smallbasic
date: 2009-06-17 07:34:00Z
---

<p>La dernière version de SmallBasic, un environnement de développement pour découvrir la programmation, est sorti.</p>  <p><a href="http://blog.christophermaneu.fr/wp-content/uploads/2009/06/splash1.png"><img style="border-right-width: 0px; display: block; float: none; border-top-width: 0px; border-bottom-width: 0px; margin-left: auto; border-left-width: 0px; margin-right: auto" title="splash[1]" border="0" alt="splash[1]" src="http://blog.christophermaneu.fr/wp-content/uploads/2009/06/splash1_thumb.png" width="391" height="240" /></a> Cette nouvelle version apporte quelques évolutions <em>globales</em> – version espagnole, correction de bugs, mise à jour de l’installeur – ainsi que de nouvelles possibilités.</p>  <h2>Les tableaux</h2>  <p>Vous pouvez désormais utiliser des tableaux dans votre code. Les clés des tableaux ne sont pas limités aux nombres et ces tableaux sont multidimensionnels. Un petit exemple :</p>  <pre lang="thinBasic" line="1">eleves[1]["Nom"]["Prenom"] = "Benjamin"
eleves[1]["Nom"]["Nom"] = "Dupont"
eleves[1]["Age"] = 45</pre>

<h2>&#160;</h2>

<h2>Variables non initialisées</h2>

<p>Le compilateur détecte désormais les variables qui ne sont pas initialisées et utilisées dans le code. Cela permet d’aider à résoudre les problèmes de noms de variables mal orthographiés (ou les fautes de frappe).</p>

<h2>Les nouveautés de l’environnement</h2>

<p>L’environnement SmallBasic propose quelques nouveautés sympathiques :</p>

<ul>
  <li><strong>La recherche</strong> : vous pouvez désormais effectuer des recherches avec Ctrl+3 et F3, </li>

  <li><strong>Enregistrer sous,</strong> </li>

  <li><strong>Remise en forme de programme : </strong>cette commande vous permet de remettre en forme votre code, c’est à dire de mettre les espaces, retours à la ligne et indentation là où il le faut afin que celui-ci soit lisible, </li>

  <li><strong>Un indicateur de ligne et de colone.</strong> </li>
</ul>

<p>Vous pouvez <a href="http://download.microsoft.com/download/C/A/F/CAF9E062-94D3-4003-80D9-44CDF7EC7BD9/SmallBasic.msi">téléchargez la version 0.5 de SmallBasic</a> ici, et si vous ne connaissez pas, téléchargez <a href="http://www.microsoft.com/downloads/details.aspx?displaylang=fr&amp;FamilyID=61481b74-eb45-42b8-a777-8f3644406787"><em>Introduction à SmallBasic</em></a>.</p>
