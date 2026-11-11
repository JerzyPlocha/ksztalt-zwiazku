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
- Po zmianie zachowania podnieś numer wersji w stopce.
- Commity: DO USTALENIA.

## Sprawdzanie
- Podgląd: python3 -m http.server 8000, potem http://localhost:8000/index.html
- Testów automatycznych nie ma. Scenariusz ręczny wymaga aktualizacji.

## Znany dług
1. Progi w dwóch miejscach: HOURS i przedziały w QUESTIONS.
2. Reguły TENSIONS, SHAPES, PRESETS sprzed pytań kotwiczących.
3. Start z wartościami przykładowymi, a czyszczenie daje profil pusty.
