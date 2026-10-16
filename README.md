# System Inteligentnej Analizy i Kategoryzacji Paragonów Fiskalnych

## Opis projektu
Projekt to kompletny potok przetwarzania danych (End-to-End Pipeline) zaprojektowany do automatycznego odczytywania, czyszczenia i klasyfikowania produktów z surowych zdjęć paragonów sklepowych. Aplikacja łączy zaawansowane algorytmy wizji komputerowej (Computer Vision) z autorskim modelem Głębokiego Uczenia (Deep Learning) do analizy tekstu.

System został zaprojektowany zgodnie z dobrymi praktykami inżynierii oprogramowania (OOP) oraz wdrażania modeli AI (MLOps) – proces uczenia jest całkowicie odseparowany od procesu wnioskowania (Inference).

**Autorzy:** Stanisław Pawlak, Piotr Kosiarski

---

## Architektura Systemu (OOP Pipeline)
Potok przetwarzania został podzielony na niezależne, obiektowe moduły:

1. **Ładowanie obrazu (`RealImageLoader`):** Bezpieczne wczytywanie surowych zdjęć z obsługą błędów.
2. **Preprocessing Wizyjny (`Preprocessor`):** Moduł OpenCV automatycznie wykrywający orientację tekstu (OSD), obracający zdjęcie, skalujący je do optymalnej rozdzielczości oraz nakładający filtry Gaussa i binaryzację w celu maksymalizacji skuteczności OCR.
3. **Silnik OCR (`RealOCREngine`):** Ekstrakcja tekstu przy użyciu silnika Tesseract (LSTM) zoptymalizowanego pod język polski i niestandardowy układ kolumn (PSM 4).
4. **Inteligentny Parser (`Parser`):** Wykorzystanie wyrażeń regularnych (Regex) oraz dopasowania rozmytego (Fuzzy Matching). Parser potrafi rozpoznać strukturę paragonu (np. format Lidl vs Żabka), odcinając nagłówki sklepu, stopki fiskalne oraz numery NIP, zostawiając czystą listę zakupów.
5. **Klasyfikator AI (`Classifier`):** Dynamiczny moduł wnioskowania zasilany autorską siecią neuronową napisaną w PyTorch. Wykorzystuje algorytm **Hashing Trick (MurmurHash3)** do wektoryzacji tekstu, co uodparnia model na literówki i błędy OCR.
6. **Wizualizator (`Visualizer`):** Prezentacja wyników końcowych na czytelnym zestawieniu graficznym.

---

## Model i Uczenie (Machine Learning)
Aby osiągnąć maksymalną skuteczność i zapobiec przeuczeniu (Overfitting), wdrożono następujące sposoby trenowania modeli:

* **Generator Danych Syntetycznych:** Z powodu braku dużych publicznych baz paragonów, stworzono własny generator tworzący 20 000 unikalnych próbek. Symuluje on typowe zniekształcenia OCR (np. "1" zamiast "l", ucięte końcówki wyrazów).
* **Optymalizacja Bayesowska (Optuna):** Architektura sieci nie jest wpisana na sztywno. Użyto biblioteki Optuna do inteligentnego poszukiwania hiperparametrów (rozmiar wektora, liczba neuronów, Learning Rate, Dropout).
* **Cross-Validation:** Skuteczność każdego zestawu parametrów oceniana była za pomocą Stratyfikowanej 3-krotnej Walidacji Krzyżowej (Stratified 3-Fold CV).
* **Dynamiczny Inference:** Zwycięski model zapisuje swoje wagi do pliku `.pth`, a architekturę do pliku `model_config.json`. Główny Pipeline buduje sieć dynamicznie na podstawie pliku konfiguracyjnego.

---

## Jak uruchomić projekt?

### 1. Wymagania
* Python oraz podane biblioteki w pliku requirements.txt
* Zainstalowany silnik **Tesseract-OCR** w systemie operacyjnym. (Dla Windows domyślnie jest to plik `.exe`. Pamiętaj o dodaniu go do zmiennych środowiskowych PATH lub wskazaniu ścieżki w kodzie).

### 2. Instalacja
Sklonuj repozytorium na swój dysk lokalny, a następnie zainstaluj wszystkie wymagane biblioteki za pomocą menedżera pakietów `pip`:

```bash
git clone [https://github.com/stanislawpawlak/receipt-ocr-pytorch.git](https://github.com/stanislawpawlak/receipt-ocr-pytorch.git)
cd receipt-ocr-pytorch
pip install -r requirements.txt

