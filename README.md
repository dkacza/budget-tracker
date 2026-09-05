# Budget Tracker

Prosta aplikacja do kontrolowania domowego budżetu. Pozwala dodawać przychody i wydatki, ustawiać limity dla kategorii oraz śledzić saldo, stopę oszczędności i przekroczenia budżetu.

## Funkcje

- dodawanie i usuwanie transakcji;
- podsumowanie przychodów, wydatków, salda i stopy oszczędności;
- zestawienie wydatków według kategorii;
- limity budżetowe oraz alerty o ich przekroczeniu;
- automatyczne dodawanie nowych kategorii wprowadzonych przy transakcji.

## Technologia

- backend: Go 1.26;
- frontend: HTML, CSS i JavaScript bez dodatkowych frameworków;
- wdrożenie kontenerowe: Docker, Nginx.

## Uruchomienie

### Backend lokalnie

Wymagany jest Go 1.26 lub nowszy.

```bash
cd backend
go run .
```

Serwer będzie dostępny pod adresem `http://localhost:8080`. Port można zmienić zmienną środowiskową `PORT`, np. `PORT=9000 go run .`.

### Cała aplikacja w Dockerze

Frontendowy Nginx przekazuje żądania `/api` do kontenera nazwanego `backend`, dlatego kontenery powinny działać w tej samej sieci Docker:

```bash
docker network create budget-tracker
docker build -t budget-tracker-backend ./backend
docker build -t budget-tracker-frontend ./frontend
docker run -d --name backend --network budget-tracker budget-tracker-backend
docker run -d --name budget-tracker-frontend --network budget-tracker -p 8081:80 budget-tracker-frontend
```

Następnie otwórz `http://localhost:8081`.

Po zakończeniu pracy kontenery można zatrzymać poleceniem:

```bash
docker rm -f budget-tracker-frontend backend
```

## Testy

```bash
cd backend
go test ./...
```

## API

| Metoda | Endpoint | Opis |
| --- | --- | --- |
| `GET` | `/health` | Kontrola stanu serwera; zwraca `ok`. |
| `GET` | `/api/data` | Wersja i czas budowy backendu. |
| `GET` | `/api/transactions` | Lista transakcji. |
| `POST` | `/api/transactions` | Dodanie transakcji. |
| `DELETE` | `/api/transactions/{id}` | Usunięcie transakcji. |
| `GET`, `POST` | `/api/limits` | Odczyt lub zapis limitów kategorii. |
| `GET` | `/api/categories` | Lista kategorii. |
| `GET` | `/api/summary` | Podsumowanie budżetu i alerty. |

Przykład dodania wydatku:

```bash
curl -X POST http://localhost:8080/api/transactions \
  -H 'Content-Type: application/json' \
  -d '{"description":"Zakupy","amount":125.50,"category":"Food","type":"expense","date":"2026-09-05"}'
```

Przykład ustawienia limitu:

```bash
curl -X POST http://localhost:8080/api/limits \
  -H 'Content-Type: application/json' \
  -d '{"category":"Food","limit":500}'
```

## Dane

Dane aplikacji są przechowywane wyłącznie w pamięci procesu. Restart backendu lub kontenera usuwa transakcje i limity. Projekt jest więc demonstracją i nie zawiera jeszcze bazy danych ani mechanizmu logowania.
