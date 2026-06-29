# Sistem de Recunoaștere a Gesturilor Mâinii Bazat pe Procesareea Imaginilor

Proiect de licență care implementează un sistem de recunoaștere a gesturilor mâinii, folosind MediaPipe pentru extragerea landmark-urilor și mai mulți algoritmi de clasificare (KNN, SVM, Random Forest, Logistic Regression, XGBoost). Sistemul suportă atât predicții pe imagini statice cât și prin cameră video.

---

## Structura proiectului

```
Proiect_Licenta/
├── src/
│   ├── esantionare.py # echilibrare dataset (max 2000 imagini/clasă)
│   ├── collect_data.py # extrage landmark-uri și le pune în gestures.csv
│   ├── train.py # antrenare modele → fișiere .pkl
│   ├── compare.py # comparare algoritmi + grafice
│   ├── interfata.py # interfața grafică (cameră + imagini statice)
│   └── test.py # script de debug pentru o singură imagine
├── dataset/
│   ├── call/
│   ├── dislike/
│   └── ...
├── data/
│   └── gestures.csv
└── model/
    ├── model_knn.pkl
    ├── model_svm.pkl
    ├── model_random_forest.pkl
    ├── model_logistic.pkl
    ├── model_xgboost.pkl
    └── comparatie/
        ├── 1_acuratete_comparatie.png
        ├── 2_confusion_matrices.png
        ├── 3_f1_per_clasa.png
        └── 4_timp_antrenare.png
```

---

## Tehnologii folosite

| Tehnologie | Rol |
|---|---|
| Python 3 | limbajul principal |
| MediaPipe | detectarea și extragerea landmark-urilor mâinii |
| OpenCV | citirea și procesarea imaginilor |
| scikit-learn | algoritmii de clasificare și metricile de evaluare |
| XGBoost | algoritm suplimentar de clasificare |
| pandas / numpy | manipularea datelor |
| matplotlib / seaborn | generarea graficelor comparative |
| customtkinter | interfața grafică |
| pickle | salvarea și încărcarea modelelor antrenate |

---

## Dependențe

```bash
pip install opencv-python mediapipe scikit-learn pandas numpy matplotlib seaborn customtkinter xgboost
```

---

## Pipeline — ordinea de rulare

### 1. Pregătire dataset

Imaginile trebuie organizate în foldere, câte un folder per clasă de gest:

```
dataset/
├── call/
│   ├── img1.jpg
│   └── ...
├── dislike/
│   └── ...
```

### 2. Eșantionare (opțional, dacă ai prea multe imagini)

```bash
python esantionare.py
```

Păstrează aleator **2000 de imagini** per clasă și șterge restul. Rulează înainte de `collect_data.py`.

### 3. Extragere features

```bash
python collect_data.py
```

Parcurge toate imaginile din `dataset/`, detectează landmarks-urile mâinii cu MediaPipe și salvează coordonatele (21 x + 21 y) în `data/gestures.csv`.

### 4. Antrenare modele

```bash
# antrenează toți algoritmii
python train.py

# sau un singur algoritm
python train.py --algorithm knn
python train.py --algorithm svm
python train.py --algorithm random_forest
python train.py --algorithm logistic
python train.py --algorithm xgboost
```

Modelele sunt salvate în `model/` ca fișiere `.pkl`.

### 5. Comparare algoritmi (opțional)

```bash
python compare.py
```

Reantrenează toți algoritmii de la zero și generează 4 grafice în `model/comparatie/`:
- acuratețe globală
- matrici de confuzie normalizate
- F1-score per clasă
- timp de antrenare

### 6. Interfața grafică

```bash
python interfata.py
```

Deschide o fereastră cu două moduri de utilizare:
- **Start Camera** — predicție prin webcam, cadru cu cadru
- **Upload Image** — predicție pe o imagine din calculator

Algoritmul activ se selectează din dropdown; modelele sunt detectate automat din folderul `model/`.

---

## Debug

```bash
python test.py cale/catre/imagine.jpg
# sau fără argument → cere input manual
python test.py
```

Afișează dacă MediaPipe detectează mâna în imaginea respectivă și salvează imaginea adnotată cu landmarks-urile desenate.

---

## Format date

`gestures.csv` conține câte un rând per imagine procesată cu succes. Fiecare rând are eticheta clasei urmată de coordonatele celor 21 de landmarks detectați de MediaPipe — mai întâi toate coordonatele X, apoi toate coordonatele Y, normalizate între 0 și 1 (43 de coloane total).
