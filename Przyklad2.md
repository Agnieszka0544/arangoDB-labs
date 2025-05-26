## Korzystanie z arangosh

1. W terminalu w katalogu `arangoDB-labs` uruchamiamy `arangosh`:

   ```bash
   docker compose exec arangodb arangosh --server.username user --server.database got
   ```

2. Wpisujemy hasło (`user`) i naciskamy `Enter`.

3. `arangosh` to tak naprawdę maszyna JavaScriptu, która posiada odpowiednie dowiązania w globalnym kontekście. Możemy podejrzeć co dokładnie tam się znajduje pisząc:

   ```js
   Object.keys(globalThis);
   ```

4. Wywołując funkcję `help()` dostaniemy informacje o dostępnych poleceniach.

5. Odtwórzmy dokumenty z naszej kolekcji `Characters` z pliku `Characters.json`:

   ```js
   const charactersJSON = require("/data/data/Characters.json");
   for (const character of charactersJSON) {
     db.Characters.insert(character);
   }
   ```

6. Wyświetlmy zawartość naszej odtworzonej kolekcji:

   ```js
   db.Characters.toArray();
   ```

7. Możemy także wykonywać zapytania za pomocą stringów AQLowych:

   ```js
   db._query('FOR c IN Characters FILTER c.name == "Ned" RETURN c');
   ```
