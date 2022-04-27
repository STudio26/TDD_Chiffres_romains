# TDD, un nouvel exemple (première partie)

## C'est quoi déjà le Test Driven Developpement&nbsp;?

Le Test Driven Development (TDD) est une manière de développer, où ce sont les spécifications écrites sous forme de tests (et leurs résultats attendus) qui guident votre développement avant l'écriture du code. Chaque test ajouté fait avancer vers la résolution du problème, chaque modification du code déjà écrit est encouragée, tant que les tests précédents ne sont pas "cassés". Si vous cassez un test que vous aviez validé, vous devez revenir en arrière sur votre code ajouté et appliquer d'autres modifications qui vous feront progresser (et jamais régresser).

Vous êtes lassés des exemples classiques de TDD tel que [FizzBuzz](https://en.wikipedia.org/wiki/Fizz_buzz) et vous souhaitez aller un tout petit peu plus loin&nbsp;? Dans cet article je vous présente un nouvel exercice à pratiquer si vous êtes en manque d'inspiration ainsi qu'une ébauche de solution. Attention, je n'ai pas dit que l'exemple FizzBuzz n'était pas un bon exemple, au contraire, c'est un exemple que je vous invite à pratiquer au moins une fois&nbsp;! Mais si vous cherchez à aller plus loin et que vous ne savez pas dans quelle direction aller, vous trouverez, je l'espère, une piste dans cet article.
La numérotation romaine

Sans vouloir rappeler toutes les règles d'écriture des nombre comme les romains la pratiquaient ([Wikipedia fait cela très bien](https://en.wikipedia.org/wiki/Fizz_buzz)), voici la liste des premiers nombres de 1 à 10 : *I*, *II*, *III*, *IV*, *V*, *VI*, *VII*, *VIII*, *IX* et *X*.

Dans la démarche TDD je vois ces nombres comme une spécification. Si on donne 1 à notre convertisseur, il répondra *I*, si on lui donne 2 il répondra *II*, si on lui donne 3 il répondra *III* et ainsi de suite (non, si on lui donne 4 il ne répondra pas *IIII* mais *IV*). Quoi de plus rassurant que d'ajouter une minuscule étape, un baby step (un nouveau test qui nous fait progresser) et d'être sûr de ne rien casser de ce qu'on a déjà codé&nbsp;?

Lorsqu'on pratique le TDD il faut ajouter le minimum de code pour faire passer le test. On part de rien (vraiment rien), on écrit un test, une spécification, et ont fait en sorte que cela fonctionne. On a d'abord une erreur de compilation (je prendra Java comme langage d'exemple) que l'on va corriger (rendez-vous compte on part de zéro, la classe que l'on référence dans le code de test n'existe même pas&nbsp;!), probablement une deuxième (la méthode que vous appelez n'existe pas non plus&nbsp;!), que l'on va corriger également puis ensuite il faudra écrire du code, et le plus souvent on retourne la valeur en dur pour commencer. Cela n'est pas intuitif, mais on va à la fois répondre à la spécification et surtout écrire le moins de code possible.

## 1, 2, 3 nous irons au bois

On commence&nbsp;? La première spécification dit qu'on attend *I* pour le nombre 1. Ce qui donne, en terme de tests&nbsp;:

```java
package com.mycompany;

import static org.junit.Assert.assertEquals;
import org.junit.Test;

public class RomanNumbersTest {
    @Test
    public void OneShouldReturnI() {
        RomanNumber roman = new RomanNumber();
        assertEquals("I", roman.convert(1));
    }
}
```

Évidemment cela ne se compile même pas, la classe `RomanNumber` n'existe pas. Pour pouvoir faire passer le test, le minimum est de créer cette classe ensuite… on verra.

Ce qui nous donne&nbsp;:

```java
package com.mycompany

public class RomanNumber {

    public RomanNumber() {
    }
}
```

Mais on a toujours une erreur de compilation dans la classe de test car la méthode `convert()` n'existe pas. Il faut donc créer cette méthode. Elle retourne une chaine, en attendant d'en savoir plus on va retourner une chaine vide.

```java
package com.mycompany;

public class RomanNumber {

    RomanNumber() {
    }

    String convert(int value) {
        return "";
    }
}
```

On n'a plus d'erreur de compilation&nbsp;! Évidemment le test ne passe pas, car à aucun moment on ne retourne *I*. Le minimum de code pour que le test passe est de retourner *I*… en dur. Si si, vous avez bien lu, **en dur**.

```java
package com.mycompany

public class RomanNumber {

    RomanNumber() {
    }

    String convert(int value) {
        return "I";
    }
}
```

Bravo, le premier test passe. 1 en nombre romain s'écrit *I*.

Passons à la suite. Si on veut convertir 2 en nombre romain on doit avoir II en résultat. Écrivons le test, qui échouera inévitablement (car on retourne toujours *I*), puis adaptons le code. Cela nous donne le code suivant pour le test&nbsp;:

```java
    @Test
    public void TwoShouldReturnII() {
        RomanNumber roman = new RomanNumber();
        assertEquals("II", roman.convert(2));
    }
```

Et voici le code le plus simple possible issu de la méthode précédente et qui ferra passer les deux tests.

```java
    String convert(int value) {
        if (value == 2)
            return "II";

        return "I";
    }
```

On ne cherche pas la "meilleure" solution. On aurait pu mettre un `else`, mais à quoi bon&nbsp;? En le mettant, bien évidemment les deux tests vont réussir. Il n'y a pas d'obligation. Vous verrez qu'en avançant on structurera assez vite le code. De même, je n'ai pas mis d'accolades après le `if`. Si un jour vous ajoutez du code et oubliez d'ajouter les accolades nécessaires, vous pourrez vraiment compter sur les tests pour vous dire que quelque chose ne va pas 😀.

Allez, on passe à trois&nbsp;? On ajoute le test, on fait échouer le code (il va retourner *I* alors qu'on attend *III*) et on adapte le code, au plus simple. Ce qui nous donne (par exemple)&nbsp;:

```java
    String convert(int value) {
        if (value == 3)
            return "III";

        if (value == 2)
            return "II";

        return "I";
    }
```

Les tests passent, cool&nbsp;! Là on se pose, et on se souvient d'avoir entendu que d'abord on écrit un test qui échoue, qu'ensuite on écrit le code pour que le test passe et ensuite on fait du réusinage de code (du **refactoring**). Si on a 1 on retourne *I*, si on a 2 on retourne *II* et si on a 3 on retourne *III*, on ne pourrait pas écrire cela plus simplement&nbsp;? Il y a [plein de manières différentes de faire cela](https://www.baeldung.com/java-string-of-repeated-characters) d'ailleurs. On essaye, et si c'est faux, les tests vont échouer, on ne risque donc rien 😉. On peut toujours revenir en arrière…

```java
    String convert(int value) {
        StringBuilder answer = new StringBuilder();

        for (int i = 1; i <= value; i++)
            answer.append("I");

        return answer.toString();
    }
```

Ça passe, les trois tests sont toujours verts.

## 4, 5, 6 cueillir des cerises

On va tenter de passer à quatre. Tout d'abord le test&nbsp;:

```java
    @Test
    public void FourShouldReturnIV() {
        RomanNumber roman = new RomanNumber();
        assertEquals("IV", roman.convert(4));
    }
```

Et le minimum de code pour que notre test passe&nbsp;:

```java
    String convert(int value) {
        if (value == 4)
            return "IV";

        StringBuilder answer = new StringBuilder();
        for (int i = 1; i <= value; i++)
            answer.append("I");

        return answer.toString();
    }
```

Mouais, ce n'est pas très joli. Posons nous encore une fois. D'un côté on a le cas de la valeur 4, d'un autre on a le cas des valeur inférieures à 4 (1 à 3). On peut extraire une méthode pour apporter un peu de lisibilité, non&nbsp;?

Cela donne, par exemple&nbsp;:

```java
    String convert(int value) {
        if (value == 4)
            return "IV";

        return convertOneToThree(value);
    }

    private String convertOneToThree(int value) {
        StringBuilder answer = new StringBuilder();
        for (int i = 1; i <= value; i++)
            answer.append("I");

        return answer.toString();
    }
```

Tous les tests sont au vert, passons au nombre 5. Je vous fais grâce du test (qu'il faut écrire et qui doit échouer avant toute chose, là il donnera *IIIII* au lieu de *V*), je vous propose le code suivant&nbsp;:

```java
    String convert(int value) {
        if (value == 5)
            return "V";

        if (value == 4)
            return "IV";

        return convertOneToThree(value);
    }
```

Bon, c'est moche, mais tout passe. Je ne vois pas trop quel refactoring on pourrait opérer… Passons au nombre 6 (si vous écrivez et exécutez le code il retournera *IIIIII* mais on attend *VI*). Le code proposé est assez simple, et on ne voit pas forcément comment l'améliorer à ce stade.

```java
    String convert(int value) {
        if (value == 6)
            return "VI";

        if (value == 5)
            return "V";

        if (value == 4)
            return "IV";

        return convertOneToThree(value);
    }
```

## 7, 8, 9 dans mon panier neuf

J'ajoute les tests pour les nombres 7 et 8 et le code correspondant, toujours en faisant un test sur la valeur et en retournant la valeur attendue… en dur. Le code finit par ressembler à cela&nbsp;:

```java
    String convert(int value) {
        if (value == 8) return "VIII";
        if (value == 7) return "VII";
        if (value == 6) return "VI";
        if (value == 5) return "V";

        if (value == 4)
            return "IV";
 
       return convertOneToThree(value);
    }
```

Et là, on se dit qu'on peut améliorer tout ça, non&nbsp;? 6, 7 et 8 c'est 5 (*V*) plus 1, 2 et 3 respectivement, non&nbsp;? Et on a déjà une méthode qui retourne *I*, *II* ou *III*&nbsp;! On peut tenter une modification du code. Si on casse quelque chose, les tests nous le diront tout de suite.

```java
    String convert(int value) {
        if (value > 5)
            return "V" + convertOneToThree(value - 5);

        if (value == 5)
            return "V";

        if (value == 4)
            return "IV";

        return convertOneToThree(value);
    }
```

C'est plus concis, tout aussi lisible… et cela continue de fonctionner. Mais, attendez… on dirait que `convertOneToThree()` retourne une chaine vide si on lui passe zéro. Du coup, on peut englober la valeur 5 (*V* plus *rien*), vous ne croyez pas&nbsp;? On essaye. Si on casse quelque chose, les tests nous le diront. C'est l'occasion de renommer la méthode `convertOneToThree()` en `convertNothingToThree()`. 

```java
    String convert(int value) {
        if (value >= 5)
            return "V" + convertNothingToThree(value - 5);

        if (value == 4)
            return "IV";

        return convertNothingToThree(value);
    }
```

On s'est pour l'instant arrêté à 8. Si on code le test pour 9, on va avoir la valeur *VIIII*. On va ajouter du code pour que le test passe. Allons au plus simple et voyons voir ce que cela donne&nbsp;:

```java
    String convert(int value) {
        if (value == 9)
            return "IX";

        if (value >= 5)
            return "V" + convertNothingToThree(value - 5);

        if (value == 4)
            return "IV";

        return convertNothingToThree(value);
    }
```

## 10, 11, 12 elles seront toutes rouges

On continue avec le nombre 10 ? En écrivant le test vous aurez la valeur *VIIIII* alors que c'est *X* qui est attendu.

Le code le plus simple pour que cela passe pour le nombre 10 pourrait être&nbsp;:

```java
    String convert(int value) {
        if (value == 10)
            return "X";

        if (value == 9)
            return "IX";

        if (value >= 5)
            return "V" + convertNothingToThree(value - 5);

        if (value == 4)
            return "IV";

        return convertNothingToThree(value);
    }
```

Après la ligne où on teste si la valeur est supérieur ou égale à 5, on traite les cas 5, 6, 7 et 8. Comme le code n'est plus très clair, on peut extraire une méthode qui explique ce que l'on fait. Par exemple&nbsp;:

```java
    String convert(int value) {
        if (value == 10)
            return "X";

        if (value == 9)
            return "IX";

        if (value >= 5)
            return convertFiveToEight(value);

        if (value == 4)
            return "IV";

        return convertNothingToThree(value);
    }

    private String convertFiveToEight(int value) {
        return "V" + convertNothingToThree(value - 5);
    }
```

On pourrait regrouper les deux premiers cas, mais cela n'apporterait pas grand chose. 9 c'est dix mois 1, tout comme 4 c'est 5 moins 1. Il y aurait peut-être des choses à améliorer de ce côté-là, mais pour l'instant rien de précis ne se dégage. Enfin… si. On a *IV*, *IX* qui sont des exceptions, tout comme, si on va plus loin dans la numérotation, *XL* pour 40 et *XC* pour 90 ou *CD* pour 400 et *CM* pour 900.

Continuons avec onze. Si on écrit le code de test il va retourner *VIIIIII*. On peut facilement le corriger, sans trop réfléchir de cette manière&nbsp;:

```java
    String convert(int value) {
        if (value == 11)
            return "XI";

        if (value == 10)
            return "X";

        if (value == 9)
            return "IX";

        if (value >= 5)
            return convertFiveToEight(value);

        if (value == 4)
            return "IV";

        return convertOneToThree(value);
    }
```

Et douze&nbsp;? Et treize&nbsp;? J'accélère légèrement, mais je m'oblige **toujours** à écrire le test et à le faire échouer avant d'ajouter le code pour faire passer le test. Ne prenez pas de raccourci, c'est dangereux&nbsp;!

```java
    String convert(int value) {
        if (value == 13) return "XIII";
        if (value == 12) return "XII";
        if (value == 11) return "XI";
        if (value == 10) return "X";

        if (value == 9)
            return "IX";

        if (value >= 5)
            return convertFiveToEight(value);

        if (value == 4)
            return "IV";

        return convertNothingToThree(value);
    }
```

Et là on peut prendre le temps de récrire les 4 première lignes comme ont l'a déjà fait. *X*, *XI*, *XII* et *XIII* c'est la concaténation de *X* et *rien*, *I*, *II* ou *III*.

```java
    String convert(int value) {
        if (value >= 10)
            return "X" + convertNothingToThree(value - 10);

        if (value == 9)
            return "IX";

        if (value >= 5)
            return convertFiveToEight(value);

        if (value == 4)
            return "IV";

        return convertNothingToThree(value);
    }
```

Ne pourrait-on pas encore améliorer le code&nbsp;? Convertir 10, 11, 12 ou 13 c'est comme aouter *rien*, *I*, *II* ou *III* à *X*. Mais *rien*, *I*, *II* ou *III* est retourné aussi bien par `convertNothingToThree()` que par `convert()`, non&nbsp;? Du coup ou peut facilement introduire une récursivité ici et coder ainsi&nbsp;:

```java
    String convert(int value) {
        if (value >= 10)
            return "X" + convert(value - 10);

        if (value == 9)
            return "IX";

        if (value >= 5)
            return convertFiveToEight(value);

        if (value == 4)
            return "IV";

        return convertNothingToThree(value);
    }
```

Ce faisant on se rend compte que le `convertFiveToEight()` peut aussi se résumer à *V* concaténé à *rien*, *I*, *II* ou *III*. Il est finalement plus simple de se séparer de cette méthode `convertFiveToEight()` introduite pour plus de lisibilité. On peut ainsi écrire&nbsp;:

```java
    String convert(int value) {
        if (value >= 10)
            return "X" + convert(value - 10);

        if (value == 9)
            return "IX";

        if (value >= 5)
            return "V" + convert(value - 5);

        if (value == 4)
            return "IV";

        return convertNothingToThree(value);
    }
```

Et là, je ne sais pas si c'est la magie du TDD, mais il émerge un design auquel on n'avait pas du tout pensé. On part d'une valeur (par exemple *XII*), on soustrait ce qu'on arrive à convertir (10, ou *X*) et on continue avec le reste (2, qui s'écrit *II*). Est-ce que ce n'est pas cela que l'on procède nous, humains, quand on doit faire la conversion ? Évidemment cela suppose que nous connaissions les jalons *I*, *V*, *X*, *L*, *C*, *D* et *M*, les règles d'additions et de répétitions (3 fois maximum) et les exceptions utilisant la soustraction comme *IV*, *IX*, *XL*, *XC*, *CD* et *CM*.
Et après&nbsp;?

Dans un prochain article on ira au delà de *XIII*, toujours avec la même méthode. Si vous voulez essayer par vous même, allez-y et faites moi part de votre retour d'expérience.

Je tiens à préciser que le code présenté ici a été écrit après avoir déjà codé ce programme de transformation une première fois. On peut considérer cela comme un [kata](https://www.linkedin.com/pulse/my-approach-gilded-rose-kata-part-1-alain-gaymard/). J'ai volontairement laissé l'introduction de la méthode `convertFiveToEigth()` dans ma démarche, car je voulais que le code soit plus clair. Il n'y a aucun problème à revenir dessus et à l'enlever, ni d'avoir honte de l'avoir ajouté à un instant *t*. Sincèrement, la solution qui émerge est différente de celle qui a déjà émergée. Et il n'y a aucun problème avec cela, car il n'y a pas qu'une seule manière de faire, et les deux passent l'ensemble des tests, et c'est bien ce qui compte, non&nbsp;?

## Épilogue

Je trouvais la méthode `convertNothingToThree()` un peu lourde :

```java
    private String convertNothingToThree(int value) {
        StringBuilder answer = new StringBuilder();
        for (int i = 1; i <= value; i++)
            answer.append("I");

        return answer.toString();
    }
```

On crée un `StringBuilder` (qui reste vide parfois), ensuite on entre dans un boucle pour compter le nombre de répétitions de *I* à générer. L'idée de la récursivité pour *X*, *XI*, *XII* et *XIII* appliquée au reste quand on a enlevé *X* ne s'appliquerait-elle pas aussi à *rien*, *I*, *II* et *III*&nbsp;? Mais si, bien sûr&nbsp;! On peut simplement écrire&nbsp:

```java
    private String convertNothingToThree(int value) {
        return (value == 0) ? 
            "" :
            "I" + convertNothingToThree(value - 1);
    }
```

Et comme je trouve qu'ici extraire une méthode pour une ligne n'apporte plus grand chose, on peut décider de tout regrouper ainsi dans `convert()`&nbsp;:

```java
    String convert(int value) {
        if (value >= 10)
            return "X" + convert(value - 10);

        if (value == 9)
            return "IX";

        if (value >= 5)
            return "V" + convert(value - 5);

        if (value == 4)
            return "IV";

        return (value == 0) ? "" : "I" + convert(value - 1);
    }
```

Évidemment les tests sont toujours verts.

Avec un peu de recul, ce qui est rassurant dans cette version, c'est qu'on ne mentionne que les jalons (*I*, *V*, *X*) et les exceptions (*IV* et *IX*). Et qu'une seule fois chacun&nbsp;! C'est rassurant dans la mesure où on se rend compte qu'on a que du code utile, rien de superflu. Et c'est effectivement **ce qu'apporte le TDD, une conception** (utiliser les jalons, les exceptions données par les spécifications, réusiner le code) qui a été **pilotée** par les **tests**.

Un énorme merci à [Benoit Gantaume](https://www.linkedin.com/in/benoitgantaume/) qui m'a conforté dans l'idée qu'[il fallait sortir du contenu et ne pas attendre que le contenu soit parfait](https://player.fm/series/artisan-developpeur/comment-ne-pas-creer-de-contenu-avec-romain-fallet) avant de le publier (sous peine de ne jamais le publier)&nbsp;!

## Annexe

Voici l'ensemble des tests qui ont été écrits jusqu'à présent&nbsp;:
```java
package com.mycompany;

import static org.junit.Assert.assertEquals;
import org.junit.Test;

public class RomanNumbersTest {
    @Test
    public void OneShouldReturnI() {
        RomanNumber roman = new RomanNumber();
        assertEquals("I", roman.convert(1));
    }

    @Test
    public void TwoShouldReturnII() {
        RomanNumber roman = new RomanNumber();
        assertEquals("II", roman.convert(2));
    }

    @Test
    public void ThreeShouldReturnIII() {
        RomanNumber roman = new RomanNumber();
        assertEquals("III", roman.convert(3));
    }

    @Test
    public void FourShouldReturnIV() {
        RomanNumber roman = new RomanNumber();
        assertEquals("IV", roman.convert(4));
    }

    @Test
    public void FiveShouldReturnV() {
        RomanNumber roman = new RomanNumber();
        assertEquals("V", roman.convert(5));
    }

    @Test
    public void SixShouldReturnVI() {
        RomanNumber roman = new RomanNumber();
        assertEquals("VI", roman.convert(6));
    }

    @Test
    public void SevenShouldReturnVII() {
        RomanNumber roman = new RomanNumber();
        assertEquals("VII", roman.convert(7));
    }

    @Test
    public void EightShouldReturnVIII() {
        RomanNumber roman = new RomanNumber();
        assertEquals("VIII", roman.convert(8));
    }

    @Test
    public void NineShouldReturnIX() {
        RomanNumber roman = new RomanNumber();
        assertEquals("IX", roman.convert(9));
    }

    @Test
    public void TenShouldReturnX() {
        RomanNumber roman = new RomanNumber();
        assertEquals("X", roman.convert(10));
    }

    @Test
    public void ElevenShouldReturnXI() {
        RomanNumber roman = new RomanNumber();
        assertEquals("XI", roman.convert(11));
    }

    @Test
    public void TwelveShouldReturnXII() {
        RomanNumber roman = new RomanNumber();
        assertEquals("XII", roman.convert(12));
    }

    @Test
    public void ThirteenShouldReturnXIII() {
        RomanNumber roman = new RomanNumber();
        assertEquals("XIII", roman.convert(13));
    }

}
```
