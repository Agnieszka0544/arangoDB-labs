## Zadanie 1

W tym zadaniu zajmiemy się podstawowymi funkcjonalnościami ArangoDB, czyli tworzeniem użytkowników, baz danych, kolekcji oraz wstawianiem, pobieraniem, edytowaniem i usuwaniem dokumentów.

### Tworzenie nowego użytkownika

Po wejściu do interfejsu webowego ArangoDB (http://localhost:8529) i zalogowaniu się jako użytkownik root, można utworzyć nowego użytkownika.

1. Przy logowaniu wybieramy (póki co) jedyną dostępną bazę danych, czyli `_system`.
2. Następnie klikamy na ikonę "Users" w lewym panelu.
3. Stwórzmy użytkownika `user` z hasłem `user`, żeby później nie musieć korzystać z konta root.

### Tworzenie nowej bazy danych

Pozostająć w bazie `_system`, możemy stworzyć nową bazę danych, która będzie wykorzystywana w dalszej części ćwiczeń.

1. Klikamy na ikonę `Databases` w lewym panelu.
2. Klikamy przycisk `Add database`.
3. Na potrzeby zadania, nazwijmy ją `got` i stwórzmy ją.
4. Poniżej ustawmy uprawnienia administratora dla użytkownika `user`, którego stworzyliśmy wcześniej.

Zalogujmy się teraz jako użytkownik `user` do bazy `got`, odpowiednie przyciski znajdują się w prawym górnym rogu.

### Tworzenie kolekcji

Z przyczyn bliżej mi nieznanych, nie można stworzyć kolekcji z poziomu AQL. Zamiast tego, możemy to zrobić z zakładki `Collections` w lewym panelu.

1. Klikamy na ikonę `Collections` w lewym panelu.
2. Klikamy przycisk `Add collection`.
3. Wybieramy typ kolekcji `Document` i nadajemy jej nazwę `Characters` i tworzymy ją.

### Wstawianie danych do kolekcji

Dane do kolekcji możemy już dodać z poziomu AQL.

1. Klikamy na ikonę `Queries` w lewym panelu.
2. Zacznijmy najpierw od testowego zapytania, który doda prosty dokument do kolekcji `Characters`:

   ```sql
   INSERT {
     _key: "test",
     name: "First",
     surname: "Test",
     alive: false,
     age: 123,
     traits: [ "X", "Y" ]
   } INTO Characters RETURN NEW
   ```

3. Teraz w zakładce `Collections` możemy podejrzeć nowo wpisany dokument.
4. Tworzymy zapytanie, które wstawi dane do kolekcji `Characters`:

   ```sql
   FOR d IN @data
     INSERT d INTO Characters
   ```

5. Powyższy syntax służy do wstawienia danych z dowiązanej zmiennej. Po prawej stronie można wstawić wartość zmiennej `data`. Wstawmy tam dane z pliku [`Characters.json`](./Characters.json):
6. W prawym dolnym rogu klikamy przycisk `Execute`.
7. Ciekawostka - AQL nie pozwala na wielokrotne użycie `INSERT` w jednym zapytaniu - trzeba użyć `FOR` i `INSERT` w pętli.
8. Podejrzyjmy teraz naszą kolekcję za pomocą zapytania:

   ```sql
   FOR c IN Characters
     RETURN c
   ```

### Pobieranie konkretnego dokumentu

Aby otrzymać konkretny dokument z kolekcji, możemy to zrobić na kilka sposobów. Jednym z nich jest użycie polecenia `DOCUMENT`:

```sql
RETURN DOCUMENT("Characters", "ned")
// -- alternatywnie
// -- RETURN DOCUMENT("Characters/ned")
```

Możemy też pobrać więcej niż jeden dokument:

```sql
RETURN DOCUMENT("Characters", ["ned", "arya"])
// -- alternatywnie
// -- RETURN DOCUMENT("Characters/ned", "Characters/arya")
```

W tym przypadku jesteśmy jednak ograniczeni do dopasowaniu dokumentów tylko po ich kluczu. Możemy też użyć polecenia `FILTER`, aby pobrać dokumenty, które spełniają określone warunki:

```sql
FOR c IN Characters
  FILTER c.name IN ["Ned", "Arya"]
  RETURN c
```

### Edytowanie dokumentów

Aby edytować dokumenty, możemy użyć polecenia `UPDATE`:

```sql
UPDATE "ned" WITH { alive: false } IN Characters
```

Możemy też użyć polecenia `REPLACE`, aby zastąpić cały dokument nowym:

```sql
REPLACE "ned" WITH {
  name: "Ned",
  surname: "Stark",
  alive: false,
  age: 41,
  traits: ["A","H","C","N","P"]
} IN Characters
```

Możemy to także robić w pętli. Nasza kolekcja nie posiada swojego scheme, więc możemy do dokumentów bez problemu dodawać nowe pola.

```sql
FOR c IN Characters
  UPDATE c WITH { season: 1 } IN Characters
```

### Usuwanie dokumentów

Aby usunąć dokumenty, możemy użyć polecenia `REMOVE`. Możemy usunąć nasz wpis testowy:

```sql
REMOVE "test" IN Characters
```

Możemy też usunąć dokumenty w pętli:

```sql
FOR c IN Characters
  REMOVE c IN Characters
```
