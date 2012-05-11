---
layout: post
title: Les outils du dev
date: 2012-05-11 10:55:48
author: adrien
tags: conseils maven git
published: true
---

Au cour d'un développement, on se retrouve toujours avec un même problème: comment
je fais pour être plus productif ? A cette question, il y a beaucoup de réponse 
possible.

***

# Maven
Maven est un outil de build/gestion de dépendance très utilisé dans le développement
surtout web/JEE. Il est bien intégré aux différents IDEs disponible mais on se 
retrouve souvent à faire des lignes de commandes pour compiler, test, packager 
notre application. Alors certe on fait rapidement des "&uarr; + `enter`" pour 
refaire la même commande 2 fois de suite (ou "!! + `enter` pour les plus 'fou').
Cependant, les nouvelles commandes peuvent venir rapidement.

Mettre un `-Dmaven.test.skip=true` est si vite arrivé ou même un `-pl 
*module-name* -am`. Mais comment être sur du nom du module, ou que l'on n'a pas
mis le "skip" avant le "test" ? Et combien même on l'écrit correctement, c'est long...

LA solution: [ici](https://github.com/juven/maven-bash-completion)

Comment l'utiliser ? Disposer d'un terminal BASH, et dans votre `~/.bashrc`
ou `~/.profile` vous mettez :

{% highlight bash %}
[ -f ~/.bash_completion.bash ] && source ~/.bash_completion.bash || echo "Couldn't load mvn completion"
{% endhighlight %}

Évidemment, vous pouvez vouloir mettre à jour ce fichier. Personnellement j'utilise
un fichier `~/.bash_funct` pour mettre toutes mes fonctions dont celle-ci :

{% highlight bash %}
function update_mvn_bash() {
  if [ -f ~/.bash_completion.bash ]; then
    cp ~/.bash_completion.bash ~/.bash_completion-`date +"%Y%m%d%k%M%S"`.bash
  fi
  curl https://github.com/juven/maven-bash-completion/blob/master/bash_completion.bash > ~/.bash_completion.bash
}
{% endhighlight %}
    
N'oublions pas de modifier le `~/.bashrc` ou `~/.profile` avec:

{% highlight bash %}
[ -f ~/.bash_funct ] && source ~/.bash_funct || echo "Couldn't load bash functions"
{% endhighlight %}

Maintenant la commande `update_mvn_bash` vous mettra à jour votre fichier de
complétion maven. Vous pourrez toujours revenir à une version précédente car les 
anciennes versions du fichiers sont stockées avec le timestamp de la mise à jour.

*** 

# GIT
Si vous avez la chance de travailler avec GIT, ou que vous allez vous y mettre,
un certain nombres de choses peuvent vous simplifier la vie. Pour commencer, je ne
saurai trop vous conseiller de ne pas utiliser Windows avec GIT. Mais bon..

## Une bonne ligne de commande
Pour utiliser toute la puissance de GIT, il vous faut une ligne de commande. C'est
un plus. Afin de travailler plus vite avec votre ligne de commande, que vous y 
soyez habitué ou non, je vous conseille d'aller faire un tour [ici](http://gitfr.net/blog/2010/11/06/ameliorer-sa-productivite-avec-un-beau-shell/).

Ce qu'il faut retenir de ce très bon article, à mon sens, c'est qu'il faut toujours
avoir la possibilité de savoir dans quel état se trouver les sources sur lesquelles
on travaille. Ainsi il *faudrait* avoir dans le shell :

    ?qui? ?où? ?état-git? $

Personnellement j'ai configuré **git completion** qui me donne ceci : 

    [0] [login] [time] [/path/to/current/folder/shortened (branch-git *%)]
    
Le gros avantage étant biensûr de savoir si on a des éléments non commité, sur 
quelle branch on travaille. 

Pour arriver à ça, plusieurs étapes:

* télécharger git-completion.bash
{% highlight bash %}
curl https://raw.github.com/git/git/master/contrib/completion/git-completion.bash > ~/.git-completion.bash
{% endhighlight %}

* modifier le `~/.bashrc` ou `~/.profile` pour utiliser le fichier 
`~/.git-completion.bash`:
{% highlight bash %}
[ -f ~/.git-completion.bash ] && source ~/.git-completion.bash || echo "Couldn't load git completion"
{% endhighlight %}

* modifier votre prompt bash pour y mettre les informations de git:
{% highlight bash %}
PROMPT_COMMAND=prompt
PS1='[${RETC}] [\033[01;34m\u\033[00m] [\033[01;33m\t\033[00m] [\[\033[1;35m\]$newPWD$(__git_ps1 " \[\033[1;34m\](%s)")\[\033[0m\]]\n\$ '
{% endhighlight %}

`PROMPT_COMMAND` peut contenir une commande qui sera exécuté avant chaque affichage
du prompt (attention de ne rien faire de gourmand ici). Moi je fais appel à la 
fonction `prompt` qui me permet d'avoir la variable `$RETC` pour connaître le code 
de sortie de la dernièer commande ainsi d'avoir les 30 derniers caractères du 
chemin d'accès au dossier courant (cf [Annexe 1](#annexe-1))

<div class="alert alert-info">
Ce qui est très important dans mon <em>PS1</em> c'est la partie <strong>
$(__git_ps1 " \[\033[1;34m\](%s)")</strong> qui permet d'afficher (en blue) 
l'état de git.
</div>

Après vous pouvez mettre en place quelques variables d'environnement pour améliorer 
le rendu de git-completion :
{% highlight bash linenos %}
GIT_PS1_SHOWDIRTYSTATE=1
GIT_PS1_SHOWUNTRACKEDFILES=1
GIT_PS1_SHOWSTASHSTATE=1
GIT_PS1_SHOWUPSTREAM="git verbose"
{% endhighlight %}

- ligne 1: montre qu'il y a des changement locaux sur la branche
- ligne 2: montre qu'il y a des éléments locaux non versionné
- ligne 3: montre qu'il y a des stashs
- ligne 4: montre la différence par rapport à la branche distante. Ici `git` pour 
ne pas suivre si on utilise aussi *git-svn*. Et `verbose` pour avoir un compteur 
de modification non pusher par rapport à la branche distante.

## Quelques aliases
Si vous venez de *svn*, vous devez savoir que `svn ci` et `svn commit` est exactement
la même commande. Pour *git*, ça ne fonctionne pas comme ça. Il vous faut définir les 
aliases que vous souhaitez utiliser. Pas très compliqué:

    git config --global alias.alias_name value
    
ou en éditant le fichier `~/.gitconfig`, ou `.git/config` de votre repository local 
GIT, dans la section (à créer si nécessaire) `[alias]`.

Personnellement, je vous recommande ces quelques aliases:

1. `ci` pour `commit`
1. `co` pour `checkout`
1. `cb` pour `checkout -b` &rarr; changement de branche de travail et création 
de celle-ci si elle n'existe pas
1. `st` pour `status` &rarr; affichage de l'état de la branche
1. `s` pour `status -s` &rarr; affichage de l'étage des fichiers de la branche 
sur une ligne
1. `br` pour `branch` &rarr; création d'une branche
1. `ignored` pour `!git ls-files --others -i --exclude-standard --directory`
&rarr; afficher les dossiers qui sont ignorés (dans le .gitignore)
1. `aliases` pour `!git config --get-regexp 'alias.*' | colrm 1 6 | sed 's/[ ]/ = /'`
&rarr; afficher la liste des aliases configurés
1. `df` pour `diff --word-diff`
1. `ds` pour `diff --stat` &rarr; montre la différence en terme d'ajout/suppression
pour le/les fichiers

Ces raccourcis sont ceux que j'utilise tous les jours, tout le temps. Le mieux 
biensûr c'est de les faire vous même, ainsi vous saurez ce que vous avez mis et 
ça vous évitera la vielle blague que l'on fait quand on fait que on découvre la 
commande `alias` sous Linux: 

    alias ls='rm -rf'

Mais sinon, allez (courez) sur GitHub et faite une recherche sur *gitconfig*, vous
devriez trouver un certain nombre d'exemple.

***

# Annexes
## Annexe 1 : truncate pwd <a id="annexe-1"></a>
Merci à Sébastien Douche pour ça!
{% highlight bash %}
function truncate_pwd {
  newPWD="${PWD/#$HOME/~}"
  local pwdmaxlen=30
  if [ ${#newPWD} -gt $pwdmaxlen ]
  then
     newPWD="...${newPWD: -$pwdmaxlen}"
  fi
}
{% endhighlight %}

***

# Links
 - Maven: [http://maven.apache.org](http://maven.apache.org)
 - \#gitfr.net: [http://gitfr.net/blog](http://gitfr.net/blog)
