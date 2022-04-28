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

14 est supérieur à 10, donc on garde *X* et on concatène le résultat de la conversion de 4 (14&nbsp;-&nbsp;10). 4 n'étant ni supérieur à 10, ni égal à neuf, ni supérieur ou égal à cinq, on retournera donc *IV*. Soit *IV* concaténé à *X*, ce qui est la bonne valeur pour 14.

Le fait qu'un test passe alors qu'on n'a pas ajouté de code doit nous faire prendre conscience que le test n'apporte rien. Mais alors pourquoi l'avoir écrit&nbsp;? Parce qu'on était un peu perdu. Mais cela en apprend aussi beaucoup sur notre compréhension du problème. Cela veut dire que le code en place n'est pas si mal et est valable pour 15, 16, 17, 18 et 19 (je vous laisse vérifier). Et pour 20&nbsp;? Aussi&nbsp;!

Ce qui fonctionne pour 20 fonctionne jusqu'à 39. Concrètement, on part d'un nombre (prenons 27) et on lui soustrait "le maximum" possible parmi 10, 9, 5, 4 ou 1 et on continue la même logique sur le nombre restant. Donc de 27 on peut soustraire *X*, il reste 17. De 17 on peut soustraire *X*, il reste 7. De 7 on on peut soustraire *V*, il reste 2. De 2 on peut soustraire *I* il reste 1. De ce 1 on peut soustraire *I* et il reste zéro. Ce qui nous donne la succession *X*, *X*, *V*, *I* puis *I*, soit *XXVII* (27).

Tant qu'on a pas atteint zéro on soustrait le "maximum" possible (valeur que l'on garde pour constituer la réponse), et on continue d'appliquer la même méthode sur la valeur restante. Cela est déjà rendu possible par la récursivité, mais on pourrait tout à fait l'écrire avec un `while` (tant que). À ce stade c'est encore assez facile à changer et de toute manière les tests vont nous rassurer que nous ne cassons rien, qu'il n'y a pas de régression. D'ailleurs, avant de commencer, on a une alternance de `==` et `>=` que j'aimerais harmoniser. On peut mettre `>=` partout. En effet, on essaye d'abord de soustraire 10, puis 9, puis 5. S'il y a égalité, il n'y a plus rien à soustraire. S'il nous reste 4, on ne peut que retourner *IV*. Essayons ceci&nbsp;:

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

Les tests sont toujours verts. On procède par petites touches pour avancer, les tests nous rassurent. Dans le même but d'harmonisation, et pour basculer vers un `while`, ne pourrait-on pas mettre `convert(value - laValeurTraitée)` partout&nbsp;?

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
            return "IV" + convert(value - 4);

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

Là, le schéma est tellement répétitif qu'on va pourvoir passer au `while` assez facilement. C'est un *gros* changement, mais on a notre filet de sécurité&nbsp;: les tests.

```java
    String convert(int value) {
        StringBuilder result = new StringBuilder();
        while (value != 0) {
            if (value >= 10) {
                result.append("X");
                value -= 10;
            }
            if (value >= 9) {
                result.append("IX");
                value -= 9;
            }
            if (value >= 5)
                result.append("V");
                value -= 5;
            }
            if (value >= 4)
                result.append("IV");
                value -= 4;
            }
            if (value >= 1) {
                result.append("I");
                value -= 1;
            }
        }
        return result.toString();
    }
```

Et là, catastrophe&nbsp;! Des tests comme 20, 21, 30 ou 39 qui passaient ne passent plus. Soit on arrive à corriger assez vite, soit on revient en arrière. Normalement un test vous présente la valeur attendue et la valeur retournée. Par exemple pour 30 on attend *XXX* et on a *XIXVIVII* (soit, je devinne, 10 + 9 + 5 + 4 + 1 + 1) qui ne respecte pas les règles de la notation que nous connaissons.

