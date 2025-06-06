<!-- - ArangoDB - wielomodelowa baza danych (dokumenty + grafy + key-value). Język AQL (1-2os.) Agnieszka Trojnowska, Tomasz Żelawski. 27.05.2025. -->

## Instalacja (Docker)

Prerekwizyt: zainstalowany Docker, najlepiej Docker Desktop.

W pierwszej kolejności należy pobrać obraz ArangoDB:

```bash
docker pull arangodb:latest
```

Gyby nie udało się automatycznie pobrać obrazu dla architektury hosta, można wymusić pobranie obrazu dla konkretnej architektury:

### amd64

```bash
docker pull arangodb:latest --platform linux/amd64
```

### arm64

```bash
docker pull arangodb:latest --platform linux/arm64/v8
```

## Uruchomienie

Najpierw sklonujmy repozytorium, żeby mieć w kontenerze dockera dostęp do plików z danymi:

```bash
git clone https://github.com/Agnieszka0544/arangoDB-labs
cd arangoDB-labs
```

Aby stworzyć i uruchomić instancję serwera ArangoDB, użyjmy następującego polecenia:

```bash
docker compose up --detach
```

W ten sposób uruchomimy instancję serwera ArangoDB w tle, który będzie dostępny pod adresem http://localhost:8529.
