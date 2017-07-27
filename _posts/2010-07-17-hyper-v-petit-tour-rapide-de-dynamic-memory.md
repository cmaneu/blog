---
layout: post
title: Hyper-V : petit tour rapide de Dynamic memory
slug: hyper-v-petit-tour-rapide-de-dynamic-memory
date: 2010-07-17 10:34:21Z
---

<p>Avec la Bêta du Service Pack 1 de Windows Server 2008 R2, une nouvelle fonctionnalité fait son apparition : Dynamic Memory. Qu’est ce donc que cela ? C’est une nouvelle fonctionnalité qui permet d’ajuster dynamiquement la mémoire vive attribuée à une machine virtuelle, et ainsi optimiser encore plus l’utilisation des ressources matérielles.</p>  <p>Cet article est basé sur la version beta, il se peut donc que cet article ne soit pas complètement précis avec la version finale.   <br />Qu’est ce que Dynamic Memory ?    <br />Jusqu’à présent, dans des machines virtuelles Hyper-V sous Windows Server 2008 et 2008 R2, l’administrateur pouvait assigner une quantité de mémoire vive fixe à une machine virtuelle. Lorsque la machine virtuelle est démarrée, cette quantité de mémoire est assignée dans la mémoire de l’hôte et n’est plus modifiée par l’hôte jusqu’à l’extinction de la machine. De cette manière, un hôte équipé de 8Go de mémoire vive pouvait avoir 10 machines virtuelles de 2 Go chacune, mais ne pouvait en démarrer que trois (le système hôte n’a pas besoin de 2 Go mais, dans notre exemple, la limite est à 3 machines).    <br />Pour comprendre en quoi ceci est une limitation, il faut revenir au scénario le plus courant qui mêne à la virtualisation : la consolidation de serveurs. L’un des objectifs de la consolidation est de mieux partager les ressources matérielles. En virtualisant un certain nombre de serveurs, on économise le nombre et la puissance de processeurs nécessaire pour faire tourner les mêmes services, mais également le disque, que l’on utilise des disques internes, DAS ou SAN. Mais qu’en est-il de la mémoire vive ? Jusqu’à présent,c’était le dernier point qui n’était pas plus efficace que l’on soit dans un scénario de virtualisation ou pas : 2 Go de RAM sur un serveur physique nécessitera toujours 2 Go en virtualisé. Tout cela était vrai….jusqu’à présent.    <br />La fonctionnalité Dynamic memory permet de donner deux valeurs&#160; à la mémoire :</p>  <ul>   <li>La mémoire de démarrage : c’est celle qui est attribuée au démarrage de la machine, celle qui est vue par le BIOS, et la quantité minimale qui sera attribuée à la machine, quelque soit les circonstances, </li>    <li>La mémoire maximum : c’est la taille maximum que pourra occuper la machine virtuelle si les conditions sont réunies (c’est à dire si elle en a besoin et que la mémoire est disponible). </li> </ul>  <p>Comment ca fonctionne ?</p>  <p>&quot;Changer la mémoire vive a chaud, c'est pas possible ! Quel système d'exploitation va prendre en charge ca ?&quot;. C'est une très bonne question ! Baguette magique ? Potion du druide ? Cela ne fonctionne que sur un os particulier entièrement managé ? Un peu des trois….</p>  <p>La difficulté technique vient du fait que nos systèmes d'exploitation n'ont pas été conçus pour un changement de la mémoire vive à chaud. Offrir cette possibilité nécessite donc en premier lieu de pouvoir gérer ce cas. Côté hôte, c'est l'architecture d'Hyper-V qui le permet (c'est la baguette magique). Ensuite, les choses se compliquent côté OS virtualisé, et ce pour plusieurs raisons : </p>  <ul>   <li>L'OS virtualisé doit pouvoir accepter qu'une partie de sa mémoire lui soit enlevée, c’est-à-dire que certaines pages mémoires ne soient plus accessibles,</li>    <li>Cette fonctionnalité de mémoire dynamique est automatique, c’est-à-dire que vous n'avez pas à intervenir pour augmenter la mémoire si cela est nécessaire (dans les limites que vous avez définies). Le système virtualisé doit donc pouvoir communiquer avec Hyper-V pour lui indiquer si il est en manque de mémoire ou non.</li> </ul>  <p>La potion magique pour réussir cela, en plus de l'architecture d'Hyper-V (VSP/VCP/VMBus) ce sont les VM Additions Tools. La version incluse avec cette bêta apporte des fonctionnalités supplémentaires au kernel, permettant ces changements. </p>  <p>Hmm, &quot;fonctionnalités supplémentaires au kernel&quot; et ca fonctionne avec mon Suse Enterprise ?&quot; Et bien non ! (du moins, pas dans cette version). Cette fonctionnalité n'est disponible que sur certains OS. Voici la liste pour cette beta (d'autres OS sont prévus pour la version finale, les versions x86 et x64 de ces OS sont supportées) : </p>  <ul>   <li>Windows Server 2008 R2 Enterprise SP1</li>    <li>Windows Server 2008 R2 Datacenter SP1</li>    <li>Windows Server 2008 Enterprise SP2</li>    <li>Windows Server 2008 Datacenter SP2 </li>    <li>Windows Server 2003 R2 Enterprise SP2 ou supérieur</li>    <li>Windows Server 2003 R2 Datacenter SP2 ou supérieur</li>    <li>Windows Server 2003 Enterprise SP2 ou supérieur</li>    <li>Windows Server 2003 Datacenter SP2 ou supérieur</li>    <li>Windows 7 Enteprise</li>    <li>Windows 7 Ultimate</li>    <li>Windows Vista Enterprise SP2</li>    <li>Windows Vista Ultimate SP2</li> </ul>  <p><a href="http://blog.maneu.net/wp-content/uploads/2010/07/clip_image001.png"><img style="border-bottom: 0px; border-left: 0px; display: block; float: none; margin-left: auto; border-top: 0px; margin-right: auto; border-right: 0px" title="clip_image001" border="0" alt="clip_image001" src="http://blog.maneu.net/wp-content/uploads/2010/07/clip_image001_thumb.png" width="349" height="327" /></a></p>  <p>Une fois la fonctionnalité activée, les composants d'intégration mis à jour, l'OS virtualisé et le système hôte vont communiquer, évaluer les besoins en mémoire et, en cas de besoin, allouer plus de mémoire à une machine virtuelle. Pour gérer les cas de pénurie, un système de priorité est prévu, vous pouvez ainsi prioriser les machines virtuelles qui se verront attribuer de la mémoire si plusieurs en ont besoin et que la quantité disponible n'est pas suffisante pour répondre à tous les besoins.</p>  <p>Vous pouvez a tout moment visualiser la mémoire qui a été allouée à une machine virtuelle directement dans Hyper-V Manager (comme le montre la capture ci-dessous), mais également à l'aide du compteur de performances (avec 16 nouvelles sondes réparties dans deux groupes).</p>  <p><a href="http://blog.maneu.net/wp-content/uploads/2010/07/clip_image002.png"><img style="border-bottom: 0px; border-left: 0px; display: block; float: none; margin-left: auto; border-top: 0px; margin-right: auto; border-right: 0px" title="clip_image002" border="0" alt="clip_image002" src="http://blog.maneu.net/wp-content/uploads/2010/07/clip_image002_thumb.png" width="369" height="152" /></a></p>  <p>Il y a bien d'autres notions à comprendre pour avoir une vision détaillée de Dynamic Memory (balloning, memory pressure, …) qui feront peut-être l'objet d'un prochain article ;)</p>  <p>Un dernier conseil avant que je me lance ?   <br />Même plusieurs ! Voici une liste de points auxquels il faut porter attention si vous voulez tester cette nouvelle fonctionnalité : </p>  <ul>   <li>L'installation des nouveaux composants d'intégration rend incompatible vos machines virtuelles avec des hôtes qui, eux aussi, ne seraient pas passés en beta du SP1 !</li>    <li>La mise en place de Dynammic memory sur une machine virtuelle nécessite son arrêt,</li>    <li>Je n'ai pas encore testé cette solution dans un environnement de haute disponibilité. Bien que el failover clustering soit supporté (avec des considérations de mémoire vives totales/disponbiles par cluster), je n'ai rien trouvé concernant dynamic memory et Live Migration. A tester, </li>    <li>Enfin n'oubliez pas, c'est une beta :)</li> </ul>  <p>Une autre nouveauté du SP1 est Remote FX, un ensemble de technologies permettant de supporter des une expérience multimédia de haut niveau via le protocole RDP (y compris des applications 3D). Peut-être le sujet d’un prochain article ;)</p>