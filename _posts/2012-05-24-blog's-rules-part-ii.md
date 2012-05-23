---
layout: post
title: Blog's rules part II
date: 2012-05-24  0:46:44
author: adrien.lecharpentier@gmail.com
tags: blog rules how-to
excerpt: La participation au blog et à la correction des posts ne doit
  pas passer inaperçu! Pour cela, je précise quelques notions
contrib: [myemailaddress@example.com]
---

Dans mon premier post sur Jekyll, j'ai dit que la contribution serai 
mise en valeur. Non, pas de veine parole!

Lorsque vous souhaitez corriger / améliorer un de mes posts, je vous
invite à vous rendre sur github, modifier le fichier du post en erreur
(dans le dossier `_posts`). Lors de cette modification, ajoutez votre 
adresse email dans l'entête du post à la variable **contrib** comme 
suit:

{% highlight yaml %}
...
author: adrien.lecharpentier@gmail.com
contrib: [john.doe@example.com]
...
{% endhighlight %}

ou 

{% highlight yaml %}
...
author: adrien.lecharpentier@gmail.com
contrib: [contrib1@not.you, this.is@y.ou]
...
{% endhighlight %}

suivant si le post à déjà ou non été contribué.

Exemple de rendu sur cette même page avec l'adresse email : 
myemailaddress@example.com.
