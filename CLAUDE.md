# Kształt związku

Narzędzie w jednym pliku index.html. Pokazuje większe decyzje w związku:
jakie są opcje, co każda daje, kosztem czego i jakiego warunku wymaga.

## Czym projekt nie jest
- Nie jest systemem zarządzania związkiem: bez śledzenia w czasie i metryk.
- Nie przewiduje przyszłości: bez ocen punktowych i prognoz.
- Profil "Druga strona" wypełnia druga osoba sama. Nie zgaduj jej odpowiedzi.
- Teksty, progi i reguły to heurystyki autora, nie wyniki badań.

## Zasady pracy
- Najpierw plan z kosztem, wartością i ryzykiem, potem implementacja.
- Nazwy w kodzie pełne i opisowe, bez skrótów. Stary kod poprawiaj przy okazji.
- Interfejs po polsku, bez długich myślników.
- Teksty trzymaj w blokach danych, nie w funkcjach rysujących.
- Kolory tylko przez zmienne CSS z :root. Motyw jasny i ciemny.
  Ciemny motyw jest zdefiniowany dwa razy (media query i `:root[data-theme="dark"]`): zmieniaj oba.
- Po zmianie zachowania podnieś numer wersji i datę w stopce (`#version`).
- Commity: DO USTALENIA.

## Sprawdzanie
- Podgląd: python3 -m http.server 8000, potem http://localhost:8000/index.html
- Testów automatycznych nie ma. Scenariusz ręczny wymaga aktualizacji.
- Spójność kształtów (w konsoli przeglądarki, ma zwrócić `true`): każdy preset daje swój kształt.
  `PRESETS.every((preset, index) => shapeIdx(Object.fromEntries(DIM_IDS.map((id, position) => [id, preset.vals[position]]))) === index)`
- Czysty start: `localStorage.removeItem("ksztalt-zwiazku")` i odświeżenie strony.

## Architektura
Jeden `<script>` na końcu index.html, bez zależności i bez budowania (z zewnątrz tylko Google Fonts).
Układ pliku: CSS, szkielet HTML z pustymi kontenerami po id, bloki danych, funkcje, podpięcie zdarzeń.

### Bloki danych
Wszystkie są kluczowane id wymiaru: czas, mieszkanie, finanse, role, rodziny, znajomi, praca, styl, dzieci, miejsce, slub.
- `DIMS`: `labels` to pięć pozycji skali. `low` / `mid` / `high` to trójki [zysk, koszt, warunek]
  dla pozycji 1-2 / 3 / 4-5 (`band()`). `group` "A" to trudno odwracalne, "B" odwracalne.
- `QUESTIONS`: pytania kotwiczące. `answers` to tablica albo `{ now, want }`.
  Tryb `now` tylko w profilu "Jak jest teraz", wszędzie indziej `want`. Wyświetla je wyłącznie `renderQuestionHelp()`.
- `TENSIONS`: pary [test, tekst] na rozwiązanych wartościach.
  `PAIRS` powtarza dwustronne reguły z TENSIONS jako zakresy (kropki i ostrzeżenia pod suwakami). Nic ich nie łączy: zmiana jednej wymaga zmiany drugiej.
- `SHAPES`: wygrywa pierwszy pasujący test, ostatni łapie wszystko, więc kolejność ma znaczenie.
  `PRESETS` jest równoległe do SHAPES po indeksie (`keys` to suwaki wyróżniane w podglądzie).
- `OWN` (czyja decyzja), `ORDER` (od najłatwiej odwracalnej), `HOURS` (budżet tygodnia), `PANEL_GROUPS` (układ panelu).

### Pułapki kolejności
- `DIMS` jest sortowane w miejscu według `AXIS_ORDER` (osie wykresu), więc indeks w DIMS to nie kolejność deklaracji.
  `PRESETS[].vals` są czytane według `DIM_IDS`, czyli kolejności deklaracji.
- `WEAVE_DIMENSION_IDS` i `EXIT_DIMENSION_IDS` muszą leżeć kolejno obok siebie w AXIS_ORDER: `drawSector()` rysuje wycinek od pierwszego id przez następne osie.
- Nowy wymiar trzeba dopisać w: DIMS, AXIS_ORDER, DIM_IDS, każdym `PRESETS[].vals`, QUESTIONS, OWN, ORDER, PANEL_GROUPS, DEFAULT_ME,
  a w razie potrzeby w HOURS, wagach wskaźników i sektorach wykresu.

### Stan
- Trzy profile: `state.me` ("Jak chcę"), `state.now` ("Jak jest teraz"), `state.her` ("Druga strona").
  Wartości 1-5 albo `null`, czyli bez odpowiedzi. `now` i `her` są `null`, dopóki ktoś nie otworzy zakładki.
- Do obliczeń `resolveProfileValues()` zamienia `null` na pozycję środkową. Porównania profili ("Jak jest a jak chcę", "Różnice między wami") pomijają skale bez odpowiedzi.
- Podgląd kształtu: `state.preview` (indeks w SHAPES) i `state.previewVals`. `current()` zwraca wtedy previewVals, więc edycje nie trafiają do profilu i nie są zapisywane.
- `save()` zapisuje do localStorage pod kluczem "ksztalt-zwiazku" z polem `schema: 2`. Zmiana kształtu zapisu: podnieś schema i obsłuż stary zapis przy wczytaniu.

### Rysowanie
- `buildPanel()` tworzy suwaki raz. `render()` przy każdej zmianie odświeża wszystko, karty przez innerHTML.
  Każdy handler robi to samo: zmienia `current()`, potem `save()` i `render()`.
- Id elementów wymiaru: `s-` suwak, `v-` etykieta wartości, `t-` kropki napięć, `r-` pasek zakresu w podglądzie,
  `k-` potwierdzenie pozycji środkowej, `c-` ostrzeżenie o napięciu, `hb-` pomoc z pytaniami.
- Każdy tekst wstawiany do HTML przechodzi przez `esc()`.

## Znany dług
1. Progi w dwóch miejscach: HOURS i przedziały w QUESTIONS.
2. Reguły TENSIONS, SHAPES, PRESETS sprzed pytań kotwiczących.
3. Start z wartościami przykładowymi, a czyszczenie daje profil pusty.
4. Reguły dwustronne w dwóch miejscach: TENSIONS i PAIRS.
5. Wagi wskaźnika splecenia wpisane dwa razy: w `render()` i w `shapeIdx()`.
6. Kolory segmentów budżetu wpisane na sztywno w `budget()`, wbrew zasadzie zmiennych CSS.
