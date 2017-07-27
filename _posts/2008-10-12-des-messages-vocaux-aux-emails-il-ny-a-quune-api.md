---
layout: post
title: Des messages vocaux aux emails, il n'y a qu'une API !
slug: des-messages-vocaux-aux-emails-il-ny-a-quune-api
date: 2008-10-12 21:22:00Z
---

<p>Il y a quelques semaines, Orange dévoilait à ses partenaires une nouvelle API qui venaient rejoindre la liste des APIs déjà publiées - à savoir Contact Everyone,&#160; Multimedia Conference et Device capability enabler - et la longue liste des APIs en versionn alpha - SMS, email, location, click-to-call, authentification et toutes les personal APIs : <strong>Voicemail APIs</strong>.</p>  <p>Cette nouvelle venue permet de gérer une boîte vocale via une API REST (GET et POST). Pour le moment, Orange fournit un numéro de téléphone, cela ne marche donc pas avec la messagerie de votre téléphone portable (pour le moment...). L'idée est sympa, mais aucune utilisation intéressante à l'horizon pour moi...jusqu'à ce que je rédige mon message d'absence de ma boîte mail:</p>  <blockquote>   <p>Merci pour votre message. Je suis actuellement en déplacement professionnel jusqu'au 00/00/00 inclus avec un accès limité à mes emails et à mon téléphone.      <br />Si vous avez des questions urgentes, envoyez moi un SMS au ...</p> </blockquote>  <p>Le problème c'est le SMS : l'option monde dans mon forfait et un téléphone tribande, mais cela m'oblige à laisser mon téléphone portable allumé. C'est à ce moment que j'ai créé un petit mashup très simple. Il consulte toutes les 15 minutes ma boîte vocale, et si il y a des messages non lus, il me les renvoie par mail ! (j'ai pas tout dit, en fait, j'ai une adresse email spéciale voyage que je consulte en déplacement et qui fonctionne à partir d'un ensemble de filtre sur ma boîte mail principale). De cette façon, j'ai directement les messages importants pourvu que j'ai une connexion internet.</p>  <p>&#160;</p>  <p>Pour l'occasion, j'ai réalisé une classe en PHP qui me permet de consulter la messagerie simplement (exemple rapide):</p>  <pre><span style="color: #0000ff">&lt;?</span>php

$voicebox = new OrangeAPIVoicemailClient(&quot;<span style="color: #8b0000">clé API</span>&quot;);
$messages = $voicebox-&gt;getUnreadMessages();
<a style="color: #0000ff" href="http://www.php.net/foreach">foreach</a>($messages <a style="color: #0000ff" href="http://www.php.net/as">as</a> $m)
{
   $m-&gt;getFrom();
   $m-&gt;getFileurl();
}
<span style="color: #0000ff">?&gt;</span></pre>

<p>Je suis en train de terminer l'implémentation des autres APIs, en PHP et en C#. Une fois terminé, tout cela sera accessible sur <a href="http://www.codeplex.com">codeplex.com</a> pour le c# et sourceforge pour la version PHP. Une permière version pour C# est déjà disponible, pour l'API SMS : <a title="http://www.codeplex.com/orangesmsapi" href="http://www.codeplex.com/orangesmsapi">http://www.codeplex.com/orangesmsapi</a></p>
