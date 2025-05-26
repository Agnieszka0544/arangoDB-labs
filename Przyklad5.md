### Geolokalizacja

ArangoDB posiada wbudowane wsparcie dla geolokalizacji. Możemy tworzyć kolekcje, które będą przechowywały dane geograficzne i wykonywać na nich zapytania. Kolekcje te nie korzystają z osobnego, specjalnego typu danych, tylko tworzą specjalne indeksy. Zobaczmy, jak tego użyć:

1. Stwórzmy kolekcję `Locations` z poziomu interfejsu webowego.
2. Uzupełnijmy ją danymi z pliku [Locations.json](./Dane-do-przykladow/Locations.json):
   ```sql
   FOR loc IN @locations
     INSERT loc INTO Locations
   ```
3. Zwizualizujmy naszą kolekcję:
   ```sql
    FOR loc IN Locations
      RETURN GEO_POINT(loc.coordinates[1], loc.coordinates[0])
   ```
4. Stwórzmy indeks geograficzny dla naszej kolekcji:
   1. Przejdźmy do zakładki `Collections` w lewym panelu.
   2. Otwórzmy kolekcję `Locations`.
   3. Przejdźmy do zakładki `Indexes`.
   4. Kliknijmy przycisk `Add index`.
   5. Wybierzmy typ indeksu `Geo Index`.
   6. W polu `Fields` wpiszmy `coordinates`.
   7. Zaznaczmy opcję `GeoJSON` i kliknijmy `Create`.
5. Odnajdźmy teraz w kolekcji lokalizację najbliższą Wydziałowi WMiI (współrzędne geograficzne: `50.030192560043375`, `19.906854470049637`):
   ```sql
   FOR loc IN Locations
    LET distance_m = DISTANCE(loc.coordinates[0], loc.coordinates[1], 53.0302, 19.9069)
    SORT distance_m
    LIMIT 1
    RETURN {
      name: loc.name,
      latitude: loc.coordinates[0],
      longitude: loc.coordinates[1],
      distance_km: ROUND(distance_m / 1000)
    }
   ```
