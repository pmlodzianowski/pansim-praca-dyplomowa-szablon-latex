# Szablon LaTeX — Praca dyplomowa PANS Ciechanów

Szablon pracy dyplomowej (licencjackiej, inżynierskiej, magisterskiej) zgodny
z Zarządzeniem nr 88/2023 Rektora Państwowej Akademii Nauk Stosowanych
im. Ignacego Mościckiego w Ciechanowie.

## Struktura projektu

```
.
├── src/                        # pliki edytowalne (tu pracujesz)
│   ├── main.tex                # dokument główny (konfiguracja + dane pracy)
│   ├── strona-tytulowa.tex     # strona tytułowa wg wzoru uczelnianego
│   ├── bibliografia.bib        # baza bibliograficzna (BibLaTeX)
│   ├── obrazy/
│   │   └── logo-uczelni.png    # logotyp uczelni
│   └── rozdzialy/
│       ├── 01wstep.tex
│       ├── 02rozdzial01.tex    # Rozdział I – podstawy teoretyczne
│       ├── 03rozdzial02.tex    # Rozdział II – założenia metodologiczne
│       ├── 04rozdzial03.tex    # Rozdział III – wyniki badań
│       ├── 05wnioski.tex
│       └── 06streszczenie.tex
├── wytyczne.txt                # wytyczne uczelniane (referencja)
├── .gitignore
└── README.md
```

## Wymagania

- **Docker** (zalecany) — nie trzeba instalować LaTeX-a lokalnie, lub
- **TeX Live** (pełna dystrybucja) z silnikiem `pdflatex` i narzędziem `biber`

## Przygotowanie środowiska Docker

1. Zainstaluj Docker Desktop:
   - Windows: https://docs.docker.com/desktop/install/windows-install/
   - macOS: https://docs.docker.com/desktop/install/mac-install/
   - Linux: https://docs.docker.com/engine/install/

2. Pobierz obraz TeX Live (jednorazowo, ~5 GB):

   ```bash
   docker pull texlive/texlive:latest
   ```

3. Sprawdź, czy obraz jest gotowy:

   ```bash
   docker run --rm texlive/texlive:latest pdflatex --version
   ```

## Kompilacja pracy

### Za pomocą VS Code (zalecane)

Projekt zawiera gotowe taski i konfiguracje uruchomienia. Wymagany Docker.

**Run and Debug** (`Ctrl+Shift+D`) — wybierz z listy i naciśnij `F5`:

| Konfiguracja | Opis |
|--------------|------|
| Kompilacja pracy (Docker) | Pełny cykl: pdflatex → biber → pdflatex → pdflatex |
| Szybka kompilacja (bez bibliografii) | Pojedynczy pdflatex (szybki podgląd zmian) |
| Wyczyść pliki kompilacji | Usuwa zawartość katalogu `build/` |

**Build Task** (`Ctrl+Shift+B`) — uruchamia pełną kompilację (domyślny task).

Wynikowy plik: **`build/main.pdf`**

### Za pomocą Dockera (ręcznie)

Z katalogu głównego projektu uruchom:

```bash
docker run --rm \
  -v "$(pwd)/src:/src" -v "$(pwd)/build:/build" -w /src \
  texlive/texlive:latest \
  sh -c "pdflatex -interaction=nonstopmode -output-directory=/build main.tex \
      && biber --input-directory=/build --output-directory=/build main \
      && pdflatex -interaction=nonstopmode -output-directory=/build main.tex \
      && pdflatex -interaction=nonstopmode -output-directory=/build main.tex"
```

Na Windows (PowerShell):

```powershell
docker run --rm `
  -v "D:\sciezka\do\projektu\src:/src" -v "D:\sciezka\do\projektu\build:/build" -w /src `
  texlive/texlive:latest `
  sh -c "pdflatex -interaction=nonstopmode -output-directory=/build main.tex && biber --input-directory=/build --output-directory=/build main && pdflatex -interaction=nonstopmode -output-directory=/build main.tex && pdflatex -interaction=nonstopmode -output-directory=/build main.tex"
