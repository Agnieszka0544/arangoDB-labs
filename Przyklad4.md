## Grafy

W ArangoDB możemy tworzyć specjalnie kolekcje krawędziowe, które wskazują na relacje między dokumentami. Stwórzmy kolekcję `ChildOf`, która będzie opisywała relacje rodzic-dziecko między postaciami.

1. Z poziomu interfejsu webowego stwórzmy kolekcję `ChildOf`, pamiętając, aby wybrać typ kolekcji `Edge`.
2. Uzupełnijmy kolekcję danymi z pliku [ChildOf.json](./Dane-do-przykladow/ChildOf.json):
   ```sql
   FOR rel in @relations
      INSERT {
         _from: CONCAT("Characters/", rel.child),
         _to: CONCAT("Characters/", rel.parent)
      } INTO ChildOf
      RETURN NEW
   ```
   Widzimy w tym zapytaniu szczególny rodzaj pola w dokumentach krawdędziowych - `_from` i `_to`.
   Po wykonaniu zapytania, poniżej przycisku `Execute`, pojawią się rysunki otrzymanych grafów.
3. Możemy też wyświetlić nasz graf zapytaniem:
   ```sql
   FOR edge IN ChildOf
      RETURN edge
   ```
4. Wywołajmy zapytanie grafowe - otrzymamy rodziców podanego wierzchołka:
   ```sql
   FOR v IN 1..1 OUTBOUND "Characters/bran" ChildOf
    RETURN v.name
   ```
5. Sprawdźmy przeglądanie grafu w drugą stronę - otrzymamy dzieci podanego wierzchołka:
   ```sql
   FOR c IN Characters
    FILTER c.name == "Ned"
    FOR v IN 1..1 INBOUND c ChildOf
      RETURN v.name
   ```
6. Zobaczmy jakich wnuków posiada Tywin Lannister:
   ```sql
   FOR c IN Characters
    FILTER c.name == "Tywin"
    FOR v IN 2..2 INBOUND c ChildOf
      RETURN v.name
   ```
   Widzimy, że `Joffrey` wypisał się dwa razy - to dlatego, że od wierzchołka `Tywin` do wierzchołka `Joffrey` można dojść dwiema ścieżkami. Moglibyśmy to naprawić używając `RETURN DISTINCT v.name`, lepszym podejściem jest jednak użycie opcji przeglądania grafu, np. algorytmem BFS:
   ```sql
   FOR c IN Characters
   FILTER c.name == "Tywin"
   FOR v IN 2..2 INBOUND c ChildOf OPTIONS { uniqueVertices: "global", order: "bfs" }
   RETURN v.name
   ```
