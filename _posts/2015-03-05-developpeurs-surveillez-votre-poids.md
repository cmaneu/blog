---
layout: post
title: Développeurs, surveillez votre poids !
slug: developpeurs-surveillez-votre-poids
date: 2015-03-05 15:07:30Z
---

Dans le développement d'applications mobiles, il existe une métrique qui est rarement observée par les développeurs: **le poids du package d'application** (appx, xap, apk, ...). Pourtant, il n'est pas rare de croiser des applications autour/au delà de 10 Mo, sans justification. Quand j'ai rédigé ce billet, j'ai vu ceci parmi les applications recommandées par le store Windows Phone: 

- [Batterie][app-batterie], une application pour afficher le niveau de batterie: 7 Mo, 
- [App in the air][appintheair], une application pour avoir les informations sur les vols: 13 Mo,
- [Viber][app-viber], l'app de chat, qui est à **21 Mo** !

Toutes ces applications n'ont aucune raison apparente d'avoir un poids aussi élevé. Cela peut pourtant poser plusieurs problèmes: bande passante de l'utilisateur (qui peut la payer selon son forfait), des mises à jour fréquentes qui augmentent le problème, l'espace disque disponible limité - notamment sur les modèles d'entrée de gamme - , ... 

J'ai donc mené une petite expérimentation sur l'application Deezer pour Windows Phone, dont voici les résultats. Pour information, [Cyril Mottier (CapitaineTrain)][cyril-apk-diet], a publié un article sur le même sujet, pour Android que je vous recommande. Plus récemment, [James Montenegro (Xamarin)][james-android-appsize] a également publié sur le sujet.

>  J'ai commencé cet article il y a plus de 4 mois. Ceci  peut expliquer que ces données sont un peu "anciennes". Le contenu et les conclusions n'en restent pas moins toujours d'actualité.

### L'état des lieux

J'ai commencé par lister le poids des 15 dernières releases publiques, ainsi que le pourcentage de variation entre la version N et la version N-1. On constate que jusqu'à la version 2.2.8, le poids n'a cessé d'augmenter. Bien que l'on reste sur des variations très raisonnables (l'augmentation de 61% entre la version 2.0.3 et 2.1 correspond à une refonte de l'application, et à l'ajout de nombreuses fonctionnalités), et sur un poids total correct (moins de 5 Mo), nous avons voulu limiter cette tendance à la prise de poids.

![](/content/images/2015/Mar/appsize_1.PNG)

Pour réduire le poids de l'app, j'ai travaillé sur deux axes: 

