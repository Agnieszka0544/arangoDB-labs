## Łączenie dokumentów

W naszej kolekcji `Characters` dokumenty posiadają pole `traits`, które zawiera tablicę symbolicznych oznaczeń cech postaci. Będziemy chcieli połączyć dokumenty z `Characters` tak, aby w dokumencie znajdowały się faktycznie cechy, a nie tylko ich symbole.

Na potrzeby stworzenia kolekcji, skorzystamy z RESTful API ArangoDB. Skorzystamy z interfejsu webowego.

1. Przejdźmy do zakładki `Support` w lewym panelu.
2. Na górnym pasku wybieramy zakładkę `REST API`.
3. Znajdujemy sekcję `Collections` i w niej operację `POST`.
4. Rozwijamy ją i naciskamy `Try it out`.
5. Wpisujemy `Traits` do pola `database-name`.
6. Jako `request body` wpisujemy:

   ```json
   {
     "name": "Traits"
   }
   ```

7. Klikamy `Execute` i sprawdzamy, czy kolekcja została stworzona. Możemy to zrobić w zakładce `Collections` w lewym panelu.

8. Teraz uzupełnijmy kolekcję `Traits` danymi z pliku [Traits.json](./Traits.json).
9. Przejdźmy do sekcji `Document` i znajdźmy drugą operację `POST`.
10. Uzupełnijmy ją danymi `database-name` `got` i `collection-name` `Traits`.
11. W polu `request body` podajmy JSONa z pliku.
12. Klikamy `Execute` i sprawdzamy, czy kolekcja została uzupełniona.
13. Teraz już używając zakładki `Queries` możemy zrobić pseudo-joina:
    ```sql
    FOR c IN Characters
      RETURN DOCUMENT("Traits", c.traits)
    ```
14. Otrzymaliśmy mapowanie postaci do dokumentów reprezentujących ich cechy. Możemy ograniczyć to tylko do interesujących nas pól:
    ```sql
    FOR c IN Characters
      RETURN DOCUMENT("Traits", c.traits)[*].en
    ```
    Użyty tutaj syntax `[*]` to tzw. `array expansion`, który mapuje każdy element tablicy tylko do pojedynczego elementu.
15. Teraz połączmy to z naszymi postaciami używając `MERGE`:
    ```sql
    FOR c IN Characters
      RETURN MERGE(c, { traits: DOCUMENT("Traits", c.traits)[*].en } )
    ```
    Efektywnie otrzymaliśmy dokumenty, w którym pole `traits` zostało nadpisane na elementy z kolekcji `Traits`.
16. Optymalniejszym rozwiązaniem jest nie używanie polecenia `DOCUMENT`, a użycie `FOR` i `FILTER`:
    ```sql
    FOR c IN Characters
      RETURN MERGE(c, {
        traits: (
          FOR key IN c.traits
            FOR t IN Traits
              FILTER t._key == key
              RETURN t.en
        )
      })
    ```
    Według twórców, takie zapytanie potrafi być dużo lepiej zoptymalizowane przez silnik bazy danych. Ponadto też możemy zrobić dopasowania nie tylko po kluczu.
