---
layout: post
title: [Astuce] Utiliser les cmdlets Exchange depuis une session PowerShell
slug: astuce-utiliser-les-cmdlets-exchange-depuis-une-session-powershell
date: 2008-12-05 07:49:00Z
---

<p>Exchange Server 2007 a introduit le <em>Exchange Management Shell</em> vous permettant d’administrer vos serveurs Exchange via la ligne de commande. Vous avez pour cela un raccourci qui vous lance une console.</p>  <p><a href="http://www.chrisetnico.com/wp-content/uploads/2008/12/image.png"><img style="border-right-width: 0px; display: block; float: none; border-top-width: 0px; border-bottom-width: 0px; margin-left: auto; border-left-width: 0px; margin-right: auto" title="image" border="0" alt="image" src="http://www.chrisetnico.com/wp-content/uploads/2008/12/image-thumb.png" width="423" height="178" /></a></p>  <p>Hors, si vous essayez d’utiliser les commandlets (les fonctions powershell) directement dans Windows PowerShell, cela ne marche pas !</p>  <p><a href="http://www.chrisetnico.com/wp-content/uploads/2008/12/image1.png"><img style="border-right-width: 0px; display: inline; border-top-width: 0px; border-bottom-width: 0px; border-left-width: 0px" title="image" border="0" alt="image" src="http://www.chrisetnico.com/wp-content/uploads/2008/12/image-thumb1.png" width="436" height="191" /></a></p>  <p>En fait, il suffit de rajouter dans notre environnement l’ensemble des fonctionnalités propres à Exchange, avec la commande ci-dessous :</p>  <pre class="csharpcode">Add-PSSnapin Microsoft.Exchange.Management.PowerShell.Admin</pre>

<p>
  <br /></p>
<style type="text/css">


.csharpcode, .csharpcode pre
{
	font-size: small;
	color: black;
	font-family: consolas, "Courier New", courier, monospace;
	background-color: #ffffff;
	/*white-space: pre;*/
}
.csharpcode pre { margin: 0em; }
.csharpcode .rem { color: #008000; }
.csharpcode .kwrd { color: #0000ff; }
.csharpcode .str { color: #006080; }
.csharpcode .op { color: #0000c0; }
.csharpcode .preproc { color: #cc6633; }
.csharpcode .asp { background-color: #ffff00; }
.csharpcode .html { color: #800000; }
.csharpcode .attr { color: #ff0000; }
.csharpcode .alt
{
	background-color: #f4f4f4;
	width: 100%;
	margin: 0em;
}
.csharpcode .lnum { color: #606060; }</style>

<p>Et voilà ! Notre fenêtre PowerShell après cette commande ;)</p>

<p><a href="http://www.chrisetnico.com/wp-content/uploads/2008/12/image2.png"><img style="border-right-width: 0px; display: inline; border-top-width: 0px; border-bottom-width: 0px; border-left-width: 0px" title="image" border="0" alt="image" src="http://www.chrisetnico.com/wp-content/uploads/2008/12/image-thumb2.png" width="435" height="198" /></a></p>

<p>Cela peut éventuellement servir avec ce genre d’outils…;)</p>

<p><a href="http://www.chrisetnico.com/wp-content/uploads/2008/12/image3.png"><img style="border-right-width: 0px; display: inline; border-top-width: 0px; border-bottom-width: 0px; border-left-width: 0px" title="image" border="0" alt="image" src="http://www.chrisetnico.com/wp-content/uploads/2008/12/image-thumb3.png" width="440" height="366" /></a></p>
