---
layout: post
title: PHP et Windows Server main dans la main
slug: php-et-windows-server-main-dans-la-main
date: 2007-10-13 20:02:00Z
---

<blockquote>   <p>Ce post a été importé depuis mon ancien blog, il se peut donc qu’il y ait des erreurs d’affichage.</p> </blockquote>  <p>Tout le sait, les plateformes Windows Server ne sont pas vraiment faites pour s’entendre avec du PHP (même si c’est possible), on ne trouve donc que très peu de serveurs Windows/IIS/PHP en production.</p>  <p>Microsoft a annoncé un partenariat avec Zend pour améliorer le support de PHP dans IIS (le serveur web de Microsoft), avec notamment à l’ordre du jour :</p>  <ul>   <li>La distribution de FastCGI directement dans le Zend CORE </li>    <li>Le support du mode <em>Server Core</em> de Windows Server 2008 de Zend Core </li>    <li>FastCGI sera livré en composant IIS de base avec Windows Server 2008 (ENFIN une installation simple ^^) </li>    <li>Le support de Windows CardSpace (un système d’authentification SSO) dans le Zend Framework (Mais alors on abandonne l’OpenID ?) </li>    <li><strong>Un Driver natif PHP pour SQL Server 2005</strong>, c’est MySQL qui ne va pas être content ^^</li> </ul>  <p>Que de bonnes nouvelles ^^</p>
