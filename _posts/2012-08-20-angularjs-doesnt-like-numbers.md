---
layout: post
title: AngularJS doesn't like numbers
date: 2012-08-20 23:10:43
author: adrien.lecharpentier@gmail.com
tags: dev js angularjs
excerpt: petite expérience sur angularJS
img: http://angularjs.org/img/AngularJS-large.png
---

Il y a peu, un collègue ([Olivier Croisier])
m'a montré le framework JS de google [AngularJS].

Pour un projet personnel en RESTFull / JS j'utilisais jusque là [KnockoutJS](http://knockoutjs.com/)
pour faire le date-binding mais je me suis dit: "pourquoi pas après tout!".

J'ai donc commencé à refaire toute mon UI (pas beaucoup d'écran donc ça va
quand même) avec [AngularJS]. Les premiers écrans sont simples, ça va vite.
Puis arrive le moment tant attendu de l'épine dans le pied: un coup de
DELETE http sur une url pour supprimer une entrée. Pas très compliqué... sauf
que c'est pas si simple que ça.

## Mise en place
[AngularJS] se met rapide et simplement en place: quelques inclusions de
fichier JS et le tour est joué

 - angularjs : la base du framework
 - angularjs-resource : pour faire du rest
 - application : le dispatcher d'url pour utiliser tel ou tel controlleur
 - controlers : les controlleurs de l'application
 - service : les objets serveur-side portés client-side

Évidemment, c'est simpliste, mais le but de l'article n'est pas de faire une
présentation de [AngularJS] mais de parler du problème que j'ai eu et résolu
ce soir. Pour une meilleur description: [Développer une application REST avec
Spring MVC et AngularJS](http://thecodersbreakfast.net/index.php?post/2012/07/30/D%C3%A9velopper-une-application-REST-avec-Spring-MVC-Angular.js)
par [Olivier Croisier].

## Mon soucis
Donc le problème est apparu lorsque j'ai voulu faire un appel à la méthode
DELETE sur ma resource. Avec [AngularJS] ça se fait comme ça :

{% highlight javascript %}
function EventListController($scope, $location, CodingEvent) {
    $scope.codingEvents = CodingEvent.query();

    $scope.removeEvent = function(codingEvent) {
        CodingEvent.delete({'id':codingEvent.id});
    };
}
{% endhighlight %}

ou comme ça :

{% highlight javascript %}
function EventListController($scope, $location, CodingEvent) {
    $scope.codingEvents = CodingEvent.query();

    $scope.removeEvent = function(codingEvent) {
        codingEvent.$delete({'id':codingEvent.id});
    };
}
{% endhighlight %}

mais ça c'est la théorie. Car si on utilise ça, tout ce que vous aurez c'est
une magnifique

    DELETE http://localhost:8081/rest/event 405 (Method Not Allowed)

oui car la méthode DELETE est bindé sur l'url `/rest/event/{id}` donc on ne
touche pas la bonne url. Le problème est étrange, puisque vous avez bien
passé un `id` (entre accolade dans les codes précédent).

## Indices
Histoire de vous prendre la tête, le problème persiste 2 longues semaines,
vous avez eu le temps de refaire la configuration 3 fois, dont une fois avec
votre gentil collègue qui maintenant parle presque courament l'AngularJS.. mais
rien de rien.

Comme ça pour rire, vous remplacez, un soir de grande fatigue, le `codingEvent.id`
par un `'0'` et là, Ô miracle du Javascript, ça fonctionne! On touche au but..

## Résolution
Si ça vous saute aux yeux alors bravo, pour les autres (comme moi), un petit
`console.log(typeof codingEvent.id);` ainsi qu'un `console.log(typeof '0');`
est indispensable. Pas le même type. `string` dans le premier cas et `number`
dans le second. Non ça ne peut pas être ça.. Mon service REST prend un Long
en paramètre, et quand je remplace `codingEvent.id` par `'totolabelette'`
j'ai la belle exception

    Class java.lang.Long can not be instantiated using a constructor with a single String argument

avec une 404 sur l'UI..

et pourtant si.. remplacez codingEvent.id par codingEvent.id.toString() et
votre appel REST fonctionne parfaitement!!

Mais alors, comment mes GET sur l'url `/rest/event/{id}` pouvaient fonctionner
avant alors que de la même façon le service ne prend qu'un Long. Tout
simplement car je récupère l'id de l'évènement dans l'url de la page via un
`$routeParams` et que cela renvoi une string, pas un number.

## Conclusion
Moralité de mon aventure :

 - débugguer du Javascript c'est très palpitant (oui mauvais pour le coeur)
 - AngularJS n'aime par les numbers!!

J'espère que cela vous a plu.

[Olivier Croisier]: http://twitter.com/OlivierCroisier
[AngularJS]: http://www.angularjs.org/
