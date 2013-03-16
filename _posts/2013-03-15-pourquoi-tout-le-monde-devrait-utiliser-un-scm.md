---
layout: post
title: Pourquoi tout le monde devrait utiliser un SCM
date: 2013-03-15 16:00:00
author: adrien.lecharpentier@gmail.com
tags: misc scm git svn bazaar mercurial
img: http://git-scm.com/images/logo@2x.png
excerpt: Un petit pamphlet pour l'utilisation de SCM en toute circonstance
---

Il s'agit là d'un point de vue que je défends depuis quelques temps mais il est temps que je m'exprime ouvertement sur le sujet (ça va m'éviter des heures et des heures de Psy).

Donc <span class="lead">Pourquoi tout le monde ~~veut devenir un cat~~ devrait utiliser un SCM?</span> <small>car […] [il] retombe toujours sur ses pattes!</small>

# Définition: un SCM c'est quoi?
Donc pour ce qui ne connaissent pas le terme, SCM pour _Source Code Manager_. Il est question d'un outil permettant de gérer du code source. Le point de cet article est de montrer que le _code_ peut en fait être n'importe quoi.

Quand on dit "_gérer_ du code source", on veut dire être capable de garder des versions de ce code dans le temps et ainsi savoir qui a fait quoi sur quelle ligne du fichier. Cela permet également de travailler à plusieurs sur le même projet de manière plus rapide. 

L'idée principale est donc d'être capable de travailler sur le contenu d'un fichier, à plusieurs si nécessaire, sans trop se marcher sur les pieds et de pouvoir revenir à tout moment à ce que l'on a écrit il y a 15 jours.

# Un SCM pour tous?
Bon ok, j'y vais un peu fort là.. Disons un SCM pour tout ceux qui font de l'informatique et qui travaillent avec des fichiers. Donc tous ceux qui utilisent un logiciel de gestion de stock ou autres, vous n'êtes pas concernés (mais j'espère que les données sont tout de même versionnée..).

Lorsque l'on ne travaille pas avec un SCM, la plupart des gens vont alors faire des copies de fichiers, soit en ajoutant un numéro d'itération (1, 2, 3…) soit avec la date d'édition, voir avec une combinaison des deux pour le cas de plusieurs versions dans la même journée, sur le nom du fichier. J'ai vu ça ~~trop~~ très souvent. Ça arrive même sur des dossiers complets! C'est une méthode suffisante lorsque l'on est étudiant, et encore avec le recule pas vraiment. Mais lorsque l'on commence à faire des choses sérieuses ou sérieusement, il faut alors aborder notre façon de travailler différemment.

# Un SCM pour les fichiers
Alors pourquoi utiliser un outil qui ne me permet pas d'éditer mon Rapport trimestriel de rentabilité? Tout simplement parce que ce fichier ne sera JAMAIS en version finale du premier coup. Combien même vous en faite depuis 15ans, que vous avez la certification de Gestion du Management du Rapport Trimestriel, vous aurez toujours plusieurs version de votre fichier. 

Ceci car Mr Doe, _votre supérieur_, est venu vous voir pour vous dire de ne pas montrer les 0,02% qui font passer votre rentabilité en dessous des 76,99%, ou que les chiffres ont changé, ou qu'un nouveau chapitre doit être intégré. Bref vous n'aurez jamais une version initial équivalente à une version finale. Je parle de rapport, mais pour cet article, il en est de même pour les retouches d'images, les intégrations de sites web, la rédaction de articles sur un blog (au hasard).

C'est utile surtout si vous ne travaillez de manière linéaire mais avec un ordonnancement par priorité: le contenu avant la mise en page, le corps avant la conclusion, etc.. Pourquoi? Tout simplement parce que vous allez écrire une première version de votre contenu, l'envoyer à un relecteur (votre patron, un ami); en attendant son retour, vous attaquez la mise en page; lorsqu'il revient vers vous, avec les 15Milliards de corrections à faire, grâce au SCM, vous pouvez mettre de côté la mise en page, travailler votre contenu et envoyer une seconde version puis reprendre votre mise en page. 

