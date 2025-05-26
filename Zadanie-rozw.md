### Graf cech

1. Tworzymy kolekcję krawędziową `Is` poprzez interfejs webowy.
2. Uzupełniamy ją korzystając z następującego zapytania:
   ```sql
   FOR c IN Characters
    FOR t IN Traits
      FILTER t._key IN c.traits
      INSERT {
        _from: c._id,
        _to: t._id,
      } INTO Is
   ```
3. Do dokumentów z kolekcji `Characters` oraz `Traits` dodajemy pole `label`:

   ```sql
   FOR c IN Characters
     UPDATE c WITH { label: c.name } IN Characters

   FOR t IN Traits
     UPDATE t WITH { label: t.en } IN Traits
   ```

4. Poprzez interfejs webowy tworzymy graf nazwany `CharactersAndTraits`. Wierzchołkami będą dokumenty z kolekcji `Characters` oraz `Traits`, a krawędziami dokumenty z kolekcji `Is`.

5. Wyświetlamy graf (klikając w jego nazwę wyświetlaną na liście w zakładce `Graphs`) i klikamy przycisk `Settings`.
6. W sekcji `Graph` ustawiamy `depth` na odpowiednio dużą liczbę (np. `512`), aby pokryć cały graf.
7. W sekcji `Node` ustawiamy `Node label` na `label`, aby wyświetlać etykiety wierzchołków.
8. Podziwiamy nasz piękny graf 🥳
