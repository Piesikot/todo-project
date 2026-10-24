# Plan migracji nazw klas do konwencji BEM

## Cel

Ujednolicić nazwy klas w [index.html](./index.html) zgodnie z konwencją BEM
(Block, Element, Modifier), używając anglojęzycznego nazewnictwa typowego dla
zachodnich projektów frontendowych. Migracja ma zachować obecny wygląd i
działanie aplikacji.

## Zakres

- markup HTML w `index.html`,
- selektory CSS zapisane w sekcji `<style>`,
- selektory używane przez JavaScript w `querySelector`,
- klasy dodawane dynamicznie przez `setAttribute`.

Reguły dotyczące elementów HTML bez klas (`nav`, `input`, `button`) należy
przenieść na klasy komponentów tylko wtedy, gdy jest to potrzebne do
jednoznacznego powiązania stylu z blokiem. Nie należy zmieniać logiki
obsługi zadań ani treści interfejsu w ramach samej migracji nazewnictwa.

## Proponowane nazewnictwo

Blokiem głównym będzie `todo-app`. Formularz dodawania zadania otrzyma własny
blok `task-form`, a pojedyncze zadanie — blok `task`, ponieważ oba komponenty
mają niezależne znaczenie i mogą być rozwijane osobno.

| Obecna klasa | Docelowa klasa BEM | Typ | Użycie |
| --- | --- | --- | --- |
| `list-container` | `todo-app` | blok | główny kontener listy i nagłówka |
| brak klasy na `nav` | `task-form` | blok | obszar dodawania zadania |
| `new-task` | `task-form__label` | element | tekst opisujący pole formularza |
| `task` | `task-form__input` | element | pole wpisywania nowego zadania |
| `add-task-button` | `task-form__submit` | element | przycisk dodawania zadania |
| `to-do-list` | `todo-app__list` | element | lista zadań |
| brak klasy na `li` | `task` | blok | pojedyncze zadanie tworzone w DOM |
| `task-deletion` | `task__delete` | element | kontrolka usuwania zadania |

Jeżeli w przyszłości pojawią się warianty wizualne, należy nazywać je
modyfikatorami, np. `task--empty`, `task--default` albo
`task-form__submit--disabled`. Nie należy tworzyć klas opisujących samą
pozycję elementu, takich jak `top-right` lub `red-text`.

## Kolejność migracji

1. **Inwentaryzacja zależności**
   - potwierdzić wszystkie klasy występujące w HTML, CSS i JavaScript,
   - sprawdzić, czy nazwy nie są używane poza [index.html](./index.html),
   - ustalić, czy elementy generowane dynamicznie wymagają klasy bloku
     `task`.

2. **Przygotowanie struktury HTML**
   - dodać `class="task-form"` do elementu `nav`,
   - zmienić `list-container` na `todo-app`,
   - zastąpić istniejące nazwy klas formularza i listy nazwami docelowymi,
   - pozostawić semantykę elementów (`nav`, `input`, `button`, `ol`) bez zmian.

3. **Aktualizacja CSS**
   - zmienić selektory na docelowe nazwy BEM,
   - zastąpić selektory tagów `input` i `button` klasami
     `task-form__input` i `task-form__submit`, aby style nie wpływały
     przypadkowo na przyszłe kontrolki,
   - zachować obecne wartości właściwości, kolejność reguł i responsywne
     jednostki,
   - pozostawić globalne reguły `*` i `h1` bez zmian, o ile nie okaże się,
     że migracja wymaga ich ograniczenia do bloku `todo-app`.

4. **Aktualizacja JavaScript**
   - zmienić selektory `querySelector` na `task-form__submit`,
     `todo-app__list` i `task-form__input`,
   - klasę ustawianą dynamicznie na kontrolce usuwania zmienić na
     `task__delete`,
   - dodać klasę `task` do każdego tworzonego elementu `li`, w tym do zadań
     domyślnych, jeśli stylowanie pojedynczego zadania będzie tego wymagało,
   - nie zmieniać warunków obsługi pustych wpisów, usuwania ani kolejności
     dodawania elementów do listy.

5. **Usunięcie nazw przejściowych**
   - wyszukać stare nazwy: `new-task`, `task`, `add-task-button`,
     `to-do-list`, `task-deletion` i `list-container`,
   - upewnić się, że żadna z nich nie pozostała w HTML, CSS ani JavaScript,
     z wyjątkiem nazwy `task` rozpatrzonej jako docelowy blok BEM,
   - sprawdzić, czy nie ma mieszania starej i nowej konwencji w jednym
     selektorze.

## Walidacja

- otworzyć stronę i potwierdzić zachowanie na szerokim i wąskim widoku,
- dodać zwykłe zadanie oraz wpis pusty i składający się wyłącznie ze spacji,
- usunąć zarówno zadanie domyślne, jak i nowo dodane,
- potwierdzić, że kontrolka usuwania nadal wyświetla czerwony symbol,
- sprawdzić w narzędziach deweloperskich, że selektory nie zwracają `null`,
- wyszukać w repozytorium wszystkie stare nazwy klas i potwierdzić ich brak
  poza dokumentacją migracji,
- sprawdzić, czy zmiana nie wprowadziła błędów w konsoli przeglądarki.

## Kryteria zakończenia

- każda klasa komponentu korzysta z formatu `block`, `block__element` lub
  `block--modifier`,
- HTML, CSS i JavaScript używają tych samych nazw docelowych,
- brak zmian w funkcjonalności aplikacji,
- wygląd i responsywność pozostają równoważne z wersją przed migracją,
- stare nazwy klas nie występują już w kodzie wykonywalnym.

## Ryzyka i sposób ograniczenia

- **Niespójny rename między warstwami** — wykonywać zmianę HTML, CSS i
  JavaScript jako jeden zakres i po niej wyszukać każdą nazwę.
- **Przypadkowa zmiana stylów globalnych** — zastąpić selektory tagów klasami
  tylko w obrębie formularza i porównać wygląd przed oraz po migracji.
- **Brak klasy na elementach tworzonych dynamicznie** — zweryfikować w DOM
  zarówno zadania domyślne, jak i zadania dodawane przez użytkownika.
- **Dwuznaczność nazwy `task`** — traktować `task` wyłącznie jako blok
  pojedynczego zadania; pole formularza nazywać `task-form__input`.

