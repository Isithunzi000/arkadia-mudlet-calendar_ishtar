# Kalendarz Ishtar — Mudlet

Pakiet do Mudleta: kalendarz domeny Ishtar (Starszy Lud) dla Arkadii MUD. Komenda `/ishtar` pokazuje przybliżony czas do najbliższych świąt i wydarzeń — RL i IG.

Port pluginu [ishtar_cal z klienta Dargoth](https://github.com/Isithunzi000/arkadia-dargoth-plugins) (v1.8.11).

---

## Jak zainstalować

1. Pobierz `.mpackage` albo `.xml` z [najnowszego wydania](https://github.com/Isithunzi000/arkadia-mudlet-calendar_ishtar/releases/latest) (oba działają tak samo, wybierz który wolisz)
2. W Mudlecie: **Toolbox → Package Manager** (`Alt+O`) → **Install** i wskaż pobrany plik
3. Gotowe — wpisz `/ishtar`

> Paczka powinna znajdować się powyżej skryptów ogólnodostępnych Arkadii — przesuń ją w górę listy w Package Manager.

Plik [`ishtar_cal.xml`](ishtar_cal.xml) w korzeniu repo to źródło pakietu — możesz podejrzeć cały kod bez pobierania.

---

## Komendy

| Komenda | Opis |
|---------|------|
| `/ishtar` | pokazuje kalendarz Ishtar |
| `/ishtar help` | pomoc (działa też `/ishtar pomoc`) |

## Co pokazuje

- Belleteyn i Saovine (główne święta magiczne)
- święta astronomiczne i magiczne: Midinvaerne, Birke, Midaete, Velen, Imbaelk, Lammas
- pełnię księżyca
- Festyn w Eysenlaan — dwa najbliższe wystąpienia z przedziałem od–do
- jeśli wydarzenie właśnie trwa — **TRWA TERAZ** z godziną zakończenia RL

## Jak to działa

- przy wywołaniu `/ishtar` pakiet sam wysyła komendę `czas` i parsuje odpowiedź serwera (linia jest ukrywana z okna gry)
- odczytany czas zapisuje się jako **kotwica** (czas IG + timestamp RL); przy kolejnych wywołaniach czas jest ekstrapolowany z kotwicy, bez dodatkowych zapytań
- jeśli serwer nie odpowie na `czas` w ciągu 3,5 s i nie ma zapisanej kotwicy, zobaczysz komunikat o błędzie

---

## Problemy z instalacją

Objaw: `installPackage` zwraca `true`, ale pakiet nie pojawia się na liście i komenda `/ishtar` nie działa.

Przyczyna: znany błąd Mudleta — jeśli wcześniejsza próba instalacji się nie powiodła (np. przerwane pobieranie, podwójna instalacja), w katalogu profilu zostaje martwy folder pakietu i każda kolejna instalacja po cichu się nie udaje. Ponawianie nie pomaga — folder trzeba usunąć ręcznie.

Naprawa:

1. W linii poleceń Mudleta wpisz `lua getMudletHomeDir()` i otwórz wyświetlony katalog
2. Skasuj folder `ishtar_cal` oraz ewentualny folder nazwany jak pobrany plik bez rozszerzenia (np. `ishtar_cal_1_8_11m`) — to martwe resztki
3. Zrestartuj profil
4. Zainstaluj pakiet przez **Toolbox → Package Manager** (`Alt+O`) → **Install**
5. Sprawdź `lua getPackages()` — pakiet powinien być na liście, a komenda działać

Jeśli nadal się nie instaluje, sprawdź konsolę główną pod kątem linii `[ ERROR ]` lub `[ WARN ]` tuż po instalacji.

---

## Uwagi

- przelicznik czasu: 2 sekundy RL = 1 minuta IG (1 godzina gry = 120 s RL)
- kalendarz Ishtar: 360 dni (8 pór roku po 45 dni)
- źródłem czasu jest wyłącznie komenda `czas` — pakiet nie zależy od GMCP
- wersja `1.8.12m` bazuje na ishtar_cal 1.8.11 (Dargoth)
- od `1.8.12m` wewnętrzna numeracja roku liczy od **1 Saovine** (konwencja gry, potwierdzona empirycznie) — czysto wewnętrzna zmiana, wyniki (odliczania, daty RL) pozostają identyczne; spójne z pakietem [paska kalendarza](https://github.com/Isithunzi000/arkadia-mudlet-pasek_czas)