- Les fichiers contenus dans le package, 
- Le "poids" du code (l'exécutable principal, ainsi que toutes les assemblies).

> Les packages d'applications ne sont ni plus ni moins que des fichiers ZIP. Cela est à prendre en compte dans la suite de l'article où nous allons plutôt aborder le poids *non  compressé* des fichiers.


### Réduire le contenu avec WinDirStat



[Windirstat][app-windirstat] fait partie depuis longtemps de ma liste d'outils incontournables. Je n'avais cependant jamais pensé à l'utiliser sur un package avant cette expérimentation. Au lancement, il est possible de choisir un dossier, et non un disque, pour analyser la répartition de la taille des fichiers par type ou répertoire.

![](/content/images/2015/Mar/appsize_3.PNG)

 Dans le cas d'applications Windows Phone / Store / universelles, il est important **d'analyser le contenu du package (.xap, .appx) et non pas du dossier de sortie (Debug, Release)**. Pour certaines applications Store / Universelles, il faut également prendre en compte les packages spécifiques (langues/résolutions) des bundles.

![](/content/images/2015/Mar/appsize_4.PNG)

Les résultats de cette première analyse sont assez prévisibles. Le code (contenu dans les assemblies) représente la majeure partie du poids de l'application. Plusieurs choses sont à noter: 

- **Les images**: Le package contient assez peu d'images. La majeure partie des images ont été préprocessées (via pnggauntlet ou équivalent). Cependant, certaines images étaient en réalité inutilles: **AlignmentGrid.png, Certaines images par défaut pour l'appbar, ...**,
- **Les fichiers XML**: Sur les 7 fichiers, seul le `WMAppManifest.xml` est utile. Il ne pèse que 4,5Kb. On peut donc gagner 6,7% du poids de l'app. Mais que sont ces fichiers ? Ce sont les XML de documentation des assemblies (Newtonsoft.Json.xml, System.Net.Http.xml, ...),
- **Le fichier TXT**: Il correspond à un fichier de licence inclus via un package Nuget. Dans notre cas, ce n'est pas trop coûteux (bien que tout de même inutille). Cependant, on peut en tirer une leçon: **Soyez vigilant sur les fichiers inclus automatiquement par les package NuGet**,
- **Le fichier TTF**: Ce fichier nous permet, à lui seul, de gagner un espace disque considérable. En effet, l'ensemble des icônes utilisées dans l'application ne sont pas stockées sous forme de fichier images (.png ou .svg), mais dans une police de caractères personnalisée. Ces seuls 12 Kb contiennent plus de 60 icônes !
- **Le fichier HTML**: On en a besoin (oui, c'est triste...),
- **Le fichier .xaml et le .winmd**: Rien de spécial ici. Le .xaml est le fichier AppManifest et le .winmd est ClrCompression.

Nous avions déjà une approche *raisonnée* des fichiers qui étaient inclus dans nos packages. Cependant, cette phase nous a permis à elle seule de récupérer 1 Mo (avant compression).

### Réduire le code avec NDepend

***NDepend**  m'a été gracieusement offert, notamment dans le cadre de cet article. Cependant, cet article ne couvre pas 1/100ème de l'utilité d'avoir NDepend dans son toolkit. Je vous conseille vivement de [visiter le site de NDepend][ndepend] pour découvrir comment il peut vous aider à améliorer la qualité de votre code. Je prévois de l'inclure dans notre toolchain en 2015, et j'espère bien rédiger d'autres articles sur le sujet ;)*


Les **assemblies .net représentent 70% du poids total de l'application**. Rien d'étonnant, cependant, en y regardant de plus près, on retrouve trois catégories: 

- Plusieurs assemblies concernant l'application elle-même, 
- Un ensemble considérable d'assemblies contenant les traductions de l'application (l'application est traduite en 31 langues), 
- Un ensemble d'assemblies externes (=du code que nous n'avons pas produit).

En ce qui concerne les traductions, il n'y a pas grand-chose à faire. Nos fichiers ne contiennent que des traductions utiles (cela serait le sujet de plusieurs articles à lui seul. Si le sujet vous intéresse, je vous conseille de regarder les slides de [ma présentation des AppDays 2014][appdays-localisation]. De plus, ce problème spécifique est résolu dans les applications universelles via les [Appx Bundles][msdn-appxbundle].

Idem pour nos assemblies: elles représentent 44% du poids total du package, pour lesquelles il y a peu de chose à faire. En revance, cela veut dire que **26% du poids total du package est utilisé par des assemblies référencées**. Cela représente une bonne piste pour réduire le poids de notre application.

Le nombre de dépendances externes de l'application est relativement réduit. Pour qu'une librairie soit ajoutée au projet (et acceptée en code review), il est important d'avoir de solides arguments: utilité, qualité du code, temps économisé vs un développement "maison", ... Du coup, seules quelques librairies ont réussi à passer ces critères: 

- HttpClient, 
- ClrCompression,
- Newtonsoft.Json, 
- Facebook C# SDK (version Outercurve foundation),
- Controls Toolkit et Coding4Fun.Toolkit.Controls,
- Une assemblie de notre solution d'analyse d'erreurs, 
- Une assemblie de notre solution de notifications Push.

Au bout de plusieurs mois de développement, d'évolutions diverses, on peut se demander si ces librairies sont toujours utiles, et donc si leur impact sur le poids de l'application est justifié ? C'est ce que nous allons vérifier avec nDepend.

L'une des librairies les plus connues parmi nos dépendances est sans contexte [Newtonsoft Json.Net][newtonsoft-json]. On peut alors se poser une question simple: Quelles sont les méthodes de mon code qui utilisent des méthodes de cette librairie ?

A l'aide de nDepend, et la fonctionnalité **CQLinq**, obtenir cette réponse ne prend que quelques lignes. CQLinq vous permet d'écrire une requêtes LINQ sur votre code. Par exemple, pour connaître l'ensemble des méthodes de l'application qui font appel à une méthode du namespace `Newtonsoft.Json`, il suffit d'exécuter la requête suivante: 

```
// <Name>Methodes using Newtonsoft.Json</Name>

let newtonsoftJsonMethods = Application.Namespaces.WithName("Newtonsoft.Json").ChildTypes().ChildMethods()
let ourMethods = Application.Namespaces.WithNameWildcardMatch("Deezer.*").ChildTypes().ChildMethods()

from methodsUsingJson in ourMethods.UsingAny(newtonsoftJsonMethods )
let newtonsoftJsonMethodsUsed = newtonsoftJsonMethods.Intersect(methodsUsingJson.MethodsCalled)
select new {methodsUsingJson, newtonsoftJsonMethodsUsed }
```

Le nombre de méthodes utilisant cette librairie est très réduit (seulement neuf méthodes). Dans le cas de cette librairie, cela est compréhensible: nous utilisons peu de points d'accès: on décode et on encode des document Json, sans manipulations particulières. Cependant, au vu de la complexité que cela représente, les 400Ko de la librairie sont, pour l'instant, justifiés. On peut tout de même observer que notre code n'est pas fortement dépendant de celle librairie.

![](/content/images/2015/Mar/appsize_5.PNG)

Regardons maintenant une autre librairie: **Phone Control Toolkit et Coding4Fun Toolkit**. Combien de contrôles de ces deux librairires sont réellement utilisés. 

```
// List types of Coding4Fun Toolkit used by Deezer App
let c4fTypes = Application.Namespaces.WithNameIn("Coding4Fun.Toolkit.Controls", 
   "Microsoft.Phone.Controls",
   "Clarity.Phone.Extensions").ChildTypes()

from c4fType in c4fTypes
where c4fType.IsUsedByNamespace("Deezer.*")
select new { c4fType }

```

![](/content/images/2015/Mar/appsize_6.png)

Dans notre cas de figure, le résultat n'est pas brillant: **5 contrôles sont utilisés, pour deux librairies qui ont une taille de 900 Ko** !  Sur une application avec un poids de 6 Mo, l'impact n'est pas négligeable. Dans ce cas de figure, nous avons réécrit ces contrôles, et économisé 1/6ème du poids de l'application.

nDepend peut également nous servir, en amont, afin de valider la qualité d'une librairie avant de l'inclure dans votre projet. Par exemple, si vous utilisez à la fois le Control Toolkit et le Coding4Fun toolkit, un certain nombre de types sont déclarés plusieurs fois. Cela peut mener à des problèmes qui peuvent gêner la qualité et ralentir la mainenance de l'application. Il n'a suffit que d'une minute dans NDepend pour soulever ce problème.

![](/content/images/2015/Mar/appsize_7.png)

### A retenir

Le poids de l'application est un critère important pour les applications mobiles. Voici une (très petite) liste de points de vigilances: 

- Réduire le poids des images embarquées dans l'application avec des outils tels que PngGauntlet,
- Supprimer les .xml de documentation, ainsi que les PDB,
- Faire attention aux fichiers annexes ajoutés automatiquement par les package NuGet,
- Analyser de manière régulière les librairies utilisées et leur pertinence (on va peut-être commencer par utiliser une librairie disponible, puis éventuellement réécrire les parties qui nous intéressent).



[cyril-apk-diet]: http://cyrilmottier.com/2014/08/26/putting-your-apks-on-diet/
[appintheair]: http://www.windowsphone.com/fr-fr/store/app/app-in-the-air/2642dc62-42fa-47e5-b87e-898c29b7255e
[app-batterie]: http://www.windowsphone.com/fr-fr/store/app/batterie/253f1c29-7a58-4860-99fe-7f048680b7ea
[app-viber]: http://www.windowsphone.com/fr-fr/store/app/viber/3d051f9b-9e03-456f-b647-bea34fe7031c
[app-windirstat]: http://www.windirstat.info
[ndepend]: http://www.ndepend.com/
[appdays-localisation]: https://speakerdeck.com/cmaneu/appdays-2014-30-langues-9-plateformes-comment-survivre-a-la-localisation-dune-application-mondiale
[msdn-appxbundle]: http://msdn.microsoft.com/en-us/library/windows/apps/bg182885.aspx#TWO
[james-android-appsize]: http://motzcod.es/post/112072508362/how-to-keep-your-android-app-size-down
[newtonsoft-json]: http://www.newtonsoft.com/json
