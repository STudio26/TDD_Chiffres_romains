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
14 est supérieur à 10, donc on garde *X* et on concatène le résultat de la conversion de 4 (14&nbsp;-&nbsp;10). 4 n'étant ni supérieur à 10, ni égal à neuf, ni supérieur ou égal à cing, on retournera donc *IV*. Soit *X* concaténé à *IV*, ce qui est exact.
Le fait qu'un test passe alors qu'on n'a pas ajouté de code doit nous faire prendre conscience que le test n'apporte rien. Mais alors pourquoi l'avoir écrit&nbsp;? Parce qu'on était un peu perdu. Mais cela en dit aussi beaucoup sur notre compréhension du problème. Cela veut dire que le code en place n'est pas si mal et est valable pour 15, 16, 17, 18 et 19 (je vous laisse vérifier). Et pour 20&nbsp;? Aussi&nbsp;!