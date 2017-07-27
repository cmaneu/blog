---
layout: post
title: Footer tout le temps en bas
slug: footer-tout-le-temps-en-bas
date: 2008-05-13 21:21:00Z
---

<blockquote>   <p>Ce post a été importé depuis mon ancien blog, il se peut donc qu’il y ait des erreurs d’affichage.</p> </blockquote>  <p>Il y a un petit moment, on s’était posé la question avec <a href="http://www.maneu.fr/www.netexplorer.fr">Bertrand</a> de la bonne façon de pourvoir afficher un footer en permanence en bas de page, quelque soit le contenu, uniquement avec du HTML et du CSS. En clair, si il y a peu de contenu, le footer est affiché en bas de la fenêtre du navigateur, et si il y a du contenu, le footer va se décaler en bas de page et il ne sera affiché que lors du défilement.</p>  <p>C’est partit pour notre page de base ! Bon, pas de fioritures, on est pas là pour valider le test W3C :p (A noter que ca marche très bien avec une page valide ^^). On a une div <em>contenu</em> qui contient tout le contenu de notre page, et un div <em>footer</em> qui contient….allez, je suis sûr que vous avez deviné :).</p>  <p>Si vous avez l’oeil, vous verrez une div <em>fin</em> qui s’est cachée dans ce code..c’est la clé de la solution :)</p>  <p>Voici le code</p>  <pre lang="html" line="1"><html>
    <div id="contenu">lipsum…<br />lipsum…<br />lipsum…<br />lipsum… <div id="fin"> </div> <div id="footer">Mon footer…Tout le temps en bas !</div></div></pre>

<p>Maintenant, passons au CSS ! Tout d’abord on réinitialise toutes les marges. ensuite, il faut étendre notre contenu à toute la page.Enfin, on va positionner notre footer non pas en bas de page, mais en bas de notre contenu,&#160; avec l’attribut <em>margin-top</em> avec une valeur négative. Donc notre contenu s’étend sur toute la surface d’affichage et notre footer, en bas de ce contenu. Cependant, il nous manque encore quelque chose. C’est la div <em>fin</em> et ses propriétés associées qui permettent de faire opérer la magie</p>

<pre lang="html">html&gt;
	<head>

		<style type="text/css">
			body
			{
				margin : 0;
				padding : 0;
				height : 100%;
			}

			#contenu
			{
				margin : 100px;
				margin-top : 0px;
				margin-bottom : 0px;
				min-height : 100%;
				background : #c0c0c0;
			}

			#footer
			{
				margin-top : -20px;
				height : 20px;
				width : 100%;
				background : #0000ff;
			}

			#fin /* ça c’est pour pas que le contenu monte sur le footer */
			{
				margin-bottom: 0;
				padding-bottom: 20px;
			}
		</style>
		<!– Vue que IE est perdu avec min height … on redéfinit le CSS ! –>
		<!–[if IE]>
			<style type="text/css">#contenu {height: 100%;}</style>
		<![endif]–>


		<div id="contenu">lipsum…<br />lipsum…<br />lipsum…<br />lipsum…<div id="fin"> </div> <div id="footer">Mon footer…Tout le temps en bas ! </div> </div></pre>

<p>Ce code n’est pas parfait, il faut notamment rajouter un header (logique..avec un footer ^^) : a vous de jouer !</p>
