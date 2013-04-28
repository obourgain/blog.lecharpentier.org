---
layout: post
title: Writing code crap and refactoring painlessly
date: 2013-04-28 20:00:00
author: adrien.lecharpentier@gmail.com
tags: dev
excerpt:
---

Quand on écrit du code, il y a deux choses de mon point de vue qui peuvent nous conduire à l'auto-censure:

 - ne pas savoir écrire du code
 - penser qu'on ne sait pas écrire du code propre

Dans un cas comme dans l'autre, le résultat, si c'est le cas, il vaut mieux s'abstenir, sinon ça donne ça [codecrap.com](http://codecrap.com/). Sauf que si on écrit jamais on ne saura jamais écrire, dixit le veille adage "c'est en forgeant qu'on devient forgerons."

Donc je pense qu'il y a au moins une solution pour chaque cause.

## Solutions

### Je ne sais pas écrire du code

Lisez, allez aux conférences, (re)tournez à l'école ou en formation. Il n'y a pas de mal à ça. Bien au contraire. On voit d'ailleurs plein de très bon développeurs Java suivre une formation (e-learning ou autre) pour mettre les mains dans le cambouis de Scala.

### Je pense que je ne sais pas écrire du code propre

De même, pas de miracle, lisez, allez aux conférences, etc. Autres choses que vous pouvez faire, c'est vous faire relire: en travaillant à plusieurs, vous aurez plusieurs façon de voir les choses et donc vous serez amené à les essayer et voir qu'elle est la meilleure. 

Si, par contre, vous être comme moi et que vous avez des collègues avec le "code" sur la main, alors écoutez-les, proposez-leur de participer à un projet perso pour avoir un retour, etc. 

### Extra

Dans tous les cas, certains outils et bonnes pratiques pourront vous aidez sur le long chemin de l'apprentissage: Git, un IDE maitrisé, des tests, un système de build automatisé, un code-reviewer. Je pense que tout ça mérite un autre post, donc j'y reviendrai.

## Mise en pratique

Imaginons que vous souhaitez mettre en place un système qui va vous permettre de discuter (grosse simplification de mon cas pratique), mais comme vous êtes gentil, vous pensez aux étrangers. 

Le code final sera surement un peu over-designed par rapport au cas d'utilisation que j'ai décris mais l'important dans un voyage ce n'est pas la destination mais le chemin pour y parvenir.

### Étape 1 : simple, efficace

On commence le code :
{% highlight java %}
public class Main {
	public static void main(String[] argv) {
		System.out.println("Bonjour!");
	}
}
{% endhighlight %}

### Étape 2 : tests

On va dire que l'on veut pouvoir tester notre message affiché simplement, donc on revoit notre copie pour avoir une classe `NiceGuy` pour nous aider :

{% highlight java %}
public class NiceGuy {
	public String sayHi() {
		return "Bonjour!";
	}
}
{% endhighlight %}

On écrit donc notre test, ainsi nous aurons un état que nous pourrons valider et ainsi éviter de contrarier le bon fonctionnement lorsque nous ferons évoluer notre application :

{% highlight java %}
public class NiceGuyTest {
	@Test
	public void sayHi() {
		assertThat("Bonjour!").isEqualTo(new NiceGuy().sayHi());
	}
	@Test
	public void notSayingHi() {
		assertThat("Au revoir!").isNotEqualTo(new NiceGuy().sayHi());
	}
}
{% endhighlight %}

 \* utilization de assertj-core et junit pour les tests unitaires.

{% highlight java %}
public class Main {
	public static void main(String[] argv) {
		System.out.println(new NiceGuy().sayHi());
	}
}
{% endhighlight %}

### Étape 3 : "parle-moi comme je parle"

Bon, on veut plusieurs langues possible à partir d'un argument de la ligne de commande :

{% highlight java %}
public class Main {
	public static void main(String[] argv) {
		System.out.println(new NiceGuy(argv[0]).sayHi());
	}
}
{% endhighlight %}

On va donc permettre d'instancier un `NiceGuy` en précisant un langage à utiliser. 

{% highlight java %}
public class NiceGuy {
	private final String locale;

	public NiceGuy() { this(null); }
	public NiceGuy(String locale) { this.locale = locale; }

	public String sayHi() {
		if (locale == null || "fr_FR".equals(locale) || "fr".equalsIgnoreCase(locale)) {
			return "Bonjour!";
		}
		throw new IllegalStateException("The locale is not recognized");
	}
}
{% endhighlight %}
	
Là, notre test nous dit que tout va bien. Cool, nous avons donc améliorer le code sans régression. Mais testons un peu plus :

{% highlight java %}
public class NiceGuyTest {
	@Test
	public void sayHi() {
		assertThat("Bonjour!").isEqualTo(new NiceGuy().sayHi());
	}

	@Test
	public void sayHiFR() {
		assertThat("Bonjour!").isEqualTo(new NiceGuy("fr").sayHi());
	}

