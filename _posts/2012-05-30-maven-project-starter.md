---
layout: post
title: Maven project starter
date: 2012-05-30 16:44:47
author: adrien.lecharpentier@gmail.com
tags: maven linux bash function enhancer dev
excerpt: Parfois commencer un project c'est long et embêtant car il
  faut commencer par créer la structure du project selon maven. 
  Comment peut-on le faire rapidement?
---

Aujourd'hui, on utilise beaucoup maven dans le développement, surtout
web, mais commencer la phase de dev d'un projet peut être un peu long.

Il faut toujours passer par la phase de création des dossiers 
conventionnés par maven et ce n'est pas toujours simple. Alors astuce:

{% highlight bash %}
function create-maven-struct(){
  local base=$1
  [ "$base" == "" ] && base="."
  mkdir -p $base/src/{main,test}/{java,resources}
}
{% endhighlight %}

Ajoutez cette fonction dans votre `~/.bashrc` ou équivalent et vous
serez en mesure de créer la structure d'un projet maven très simplement
grâce à la fonction `create-maven-struct`.

A noter que sans argument, la structure sera créée dans le dossier 
courant, et avec argument la structure sera créée à partir de 
l'argument (également créé si il n'existe pas).

Enjoy :)

