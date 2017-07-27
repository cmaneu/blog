---
layout: post
title: Le service bus d’Azure désormais disponible pour tous…même pour votre laptop !
slug: le-service-bus-dazure-desormais-disponible-pour-tousmeme-pour-votre-laptop
date: 2012-07-20 01:10:45Z
---

Ces derniers temps, le cloud prend une importance considérable, et on ne compte plus les produits qui ont leurs versions dans les nuages – comme Office 2013. On en ouvlierai rapidement l'un des points les plus importants de la stratégie de Microsoft : offrir ses produits à la fois pour le cloud et le <em>on premises</em> (ie. Sur vos serveurs). On commence ainsi à voir des produits qui ont été créés initialement pour le cloud, arriver sur nos serveurs.

Windows azure Service Bus est une infrastructure de messagerie permettant de connecter des applications en elles à travers un grand nombre de protocoles et une communication basé sur des messages. Microsoft vient de sortir <strong>Service Bus for Windows Server</strong>, permettant de bénéficier d'une partie de ces technologies sur notre propre infrastructure.

<!--more-->
<h2>Tour d'horizon de Service Bus</h2>
Au commencement, il y avait Windows Azure Service Bus. Cette fonctionnalité de Windows Azure permet d'intégrer les applications entre elles via un échange de messages standardisés. Cela était jusqu'à présent réalisable au travers de plateformes telles que Microsoft BizTalk Server, principalement réservées à de grandes entreprises.

La mouture Azurée reprenait les principales fonctionnalités d'un bus de service, au travers d'une plateforme élastique, stable et accessible depuis n'importe quelle machine ayant accès à internet. Les principales fonctionnalités disponibles étaient :
<ul>
	<li><span style="font-family: PT Sans;">La messagerie – au travers de files et de systèmes de messageries « publish-subscribe » (avec des topics, et des abonnements),
</span></li>
	<li><span style="font-family: PT Sans;">La sécurité, pour protéger l'accès à ces services,
</span></li>
	<li><span style="font-family: PT Sans;">Le management de ces services.
</span></li>
</ul>
Ces services sont désormais disponibles pour votre cloud privé, ou plus simplement pour vos propres serveurs en interne. L'architecture globale, telle que <a href="http://msdn.microsoft.com/en-us/library/jj193012">décrite dans la documentation</a>, est la suivante :
<p style="text-align: center;"><img src="http://blog.maneu.net/wp-content/uploads/2012/07/071912_0910_Leservicebu1.jpg" alt="" /></p>

<h2>Installation et configuration</h2>
Un certain nombre de pré-requis sont nécessaires avant de pouvoir installer cette plateforme :
<ul>
	<li><span style="font-family: PT Sans;">.Net Framework 4.5 (Platform Update 3) ou .net 4,
</span></li>
	<li><span style="font-family: PT Sans;">Windows Fabric (installé automatiquement par WebPI),
</span></li>
	<li><span style="font-family: PT Sans;">Windows Server 2008 R2 SP1 x64 ou Windows Server 2012 x64 – et Windows 7 SP1/Windows 8 x64 pour du développement,
</span></li>
	<li><span style="font-family: PT Sans;">SQL Server 2008 R2 SP1 ou SQL Server 2012 (express ou autre) avec le service browser actif et les connexions TCP/IP et Named Pipes configurées,
</span></li>
	<li><span style="font-family: PT Sans;">Windows Powershell 3.0
</span></li>
	<li><span style="font-family: PT Sans;">Web Platform Installer 4.0,
</span></li>
	<li><span style="font-family: PT Sans;">Les ports 4446 et 5112 disponibles
</span></li>
	<li><span style="font-family: PT Sans;">Une interface IPv4 ou IPv4+IPv6 (l'IPv6 seul n'est pas supporté).
</span></li>
</ul>
Si vous avez tout cela, l'installation est très simple : il suffit de vous rendre à l'adresse suivante et de lancer l'exécutable. L'installation est réalisée au travers de Web Platform Installer. <a href="http://www.microsoft.com/en-us/download/details.aspx?id=30376">http://www.microsoft.com/en-us/download/details.aspx?id=30376</a>

Une fois installé, il n'y a qu'un élément qui fait son apparition : un raccourci pour une invite PowerShell spécifique au Service Bus.
<p style="text-align: justify; background: #f2f2f2; margin-left: 42pt;"><span style="font-family: PT Sans;">Vous pouvez avoir le même résultat dans n'importe quelle invite Powershell si vous exécutez la commande </span><span style="font-family: Consolas;">Import-Module "C:Program FilesWindows Azure Service Bus1.0Microsoft.ServiceBus.Commands.dll"
</span></p>
&nbsp;
<p style="text-align: center;"><img src="http://blog.maneu.net/wp-content/uploads/2012/07/071912_0910_Leservicebu2.png" alt="" /></p>
La suite de la configuration s'effectue depuis Powershell. La documentation n'indique pas tout à fait cette syntaxe, mais celle-ci fonctionne (pas celle de la documentation sur ma machine avec ma configuration). Vous avez tout de même quelques éléments à modifier :
<ul>
	<li><span style="font-family: PT Sans;">La chaîne de connexion à votre base de données,
