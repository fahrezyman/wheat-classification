# Klasifikasi Kualitas Biji Gandum (Wheat Kernel Classification)

Repository ini berisi notebook untuk klasifikasi kualitas biji gandum ke dalam 4 kelas menggunakan **ekstraksi fitur tekstur (GLCM)** dan **fitur warna (RGB & HSV)**, yang kemudian diklasifikasikan menggunakan **Support Vector Machine (Linear SVM)**. Repository ini dibuat sebagai lampiran skripsi.

## Kelas yang Diklasifikasikan

| Kelas | Deskripsi | Jumlah Gambar |
|---|---|---|
| `healthy_kernel` | Biji gandum sehat | 500 |
| `green_immature` | Biji gandum belum matang (hijau) | 500 |
| `shrunken_broken` | Biji gandum keriput / pecah | 500 |
| `insect_chewed` | Biji gandum bekas gigitan serangga | 500 |

Total: **2000 gambar** (format `.jpg`).

## Struktur Repository

```
.
├── src/
│   ├── notebook.ipynb          # Notebook utama (ekstraksi fitur, training, evaluasi)
│   └── dataset/
│       ├── healthy_kernel/
│       ├── green_immature/
│       ├── shrunken_broken/
│       └── insect_chewed/
├── requirements.txt             # Daftar dependency Python
└── README.md
```

> Catatan: file hasil ekstraksi fitur (`features_corrected.xlsx` / `.csv`) **tidak** disertakan di repository ini. File tersebut akan otomatis dibuat ulang di folder root saat notebook dijalankan (lihat bagian [Alur Notebook](#alur-notebook)).

## Requirements

- Python **3.10** atau lebih baru
- Dependency (lihat [requirements.txt](requirements.txt)):
  - `numpy`, `pandas`
  - `opencv-python` — membaca & pra-pemrosesan citra
  - `scikit-image` — ekstraksi fitur GLCM
  - `scikit-learn` — preprocessing, split data, cross validation, SVM, evaluasi
  - `matplotlib` — visualisasi (confusion matrix, dll.)
  - `openpyxl` — menyimpan hasil ke `.xlsx`
  - `jupyterlab` — menjalankan notebook

## Cara Menjalankan

1. **Clone repository**

   ```bash
   git clone <url-repo-ini>
   cd wheat-classification
   ```

2. **Buat virtual environment** (opsional tapi disarankan)

   ```bash
   python -m venv .venv
   # Windows
   .venv\Scripts\activate
   # macOS/Linux
   source .venv/bin/activate
   ```

3. **Install dependency**

   ```bash
   pip install -r requirements.txt
   ```

4. **Jalankan Jupyter**

   ```bash
   jupyter lab
   ```

   Buka `src/notebook.ipynb`, lalu jalankan seluruh cell secara berurutan dari atas ke bawah (menu **Run → Run All Cells**, atau `jupyter nbconvert --to notebook --execute --inplace src/notebook.ipynb` dari terminal).

   Dataset pada folder `src/dataset/` sudah termasuk di repository ini sehingga notebook dapat langsung membaca gambar tanpa konfigurasi tambahan.

## Alur Notebook

`src/notebook.ipynb` terdiri dari 16 tahap:

1. Import library
2. Memuat & resize seluruh gambar dari `src/dataset/` (225×225 px)
3. Ekstraksi fitur tekstur GLCM (contrast, dissimilarity, homogeneity, energy, correlation)
4. Ekstraksi fitur warna — channel Green (RGB)
5. Ekstraksi fitur warna — channel Value (HSV)
6. Penggabungan seluruh fitur menjadi satu vektor per gambar
7. Ekstraksi fitur untuk seluruh dataset (2000 gambar)
8. Menyusun fitur & label ke dalam `pandas.DataFrame`
9. Pemeriksaan kualitas data (missing value, tipe data, statistik deskriptif) & menyimpan hasil ekstraksi ke `features_corrected.xlsx` / `.csv` di root project
10. Cek distribusi label antar kelas
11. Split data training (80%) / testing (20%) secara stratified
12. 5-Fold Stratified Cross Validation pada data training
13. Feature scaling (`StandardScaler`, fit hanya pada data training)
14. Training model final `LinearSVC`
15. Evaluasi model pada test set (accuracy, classification report, confusion matrix)
16. Pengujian skenario kombinasi fitur (GLCM saja, warna saja, gabungan, gabungan tanpa normalisasi)

## Output

Setelah dijalankan penuh, notebook akan menghasilkan:

- `features_corrected.xlsx` dan `features_corrected.csv` di root project — tabel fitur hasil ekstraksi (GLCM + warna) beserta labelnya.
- Classification report & confusion matrix untuk model SVM pada test set.
- Ringkasan perbandingan akurasi antar skenario kombinasi fitur (cross validation).
