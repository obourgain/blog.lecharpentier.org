---
layout: post
date: 2012-05-17 18:05:12
title: Génération automatique
tags: jekyll git github apache cgi webhooks-url markdown
author: adrien
---
Alors ce n'est pas parce aue j'ai choisi de passer sur Jekyll aue j'ai 
envie de tout refaire à la main. Par exemple, je n'ai pas envie de devoir
regénérer le site web à chaque fois que j'écris un nouvel article.

Pour faire ça, ce n'est pas très compliqué avec le bon combo d'outils :
**git** + **github** + **apache - cgi**

<div class="alert alert-info">Je ne vais pas mettre les chemins d'accès 
disque dans l'article car je suis un peu timide...</div>

## Création du script de mise à jour
On commence par mettre en place un script qui permettra de récupérer les
derniers articles publiés et générer les pages statiques. 

Le choix de git et github se fait alors comprendre: simple, efficace et 
versionner des textes c'est après tout ce que git fait de mieux. Donc des
posts en *markdown* ça se comprend aussi.

{% highlight bash %}
#!/bin/sh

echo -e "Content-type:text/plain\n\n" 
#Entête http nécessaire pour que le script soit correctement exécuter

git_dir="/path/to/git/clone"
site_dir="/path/to/virtualhost/location"

cd ${git_dir}
git fetch origin
git checkout master
git merge origin/master

jekyll ${git_dir} ${site_dir} 2>&1
{% endhighlight %}

Ici on part du principe que le clone est déjà effectué mais on pourrait 
très bien rajouter quelques lignes pour le faire si il n'existe pas: 

{% highlight bash %}
...
if [ ! -d ${git_dir}/.git ]; then
  git clone git@github.com:user/repository ${git_dir}
fi
...
{% endhighlight %}

Une fois l'exécution du script autorisé (`chmod a+x /path/to/script`), 
essayez de l'exécuter vous-même.

## Exécuter le script via une URL
Pour ça, j'ai besoin du module cgi de apache. Parce que j'héberge d'autres
site internet utilisant wordpress, j'utilise un serveur httpd apache. Je
vais l'utiliser pour ne pas multiplier les outils pour faire la même chose.

Donc dans la configuration de votre apache (ou virtualhost), il suffit 
de mettre en place la directive `ScriptAlias` vers le dossier contenant 
le script que l'on a écrit juste avant.

    <VirtualHost *:80>
      ServerName name.extension
      DocumentRoot /path/to/site/folder
      ScriptAlias /name-that-you-choose/ /path/to/scripts/folder
    </VirtualHost>

Après avoir redémarrer le service apache (`/etc/init.d/apache2 restart`),
vous pouvez essayer l'URL *http://name.extension/name-that-you-choose/script-name*
qui doit exécuter le script.

## Configuration de Github.com
On configure maintenant github pour qu'à la réception d'un push, il sollicite
une url, l'URL du script configuré ci-dessus.

Dans la partie administration de votre repository, vous pouvez configurer
des hooks dans la partie **Service Hooks** (*https://github.com/user/repository/admin/hooks*).
Le hooks que l'on souhaite mettre en place est le premier: *Webhook URLs*
qui enverra une requête POST http sur l'URL en question. Mettez l'URL d'
accès à votre script (*http://name.extension/name-that-you-choose/script-name*),
sauvegardez et vous pouvez ensuite l'essayer.

## Conclusion
Avec cette configuration, je me contente de mettre en place sur la branche
master les articles que je souhaite publier. Ceci ne m'empêche pas de 
travailler sur plusieurs branches (une part article par exemple) mais me
permet de m'assurer que la publication d'article n'est fait que sur la 
branche master. Il suffit de merger ou rebaser les articles dans master
puis de pusher master sur github pour que le site soit généré à nouveau
grâce au script.