</span></li>
	<li><span style="font-family: PT Sans;">Le nom et le mot de passe du compte de service pour exécuter les services du Service Bus.
</span></li>
</ul>
<pre># Création d'une ferme
$mycert=ConvertTo-SecureString -string MonPasse -force -AsPlainText
New-SBFarm -FarmMgmtDBConnectionString 'Data Source=.;Initial Catalog=SbManagementDB;Integrated Security=True' -PortRangeStart 9000 -TcpPort 9354 -RunAsName '.servicebususer' -AdminGroup 'BUILTINAdministrators' -GatewayDBConnectionString 'Data Source=.;Initial Catalog=SbGatewayDatabase;Integrated Security=True' -CertAutoGenerationKey $mycert -ContainerDBConnectionString 'Data Source=.;Initial Catalog=ServiceBusDefaultContainer;Integrated Security=True'; 

# Ajout de l'hote courant à la ferme
$SBRunAsPassword = ConvertTo-SecureString -AsPlainText -Force -String sbuser;
Add-SBHost -FarmMgmtDBConnectionString 'Data Source=.;Initial Catalog=SbManagementDB;Integrated Security=True' -RunAsPassword $SBRunAsPassword -CertAutoGenerationKey $mycert; 

# Vérification de l'état de la ferme
Get-SBFarmStatus

# Création d'un nouveau namespace
New-SBNamespace -Name 'NsTest' -ManageUser 'chrstopher'; 

# Validation
Get-SbMessageContainer
Get-SbNamespace

# Récupère la Configuration cliente
Get-SBClientConfiguration -Namespaces 'NsCmaneu';</pre>
<p style="text-align: justify; background: #f2f2f2; margin-left: 42pt;"><span style="font-family: PT Sans;"><strong>Attention</strong> : Vous passez un nom d'utilisateur à la méthode New-SBNamespace. Ce nom d'utilisateur sera celui qui managera le namespace. Il existe trois droits : <strong>Manage – </strong>je gère le namespace, <strong>Send</strong> – je peux envoyer un message, <strong>Listen</strong>, je suis abonné à des publications de messages. Pour les exemples suivants, on suppose que l'utilisateur qui exécutera le programme est le même.
Dans le cas contraire, il faudra utiliser les APIs pour ajouter les bons droits à cet utilisateur. Cette configuration n'est pas expliquée <a href="http://msdn.microsoft.com/en-us/library/jj193003(v=azure.10)">dans la documentation MSDN</a>.
</span></p>

<h2>Utilisation depuis une application .net</h2>
Le code du client n'est pas tout à fait le même que pour l'appel au Service Bus de Windows Azure. Le principe reste le même et l'utilisation est simplifiée avec le package <a href="http://nuget.org/packages/ServiceBus.v1_0">nuget Service Bus 1.0 Beta</a>. Je n'ai pas réussi à trouver ce package par son nom, je l'ai donc ajouté dans mon projet à l'aide du Package Manager Console.

Le code suivant est une copie de l'article de <a href="http://seroter.wordpress.com/2012/07/17/installing-and-testing-the-new-service-bus-for-windows/">Richard Seroter</a> (tout comme une partie du code PowerShell). Il existe également des samples de code plus complets sur <a href="http://code.msdn.microsoft.com">http://code.msdn.microsoft.com</a>.

&nbsp;
<h3>Création d'une file de messages</h3>
<pre>
//define variables
string servername = "WA1BTDISEROSB01";
int httpPort = 4446;
int tcpPort = 9354;
string sbNamespace = "NsSeroterDemo"; 

//create SB uris
Uri rootAddressManagement = ServiceBusEnvironment.CreatePathBasedServiceUri("sb", sbNamespace, string.Format("{0}:{1}", servername, httpPort));
Uri rootAddressRuntime = ServiceBusEnvironment.CreatePathBasedServiceUri("sb", sbNamespace, string.Format("{0}:{1}", servername, tcpPort)); 

//create NS manager
NamespaceManagerSettings nmSettings = new NamespaceManagerSettings();
nmSettings.TokenProvider = TokenProvider.CreateWindowsTokenProvider(new List() { rootAddressManagement });
NamespaceManager namespaceManager = new NamespaceManager(rootAddressManagement, nmSettings);
 

