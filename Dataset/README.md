# Dokumentasi Dataset — CFPB Consumer Complaint Database

## 1. Identitas dan Sumber

| Aspek | Keterangan |
|---|---|
| Nama dataset | Consumer Complaint Database |
| Penyedia | Consumer Financial Protection Bureau (CFPB), Amerika Serikat |
| Sumber resmi | https://www.consumerfinance.gov/data-research/consumer-complaints/ |
| Format yang digunakan | CSV (`complaints.csv`) |
| Unit observasi | Satu pengaduan konsumen / `Complaint ID` per baris |
| Target klasifikasi | `Company response to consumer` |
| Jenis proyek | Text Mining, klasifikasi multikelas dengan classical ML |

Repository hanya menyimpan **tautan dan dokumentasi sumber**, bukan CSV besar. File `Link Dataset.txt` yang sudah tersedia di folder ini mengarah ke halaman CFPB yang benar.

## 2. Cara Memperoleh Dataset

1. Buka halaman [CFPB Consumer Complaint Database](https://www.consumerfinance.gov/data-research/consumer-complaints/).
2. Gunakan opsi pengunduhan data yang tersedia dari CFPB untuk mendapatkan CSV pengaduan konsumen. Antarmuka dan pilihan pengunduhan dapat berubah; ikuti petunjuk pada situs resmi.
3. Simpan berkas sebagai `complaints.csv` (ganti nama bila diperlukan tanpa mengubah isinya).
4. **Jangan buka lalu simpan kembali CSV besar menggunakan Microsoft Excel** karena Excel memiliki batas 1.048.576 baris lembar kerja termasuk header, yang dapat menyebabkan data terpotong.
5. Unggah CSV ke Google Drive, misalnya ke folder `MyDrive/Dataset/`, atau simpan lokal dan ubah `DATA_PATH` pada notebook.
6. Pastikan dataset memiliki kolom `Company response to consumer`, `Product`, `Sub-product`, `Issue`, dan `Sub-issue` sebelum menjalankan eksperimen.

**Konfigurasi asli notebook:**

```python
DATA_PATH = "/content/drive/MyDrive/Dataset/complaints.csv"
```

Unduhan terbaru dari CFPB adalah dataset yang **dapat berubah dari waktu ke waktu**. Bila ingin mereproduksi angka UTS persis, diperlukan salinan data yang sama dengan yang digunakan saat eksperimen, berikut identitas berkas seperti ukuran dan SHA-256 (belum dicatat dalam artefak yang diperiksa).

## 3. Karakteristik Salinan yang Digunakan pada UTS

| Karakteristik | Hasil audit notebook |
|---|---|
| Baris data awal | 1.048.575 |
| Kolom | 15 |
| Target kosong | 2 baris |
| Baris setelah target kosong dikeluarkan | 1.048.573 |
| Sampel kerja pemodelan | 300.000 (stratified) |
| Pembagian sampel kerja | 180.000 train / 60.000 validation / 60.000 test |
| Rentang `Date received` | 2014-04-03 s.d. 2026-09-21 |
| Jumlah kelas target | 5 |
| `Consumer complaint narrative` | Tidak tersedia dalam salinan UTS |
| Mode teks notebook | `structured` (teks dari 4 kolom kategori masalah) |

**Keterbatasan kritis:** jumlah baris awal tepat sama dengan batas data Excel (1.048.575 baris, setelah memperhitungkan header). Hal ini **mengindikasikan kemungkinan pemotongan data**, tetapi tidak membuktikan dengan pasti bagaimana berkas diproses. Distribusi tahun juga sangat tidak merata, terutama 2023 dan 2026. Karena itu statistik serta metrik UTS **tidak dapat digeneralisasi begitu saja** ke keseluruhan basis data CFPB.

## 4. Kolom yang Relevan

| Kolom | Peran pada eksperimen |
|---|---|
| `Product` | Komponen teks |
| `Sub-product` | Komponen teks |
| `Issue` | Komponen teks |
| `Sub-issue` | Komponen teks |
| `Company` | Fitur kategorikal |
| `State` | Fitur kategorikal |
| `Submitted via` | Fitur kategorikal |
| `Tags` | Fitur kategorikal |
| `Company response to consumer` | **Target/label** |
| `Timely response?` | **Tidak digunakan**; berpotensi bocor karena pasca-respons |
| `Company public response` | **Tidak digunakan**; berpotensi bocor karena pasca-respons |
| `Complaint ID` | Identitas pengaduan untuk audit, bukan fitur pemodelan |
| `Date received` | Audit rentang waktu; bukan fitur pemodelan utama |
| `Date sent to company` | Tidak digunakan sebagai fitur |
| `ZIP code` | Tidak digunakan sebagai fitur |

Notebook mengisi nilai kosong pada fitur kategorikal dengan `Unknown`, menggabungkan empat kolom teks, lalu melakukan pembersihan teks sebelum TF-IDF. `TfidfVectorizer` dan `OneHotEncoder` di-*fit* dalam pipeline pada data latih untuk mengurangi risiko *data leakage*.

## 5. Kategori Target

Lima kelas yang digunakan adalah:

1. `Closed with explanation`
2. `Closed with monetary relief`
3. `Closed with non-monetary relief`
4. `In progress`
5. `Untimely response`

Distribusi antar kelas sangat tidak seimbang. Karena itu penelitian menekankan **F1-macro** serta metrik pendamping yang mempertimbangkan seluruh kelas.

## 6. Penyimpanan dan Reprodusibilitas

- **Jangan commit `complaints.csv` ke GitHub** hanya untuk memenuhi dokumentasi dataset; ukurannya besar dan data sumber sudah tersedia publik.
- Simpan data mentah pada penyimpanan lokal atau Google Drive, lalu ubah `DATA_PATH` bila perlu.
- Simpan dokumentasi URL resmi, tanggal pengunduhan, jumlah baris, dan SHA-256 jika nanti mengunduh ulang data. Detail tanggal pengunduhan dan checksum **belum tersedia**, sehingga tidak diisi secara asumtif di sini.
- Output yang sudah dihasilkan dalam eksperimen UTS tersedia pada folder [`../Outputs/`](../Outputs/).
- Jika mengunduh ulang dataset, jalankan audit pada awal notebook sebelum membandingkan hasil dengan laporan UTS.

### Catatan reproduksi untuk tim

| Metadata berkas asli UTS | Nilai |
|---|---|
| Tanggal unduh CSV UTS | 17 September 2026 |
| Ukuran CSV UTS (byte) | Ukuran asli 5GB namun saat dibuka dengan excel terjadi kompres otomatis menjadi kisaran 300MB-an |
| SHA-256 CSV UTS | Belum dicatat |
| Sumber CSV | CFPB Consumer Complaint Database |

## 7. Referensi

Consumer Financial Protection Bureau (CFPB). *Consumer Complaint Database*. https://www.consumerfinance.gov/data-research/consumer-complaints/