Vous trouvez ça superflu? Entre les relectures, vous ne faites rien?

Vous envoyer une seconde version de votre contenu avec un morceau non complet de votre présentation et ainsi perdre définitivement l'attention de votre relecteur?

# Comment s'y mettre?
Si vous êtes encore entrain de me lire et que vous avez compris mon point, je suis déjà content! Vous êtes sur la bonne voie! Elle est longue, sinueuse et semée d'embûche mais vous en sortirez plus fort! Et votre travaille plus productif.

Donc par où commencer? Faire un choix. Il vous faut en effet choisir le SCM que vous souhaitez utiliser. Là, pas de miracle, demander à vos amis! Pourquoi? Sans tourner autour du pot, c'est à eux que vous ferez appel en cas de problème! Il est donc préférable qu'ils connaissent ou puissent vous orienter.

Une fois le choix fait, documentez-vous, lisez, apprenez. Certains sont plus simple à prendre en main que d'autre. D'expérience, plus le démarrage est rapide, plus vous aurez de problème par la suite! Il faut donc que votre choix soit réfléchi, que vous puissiez supporter tous les reproches/moqueries que vos amis pourront vous faire.

Quelques critères de démarrage: ne payez pas, ayez la possibilité de faire des essais sur votre poste de travail, qu'il est une interface graphique ou qu'un logiciel pouvant servir d'interface graphique, regarder des tendances sur Twitter ou Google pour voir si il est beaucoup utilisé. 

Autre chose, ce n'est pas parce qu'un informaticien vous dit que tel ou tel SCM est simple que c'est le cas! Nous avons une façon de penser proche de ceux qui ont écrit ce SCM, on le comprend donc plus rapidement. De plus, de part notre travail, il est fort possible que l'on ai utilisé ce SCM durant de nombreuses heures.

<div class="row"><div class="well well-large offset3 span6 text-center text-warning">Toutes ressemblance avec des personnes existantes ou ayant existés est tout à fait fortuite</div></div>

# La note de l'auteur
Personnellement, je vous conseillerai Git. Tout le monde le dit compliqué, long à prendre en main. Je ne suis absolument pas d'accord avec ça (cf note du paragraphe précédent)!

Dans son ensemble, oui c'est plus long à prendre une main, comprendre comment ça fonctionne, pourquoi il râle de temps en temps. Mais pour démarrer vous n'avez besoin que de trois mots: `add`, `commit`, `push`. Il y a quelque semaine, un codeur avec qui j'ai fait mes premières armes m'a avoué n'avoir jamais eu besoin de plus jusqu'alors. J'étais étonné mais il est vrai que dans un contexte personnel, le reste des actions n'est que très rarement mises en pratique. Par contre, une fois que vous avez compris c'est trois termes, ne vous arrêtez pas à vous documenter, mais vous pouvez déjà faire des testes et des essais.

D'après mes critères de choix il est pas mal du tout:

 - gratuit,
 - pas besoin de serveur pour fonctionner,  
 si nécessaire, il y a beaucoup de service pouvoir prendre place de serveur Git: 
    - GitHub, 
    - Google Code,
    - BitBucket  
 pour les plus connu (et gratuit dans une certaine mesure)
 - GitG (Linux), GitX (Mac), SourceTree (Mac mais qui arrive sur Windows!!),
 - très grosse communauté d'utilisateur sur Twitter, beaucoup de blog en FR et EN, beaucoup de livre

Liste non exhaustive de choses à lire / voir sur Git:

 - [Git-SCM](http://git-scm.com) : LE livre / site à lire, du débutant au confirmé (les experts étant ceux qui codent Git)
 - [Git-cheatsheet](http://www.ndpsoftware.com/git-cheatsheet.html) : la représentation visuelle des espaces Git