```

### Za pomocą lokalnego TeX Live

Z katalogu głównego projektu:

```bash
mkdir -p build
cd src
pdflatex -interaction=nonstopmode -output-directory=../build main.tex
biber --input-directory=../build --output-directory=../build main
pdflatex -interaction=nonstopmode -output-directory=../build main.tex
pdflatex -interaction=nonstopmode -output-directory=../build main.tex
```

Wynikowy plik: **`build/main.pdf`**

### Dlaczego trzy przebiegi?

| Przebieg | Rola |
|----------|------|
| 1. `pdflatex` | Generuje pliki pomocnicze (`.aux`, `.bcf`, `.toc`) |
| 2. `biber` | Przetwarza bibliografię na podstawie `.bcf` |
| 3. `pdflatex` | Wstawia bibliografię i aktualizuje odniesienia |
| 4. `pdflatex` | Finalizuje numerację stron i spis treści |

## Edycja pracy

### Dane na stronie tytułowej

Otwórz `src/main.tex` i uzupełnij sekcję **Dane pracy**:

```latex
\newcommand{\wydzial}{Wydział Inżynierii i Ekonomii}
\newcommand{\kierunek}{Nazwa kierunku}
\newcommand{\imienazwisko}{Imię Nazwisko}
\newcommand{\nralbumu}{000000}
\newcommand{\tytulpracy}{Tytuł pracy dyplomowej}
\newcommand{\rodzajpracy}{Praca licencjacka}   % inżynierska / magisterska
\newcommand{\promotor}{dr Jan Kowalski}
\newcommand{\rokpracy}{2026}
\newcommand{\miasto}{Ciechanów}                 % lub Mława (WNTiS)
```

Studenci Wydziału Nauk Technicznych i Społecznych powinni dodatkowo odkomentować
linię z filią w `src/strona-tytulowa.tex` oraz ustawić `\miasto` na `Mława`.

### Pisanie rozdziałów

Każdy rozdział to osobny plik w katalogu `src/rozdzialy/`. Rozdziały numerowane
używają `\chapter{Tytuł}`, nienumerowane — `\rozdzialnienumerowany{Tytuł}`.
Tytuły rozdziałów są automatycznie zamieniane na wielkie litery.

```latex
% Rozdział numerowany (ROZDZIAŁ I, II, III, ...)
\chapter{Podstawy teoretyczne}

\section{Podrozdział}

Treść akapitu z~odniesieniem do źródła~\cite{klucz-bib}.

\subsection{Pod-podrozdział}

Dalsza treść.
```

### Dodawanie rozdziałów

1. Utwórz nowy plik w `src/rozdzialy/`, np. `04arozdzial03a.tex`.
2. Dodaj odpowiedni `\input{}` w `src/main.tex` w sekcji **Treść pracy**.

### Wstawianie rycin

Pliki graficzne umieszczaj w katalogu `src/obrazy/`. W tekście:

```latex
Przykład — patrz (Ryc.~\ref{fig:etykieta}).

\begin{figure}[htbp]
  \centering
  \includegraphics[width=0.7\textwidth]{nazwa-pliku}
  \caption{Opis ryciny}\label{fig:etykieta}
  \zrodlo{opis źródła.}
\end{figure}
```

### Wstawianie tabel

```latex
Przykład — patrz (Tab.~\ref{tab:etykieta}).

\begin{table}[htbp]
  \caption{Opis tabeli}\label{tab:etykieta}
  \centering
  \begin{tabular}{lcc}
    \toprule
    \textbf{Kolumna A} & \textbf{Kolumna B} & \textbf{Kolumna C} \\
    \midrule
    Wiersz 1 & 10 & 20 \\
    Wiersz 2 & 30 & 40 \\
    \bottomrule
  \end{tabular}
  \zrodlo{opracowanie własne.}
\end{table}
```

### Wstawianie wykresów

Wykresy mają osobny typ float z podpisem **nad** wykresem (w odróżnieniu od rycin).
Można je tworzyć za pomocą pakietu `pgfplots` lub wstawiać jako obrazy:

```latex
Przykład — patrz (Wykres~\ref{wyk:etykieta}).

\begin{wykres}[htbp]
  \caption{Opis wykresu}\label{wyk:etykieta}
  \centering
  \includegraphics[width=0.7\textwidth]{nazwa-pliku-wykresu}
  \zrodlo{badania własne.}
\end{wykres}
```

### Bibliografia

Pozycje bibliograficzne dodawaj do pliku `src/bibliografia.bib`. W tekście cytuj
za pomocą `\cite{klucz}`:

```latex
Zgodnie z~badaniami~\cite{przyklad-ksiazka} stwierdzono, że...
```

Obsługiwane typy wpisów: `@book`, `@article`, `@inproceedings`, `@online`, `@misc` i inne
zgodne ze standardem BibLaTeX.

### Polecenie `\zrodlo{}`

Pod każdą ryciną, tabelą i wykresem należy podać źródło. Polecenie `\zrodlo{}`
automatycznie formatuje je czcionką 10pt ze słowem *Źródło:* kursywą:

```latex
\zrodlo{opracowanie własne na podstawie~\cite{klucz-bib}.}
```

## Formatowanie (podsumowanie wytycznych)

| Element | Rozmiar | Styl |
|---------|---------|------|
| Tekst główny | 12pt | Times New Roman, interlinia 1.5, justowanie |
| Tytuły rozdziałów | 14pt | pogrubione, wersaliki, wyśrodkowane |
| Tytuły podrozdziałów | 13pt | pogrubione |
| Tytuły pod-podrozdziałów | 12pt | pogrubione |
| Przypisy dolne | 10pt | — |
| Tekst w tabelach | 10pt | interlinia 1.0 |
| Podpisy rycin/tabel/wykresów | 12pt | pogrubione, wyrównane do lewej, interlinia 1.0 |
| Źródła | 10pt | *Źródło:* kursywą |
| Marginesy | — | lewy 3.5 cm, prawy 3.5 cm, górny 2.5 cm, dolny 2.5 cm |
| Wcięcie akapitowe | — | 1.25 cm |
| Numeracja stron | — | dół strony, środek (brak na stronie tytułowej) |
