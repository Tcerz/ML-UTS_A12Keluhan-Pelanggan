# Prediksi Respons Perusahaan terhadap Pengaduan Konsumen CFPB

**Project ID:** A12  
**Kelompok:** Keluhan Pelanggan    
**Mata kuliah:** Pembelajaran Mesin  
**Tahap:** Milestone 1 - Ujian Tengah Semester (UTS), proyek Text Mining  
**Perguruan tinggi:** Universitas Mikroskil - S-1 Teknik Informatika

## Anggota Kelompok

| NIM | Nama |
|---|---|
| 231110937 | Fauzan Ramadhan Harahap |
| 231111582 | Eka Satria |
| 231111710 | Mhd. Hatami |

## Deskripsi Proyek

Proyek ini memprediksi kategori **`Company response to consumer`** pada pengaduan konsumen dari **Consumer Financial Protection Bureau (CFPB)**. Masalah dirumuskan sebagai **klasifikasi multikelas** menggunakan *classical machine learning*. Tujuannya adalah mengevaluasi sejauh mana informasi yang tersedia saat pengaduan masuk dapat digunakan untuk memperkirakan kategori respons perusahaan, serta memahami keterbatasan data dan model.

Berkas yang digunakan dalam eksperimen UTS mencakup **1.048.575 baris dan 15 kolom**. Setelah mengeluarkan dua baris tanpa target, tersedia 1.048.573 observasi; eksperimen pemodelan menggunakan sampel **300.000 baris secara stratified**. Dataset memiliki **lima kelas** dengan ketidakseimbangan berat. Angka ini menggambarkan **salinan data yang dipakai pada UTS**, bukan ukuran pasti seluruh database CFPB.

### Fitur dan target

- **Target:** `Company response to consumer`.
- **Teks terstruktur:** gabungan `Product`, `Sub-product`, `Issue`, dan `Sub-issue`.
- **Fitur kategorikal:** `Company`, `State`, `Submitted via`, dan `Tags`.
- **Dikecualikan dari prediktor:** `Timely response?` dan `Company public response`, karena informasi tersebut baru tersedia bersamaan dengan atau setelah respons perusahaan (*data leakage*).

Kolom `Consumer complaint narrative` tidak tersedia pada berkas eksperimen ini. Karena itu, penelitian menggunakan teks yang dibentuk dari kategori masalah, **bukan narasi bebas pengaduan**.

## Metode dan Tahapan

1. **Problem Formulation:** menetapkan tujuan, input, target, dan tipe masalah.
2. **Data Understanding dan EDA:** audit struktur dataset, kualitas data, distribusi kelas, panjang teks, dan hubungan fitur–target.
3. **Preprocessing:** pembersihan teks, penanganan nilai hilang, dan pemisahan data 60% latih, 20% validasi, 20% uji; transformasi TF-IDF dan one-hot encoding di dalam *pipeline* untuk mencegah kebocoran data.
4. **Baseline:** *DummyClassifier* serta TF-IDF teks dengan Logistic Regression.
5. **Model Kandidat:** Logistic Regression dengan fitur terstruktur, Multinomial Naive Bayes, Linear SVM, Random Forest, serta *voting ensemble*.
6. **Evaluasi:** F1-macro sebagai metrik utama; akurasi, balanced accuracy, MCC, dan PR-AUC sebagai metrik tambahan. Validasi menggunakan *Stratified 5-Fold* dan uji ketahanan *GroupKFold*.
7. **Analisis Lanjutan Milestone 1:** penanganan ketidakseimbangan kelas, eksplorasi tanpa label (LSA/K-Means/NMF), seleksi fitur, *hyperparameter tuning*, analisis kesalahan, dan evaluasi akhir pada data uji.

Model terbaik pada **pengujian akhir UTS** mencapai **akurasi 0,6891** dan **F1-macro 0,5323**, dibandingkan F1-macro **0,3277** untuk baseline. Model ini adalah **model terbaik sementara**, bukan klaim model siap diterapkan pada populasi CFPB secara umum.

## Struktur Repository

```text
ML-UTS_A12Keluhan-Pelanggan/
├── README.md                       # Penjelasan proyek dan cara penggunaan
├── A12 Keluhan Pelanggan.ipynb     # Notebook eksperimen utama
├── requirements.txt                # Dependensi untuk menjalankan notebook
├── Dataset/
│   ├── Link Dataset.txt            # Tautan sumber resmi CFPB (sudah ada)
│   └── README.md                   # Dokumentasi dan cara memperoleh dataset
├── Artifact/
│   └── Laporan Milestone UTS A12 Keluhan Pelanggan.pdf
└── Outputs/
    ├── 01_Gambar_Notebook/         # Grafik dari eksekusi notebook
    ├── 02_Tabel_CSV/               # Tabel eksperimen dalam format CSV
    ├── 03_Output_Teks/             # Output konsol / teks
    ├── 04_Gambar_Laporan/          # Gambar yang dipakai dalam laporan
    └── A12 Keluhan Pelanggan - Colab Outputs.pdf
```

Struktur di atas mengikuti berkas yang diperiksa pada branch `main` dan menambahkan berkas dokumentasi yang disediakan bersama README ini.

## Dataset

Dataset bersumber dari **CFPB Consumer Complaint Database**:  
https://www.consumerfinance.gov/data-research/consumer-complaints/

