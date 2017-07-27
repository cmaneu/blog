---
layout: post
title: Doctrine et UTF-8
slug: doctrine-et-utf-8
date: 2007-04-06 16:32:00Z
---

<blockquote>   <p>Ce post a été importé depuis mon ancien blog, il se peut donc qu’il y ait des erreurs d’affichage.</p> </blockquote>  <p>&#160;</p>  <p>Je travaille en ce moment sur un projet web multilingue, donc un seul mot d’ordre : <strong>UTF8 !</strong>. </p>  <p>Pour me simplifier la vie, j’utilise un outil d’<a href="http://fr.wikipedia.org/wiki/Object-relational_mapping">ORM</a> appelé Doctrine. Cependant, même avec les pages et la base en UTF8, tous mes caractères bizarres étaient automatiquement remplaçés par des ???. J’ai cherché pendant pas mal de temps la réponse et voici la solution ! </p>  <pre lang="php">$conn-&gt;execute(”SET CHARACTER SET utf8″);</pre>

<p>Il suffit de placer cette ligne et le tour est joué ! </p>
