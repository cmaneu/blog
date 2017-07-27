---
layout: post
title: symfony : Exécuter du code avant/après une action
slug: symfony-excuter-du-code-avantaprs-une-action
date: 2008-01-13 20:54:00Z
---

<blockquote> <p>Ce post a été importé depuis mon ancien blog, il se peut donc qu’il y ait des erreurs d’affichage.</p></blockquote> <p>Vous développez une application avec le framework PHP symfony ? Vous voulez exécuter du code avant ou après toute action sur un module ? C’est possible ! </p> <h4>Comment faire</h4> <p>Voici votre module à l’heure actuelle : </p><pre lang="php" line="1"><php class moduleActions extends sfActions
{

  public function executeIndex()
  {
    …
  }
  public function executeList()
  {
    …
  }
}
?&gt;</pre>
<p>Nous avons donc deux actions : Index et List. Vous savez déjà que vous pouvez créer d’autres fonctions pour pouvoir vous en servir. Pour exécuter du code avant l’exécution de Index ou de List, il suffit de créer une méthode <em>preExecute</em>. Pour exécuter du code après l’exécution du code de votre actions, c’est la même opération, mais avec…<em>postExecute</em> ! Voici ce que cela donne :</p><pre lang="php" line="1"><?php
class moduleActions extends sfActions
{
  public function preExecute()
  {
	// Le code ici s’exécutera avant le code présent dans “execute…”
  }

  public function executeIndex()
  {
    …
  }
  public function executeList()
  {
    …
  }

  public function postExecute()
  {
	// Le code ici s’exécutera avant le code présent dans “execute…”
  }

}
?&gt;</pre>
<h4>Oui, mais ça sert a quoi ?</h4>
<p>A plein de choses :). Voici cependant un exemple concret qui peut vous servir dans vos développements. symfony permet de créer facilement et rapidement des API utilisant le <a href="http://pompage.net/pompe/comment-j-ai-explique-rest-a-ma-femme/">“protocole” REST</a>. Cependant, en mode développement, cela ne fonctionne pas, tout simplement parce que le framework retourne le code XML <strong><em>suivi du code JavaScript de la barre de débogage, </em></strong>le fichier XML est donc inutilisable. Pour palier ce problème, il suffit, pour le module contenant l’API, de ne jamais l’exécuter en mode de débogage, y compris quand on est dans ce mode ! <pre lang="php" line="1">public function preExecute()
{
  sfConfig::set(’sf_web_debug‘, false);
}</pre>
<p></p><pre></pre>
