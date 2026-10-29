# Klasifikasi Judul Berita Clickbait Berbahasa Indonesia Menggunakan TF-IDF dan Multilayer Perceptron (MLP)

**Proyek Ujian Tengah Semester (UTS) — Kelompok 2**
Mata Kuliah: Deep Learning  
Jalur: B — Teks/Sekuens

## 1. Deskripsi Proyek

Proyek ini bertujuan untuk membangun model klasifikasi judul berita berbahasa Indonesia ke dalam dua kategori, yaitu **clickbait** dan **non-clickbait**, menggunakan pendekatan *Deep Learning*.

Dataset yang digunakan adalah **CLICK-ID**, dengan pemilihan data berdasarkan kesepakatan ketiga anotator (*All-Agree*). Data diproses melalui tahapan preprocessing teks dan ekstraksi fitur menggunakan **Term Frequency-Inverse Document Frequency (TF-IDF)**.

Model utama yang dikembangkan adalah **Multilayer Perceptron (MLP)**, dengan **Logistic Regression** dan **Naive Bayes** sebagai model baseline untuk membandingkan performa klasifikasi.

Proyek ini merupakan tahap awal pengembangan sistem klasifikasi clickbait pada UTS, yang direncanakan untuk dikembangkan lebih lanjut menggunakan model sekuensial pada UAS.

## 2. Dataset

Dataset yang digunakan adalah **CLICK-ID**, dataset klasifikasi judul berita clickbait berbahasa Indonesia.