	@Test
	public void notSayingHi() {
		assertThat("Au revoir!").isNotEqualTo(new NiceGuy().sayHi());
	}

	@Test(expected = IllegalStateException.class)
	public void unknownLocale() {
		assertThat("Au revoir!").isEqualTo(new NiceGuy("etps").sayHi());
	}
}
{% endhighlight %}

Maintenant, nous sommes sûr que lorsque nous précisons un langage ou non, nous avons bien un retour en français. En plus, si un langage n'est pas connu, nous avons bien l'erreur que nous attentions. Tout cela semble parfait.

Donc comme "we areu completely bilinguale" (complètement bilingue en français dans l'accent), on peut faire le code pour parler en Anglais :

{% highlight java %}
public class NiceGuy {
	private final String locale;
	public NiceGuy() { this(null); }
	public NiceGuy(String locale) {
		this.locale = locale;
	}

	public String sayHi() {
		if (locale == null || "fr_FR".equals(locale) || "fr".equalsIgnoreCase(locale)) {
			return "Bonjour!";
		}
		if ("en_US".equals(locale) || "us".equalsIgnoreCase(locale)) {
			return "Hi motherfucker!";
		}
		throw new IllegalStateException("The locale is not recognized");
	}
}
{% endhighlight %}

On complète notre test pour être sûr que nous avons un code validé :

{% highlight java %}
public class NiceGuyTest {
	…
	@Test
	public void sayHiUS() {
		assertThat("Au revoir!").isNotEqualTo(new NiceGuy("us").sayHi());
		assertThat("Hi motherfucker!").isEqualTo(new NiceGuy("us").sayHi());
	}
}
{% endhighlight %}

Bon ok maintenant ça fonctionne, mais on se rend bien compte que la méthode `sayHi` va vite devenir très longue et potentiellement compliquée à suivre dans le temps.

### Étape 4 : et dans 6 mois, je ferai comment ?

Donc pensons 3 secondes. Il nous faudrait un système pour avoir différentes façon de répondre à `sayHi` et de découvrir ces propositions tout seul (ou presque).

Je reviendrai sur le presque. Intéressons-nous à la transformation de notre implémentation. On peut utiliser le mécanisme d'héritage ou d'implémentation. Compte tenu qu'aucun langage n'aura de code en commun, je choisi de créer une interface à partir de NiceGuy. Pour des raisons de nommage, transformons l'actuelle `NiceGuy` en `FrenchNiceGuy` :

{% highlight java %}
public class FrenchNiceGuy {

	public String sayHi() {
		return "Bonjour!";
	}
}
{% endhighlight %}

Puis on peut faire une "extraction" vers une interface pour créer l'interface `NiceGuy` :

{% highlight java %}
public interface NiceGuy {
	String sayHi();
}
{% endhighlight %}

Si on revient vers `FrenchNiceGuy` on peut voir que la classe implémente notre nouvelle interface. Créons notre implémentation `AmericanNotSoNiceGuy` :

{% highlight java %}
public class AmericanNotSoNiceGuy implements NiceGuy {

	@Override
	public String sayHi() {
		return "Hi motherfucker!";
	}
}
{% endhighlight %}

On doit transformer nos tests pour qu'ils valident nos implémentations :

{% highlight java %}
public class NiceGuyTest {
	@Test
	public void sayHi() throws Exception {
		assertThat("Bonjour!").isEqualTo(new FrenchNiceGuy().sayHi());
	}

	@Test
	public void notSayingHi() throws Exception {
		assertThat("Au revoir!").isNotEqualTo(new FrenchNiceGuy().sayHi());
	}

	@Test
	public void sayHiUS() throws Exception {
		assertThat("Au revoir!").isNotEqualTo(new AmericanNotSoNiceGuy().sayHi());
		assertThat("Hi motherfucker!").isEqualTo(new AmericanNotSoNiceGuy().sayHi());
	}
}
{% endhighlight %}


On peut donc faire une liste d'implémentations. Il faut que cette liste fasse partie du langage de l'implémentation. Ceci est le "presque" un peu plus haut.

Il faut toutefois permettre à d'autres d'amener leurs langages.

{% highlight properties %}
// languages.properties
fr = FrenchNiceGuy
us = AmericanNotSoNiceGuy
{% endhighlight %}

J'ai donc un fichier `properties` pour lister les implémentations de mes langages. Maintenant, je dois mettre en place un mécanisme pour permettre d'utiliser nos différentes implémentations. 

{% highlight java %}
public class NiceGuyManager {
	private final Properties gentlemen = new Properties();
	public NiceGuyManager() throws IOException {
		this.gentlemen.load(NiceGuyManager.class.getResourceAsStream("/languages.properties"));
	}
	
	public boolean isLanguageSpoken(String language) {
		return gentlemen.containsKey(language);
	}
	
	public NiceGuy forLocale(String language)
			throws ClassNotFoundException, IllegalAccessException, InstantiationException { 
		if (!isLanguageSpOken(language)) {
			throw new ClassNotFoundException(String.format("The language %s is not recognized", language));
		}
		return (NiceGuy) Class.forName(gentlemen.get(language).toString()).newInstance();
	}

}
{% endhighlight %}

Et on remplace dans Main `new NiceGuy("XX").sayHi()` par `manager.forLocale("XX")`. Normalement, tous les tests sont bons. On a donc maintenant un état validé de notre comportement. Rajoutons quelques tests pour notre `NiceGuyManager` :

{% highlight java %}
public class NiceGuyManagerTest {

	private NiceGuyManager manager;

	@Before
	public void setUp() throws Exception {
		this.manager = new NiceGuyManager();
	}

	@Test
	public void getFR() throws Exception {
		assertThat(manager.forLocale("fr")).isNotNull().isInstanceOf(FrenchNiceGuy.class);
	}

	@Test(expected = ClassNotFoundException.class)
	public void getUnknown() throws Exception {
		manager.forLocale("notdefined");
	}
}
{% endhighlight %}

### Étape 5 : relecture par un très très bon

> "Et sinon SPI tu connais?"

On cherche, on trouve, [on lit](http://thecodersbreakfast.net/index.php?post/2008/12/26/Java-%3A-pr%C3%A9sentation-du-Service-Provider-API), on s'incline: Oui j'ai réinventé la roue dans l'étape 4. Donc on va faire du refactor. Pas de peur, grâce aux tests, on sait que l'on retrouvera au moins aussi bien.

On rajoute le langage dans `NiceGuy`: 
{% highlight java %}
public interface NiceGuy {
	String sayHi();
	String getLocale();
}
{% endhighlight %}

On implémente la nouvelle méthode dans `FrenchNiceGuy` et `AmericanNotSoNiceGuy`. Les tests n'ont pas à bouger et sont encore valides. On voit là l'importance de ces tests, puisque nous avons "amélioré" nos implémentations sans contrarier le bon fonctionnement du code existant.

On réécrit `NiceGuyManager` pour utiliser le fonctionnement de _SPI_ :

{% highlight java %}
public class NiceGuyManager {
	private final Map<String, NiceGuy> gentlemen = new HashMap<String, NiceGuy>();

	public NiceGuyManager() {
		ServiceLoader<NiceGuy> gentlemenServiceLoader = ServiceLoader.load(NiceGuy.class);
        Iterator<NiceGuy> iterator = gentlemenServiceLoader.iterator();
        for (NiceGuy gentleman; iterator.hasNext(); ) {
            try {
                gentleman = iterator.next();
                gentlemen.put(gentleman.getLocale(), gentleman);
            } catch (ServiceConfigurationError error) {
            	// can be logged
            }
        }
	}
	
	public boolean isLanguageSpeaken(String language) {
		return gentlemen.containsKey(language);
	}
	
	public NiceGuy forLocale(String language)
			throws ClassNotFoundException {
		NiceGuy gentleman = gentlemen.get(language);
        if (gentleman == null) {
            throw new ClassNotFoundException(String.format("There is no class that can manage the language %s",
                    language));
        }
        return gentleman;
	}

}
{% endhighlight %}

et nous transformons le fichier _properties_  en un fichier _NiceGuy_ dans un dossier "META-INF/services". On peut rejouer les tests et on s'aperçoit que nous n'avons là encore pas dégradé le fonctionnement de notre implémentation. Certes, nous aurions pu nous arrêter en étape 4, mais nous avons bénéficié d'un bon conseil et qui a potentiellement grandement amélioré le futur de notre application.

## Conclusion

En partant d'un code moche et inutilisable sur le moyen terme, nous avons pu obtenir un code simple et efficace. Un brin de lecture, d'aide et c'est tout. Par principe, personne ne sait tout: travailler à plusieurs permet de combler certain manque.

D'autre part, nous avons : 

 - écrit des tests, afin de garder un état valide de notre code, 
 - utilisé un éditeur de code que l'on connait bien,
 - un SCM efficace, Git évidemment.

En un sens, il faut toujours se remettre en cause en informatique, en appliquant certaines pratiques de développement informatique, on peut rapidement s'améliorer. Vous pouvez également mettre en place des outils de relecture de code comme Gerrit ou des phases de Pair Programming. Ces éléments peuvent avoir un retour sur investissement (puisque certains ne comprennent que ça) très important, au même titre que [plus de RAM, plus d'écran](http://thecodersbreakfast.net/index.php?post/2012/08/26/equipez-vos-d%C3%A9veloppeurs) ou une formation..

Merci de m'avoir lu!

## Références

 - [Java : présentation du Service Provider API](http://thecodersbreakfast.net/index.php?post/2008/12/26/Java-%3A-pr%C3%A9sentation-du-Service-Provider-API) par Olivier Croisier
 - [Code source pour cet article](https://github.com/alecharp/crap-code-refactoring)
 - [Équipez vos développeurs](http://thecodersbreakfast.net/index.php?post/2012/08/26/equipez-vos-d%C3%A9veloppeurs) par Olivier Croisier
