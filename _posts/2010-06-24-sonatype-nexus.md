---
layout: post
title: Sonatype Nexus
date: 2010-06-24 14:44:58
author: adrien.lecharpentier@gmail.com
tags: nexus sonatype maven-repository-manager installation
excerpt: Nexus de Sonatype. Partage de connaissance sur un maven 
  repositories manager, Nexus. Explication, installation, configuration
  et fonctionnement.
 ---

Depuis quelques temps, j'utilise *Nexus* de Sonatype pour certain
 développement perso ou pro. Comme je trouve ce produit très simple, 
 convivial et solide dans le cadre de l'intégration continue et plus 
 largement, dans son utilisation avec Maven, j'ai décidé d'écrire un 
 article pour partager l'expérience que j'en ai.

Pour cela, je tenterai de vous expliquer simplement (c'est pas toujours 
facile à faire)  l'utilité de *Nexus*. J'espère que ce fait sera 
prouvé par la suite de mon exposé. Je parlerai ainsi de l'installation 
puis de la configuration de ce serveur pour une utilisation classique, 
en vous expliquant les personnalisations possibles du serveur. Pour 
finir, je montrerai le fonctionnement du serveur à l'usage.

## Avant propos
*Nexus* est un <strong>*M*</strong>*aven 
<strong>R</strong>epository <strong>M</strong>anager*. Il permet de 
gérer les artefacts qui sont disposés grâce à Maven (ou maven-ant-tasks 
pour Ant).

>Rappel: un artefact est un composant fonctionnel dans un format de 
>distribution (jar, war, zip, ear...).

Au cours de cet article, je ne vais pas essayer de vous faire de la 
propagande pour le produit, mais établir un portrait le plus vrai 
possible de *Nexus*. Ce produit est disponible en version 
gratuite comme payante, et pour rassurer certains d'entre vous, la 
version payante utilise le noyau dur du fonctionnement du serveur mais 
ajoute des fonctionnalités qui peuvent vous intéresser.

Un certain nombre de question ne sont pas abordées soit parce que je 
n'ai pas de réponse soit parce que je n'ai pas les questions :). Alors 
posez-les et nous verrons bien ce que nous pourrons faire..

## Installation
Tout d'abord, commençons par parler de l'installation.

Comme la plupart des produits "serveur" de nos jours, il est disponible 
en deux versions: une bundle et une application web. La version que 
vous choisissez ne modifie pas le comportement de l'application. Je ne 
pourrai pas dire qu'elle version est la mieux des deux.

>Si vous désirez utiliser *Nexus* gratuitement pour faire des 
>essais mais que c'est la version payante qui vous intéresse, sachez 
>(1) qu'une licence temporaire de 30j vous permet d'utiliser la version 
>payante et ainsi essayer les atouts de la version payante avant de 
>l'acheter, (2) que la version payante n'est disponible dans version 
>bundle, alors essayez cette version.

## Application web
Pour l'installation de la version appli-web, il est nécessaire de 
disposer d'un serveur d'application du style *Tomcat*. Une 
fois votre serveur d'application installé, le déploiement se fait 
très facilement.

Toutefois, je ne saurai trop vous conseiller de changer l'emplacement 
par défaut qui est utilisé pour créer les dossiers des repositories de 
notre Nexus. Pour cela, vous devez modifier la constante 
*nexus-work* qui est utilisée comme variable pour trouver les 
répositories. Vous avez deux possibilités:

 - dans un fichier: `path/to/applet/server/webapp/nexus/WEB-INF/plexus.properties`
 - une variable d'environnement: `PLEXUSNEXUSWORK`

Une fois mise à jour, redémarrez votre application et normalement tout 
est ok.

Personnellement j'ai installé la version application web car je n'ai pas
 l'intention de passer en version payante. Cette version a, à mon sens, 
 un intérêt certain pour les entreprises. De plus, j'avais déjà un 
 *Tomcat* qui tournait avec Hudson et j'avais configuré 
 *Apache* pour rediriger un *virtualhost* vers l'instance 
 de *Tomcat*.
 
## Bundle
L'installation de la version *bundle* ne nécessite pas de serveur
d'application. Cela permet justement de pouvoir fonctionner sans autres
outils.

Lorsque vous dés-archivez cette version de Nexus, vous obtenez deux 
dossiers:

 - nexus-*${version}*: contient la configuration du serveur,
 - sonatype-nexus: contient la configuration des repositories et leurs 
 stockages.

L'exécutable d'installation du serveur se trouve dans le dossier 
*"nexus-*<version>*/bin/jsw/*<platform-type>*/"*. La configuration du 
serveur se fait par l'intermédiaire des fichiers qui se trouvent dans le
dossier *"nexus-*<version>*/conf"*. Le fichier *plexus.properties* 
permet de mettre à jour le port d'écoute, l'adresse IP d'écoute ainsi 
que les chemins d'accès aux différents dossiers de nexus (dont 
l'emplacement des repositories: *nexus-work*).