Le problème vient du fait qu'avec ce `while` on enchaine les différentes étapes et on n'essaye pas de soustraire le maximum possible. Si on reprend notre exemple, de 30 on peut soustraire 10 (*X*) et ensuite de 20 on peut encore soustraire 10 (*X*). Sauf qu'on soustrait 9 (*IX*) parcequ'**on ne reprend pas la boucle au début**, là est l'erreur. Dans chaque bloc `if` il faut veiller à arrêter la boucle et reprendre au début. On peut simplement ajouter un `continue`. Ce qui donne&nbsp;:

```java
    String convert(int value) {
        StringBuilder result = new StringBuilder();
        while (value != 0) {
            if (value >= 10) {
                result.append("X");
                value -= 10;
                continue;
            }
            if (value >= 9) {
                result.append("IX");
                value -= 9;
                continue;
            }
            if (value >= 5) {
                result.append("V");
                value -= 5;
                continue;
            }
            if (value >= 4) {
                result.append("IV");
                value -= 4;
                continue;
            }
            if (value >= 1) {
                result.append("I");
                value--;
            }
        }
        return result.toString();
    }
```

Et voilà, tous les tests sont à nouveau au vert. Points positifs&nbsp;: on a supprimé la récursivité, on a un `StringBuilder` plus adapté à la concaténation de chaines. Point négatif&nbsp;: le code est plus long et toujours très répétitif (et accessoirement on est toujours plafonné à 39).

Il est difficile de choisir la prochaine étape. Est-ce qu'on simplifie le code où on essaye d'aller plus loin dans les tests&nbsp;? Il n'y a pas de règle. Je décide de voir ce que va donner le code avec 40 (*XL*). On peut dores et déjà dire que le test va échouer, à aucun moment on ne propose la lettre *L* en sortie.

Le test échoue donc avec *XXXX* au lieu de *XL*. Comment corriger cela&nbsp;? On peut ajouter le moins de code possible&nbsp;:

```java
    if (value == 40) {
        result.append("XL);
        value = 0;
    }
```

Et cela passerait. Mais 41 ne passerait pas. Au vu de notre expérience, on va appliquer la même règle. On soustrait le "maximum" possible et on continue. Ici 40 (*XL*) se comporte un peut comme 4 (*IV*) que nous avons déjà traité.

Essayons cela&nbsp;:

```java
    String convert(int value) {
        StringBuilder result = new StringBuilder();
        while (value != 0) {
            if (value >= 40) {
                result.append("XL");
                value -= 40;
                continue;
            }
            if (value >= 10) {
                result.append("X");
                value -= 10;
                continue;
            }
            if (value >= 9) {
                result.append("IX");
                value -= 9;
                continue;
            }
            if (value >= 5) {
                result.append("V");
                value -= 5;
                continue;
            }
            if (value >= 4) {
                result.append("IV");
                value -= 4;
                continue;
            }
            if (value >= 1) {
                result.append("I");
                value--;
            }
        }
        return result.toString();
    }
```

Et le test passe au vert. Si on prend la peine de vérifier, on arrive même jusqu'à 49 sans encombre. Maintenant le schéma est **limpide**. Il est répétitif et on peut sans trop de risque ajouter *L* (50), *XC* (90), *C* (100), *CD* (400), *D* (500), *CM* (900) et *M* (1000). L'ordre a son importance, il faut essayer en premier lieu de soustraire le maximum possibe comme cela est déjà en place.

Mais avant de faire cela et d'ajouter des lignes de code (un peu trop nombreuses à mon gout), ne pourrait-on pas améliorer le code existant&nbsp;?

Décorticons un bloc&nbsp;:

```java
            if (value >= 40) {
                result.append("XL");
                value -= 40;
                continue;
            }
```

Dans ce bloc on a 40, sa valeur associée *XL* et une soustraction de 40 sur la valeur traitée. C'est exactement pareil pour chaque bloc (sauf le dernier, le *I* où le `continue` a été omis, mais cela ne poserait pas de problème de l'avoir, vous pouvez tester).

