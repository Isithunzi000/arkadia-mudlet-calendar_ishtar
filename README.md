# Kalendarz Ishtar — Mudlet

Pakiet do Mudleta: kalendarz domeny Ishtar (Starszy Lud) dla Arkadii MUD. Komenda `/ishtar` pokazuje przybliżony czas do najbliższych świąt i wydarzeń — RL i IG.

Port pluginu [ishtar_cal z klienta Dargoth](https://github.com/Isithunzi000/arkadia-dargoth-plugins) .

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
| `/ishtar reset` | czyści zapisaną datę (kotwicę) |
| `/ishtar aktualizuj` | sprawdza i instaluje aktualizację z GitHub Releases |

## Co pokazuje

- Belleteyn i Saovine (główne święta magiczne)
- święta astronomiczne i magiczne: Midinvaerne, Birke, Midaete, Velen, Imbaelk, Lammas
- pełnię księżyca
- Festyn w Eysenlaan — dwa najbliższe wystąpienia z przedziałem od–do
- jeśli wydarzenie właśnie trwa — **TRWA TERAZ** z godziną zakończenia RL

## Jak to działa

- przy wywołaniu `/ishtar` pakiet sam wysyła komendę `czas` i parsuje odpowiedź serwera (linia jest ukrywana z okna gry)
- udany odczyt zapisuje się jako **kotwica** (czas IG + timestamp RL) — to zapas na wypadek błędu odczytu, nie skrót: pakiet zawsze pyta serwer
- kotwica odnawia się też **pasywnie**: każda poprawna odpowiedź serwera na `czas` zapisuje datę, nawet gdy nikt nie wywołał `/ishtar` (np. gdy o `czas` poprosił inny pakiet) — linia zostaje wtedy w oknie gry i nie ma raportu
- kotwica zapisuje się na dysku profilu (`ishtar_cal_anchor_v2.lua`) i przeżywa restart klienta; uszkodzony, stary albo obcy plik jest po cichu ignorowany; `/ishtar reset` usuwa kotwicę z dysku i pamięci
- gdy odczyt `czas` się nie powiedzie albo postać jest w innej domenie, pakiet liczy z zapisanej daty i **mówi o tym** — przed raportem pojawia się linia „Pokazuje Ishtar wyliczone z zapisanej daty (ostatni odczyt: …)"
- gdy zapisanej daty nie ma, pakiet wyświetla komunikat zamiast zgadywać
- jeśli serwer nie odpowie na `czas` w ciągu 3,5 s i nie ma zapisanej daty, zobaczysz komunikat o błędzie
- gdy odpowiedź przyjdzie po upływie timeoutu (np. przez throttle'owanie timerów w mudlet-web), linia `czas` zostaje widoczna, a pakiet zapisuje tylko kotwicę — bez raportu i bez komunikatów (paritet z klientami przeglądarkowymi)

> W mudlet-web (Mudlet w przeglądarce) zapis działa przez IndexedDB — per origin i profil, best-effort (np. czyszczenie danych przeglądarki kasuje kotwicę).

---

## Aktualizacje

Pakiet sam sprawdza aktualizacje: przy starcie klienta (nie częściej niż co 8 godzin) pyta o najnowsze wydanie na GitHubie i — jeśli jest nowsza wersja — wyświetla powiadomienie. Sam nic nie instaluje: aktualizację uruchamiasz świadomie komendą `/ishtar aktualizuj`, która pobiera paczkę, podmienia ją i prosi o restart Mudleta.

Od wersji **1.8.19m** assety wydania mają stałe nazwy (`ishtar_cal.mpackage`, `ishtar_cal.xml`), a aktualizator przed instalacją sprząta historyczne nazwy pakietów — jedna paczka zostaje w profilu zawsze pod nazwą `ishtar_cal`.

### Mudlet web — jednorazowe czyszczenie

Starsze wydania na mudlet-web (Mudlet w przeglądarce) brały nazwę paczki od nazwy pliku, więc po aktualizacjach mogły zostać duplikaty. Po zainstalowaniu wersji 1.8.19m lub nowszej otwórz **Package Manager** i odinstaluj ręcznie wszystkie pozycje z poniższej listy, jeśli je widzisz (zostaw tylko `ishtar_cal`):

- `ishtar_cal_update`
- `ishtar_cal_1_8_11m`, `ishtar_cal_1_8_12m`, `ishtar_cal_1_8_13m`, `ishtar_cal_1_8_14m`, `ishtar_cal_1_8_15m`, `ishtar_cal_1_8_16m`, `ishtar_cal_1_8_17m`, `ishtar_cal_1_8_18m`

To czyszczenie robisz tylko raz — kolejne aktualizacje sprzątają te nazwy samoczynnie.

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