Dans cette version, le moteur de rendu utilisé est *jetty*. Comme 
certains peuvent avoir envie de mettre leurs nexus sur SSL/TLS, il y a 
un exemple d'une configuration possible (avec redirection du port non 
sécurisé vers le port sécurisé...) dans le dossier 
*${nexus-home}/nexus-*<version>*/conf/examples/*. Ces configurations se 
font dans le fichier *jetty.conf*.

## Configuration
Quelque soit la version que vous ayez choisie, la configuration de nexus
(pas du service) est la même et est assez simple.

La configuration n'est possible qu'avec l'utilisateur *admin*. Par 
défaut son mot de passe est *admin123*.

## La configuration général
Si votre service est placé derrière un proxy, il est nécessaire de 
renseigner cette information sur la configuration de *Nexus*. Pas de 
panique, la seule raison pour laquelle *Nexus* irai sur internet, c'est 
que vous lui demandez un artefact qu'il ne possède pas mais qui se 
trouve sur un autre repository manager (le central par exemple..). Il 
ne sera jamais amené à faire du facebook.

D'autre part, il semble judicieux de changer les mots de passe des 
différents utilisateurs de *Nexus*. Le changement de mot de passe de 
*anonymous* se fait par la configuration du serveur et non pas avec la 
configuration des autres utilisateurs (logique).

## Les repositories
Lors du premier lancement du service, un certain nombre de repositories 
ont été créés. Ils sont présents pas défaut, mais rien ne vous empêche 
de les enlever. Il faut noter qu'il s'agit des liens vers des 
repositories externes et qu'ils vous permettent de trouver les artefacts 
que vous ne possédez pas vous même (*maven-compile-plugin*, 
*checkstyle*, *jdom*,... etc). Si vous regardez la configuration, il 
existe 4 types de repositories:

 - *hosted*:  repository que vous gérez.
 - *proxy*: repository dont vous n'êtes qu'un relais.
 - *virtual*: repository pour "transformer" un repository Maven1 en 
 Maven2.
 - *group*: permet de regrouper un certain nombre de repositories sous 
 un même URL.

La première chose à faire est de vous créer des repositories pour 
**vos** artefacts, donc des repositories "*hosted*". Deux en fait. 
Pourquoi deux? Un pour les "*releases*" et un pour les "*snapshots*".

 - **Release**

Dans le contexte de Maven mais également de l'intégration/développement 
Continue, une release est une version du projet qui est fixe. On peut 
le voir comme l'arrivée que l'on s'est fixée au début du développement: 
ensemble de features, rapports... Une fois ce point atteint, il n'est 
pas possible de l'améliorer. On dit que le projet est livrable.

Si nous rajoutons des features dans une version après avoir effectué 
toutes celles prévues pour cette version, alors c'est que nous changeons
de version.

Il faut comprendre, dans le contexte de *Nexus*, qu'une version 
"*release*" ne change plus, elle est définitive dans une version donnée.
Il est impossible de la redéployer.

 - **Snapshot**

Une version "*snapshot*" est une version qui est en cours de 
développement, nous n'avons pas encore terminé toutes les features de 
cette version.

De ce fait, il est possible d'avoir une multitude de déploiements pour 
une même version de l'artefact.

Dans *Nexus*, vous pouvez appliquer une politique générale sur le 
repository. Elle permet de spécifier le type de contenu et connaitre 
l'utilité de regarder des mises à jour dans ce repository. Cette 
politique peut être surchargée par les politiques de déploiement. Ces 
politiques permettent d'autoriser ou non le redéploiement.

>Il est possible de mettre en place un repository de type *release* avec
>une politique autorisant le redéploiement. On perd alors la différence 
>entre *release* et *snapshot* ce qui peut à mon avis être source de 
>confusion.

Maintenant vos deux repositories créés, il vous faut vérifier la 
répartition de ceux-ci dans les groupes. Par défaut, il y a deux 
repositories de type *group* qui sont créés par *Nexus*. Il faut que 
dans le groupe que vous souhaitez utiliser (le public) soit présent les 
_deux_ repositories que vous avez créé.

Il est possible de se passer de groupe, mais l'écriture du fichier 
*settings.xml* pour Maven est alors bien plus long.

>Depuis la version 1.7.0, il est possible de mettre des group dans 
>d'autre group. Je ne sais pas comment sont gérés les *possible* 
>problèmes de cycles, mais cela peut permettre de faire de l'héritage 
>de repositories dans le cas où certains groupes de développeurs n'ont 
>pas besoin de tous les repositories de leurs voisins..

## Fonctionnement
### La recherche d'artefacts
### *settings.xml*
Il est maintenant nécessaire de créer un *settings.xml* pour que les 
développeurs puissent utiliser le service que vous venez de mettre en 
place. Ce fichier permet en effet de spécifier à Maven où il doit 
chercher les artefacts dont le développeur a besoin. Il doit être 
placé dans le dossier `${HOME}/.m2` ou dans le dossier `${MVN_HOME}/conf`.
Voici la structure d'un tel fichier:

{% highlight xml %}
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0
http://maven.apache.org/xsd/settings-1.0.0.xsd">
    <localRepository/>
    <interactiveMode/>
    <usePluginRegistry/>
    <offline/>
    <pluginGroups/>
    <servers/>
    <mirrors/>
    <proxies/>
    <profiles/>
    <activeProfiles/>
</settings>
{% endhighlight %}

L'important ici est l'élément profiles. Nous allons rajouter un 
*profile* pour pouvoir faire le lien avec le *Nexus*. Voici ce que ça 
donne:

{% highlight xml %}
<profiles>
    <profile>
        <id>default</id>
        <repositories>
            <repository>
                <id>public</id>
                <name>Public Repositories</name>
                <url>http://url_to_server:port/nexus/content/groups/public/</url>
            </repository>
        </repositories>
        <pluginRepositories>
            <pluginRepository>
                <id>public</id>
                <url>http://url_to_server:port/nexus/content/groups/public/</url>
            </pluginRepository>
        </pluginRepositories>
    </profile>
</profiles>
<activeProfiles>
    <activeProfile>default</activeProfile>
</activeProfiles>
{% endhighlight %}

L'URL du repository que nous rajoutons est celui du groupe *public* où 
sont référencés les deux repositories que nous gérons (les *hosted*). Si
vous n'avez pas souhaité mettre en place de groupe (mauvais choix mais 
bon...), vous devez rajouter toutes les URLs des repositories de votre 
serveur *Nexus* afin de pouvoir l'attaquer pour trouver les dépendances 
dont vous avez besoin.

>Si vous voulez plus d'informations, allez faire un tour sur le site 
>de [Apache Maven](http://maven.apache.org/settings.html).

Maintenant vous pouvez utiliser Maven sur votre poste de développement.

## Le déploiement d'artefacts
J'ai dit que vous pouvez l'utiliser, pas forcément que ça marcherai. 
En fait, l'intérêt d'un tel processus (si ce n'est pas encore clair), 
c'est de permettre à votre voisin d'utiliser la librairie que vous 
développez au fur et à mesure de votre avancement.

Le truc c'est que pour le moment, vous n'avez permis à votre poste de 
développement que de récupérer les librairies qui se trouvent sur 
*Nexus*, mais vous ne pouvez pas encore mettre votre travail sur le 
repository (*release* ou *snapshot*). Pour cela, il vous faut mettre des
identifiants, car le déploiement est restreint à certains utilisateurs.
Donc dans le fichier *settings.xml*, il faut rajouter:

{% highlight xml %}
<settings>
    <servers>
        <server>
            <id>nexus</id>
            <username>deployment</username>
            <password>deployment123</password>
        </server>
    </servers>
</settings>
{% endhighlight %}

J'aurai cependant à vous suggérer une chose: ne permettre le déploiement
d'artefact que depuis votre système d'intégration continue. De ce fait,
vous pouvez être sûr du comportement qui a abouti à la mise sur étagère
d'une nouvelle version de l'artefact : tests, couverture de code,
rapport...

Cependant, il ne faut pas que la création d'une version *release* soit
faite par le biais de l'intégration continue. Il s'agit d'une étape
importante dans la vie d'un projet, et elle n'est effectuée qu'une fois
de temps en temps...

Donc maintenant que votre environnement est prêt, vous pouvez effectuer
la commande maven suivante pour déployer votre livrable:

 - *mvn deploy*

>*Je vais sûrement faire un article sur le sujet de Maven un de ces 
>jours, je reviendrai sur l'utilisation de Maven à ce moment là.*

## Conclusion
Comme nous avons pu le voir, l'installation et la configuration (de 
base) de *Nexus* n'est pas une tâche très complexe.

*Nexus* fait parti d'une famille de produit qui est très utile en 
Intégration Continue mais pas forcément essentiel, puisque ce principe 
de fonctionnement ne s'applique pas seulement au projet maven ni 
uniquement au projet java...

## Références
 - [Le post d'Arnaud Héritier sur son blog](http://blog.aheritier.net/from-apache-archiva-to-sonatype-nexus/)
 concernant la comparaison entre Archiva et Nexus et le passage du premier au second.
 - [Le site de Sonatype Nexus](http://nexus.sonatype.org/)
 - [La documentation de Nexus](http://www.sonatype.com/books/nexus-book/reference/)
 - la FAQ [officielle](https://docs.sonatype.com/pages/viewpage.action?pageId=15075782)