On a donc une liste de valeur 40, 10, 9, 5, 4, 1 et les chiffres romains associés *XL*, *X*, *IX*, *V*, *IV* et *I*. Ne pourrait-t-on pas les **stocker dans une structure** et **boucler sur ces différentes valeurs** pour éviter de répéter les blocs `if`&nbsp;? C'est certes encore un gros changement, mais les tests sont là pour nous aider en cas d'erreur.

Tout d'abord on ajoute cette structure de donnée de manière très simple sous forme de deux tableaux&nbsp;:

```java
package com.mycompany;

public class RomanNumber {

    int[] arabicValues = {40, 10, 9, 5, 4, 1};
    String[] romanValues = {"XL", "X", "IX", "V", "IV", "I"};

    public RomanNumber() {
    }

    String convert(int value) {
        [...]
    }
}
```

Ensuite, au lieu d'avoir 6 `if`, on va boucler sur les 6 valeurs du tableau. Si on traite le nombre 42 et qu'on a une correspondance (40 est supérieur ou égal à 42), on va garder *XL*, décrémenter de 40 et poursuivre le traitement avec 2.

Ce qui donne (si `i` est notre index qui parcourt `arabicValues`), pour chaque `if`&nbsp;:

```java
                if (value >= arabicValues[i]) {
                    result.append(romanValues[i]);
                    value -= arabicValues[i];
                    continue;
                }
```

Le `continue` doit être transformé en `break` car on sera dans une boucle `for`. La boucle `for` est elle même dans la boucle `while` qu'on conserve. Ce qui donne au final&nbsp;:

```java
    String convert(int value) {
        StringBuilder result = new StringBuilder();
        while (value != 0)
            for(int i = 0; i < arabicValues.length; i++)
                if (value >= arabicValues[i]) {
                    result.append(romanValues[i]);
                    value -= arabicValues[i];
                    break;
                }
        return result.toString();
    }
```

Tous les tests restent verts, il n'y a pas eu de régression.

Le principe des nombres "jalons" dans les tableaux peut enuite être étendu pour aller jusqu'à *M* (1000). Doit-t-on ajouter du code avant d'étendre la liste des jalons&nbsp;? En effet, on a répété que pour chaque progression on ajoutait un test avant d'ajouter du code. Mais croyez-vous que changer la liste des nombres est un modification de code&nbsp;? Oui et non. On touche au code, mais ces tableaux ne sont que du paramétrage. Donc il n'y a pas de bonne raison pour ajouter des tests. Le design a émergé, il fonctionne, les tests nous l'ont montré. Évidemment pour se rassurer on peut ajouter des tests pour des valeurs clés (celle de la table `arabicValues`), ou des valeur à la marge (49, 51, 99, 101, 399, 401, 499, 501, 899, 901, 1001).

Au final on a obtenu du code concis, on est passé par plein d'étapes (introduction et abandon de la récursion, boucle pour remplacer les `if`, erreur), et on a à la fois répondu aux spécifications et fait évoluer le code de manière plutôt "élegante", vers une solution qui ne nous serait peut-être jamais venue autrement, et surtout vers une solution qui n'a probablement pas de bugs&nbsp;!

Le code final&nbsp;:

```java
package com.mycompany;

public class RomanNumber {

    int[] arabicValues = {1000, 900, 500, 400, 100, 90, 50, 40, 10, 9, 5, 4, 1};
    String[] romanValues = {"M", "CM", "D", "CD", "C", "XC", "L", "XL", "X", "IX", "V", "IV", "I"};

    public RomanNumber() {
    }

    String convert(int value) {
        StringBuilder result = new StringBuilder();
        while (value != 0)
            for(int i = 0; i < arabicValues.length; i++)
                if (value >= arabicValues[i]) {
                    result.append(romanValues[i]);
                    value -= arabicValues[i];
                    break;
                }
        return result.toString();
    }
}
```