- **Sumber:** [CLICK-ID — Mendeley Data](https://data.mendeley.com/datasets/k42j7x2kpn/1)
- **Referensi:** William & Sari (2020)
- **Lisensi dataset:** CC BY 4.0
- **Jumlah data awal CLICK-ID:** 15.000 judul berita
- **Data All-Agree:** 8.613 judul berita
- **Data akhir setelah preprocessing dan penyaringan:** 8.596 judul berita

### Distribusi Dataset Akhir

| Kategori | Label | Jumlah |
|---|---|---:|
| Non-clickbait | 0 | 5.285 |
| Clickbait | 1 | 3.311 |
| **Total** | | **8.596** |

Data akhir diperoleh setelah pemeriksaan konflik label, penyaringan judul duplikat, dan preprocessing teks.

## 3. Metodologi

### 3.1 Preprocessing Teks

Proses preprocessing dilakukan melalui dua tahapan utama:

1. **Case Folding:** Mengubah seluruh huruf menjadi lowercase untuk menyeragamkan format teks.
2. **Cleaning Karakter:** Membersihkan karakter yang tidak diperlukan, mengatur tanda baca, dan merapikan spasi berlebih. Angka serta tanda seru dan tanda tanya tetap dipertahankan pada teks hasil cleaning.

Sebelum dan setelah preprocessing, dilakukan pemeriksaan kualitas data untuk menangani judul duplikat dan konflik label.

### 3.2 Pembagian Dataset

Dataset dibagi menggunakan metode *stratified splitting* untuk mempertahankan proporsi kelas pada setiap subset.

| Subset | Proporsi | Jumlah |
|---|---:|---:|
| Training | 70% | 6.016 |
| Validation | 10% | 860 |
| Testing | 20% | 1.720 |
| **Total** | **100%** | **8.596** |

Pembagian dataset menggunakan `random_state=42`.

### 3.3 Ekstraksi Fitur TF-IDF

Teks judul berita diubah menjadi representasi numerik menggunakan TF-IDF dengan konfigurasi:

| Parameter | Nilai |
|---|---|
| Maximum features | 10.000 |
| N-gram range | (1, 2) |
| Minimum document frequency | 2 |
| Maximum document frequency | 0,95 |
| Sublinear TF | True |

TF-IDF hanya di-*fit* menggunakan data training untuk mencegah *data leakage* dari validation dan test set.

### 3.4 Model Klasifikasi

Tiga model yang digunakan dalam eksperimen adalah:

- **Logistic Regression:** Model baseline klasifikasi linear.
- **Multinomial Naive Bayes:** Model baseline probabilistik.
- **Multilayer Perceptron (MLP):** Model utama berbasis jaringan saraf tiruan.

Arsitektur MLP menggunakan konfigurasi berikut:

| Komponen | Konfigurasi |
|---|---|
| Input layer | Fitur TF-IDF |
| Hidden layer | Dense 64 neuron, ReLU |
| Regularisasi | L2 = 0,0001 |
| Dropout | 0,4 |
| Output layer | Dense 1 neuron, Sigmoid |
| Optimizer | Adam |
| Learning rate awal | 0,0003 |
| Loss function | Binary Cross-Entropy |
| Batch size | 64 |
| Maximum epochs | 50 |
| EarlyStopping | Patience 7, monitor val_loss |
| ReduceLROnPlateau | Factor 0,5, patience 3 |

Pembobotan kelas (*class weight*) diterapkan pada pelatihan MLP untuk membantu menangani ketidakseimbangan jumlah sampel antarkelas.

## 4. Hasil Eksperimen

Evaluasi dilakukan pada test set menggunakan metrik accuracy, precision, recall, F1-score, serta confusion matrix.

### Perbandingan Performa Model

| Model | Accuracy | Precision | Recall | F1-Score | F1 Macro |
|---|---:|---:|---:|---:|---:|
| Logistic Regression | 88,37% | 94,26% | 74,36% | 83,14% | 87,13% |
| Naive Bayes | 85,81% | 89,01% | 72,10% | 79,67% | 84,39% |
| **MLP** | **89,13%** | 86,84% | **84,62%** | **85,71%** | **88,47%** |

*Precision, recall, dan F1-score pada tabel dihitung berdasarkan kelas clickbait (label 1).*

### Hasil Training MLP

- **Epoch yang dijalankan:** 40
- **Epoch terbaik:** 33
- **Validation loss terbaik:** 0,3565
- **Test accuracy:** 89,13%
- **F1-score clickbait:** 85,71%

Berdasarkan hasil evaluasi, MLP memperoleh accuracy, recall, dan F1-score clickbait yang lebih tinggi dibandingkan kedua model baseline. Sementara itu, Logistic Regression memperoleh precision clickbait tertinggi.

Visualisasi kurva loss, kurva accuracy, dan confusion matrix tersedia pada berkas hasil evaluasi proyek.

## 5. Struktur Repository

Repository disusun mengikuti struktur pengumpulan proyek UTS.

```text
repository/
├── data/
│   ├── main_dataset.xlsx
│   └── dataset_preprocessed.csv
│
├── notebooks/
│   └── UTS_DeepLearning.ipynb
│
├── models/
│   ├── mlp_model.keras
│   ├── logistic_regression.pkl
│   ├── naive_bayes.pkl
│   └── tfidf_vectorizer.pkl
│
├── reports/
│   ├── Kelompok2_UTS_Laporan_Kemajuan_DeepLearning.pdf
│   ├── history_mlp.csv
│   ├── tabel_perbandingan.csv
│   ├── classification_report_mlp.csv
│   ├── confusion_matrix_mlp.csv
│   ├── kurva_loss_mlp.png
│   ├── kurva_accuracy_mlp.png
│   └── confusion_matrix_mlp.png
│
└── README.md
```

**Keterangan:**

- `data/`: Dataset asli dan dataset hasil preprocessing.
- `notebooks/`: Notebook utama untuk preprocessing, training, dan evaluasi.
- `models/`: Model terlatih dan TF-IDF vectorizer.
- `reports/`: Laporan UTS, hasil evaluasi, serta visualisasi.
- `README.md`: Dokumentasi proyek dan petunjuk menjalankan notebook.

File perantara seperti matriks fitur `.npz` dan label `.npy` dapat dihasilkan kembali melalui notebook. Hasil eksperimen lengkap juga dapat diunduh dalam bentuk ZIP setelah notebook dijalankan.

## 6. Persyaratan dan Library

Proyek dikembangkan menggunakan **Python 3** dan dijalankan melalui **Google Colab**.

Library utama yang digunakan:

- Pandas
- NumPy
- SciPy
- Scikit-learn
- TensorFlow/Keras
- Matplotlib
- Joblib
- OpenPyXL

Sebagian besar library tersedia secara bawaan pada Google Colab.

Jika menjalankan proyek pada lingkungan Python lokal, dependensi dapat dipasang menggunakan:

```bash
pip install pandas numpy scipy scikit-learn tensorflow matplotlib joblib openpyxl notebook
```

Versi library dapat memengaruhi kompatibilitas maupun hasil komputasi. Google Colab merupakan lingkungan yang direkomendasikan untuk menjalankan notebook proyek ini.

## 7. Cara Menjalankan Proyek

### 7.1 Menjalankan melalui Google Colab

1. Buka [Google Colab](https://colab.research.google.com/).
2. Pilih **File → Open notebook → GitHub**.
3. Masukkan URL repository proyek atau cari notebook `UTS_DeepLearning.ipynb`.
4. Buka notebook dan pilih runtime Python.
5. Jalankan seluruh sel secara berurutan menggunakan **Runtime → Run all**.

Notebook mencakup proses:

- Persiapan lingkungan dan import library.
- Pengambilan dataset.
- Pemeriksaan kualitas data dan preprocessing.
- Pembagian dataset serta ekstraksi fitur TF-IDF.
- Pelatihan Logistic Regression, Naive Bayes, dan MLP.
- Evaluasi dan visualisasi performa.
- Pengemasan dan pengunduhan hasil eksperimen.

### 7.2 Pengambilan Dataset

Notebook dirancang agar dapat mengambil dataset secara otomatis melalui URL raw file dari repository GitHub.

Jika proses pengunduhan berhasil, dataset akan langsung tersimpan pada direktori sementara Google Colab dan digunakan untuk preprocessing.

Apabila pengunduhan gagal atau file tidak valid, notebook menyediakan alternatif **upload manual** menggunakan file `main_dataset.xlsx`.

Dengan demikian, pengguna tidak perlu menghubungkan Google Drive maupun menyesuaikan path dataset secara manual.

### 7.3 Hasil Eksekusi

Setelah seluruh proses selesai, notebook menghasilkan dataset preprocessing, model terlatih, riwayat training, tabel evaluasi, dan visualisasi.

Hasil dapat diunduh melalui file:

`kelompok2_hasil_uts_clickbait.zip`

Arsip tersebut terdiri atas dua direktori utama:

- `data/`: Dataset asli dan dataset hasil preprocessing.
- `models/`: Model terlatih, fitur dan label hasil transformasi, riwayat training, metrik evaluasi, serta visualisasi.

File hasil eksekusi disimpan sementara di runtime Google Colab. Oleh karena itu, hasil perlu diunduh sebelum sesi runtime berakhir.

## 8. Rencana Pengembangan UAS

Pengembangan selanjutnya direncanakan menggunakan pendekatan **word embedding dan Long Short-Term Memory (LSTM)** untuk mempelajari hubungan sekuensial dalam teks judul berita.

Model LSTM akan dibandingkan dengan MLP yang telah dikembangkan pada tahap UTS. Pengembangan juga direncanakan mencakup penyempurnaan evaluasi, analisis kesalahan prediksi, dan pembuatan demonstrasi aplikasi klasifikasi judul berita.

## 9. Referensi dan Atribusi

- William, A., & Sari, Y. (2020). **CLICK-ID: A Novel Dataset for Indonesian Clickbait Headlines.**
- Dataset CLICK-ID: https://data.mendeley.com/datasets/k42j7x2kpn/1
- Dokumentasi TensorFlow: https://www.tensorflow.org/
- Dokumentasi Scikit-learn: https://scikit-learn.org/stable/

Dataset CLICK-ID digunakan dengan mengacu pada lisensi **Creative Commons Attribution 4.0 International (CC BY 4.0)**. Atribusi dataset tidak secara otomatis menetapkan lisensi yang sama untuk kode proyek ini.

---

**Kelompok 2 — Pengantar Algoritma Deep Learning**  
*Proyek Ujian Tengah Semester (UTS)*