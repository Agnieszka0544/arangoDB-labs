## Grafy

W ArangoDB możemy tworzyć specjalnie kolekcje krawędziowe, które wskazują na relacje między dokumentami. Stwórzmy kolekcję `ChildOf`, która będzie opisywała relacje rodzic-dziecko między postaciami.

1. Z poziomu interfejsu webowego stwórzmy kolekcję `ChildOf`, pamiętając, aby wybrać typ kolekcji `Edge`.
2. Uzupełnijmy ją danymi z pliku [ChildOf.json](./ChildOf.json):
   ```sql
   FOR rel in @relations
      INSERT {
         _from: CONCAT("Characters/", rel.child),
         _to: CONCAT("Characters/", rel.parent)
      } INTO ChildOf
      RETURN NEW
   ```
   Widzimy w tym zapytaniu szczególny rodzaj pola w dokumentach krawdędziowych - `_from` i `_to`.
3. Wywołajmy zapytanie grafowe:
   ```sql
   FOR v IN 1..1 OUTBOUND "Characters/bran" ChildOf
    RETURN v.name
   ```
4. Możemy też wyświetlić nasz graf graficznie:
   ```sql
   FOR edge IN ChildOf
     RETURN edge
   ```
5. Sprawdźmy przeglądanie grafu w drugą stronę:
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
   Widzimy, że `Joffrey` wypisał się dwa razy - to dlatego, że od wierzchołka `Tywin` można do niego dojść dwiema ścieżkami. Moglibyśmy to naprawić używając `RETURN DISTINCT v.name`, lepszym podejściem może być jednak użycie opcji przeglądania grafu, np. BFS:
   ```sql
   FOR c IN Characters
   FILTER c.name == "Tywin"
   FOR v IN 2..2 INBOUND c ChildOf OPTIONS { uniqueVertices: "global", order: "bfs" }
   RETURN v.name
   ```