//create factory
MessagingFactorySettings mfSettings = new MessagingFactorySettings();
mfSettings.TokenProvider = TokenProvider.CreateWindowsTokenProvider(new List() { rootAddressManagement });
MessagingFactory factory = MessagingFactory.Create(rootAddressRuntime, mfSettings);
 
//check to see if topic already exists
if (!namespaceManager.QueueExists("OrderQueue"))
{
	MessageBox.Show("queue is NOT there ... creating queue");
	//create the queue
	namespaceManager.CreateQueue("OrderQueue");
}
else
{
	MessageBox.Show("queue already there!");
}
</pre>


<h3>Envoi d'un message</h3>
<pre>//write message to queue
MessageSender msgSender = factory.CreateMessageSender("OrderQueue");

BrokeredMessage msg = new BrokeredMessage("This is a new order");
msgSender.Send(msg); 

MessageBox.Show("Message sent!");</pre>
<h3>Réception d'un message</h3>
<pre>//receive message from queue
MessageReceiver msgReceiver = factory.CreateMessageReceiver("OrderQueue");
BrokeredMessage rcvMsg = new BrokeredMessage();

string order = string.Empty;
rcvMsg = msgReceiver.Receive();

if(rcvMsg != null)
{
	order = rcvMsg.GetBody();
	//call complete to remove from queue
	rcvMsg.Complete();
}</pre>
<h2>Utilisation depuis des applications non .net</h2>
Il est tout à fait possible d'utiliser le Service Bus pour Windows Server depuis une application non .net. Pour cela, il vous faudra :
<ul>
	<li><span style="font-family: PT Sans;">Récupérer un jeton d'authentification OAuth. L'exemple de code <a href="http://msdn.microsoft.com/en-us/library/jj193003(v=azure.10)">est donné en c#</a> (rubrique Using http), mais facilement adaptable dans d'autres langages,
</span></li>
	<li><span style="font-family: PT Sans;">Utiliser les <a href="https://www.windowsazure.com/en-us/develop/php/how-to-guides/service-bus-queues/">APIs déjà disponibles</a> pour Windows Azure. Certaines adaptations seront tout de même nécessares.
</span></li>
</ul>
<h2>Pour aller plus loin</h2>
<h3>Haute disponibilité</h3>
Nous avons vu qu'il était possible de déployer l'ensemble du service bus sur son propre serveur, ou sa propre machine de développement. Il est également possible de déployer une infrastructure permettant d'avoir une haute disponibilité de ce service. Pour cela, SBWS supporte une topologie avec plusieurs serveurs Service Bus roles et un serveur pour les bases de données. Cela assure la redondance du service. La haute disponibilité du serveur SQL est-elle assurée par les fonctionnalités natives de SQL Server (AlwaysOn, Failover Cluster, etc…). La documentation <a href="http://msdn.microsoft.com/en-us/library/jj193006(v=azure.10)">en parle brièvement</a>.

A noter toutefois qu'il semble que<strong> seulement deux topologies soient supportées : Une ferme avec un serveur, ou une ferme avec 3 serveurs.
</strong>
<h3>Configuration via Powershell</h3>
L'ensemble de la gestion de votre ferme Service Bus peut être opérée via Powershell. <a href="http://msdn.microsoft.com/en-us/library/jj193018(v=azure.10)">L'aide détaille les commandes</a>, et un <a href="http://msdn.microsoft.com/en-us/library/jj193005(v=azure.10)">get-help</a> vous <a href="http://msdn.microsoft.com/en-us/library/jj200798">donnera le détail </a>;).
<h3>Monitoring</h3>
Vous avez un certain nombre d'outils à votre disposition pour monitorer votre infrastructure Service Bus :
<ul>
	<li><span style="font-family: PT Sans;">Des <a href="http://msdn.microsoft.com/en-us/library/jj192996(v=azure.10)">compteurs de performance dédiés</a>, à ajouter à ceux de Windows et de SQL,
</span></li>
	<li><a href="http://msdn.microsoft.com/en-us/library/jj193001(v=azure.10)"><span style="font-family: PT Sans;">L'event viewer</span></a><span style="font-family: PT Sans;">, avec un channel Microsoft-ServiceBus.
</span></li>
</ul>
<h2>Les téléchargements</h2>
<ul>
	<li><span style="font-family: PT Sans;">La documentation sur MSDN : <a href="http://msdn.microsoft.com/library/jj193022%28Azure.10%29.aspx">http://msdn.microsoft.com/library/jj193022%28Azure.10%29.aspx</a>
</span></li>
	<li><span style="font-family: PT Sans;">Le téléchargement du runtime : <a href="http://www.microsoft.com/en-us/download/details.aspx?id=30376">http://www.microsoft.com/en-us/download/details.aspx?id=30376</a>
</span></li>
	<li><span style="font-family: PT Sans;">Le Service Bus Explorer : <a href="http://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a">http://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a</a></span></li>
</ul>
