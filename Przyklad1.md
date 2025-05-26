## Podstawowe operacje w ArangoDB

Korzystając z tego przykładu przedstawimy podstawowe funkcjonalności ArangoDB, czyli tworzenie użytkowników, baz danych, kolekcji oraz wstawianie, pobieranie, edytowanie i usuwanie dokumentów.

### Tworzenie nowego użytkownika

Po wejściu do interfejsu webowego ArangoDB (http://localhost:8529) i zalogowaniu się jako użytkownik root (hasło: `root`), można utworzyć nowego użytkownika.

1. Przy logowaniu wybieramy (póki co) jedyną dostępną bazę danych, czyli `_system`.
2. Następnie klikamy ikonę "Users" w lewym panelu.
3. Klikamy przycisk `Add user`.
4. Tworzymy użytkownika `user` z hasłem `user`, żeby później nie musieć korzystać z konta root.

### Tworzenie nowej bazy danych

Pozostając w bazie `_system`, tworzymy nową bazę danych, która będzie wykorzystywana w dalszej części ćwiczeń.

1. Klikamy ikonę `Databases` w lewym panelu.
2. Klikamy przycisk `Add database`.
3. Na potrzeby zadania, nadajmy bazie danych nazwę `got` i poniżej, w sekcji konfiguracji, ustawmy uprawnienia administratora dla użytkownika `user`, którego stworzyliśmy wcześniej.
4. Przyciskiem `Create` stwórzmy bazę danych.

Zalogujmy się teraz jako użytkownik `user` do bazy `got` (odpowiednie przyciski znajdują się w prawym górnym rogu strony).

### Tworzenie kolekcji

Nie można stworzyć kolekcji z poziomu AQL - tak ten język jest zaprojektowany. Zamiast tego, możemy to zrobić z zakładki `Collections` w lewym panelu.

1. Klikamy ikonę `Collections` w lewym panelu.
2. Klikamy przycisk `Add collection`.
3. Wybieramy typ kolekcji `Document`, nadajemy jej nazwę `Characters` i tworzymy ją.

### Wstawianie danych do kolekcji

Dane do kolekcji możemy już dodać z poziomu AQL.

1. Klikamy w ikonę `Queries` w lewym panelu.
2. Zacznijmy od testowego zapytania, które doda prosty dokument do kolekcji `Characters`:

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

3. W prawym dolnym rogu klikamy przycisk `Execute`. Na dole strony pojawi się informacja o wykonaniu zapytania.
4. W zakładce `Collections` możemy podejrzeć nowo wpisany dokument.
5. Tworzymy zapytanie, które wstawi dane do kolekcji `Characters`.

   ```sql
   FOR d IN @data
     INSERT d INTO Characters
   ```

   Powyższy syntax służy do wstawienia danych z dowiązanej zmiennej.

   Ciekawostka - AQL nie pozwala na wielokrotne użycie `INSERT` w jednym zapytaniu - trzeba użyć `FOR` i `INSERT` w pętli.

6. Po prawej stronie znajduje się pole, w którym należy podać wartość zmiennej `data`. Wstawmy tam dane z pliku [`Characters.json`](./Dane-do-przykladow/Characters.json).
7. Kliknijmy przycisk `Execute`.
8. Podejrzyjmy teraz naszą kolekcję za pomocą zapytania:

   ```sql
   FOR c IN Characters
     RETURN c
   ```

   Po wykonaniu zapytania na dole strony pojawi się "tabela" z danymi.

### Pobieranie konkretnego dokumentu

Istnieje kilka sposobów na znalezienie konkretnego dokumentu w kolekcji. Jednym z nich jest użycie polecenia `DOCUMENT`:

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

W tym przypadku jesteśmy jednak ograniczeni do dopasowania dokumentów tylko po ich kluczu. Możemy zamiast tego użyć polecenia `FILTER`, aby pobrać dokumenty, które spełniają określone warunki:

```sql
FOR c IN Characters
  FILTER c.name IN ["Ned", "Arya"]
  RETURN c
```

### Edytowanie dokumentów

Aby edytować pojedyncze atrybuty w dokumencie, możemy użyć polecenia `UPDATE`:

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

Aby usunąć dokumenty, możemy użyć polecenia `REMOVE`. Usuńmy nasz wpis testowy:

```sql
REMOVE "test" IN Characters
```

Dokumenty możemy też usuwać w pętli:

```sql
FOR c IN Characters
  REMOVE c IN Characters
```
