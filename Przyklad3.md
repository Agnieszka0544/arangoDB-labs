## Łączenie dokumentów

W naszej kolekcji `Characters` dokumenty posiadają pole `traits`, które zawiera tablicę symbolicznych oznaczeń cech postaci. Stworzymy kolekcję `Traits`, mapującą symboliczne oznaczenia na nazwy konkretnych cech postaci. Będziemy chcieli połączyć dokumenty z `Characters` z cechami z `Traits` tak, aby w dokumencie znajdowały się faktycznie cechy, a nie tylko ich symbole.

Na potrzeby stworzenia kolekcji, skorzystamy z RESTful API ArangoDB za pośrednictwem interfejsu webowego. Będziemy pracować na wcześniej utworzonej bazie danych `got`.

1. Przechodzimy do zakładki `Support` z lewego panelu.
2. Na górnym pasku wybieramy zakładkę `REST API`.
3. Znajdujemy sekcję `Collections` i w niej operację `POST`, którą rozwijamy.
4. Naciskamy `Try it out`.
5. Wpisujemy `got` w polu `database-name`.
6. Jako `request body` podajemy:

   ```json
   {
     "name": "Traits"
   }
   ```

7. Klikamy `Execute` i sprawdzamy, czy kolekcja została stworzona. Możemy skorzystać z wyświetlonej poniżej `Execute` odpowiedzi HTTP lub otworzyć listę kolekcji w zakładce `Collections` w lewym panelu.

Teraz uzupełnimy kolekcję `Traits` danymi z pliku [Traits.json](./Dane-do-przykladow/Traits.json).

8. Pozostając w zakładce `REST API`, przejdźmy do sekcji `Documents`. Znajdźmy drugą od góry operację `POST` (z dopiskiem `Create multiple documents`) i rozwińmy ją.
9. Kliknijmy `Try it out` i podajmy dane: `database-name` - `got` i `collection` - `Traits`.
10. W polu `request body` podajmy JSONa z pliku [Traits.json](./Dane-do-przykladow/Traits.json).
11. Klikamy `Execute` i sprawdzamy, czy kolekcja została uzupełniona.
12. Teraz, używając zakładki `Queries`, możemy zmapować każdą postać na dokument reprezentujący jej cechy.
    ```sql
    FOR c IN Characters
      RETURN DOCUMENT("Traits", c.traits)
    ```
13. Otrzymaliśmy mapowanie postaci do dokumentów reprezentujących ich cechy. Możemy ograniczyć to tylko do interesujących nas pól (nazw cech postaci w języku angielskim):
    ```sql
    FOR c IN Characters
      RETURN DOCUMENT("Traits", c.traits)[*].en
    ```
    Użyty tutaj syntax `[*]` to tzw. `array expansion`, który mapuje każdy element tablicy na pojedynczą wartość atrybutu.
14. Teraz połączmy otrzymane przed chwilą tablice cech z naszymi postaciami, używając `MERGE`:
    ```sql
    FOR c IN Characters
      RETURN MERGE(c, { traits: DOCUMENT("Traits", c.traits)[*].en } )
    ```
    Otrzymaliśmy dokumenty, w którym pole `traits` zostało nadpisane elementami z kolekcji `Traits`.
15. Optymalniejszym rozwiązaniem jest użycie `FOR` i `FILTER` zamiast polecenia `DOCUMENT`:
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
    Według twórców, takie zapytanie jest dużo lepiej zoptymalizowane przez silnik bazy danych. Ponadto tym sposobem możemy zrobić dopasowania nie tylko po kluczu.