**Berkas CSV lengkap tidak diunggah** ke GitHub karena ukurannya besar. Tautan sumber resmi, petunjuk unduh, daftar kolom, batasan kualitas, serta cara mengatur lokasi file dijelaskan pada [`Dataset/README.md`](Dataset/README.md). Folder `Dataset/` berisi **dokumentasi**, bukan seluruh data mentah.

> **Penting:** Salinan UTS berisi tepat 1.048.575 baris, sama dengan batas jumlah baris data ketika CSV tersimpan melalui Microsoft Excel (di luar header). Notebook menandainya sebagai **kemungkinan data terpotong**. Data hasil pengunduhan baru dari CFPB dapat memiliki jumlah baris, rentang waktu, distribusi kelas, dan hasil eksperimen yang berbeda. Jangan menganggap eksperimen dapat direproduksi persis hanya dari tautan dataset terkini.

## Cara Menjalankan Notebook (Google Colab)

1. Unduh atau *clone* repository ini, kemudian buka **`A12 Keluhan Pelanggan.ipynb`** di Google Colab.
2. Unduh dataset CSV dari sumber resmi sesuai petunjuk [`Dataset/README.md`](Dataset/README.md). **Jangan buka lalu simpan ulang CSV besar melalui Microsoft Excel**, karena risiko pemotongan baris.
3. Unggah file CSV ke Google Drive pada lokasi yang sesuai, misalnya `MyDrive/Dataset/complaints.csv`.
4. Pada bagian **`0. Setup & Konfigurasi`**, sesuaikan variabel:

   ```python
   DATA_PATH = "/content/drive/MyDrive/Dataset/complaints.csv"
   SAMPLE_N = 300_000
   CV_SAMPLE_N = 60_000
   TUNE_SAMPLE_N = 60_000
   RANDOM_STATE = 42
   ```

5. Bila menjalankan di lingkungan Python lokal, instal dependensi dari `requirements.txt`; sesuaikan `DATA_PATH` ke lokasi CSV pada komputer. Notebook menggunakan mekanisme *mount* Google Drive bila berjalan di Colab.
6. Jalankan sel secara berurutan atau pilih **Runtime → Run all**. Proses pelatihan, validasi silang, dan tuning dapat memerlukan sumber daya komputasi yang cukup besar.
7. Bandingkan output baru dengan file di folder [`Outputs/`](Outputs/) dan penjelasannya dalam [`Artifact/`](Artifact/). Perbedaan dapat terjadi bila sumber data atau versi pustaka berubah.

Notebook menggunakan `pandas`, `numpy`, `scipy`, `scikit-learn`, `matplotlib`, `seaborn`, dan `joblib`. Lingkungan Colab pada eksperimen mencatat `pandas 2.2.3` dan `scikit-learn 1.6.1`.

## Konfigurasi Eksperimen UTS

| Parameter | Nilai |
|---|---|
| `RANDOM_STATE` | `42` |
| `SAMPLE_N` | `300_000` |
| `CV_SAMPLE_N` | `60_000` |
| `TUNE_SAMPLE_N` | `60_000` |
| `CV_FOLDS` | `5` |
| `N_ITER_TUNE` | `12` |
| `TEST_SIZE` | `0.20` |
| `VAL_SIZE` | `0.25` dari data non-test |
| `MAX_FEATURES` | `5000` |

## Hasil Eksperimen dan Artefak

- **Notebook:** seluruh alur eksperimen dari Pertemuan 1 hingga evaluasi UTS.
- **`Outputs/01_Gambar_Notebook/`:** grafik dan visualisasi hasil eksekusi.
- **`Outputs/02_Tabel_CSV/`:** keluaran tabel eksperimen dalam format CSV.
- **`Outputs/03_Output_Teks/`:** laporan klasifikasi, catatan, dan keluaran konsol.
- **`Outputs/04_Gambar_Laporan/`:** visual yang dipakai dalam laporan.
- **`Outputs/A12 Keluhan Pelanggan - Colab Outputs.pdf`:** dokumentasi keluaran Colab dalam PDF.
- **`Artifact/Laporan Milestone UTS A12 Keluhan Pelanggan.pdf`:** laporan utama Milestone 1.

**Interpretasi:** metrik akhir diperoleh pada data kerja yang tersedia saat UTS dan tidak menjamin performa pada semua jenis pengaduan CFPB. Data tidak lengkap dan beberapa kombinasi fitur identik memiliki kelas respons berbeda.

## Keterbatasan

1. Berkas data UTS **kemungkinan terpotong** akibat batas baris Excel; rentang pengaduannya tidak mewakili distribusi waktu penuh CFPB.
2. Tidak ada teks narasi pengaduan, sehingga informasi tekstual relatif terbatas.
3. Distribusi lima kelas tidak seimbang; angka akurasi tidak boleh menjadi satu-satunya tolok ukur.
4. Pengaduan dengan fitur input sama dapat memiliki respons yang berbeda.
5. Data uji telah digunakan sekali pada UTS; eksperimen berikutnya membutuhkan protokol evaluasi baru.

## Sumber dan Penggunaan

- **Sumber dataset:** CFPB Consumer Complaint Database — https://www.consumerfinance.gov/data-research/consumer-complaints/
- **Dokumentasi dataset:** [`Dataset/README.md`](Dataset/README.md)
- **Notebook dan laporan:** artefak akademik Kelompok A12, Universitas Mikroskil, untuk Milestone 1 UTS Pembelajaran Mesin.

Jika menggunakan atau mendistribusikan ulang data, periksa ketentuan sumber resmi CFPB dan jangan memasukkan informasi pribadi konsumen yang tidak diperlukan ke repository publik.
