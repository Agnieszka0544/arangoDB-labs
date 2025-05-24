### Graf cech

1. Tworzymy kolekcję krawędziową `Is` poprzez interfejs webowy.
2. Uzupełniamy ją następującym zapytaniem:
   ```sql
   FOR c IN Characters
    FOR t IN Traits
      FILTER t._key IN c.traits
      INSERT {
        _from: c._id,
        _to: t._id,
      } INTO Is
   ```
3. Do kolekcji `Characters` oraz `Traits` dodajemy pole `label`:

   ```sql
   FOR c IN Characters
     UPDATE c WITH { label: c.name } IN Characters

   FOR t IN Traits
     UPDATE t WITH { label: t.name } IN Traits
   ```

4. Tworzymy nazwany graf `CharactersAndTraits` poprzez interfejs webowy, jako wierzchołki wybieramy kolekcje `Characters` oraz `Traits`, a jako krawędź `Is`.

5. Wyświetlamy graf i otwieramy zakładkę `Settings`.
6. W sekcji `Graph` ustawiamy `depth` na odpowiednio dużą liczbę (np. `512`), aby pokryć cały graf.
7. W sekcji `Node` ustawiamy `Node label` na `label`, aby wyświetlać etykiety wierzchołków.
8. Podziwiamy nasz piękny graf 🥳
