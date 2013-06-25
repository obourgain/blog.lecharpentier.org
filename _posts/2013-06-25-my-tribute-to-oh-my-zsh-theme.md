---
layout: post
title: My tribute to oh-my-zsh Theme
date: 2013-06-25 20:00:00
author: adrien.lecharpentier@gmail.com
tags: zsh theme customization
excerpt: parce qu'un prompt qui convient vaux mieux que 1000 mots
---

## Rappel

Ne réinventons pas la roue, même pour les descriptions: 

### ZSH

> Zsh is a shell designed for interactive use, although it is also a powerful scripting language.

### oh-my-zsh


> oh-my-zsh is an open source, community-driven framework for managing your ZSH configuration. It comes bundled with a ton of helpful functions, helpers, plugins, themes, and few things that make you shout…

## oh-my-zsh theme

Donc graphiquement, voilà à quoi ressemble mon `prompt`:

![oh-my-zsh theme](/images/2013-06-25-my-tribute-to-oh-my-zsh-theme/screenshot-01.png)

### Les bases

Je ne vois pas beaucoup l'intérêt d'afficher le `hostname` de la machine courante, étant donné que le `prompt` en lui même ne sera jamais que sur ma machine. Sinon, j'apprécie le fait de voir l'utilisateur ainsi que le dossier courant. Par contre, les commandes que le tape, je préfère qu'elles soient écrites sur la ligne en dessous, question de lisibilité.

Donc ça nous donne un `prompt` 

{% highlight sh %}
PROMPT='%{$fg_bold[cyan]%}%n: %{$fg[yellow]%}%04~%{$reset_color%}
» '
{% endhighlight %}

### Le caractère de `prompt`
Pour se mettre dans le bain: le caractère "»" change de couleur en fonction de la réussite ou non de la dernière commande. Pour faire ça:

{% highlight sh %}
function prompt_char {
	if [[ $? -ne 0 ]]; then
		echo "%{$fg_bold[red]%}»%{$reset_color%}"
	else
		echo "%{$fg_bold[green]%}»%{$reset_color%}"
	fi
}

PROMPT='%{$fg_bold[cyan]%}%n: %{$fg[yellow]%}%04~%{$reset_color%}
$(prompt_char) '
{% endhighlight %}

### Git 

car c'est selon moi le meilleurs (D)VCS sur le marché, je l'utilise partout (ce blog, les documentations que j'écris, les projets, etc.), je l'utilise même pour gérer les dépôts SVN.. Du coup je peux me servir que de Git.

Donc pour utiliser correctement Git, il faut voir ce que l'on fait:

 - la branche sur laquelle on travaille,
 - la branche distante (si elle existe) et la différence avec la branche courante,
 - l'état du workspace, de l'index ainsi que du repository local.

Ceci n'est pas trop compliqué car avec oh-my-zsh, il y a la fonction `git_prompt_status` qui permet de montrer ces informations. Il est uniquement nécessaire de mettre en place des variables:

<table class="table">
	<thead>
	<tr>
		<th>Symbole</th>
		<th>Description</th>
		<th>Variable</th>
	</tr>
	</thead>
	<tbody>
	<tr>
		<td><strong>-</strong></td>
		<td>Un fichier a été supprimé du local repository</td>
		<td><em>ZSH_THEME_GIT_PROMPT_DELETED</em></td>
	</tr>
	<tr>
		<td><strong>*</strong></td>
		<td>Un fichier est modifié dans le workspace</td>
		<td><em>ZSH_THEME_GIT_PROMPT_MODIFIED</em></td>	</tr>
	<tr>
		<td><strong>+</strong></td>
		<td>Un fichier a été ajouté dans l'index du repository</td>
		<td><em>ZSH_THEME_GIT_PROMPT_ADDED</em></td>	</tr>
	<tr>
		<td><strong>?</strong></td>
		<td>Un fichier n'est ni dans l'index ni dans le local repository</td>
		<td><em>ZSH_THEME_GIT_PROMPT_UNTRACKED</em></td>	</tr>
	</tbody>
</table>

Je veux maintenant pouvoir voir la branche sur laquelle je travaille:

{% highlight sh %}
ref=$(command git symbolic-ref HEAD 2> /dev/null) || \
ref=$(command git rev-parse --short HEAD 2> /dev/null) || return

STATUS=${ZSH_THEME_GIT_PROMPT_PREFIX}
STATUS=$STATUS${ZSH_THEME_GIT_PROMPT_BRANCH_PREFIX}${ref#refs/heads/}${ZSH_THEME_GIT_PROMPT_BRANCH_SUFFIX}
{% endhighlight %}

Je veux ensuite avoir la branche distante:

{% highlight sh %}
remote=${$(command git rev-parse --verify ${hook_com[branch]}@{upstream} --symbolic-full-name 2>/dev/null)/refs\/remotes\/}
if [[ -n ${remote} ]] ; then
    STATUS="$STATUS ${remote#/refs/remote}"
fi
{% endhighlight %}

Puis après je veux savoir l'état de la branche locale par rapport à la branche distante

{% highlight sh %}
ahead=$(command git rev-list ${hook_com[branch]}@{upstream}..HEAD 2>/dev/null | wc -l | tr -d ' ')
behind=$(command git rev-list HEAD..${hook_com[branch]}@{upstream} 2>/dev/null | wc -l | tr -d ' ')

if [ $ahead -gt 0 ]; then
    STATUS="$STATUS $ahead$ZSH_THEME_GIT_PROMPT_AHEAD_REMOTE"
fi
if [ $behind -gt 0 ]; then
    STATUS="$STATUS $ZSH_THEME_GIT_PROMPT_BEHIND_REMOTE_PREFIX$behind$ZSH_THEME_GIT_PROMPT_BEHIND_REMOTE ZSH_THEME_GIT_PROMPT_BEHIND_REMOTE_SUFFIX"
fi
if [ $ahead -eq 0 ] && [ $behind -eq 0 ]; then
	STATUS="$STATUS ="
fi
{% endhighlight %}

Donc on met tout ce code dans une fonction, ainsi on peut l'appeler sur notre `prompt`. Petite nuance, je le met sur le `rprompt` pour que le `prompt` avec ou sans information Git ne bouge.

Vous pouvez remarquer que j'ai usé et abusé des variables pour mettre les messages: c'est pour toi public! Non sans blague, ça permet de pouvoir changer les caractères / icônes utilisés sans toucher au code qui permet des les afficher.. oui oui c'est très MVC tout ça.. bref

## Conclusion

Vous pouvez trouver sur mon [github](https://github.com/alecharp/oh-my-zsh/blob/alecharp-theme/themes/alecharp.zsh-theme) le fichier complet de ce code.

Les retours, commentaires, avis sont évidemment les bienvenus!
