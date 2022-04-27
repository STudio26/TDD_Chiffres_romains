# TDD, un nouvel exemple (deuxième partie)

Lors de la première partie nous étions arrivé à ce code pour convertir le nombre en chiffres romains de 1 à 13. Uniquement avec une démarche basée sur des tests, des ajouts successifs, des re-écritures.

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
 
On a procédé étape par étape en utilisant les 13 premiers nombres comme spécification. Ce n'est pas "faux" de dire cela, mais pour aller jusqu'à 3999 (oui, on ne peut compter que jusqu'à 3999 avec ce système), on ne va pas écrire 4000 tests tout de même&nbsp;!
Rappelons que les règles d'écriture sont basées sur la répétition de trois fois maximum du *I*, du *X*, du *C* et du *M* et la soustraction pour *IV* (5&nbsp;-&nbsp;1), *IX* (10&nbsp;-&nbsp;1), *XL* (50&nbsp;-&nbsp;10), *XC* (100&nbsp;-&nbsp;10), *CD* (500&nbsp;-&nbsp;100) et *CM* (1000&nbsp;-&nbsp;100). Ces règes font aussi parti des spécifications.
Formulé autrement, tant qu'on le peut, on répète le nombre (*I*, *X*, *C*, *M*). On peut ainsi très vite écrire 3 (*III*), 20 (*XX*), 100 (*C*) ou trois mille (*MMM*). Je cherche pour l'instant à passer à l'étape suivante. Mais j'ai tout de même envie d'essayer avec le nombre 14.

Je code le test&nbsp;:

```java
    @Test
    public void FourteenShouldReturnXIV() {
        RomanNumber roman = new RomanNumber();
        assertEquals("XIV", roman.convert(14));
    }
```
Et là, *miracle*, le test passe&nbsp;! Par quelle magie&nbsp;?

14 est supérieur à 10, donc on garde *X* et on concatène le résultat de la conversion de 4 (14&nbsp;-&nbsp;10). 4 n'étant ni supérieur à 10, ni égal à neuf, ni supérieur ou égal à cinq, on retournera donc *IV*. Soit *X* concaténé à *IV*, ce qui est exact.
Le fait qu'un test passe alors qu'on n'a pas ajouté de code doit nous faire prendre conscience que le test n'apporte rien. Mais alors pourquoi l'avoir écrit&nbsp;? Parce qu'on était un peu perdu. Mais cela en dit aussi beaucoup sur notre compréhension du problème. Cela veut dire que le code en place n'est pas si mal et est valable pour 15, 16, 17, 18 et 19 (je vous laisse vérifier). Et pour 20&nbsp;? Aussi&nbsp;!

Ce qui fonctionne pour 20 fonctionne jusqu'à 39. Concrètement, on part d'un nombre (prenons 27) et on lui soustrait "le maximum" possible parmi 10, 9, 5, 4 ou 1 et on continue la même logique sur le nombre restant. Donc de 27 on peut soustraire *X*, il reste 17. De 17 on peut soustraire *X*, il reste 7. De 7 on on peut soustraire *V*, il reste 2. De 2 on peut soustraire *I* il reste 1. De ce 1 on peut soustraire *I* et il reste zéro. Ce qui nous donne la successions *X*, *X*, *V*, *I* pui *I*, soit *XXVII* (27).

Tant qu'on a pas atteind zéro on soustrait le "maximum" possible (valeur que l'on garde pour constituer la réponse), et on continue d'appliquer la même méthode sur la valeur restante. Cela est déjà rendu possible par la récursivité, mais on pourrait tout à fait l'écrire avec un `while`. À ce stade c'est encore assez facile à changer et de toute manière les tests vont nous rassurer que nous ne cassons rien, qu'il n'y a pas de régression. D'ailleurs, avant de commencer, on a une alternance de `==` et `>=` que j'aimerais harmoniser. On peut mettre `>=` partout. Essayons ceci&nbsp;:

```java
    String convert(int value) {
        if (value >= 10)
            return "X" + convert(value - 10);

        if (value >= 9)
            return "IX";

        if (value >= 5)
            return "V" + convert(value - 5);

        if (value >= 4)
            return "IV";

        return (value == 0) ? "" : "I" + convert(value - 1);
    }
```

Les tests sont toujours verts. On procède par petites touches pour avancer, les tests nous rassurent. Dans le même but d'harmonisation, et pour basculer vers un `while`, ne pourrait-on pas mettre `convert(value - *quelque chose*)` partout&nbsp;?

Tentons cela&nbsp;:

```java
    String convert(int value) {
        if (value >= 10)
            return "X" + convert(value - 10);

        if (value >= 9)
            return "IX" + convert(value - 9);

        if (value >= 5)
            return "V" + convert(value - 5);

        if (value >= 4)
            return "IV" convert(value - 4);

        return (value == 0) ? "" : "I" + convert(value - 1);
    }
```

Et pour harmoniser la dernière ligne on peut inverser le test&nbsp;:

```java
    String convert(int value) {
        if (value >= 10)
            return "X" + convert(value - 10);

        if (value >= 9)
            return "IX" + convert(value - 9);

        if (value >= 5)
            return "V" + convert(value - 5);

        if (value >= 4)
            return "IV" + convert(value - 4);

        return (value >= 1)
            return "I" + convert(value - 1);
            
        return "";
    }
```

Là je pense qu'on va pou
