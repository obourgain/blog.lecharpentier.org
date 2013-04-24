---
layout: post
title: AngularJS, directive et les miettes de pains
date: 2012-09-17 14:38:39
author: adrien.lecharpentier@gmail.com
tags: dev angularjs directive
excerpt: Ma première directive avec angularJS
img: http://angularjs.org/img/AngularJS-large.png
---

Dans un blog précédent, j'ai fait une petite critique à AngularJS. Ici je vais
montrer comment j'ai créé un breadcrumb avec AngularJS en étandant un petit peu
sont comportement et sa configuration. Sans trop me prendre la tête.

## But
Mon application (pour le moment) est en mode "one-page application": un html
`index.html`, hormis les templates. Cependant, je veux pouvoir revenir
simplement sur la page "précédente", de la logique de mes pages.

Donc ce que je veux c'est pouvoir dans le formulaire d'ajout d'évènement
revenir simplement sur la liste de mes évènements.

	Home > Liste des évènements > Ajouter un évènement

Simple et plutôt jolie.

## Choix
On peut faire à la main, dans chaque template, mettre notre breadcrumb. C'est
moche et pas vraiment maintenable.

L'autre possibilité est d'utiliser la configuration des routes et de faire un
mapping pour construire. C'est moins simple, à première vue, mais plus
maintenable et extensible.

## Configuration
Le mieux serait alors de mettre dans la configuration des routes les textes que
l'on veut voir dans notre breadcrumb. Ainsi on a en un clin d'oeil l'URL de la
page, son template AINSI que le texte à afficher. Ça serai pas mal ça.

Donc la configuration ça donne:

    'use strict';
    var tlbApp = angular.module('tlbApp', ['tlbService']);
    tlbApp.config(['$routeProvider', function ($routeProvider) {
        $routeProvider.
            when('/', {
                templateUrl:'views/welcome-page.html',
                breadcrumb:'<i class="icon-home"></i>'
            }).
            when('/event', {
                templateUrl:'views/event-list.html',
                controller:EventListController,
                breadcrumb:'Liste des évènements'
            }).
            when('/event/new', {
                templateUrl:'views/event-form.html',
                controller:EventNewController,
                breadcrumb:'Ajouter un évènement'
            }).
            otherwise({redirectTo:'/'});
    }]);

Donc j'ai dans ma `$route` correspondant à `/`, la notion de template, ie
le dom à utiliser, mais également le texte à afficher dans la page. On voit ici
que j'ai mis du texte mais également des images.

Maintenant, comment on met ça en forme?

## Le template
Pas très compliqué, une simple liste pour afficher notre liste successive de
page. Ici, grand fan de Twitter Bootstrap oblige, j'utilise la classe
`breadcrumb` du framework.

    <div>
      <ul class='breadcrumb'>
      </ul>
    </div>

## Création d'une directive
Avec AngularJS, on peut créer des directives. Dixit la documentation, une
directive est le _seul_ emplacement dans lequel on peut toucher au DOM d'une
page. Comme c'est ce que l'on veut faire, c'est la bonne place.

On va dire que notre directive correspond à une balise précise, qu'on va
appeler `<breadcrumb/>`. Mais qu'il ne peut s'agir que d'une balise, pas d'une
classe ni d'un attribut dans une autre balise. Cependant, dans le rendu de ma
page, je ne veux plus voir la balise `<breadcrumb/>`.

    tlbApp.directive('breadcrumb', function ($location, $route) {
        var breadcrumbDescriptionObject = {
            restrict:'E',
            priority:99,
            replace:true,
            transclude:true,
            templateUrl:'views/breadcrumb.html',
            link:function (_, iElement) {
                var ul = angular.element(iElement.children()[0]);
                var paths = $location.path().split('/');
                var href = "#";
                var path = "";
                angular.forEach(paths, function (item) {
                    item=item.trim();
                    var last = paths.indexOf(item) == (paths.length - 1);
                    if (item=="" || (path[path.length-1]!='/')) {
                        path += '/';
                    }
                    path += item;
                    if($route.routes.hasOwnProperty(path)) {
                        var text = $route.routes[path].breadcrumb || item;
                        href += item + "/";
                        var lnk;
                        if (!last) {
                            lnk = "<li><a href='" + href + "'>" + text + "</a> <span class='divider'>&gt;</span></li>";
                        } else {
                            lnk = "<li class='active'>" + text + "</li>";
                        }
                        this.append(lnk);
                    }
                }, ul);
            }
        };
        return breadcrumbDescriptionObject;
    });

Donc avant le `function`, nous avons la configuration pour notre directive avec:

 - transclude: permet d'avoir un widget isolé du reste de l'application
 - replace: supprime la / les balises appelant la directive
 - restrict: le type de déclaration de la directive. 
    - E=nom d'élément, 
    - A=attribut, 
    - C=classe, 
    - M=commentaire
 - priority: lors de l'utilisation de plusieurs directive, permet de les faire
   dans un certain ordre.
 - templateUrl: l'url vers le fichier contenant le template à utiliser

Ensuite l'algo de construction de mon breadcrumb est <s>un peu</s> très
glouton: je prend la `route` de la page actuellement à l'écran et je sépare par
les '/'. Pour chacun de ces éléments, je concatène avec l'élément précédent et
regarde si ça match une de mes routes avec la fonction

    $route.routes.hasOwnProperty(path)

Simple, gourmant mais fonctionnel.

## Mais..
Il y a un __gros__ mais. Certaine de vos routes peuvent avoir des paramètres:
un id, un nom, etc. Soucis, la fonction `hasOwnProperty` ne match pas les
paramètre. Donc il n'est pas possible d'utiliser ma solution avec ces urls
mais j'y travaille.

Merci.