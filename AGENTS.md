# AGENTS.md — Protokół aktualizacji planu (pedagogium-plan, stopień magisterski)

**ZAWSZE przeczytaj ten plik PRZED wprowadzeniem jakichkolwiek zmian w `index.html`.**

## Mapowanie żądań użytkownika

- „update magisterka / magisterskie / plan magisterski" → **to repozytorium** (`marek-priv/pedagogium-plan`, strona https://marek-priv.github.io/pedagogium-plan/)
- „update PP / pierwsze stopień / pedagogium-pp-plan" → repozytorium `marek-priv/pedagogium-pp-plan` (osobny projekt — analogiczny protokół)

## Źródło danych

- Użytkownik dostarcza nowy plan jako plik `.docx` w promptcie (ścieżka w `C:\Users\Dell\CascadeProjects\Studia\`).
- **NIGDY nie polegaj na spłaszczonym tekście dokumentu** — wypakuj `word/document.xml` i parsuj strukturę `w:tbl` → `w:tr` → `w:tc` komórka po komórce. Klasy w slocie czasowym to osobne akapity wewnątrz komórki; lista `/zjazd: .../` jest na końcu opisu przedmiotu.

## Zasady aktualizacji

1. **Zjazdy 1–3 są przeszłe i nietknięte** — nie wolno zmieniać ich wpisów, dat, prowadzących ani statusów. Zmiany stosuj tylko dla zjazdów 4–9 (`PAST_ZJ = {1,2,3}`).
2. **Weryfikacja komórka po komórce:** zbuduj oczekiwaną siatkę (dzień × slot × zjazd → klucz przedmiotu+forma) bezpośrednio z docx i porównaj z `CLASSES.sobota` / `CLASSES.niedziela` **programowo** (skrypt Node), a nie „na oko". Dopuszczalne różnice: wpisy wyłącznie dla zjazdów 1–3 (historia) oraz ręczne elementy dodane wcześniej (np. spotkanie organizacyjne, zwolnienia `exemptZj`, `notHeldZj`).
3. **Zachowuj mechanizmy:** `PAST_ZJ`, `PAST_SOB_ZJ`, `ONLINE_NDZ_ZJ`, `SALA_NDZ`, `AULA_A`, `SALA_NDZ_ALT`, `exemptZj`, `notHeldZj`, `star`, `note` — nie usuwaj ich bez wyraźnej potrzeby.
4. **Zaliczenia i kontakty:** jeśli docx zmienia prowadzących — synchronizuj `SUBJECTS` i sekcję Kontakt.
5. **Nowe lokalizacje:** sobota = online; niedziela = `SALA_NDZ` domyślnie, `loc: "aulaA"` na poziomie wpisu klasy, `SALA_NDZ_ALT[zjazd]` dla całego zjazdu.

## Dokumentowanie zmian

- **KAŻDA** zmiana musi być opisana w zakładce **Zmiany** (`<div class="changes">`) — nowa grupa `chg-group` z datą na górze listy + zaktualizuj stempel `Aktualizacja:` w nagłówku.
- Format: zwięzłe `<li>` z podziałem na dni / przedmioty / lokalizacje.

## Jakość renderowania

- Tekst w elementach graficznych (karty `.cls`, chipy `.fchip`, kolumny tabeli, notatki) **nie może być ucinany ani wychodzić poza kontener**. Jeśli dodajesz dłuższe napisy — sprawdź, czy nie rozjeżdża layoutu (np. długie adresy w tooltipach są OK, ale widoczne chipy mają być krótkie).
- Zwijane kolumny zjazdów: `display:none` na `td` jest ZAKAZANY (psuje wyrównanie tabeli) — używaj `col-hidden` (zwężanie komórek).

## Weryfikacja przed commitem

1. `node --check` na wyekstrahowanym `<script>`.
2. Programowe porównanie siatki z docx (punkt 2 powyżej).
3. `git add -A && git commit && git push` → GitHub Pages aktualizuje się w ~2–4 minuty.
