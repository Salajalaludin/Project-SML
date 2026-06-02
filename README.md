# Segmentasi Pelanggan Online Retail II dengan RFM dan Clustering

Proyek ini melakukan segmentasi pelanggan pada dataset **Online Retail II** menggunakan pendekatan **RFM** (*Recency, Frequency, Monetary*) dan beberapa algoritma clustering. Karena dataset tidak memiliki label kelas bawaan, analisis ini termasuk **unsupervised learning murni**. Hasil akhir dipilih secara objektif melalui **Internal Criteria**, bukan berdasarkan satu algoritma yang ditentukan manual.

## Daftar Isi

- [Ringkasan Proyek](#ringkasan-proyek)
- [Dataset](#dataset)
- [Fitur RFM](#fitur-rfm)
- [Alur Analisis](#alur-analisis)
- [Algoritma Clustering](#algoritma-clustering)
- [Validasi Cluster](#validasi-cluster)
- [Rangkuman Hasil Validasi](#rangkuman-hasil-validasi)
- [Interpretasi Cluster Final](#interpretasi-cluster-final)
- [External Criteria](#external-criteria)
- [Struktur File](#struktur-file)
- [Cara Menjalankan](#cara-menjalankan)
- [Output](#output)

## Ringkasan Proyek

| Komponen | Keterangan |
|---|---|
| Dataset | Online Retail II |
| Periode data | 2009-2011 |
| Unit analisis | Pelanggan |
| Data awal | 1.067.371 transaksi |
| Data setelah cleaning | 805.549 transaksi |
| Customer unik | 5.878 pelanggan |
| Data RFM | 5.878 pelanggan |
| Jumlah cluster (`k`) | `k = 2` |
| Algoritma terpilih | K-Means |

Pemilihan `k = 2` didasarkan pada **Silhouette Method**, karena nilai rata-rata silhouette tertinggi berada pada `k = 2`. Dengan jumlah cluster ini, segmentasi dapat dibaca sebagai dua kelompok besar pelanggan: pelanggan lebih aktif/bernilai tinggi dan pelanggan kurang aktif/bernilai rendah.

## Dataset

Dataset yang digunakan adalah file:

```text
online_retail_II.xlsx
```

File tersebut berisi dua sheet transaksi:

```text
Year 2009-2010
Year 2010-2011
```

Kedua sheet digabungkan sebelum dilakukan preprocessing. Data kemudian dibersihkan dengan menghapus:

- transaksi tanpa `Customer ID`,
- transaksi retur atau invoice yang diawali huruf `C`,
- transaksi dengan `Quantity <= 0`,
- transaksi dengan `Price <= 0`.

## Fitur RFM

RFM digunakan untuk mengubah data transaksi menjadi data pelanggan. Setiap pelanggan direpresentasikan oleh tiga fitur utama:

| Fitur | Deskripsi | Interpretasi |
|---|---|---|
| Recency | Selisih hari sejak transaksi terakhir pelanggan | Semakin kecil, pelanggan semakin aktif |
| Frequency | Jumlah invoice unik pelanggan | Semakin besar, pelanggan semakin sering bertransaksi |
| Monetary | Total nilai belanja pelanggan | Semakin besar, pelanggan semakin bernilai |

Sebelum clustering, fitur RFM ditransformasi dan distandarisasi menggunakan `scale()` agar perbedaan skala antarfitur tidak mendominasi perhitungan jarak.

## Alur Analisis

1. Import dua sheet transaksi dari file Excel.
2. Gabungkan data transaksi 2009-2010 dan 2010-2011.
3. Periksa missing value (`NA`).
4. Bersihkan transaksi tidak valid.
5. Bentuk fitur RFM pada level pelanggan.
6. Transformasi dan scaling fitur RFM.
7. Tentukan jumlah cluster optimal menggunakan Elbow Method dan Silhouette Method.
8. Jalankan beberapa algoritma clustering.
9. Validasi hasil clustering menggunakan Internal Criteria.
10. Pilih algoritma terbaik ke dalam variabel `Cluster_Final`.
11. Interpretasikan profil cluster berdasarkan rata-rata RFM.

## Algoritma Clustering

| Algoritma | Keterangan | Fungsi R |
|---|---|---|
| K-Means | Clustering berbasis centroid | `kmeans()` |
| Gaussian Mixture Model | Clustering berbasis model probabilistik | `Mclust()` |
| Hierarchical Average | Clustering hierarkis dengan average linkage | `hclust(method = "average")`, `cutree()` |
| PAM / K-Medoids | Clustering berbasis medoid | `pam()` |
| DBSCAN | Clustering berbasis kepadatan dan deteksi noise | `dbscan()`, `kNNdistplot()` |

Semua algoritma diperlakukan sebagai kandidat. Hasil akhir dipilih berdasarkan kualitas validasi, bukan berdasarkan urutan pembahasan algoritma.

## Validasi Cluster

Karena dataset ini tidak memiliki label kelas bawaan, hasil clustering tidak dapat dinilai menggunakan akurasi terhadap label asli. Oleh karena itu, proyek ini **wajib menggunakan Internal Criteria** untuk menilai kualitas pembagian kelompok secara objektif.

Internal Criteria yang digunakan:

| Metrik | Arah Evaluasi | Fungsi R |
|---|---|---|
| Silhouette Width | Lebih besar lebih baik | `silhouette()` |
| Shadow Value | Lebih besar lebih baik | `cluster.stats()` |
| Davies-Bouldin Index | Lebih kecil lebih baik | `index.DB()` |
| Calinski-Harabasz Index | Lebih besar lebih baik | `cluster.stats()` |

Keterangan singkatan pada tabel hasil:

| Singkatan | Arti |
|---|---|
| `Sil` | Silhouette Width |
| `Shad` | Shadow Value |
| `DB` | Davies-Bouldin Index |
| `CH` | Calinski-Harabasz Index |
| `Rank` | Total ranking dari metrik internal |

Notebook dan Rmd membuat tabel `validasi_semua`, lalu menghitung ranking untuk tiap metrik:

```text
Rank.Silhouette
Rank.Davies.Bouldin
Rank.Calinski.Harabasz
Rank.Total
```

Algoritma dengan `Rank.Total` terbaik dipilih sebagai `Cluster_Final`. Pada hasil saat ini, algoritma yang terpilih adalah **K-Means**.

## Rangkuman Hasil Validasi

| Metode | N | K | Noise | Sil | Shad | DB | CH | Rank |
|---|---:|---:|---:|---:|---:|---:|---:|---:|
| K-Means | 5.878 | 2 | 0 | 0.4381 | 0.4381 | 0.9693 | 6205.27 | 5 |
| HClust Avg | 5.878 | 2 | 0 | 0.5815 | 0.5815 | 0.7459 | 151.62 | 7 |
| PAM | 5.878 | 2 | 0 | 0.4340 | 0.4340 | 0.9765 | 6161.99 | 8 |
| GMM | 5.878 | 2 | 0 | 0.2924 | 0.2924 | 1.0982 | 3056.20 | 11 |
| DBSCAN | 5.819 | 2 | 59 | 0.2784 | 0.2784 | 1.1334 | 2902.03 | 14 |

DBSCAN menghasilkan 59 data noise. Untuk validasi internal DBSCAN, data noise (`cluster = 0`) dikeluarkan dari perhitungan metrik agar evaluasi hanya membandingkan anggota cluster.

Berdasarkan total ranking internal, **K-Means** dipilih sebagai hasil akhir karena memiliki kombinasi metrik terbaik secara keseluruhan.

## Interpretasi Cluster Final

Interpretasi dilakukan berdasarkan profil rata-rata RFM pada `Cluster_Final`.

| Pola RFM | Makna Segmen | Rekomendasi |
|---|---|---|
| Recency rendah, Frequency tinggi, Monetary tinggi | Pelanggan aktif dan bernilai tinggi | Program loyalitas, rekomendasi personal, penawaran eksklusif |
| Recency tinggi, Frequency rendah, Monetary rendah | Pelanggan kurang aktif atau bernilai rendah | Kampanye reaktivasi, diskon terbatas, reminder produk |

Nomor cluster dapat berubah tergantung hasil algoritma, sehingga interpretasi sebaiknya selalu membaca tabel profil RFM, bukan hanya angka label cluster.

## External Criteria

External Criteria seperti **Cluster Accuracy**, **Cluster Purity**, dan **Rand Index** hanya dapat digunakan jika tersedia label asli. Karena dataset ini tidak memiliki label asli segmentasi pelanggan, bagian tersebut bersifat opsional/template.

Jika label asli tersedia, Rand Index dapat dihitung dengan package `clusterCrit`.

## Struktur File

```text
.
|-- LICENSE
|-- README.md
|-- online_retail_II.xlsx
|-- online_retail_II_SMLpraUAS.Rmd
|-- online_retail_II_SMLpraUAS.ipynb
`-- online_retail_II_SMLpraUAS.pdf
```

## Cara Menjalankan

Install package R yang dibutuhkan:

```r
install.packages(c(
  "readxl",
  "dplyr",
  "lubridate",
  "ggplot2",
  "factoextra",
  "cluster",
  "mclust",
  "fpc",
  "scales",
  "clusterSim",
  "dbscan",
  "knitr",
  "rmarkdown"
))
```

Package opsional untuk external validation:

```r
install.packages("clusterCrit")
```

Render laporan dari R:

```r
rmarkdown::render("online_retail_II_SMLpraUAS.Rmd")
```

Atau jalankan notebook:

```text
online_retail_II_SMLpraUAS.ipynb
```

## Output

Output proyek:

```text
online_retail_II_SMLpraUAS.pdf
```

Laporan berisi proses import data, preprocessing, pembentukan RFM, penentuan jumlah cluster, perbandingan algoritma clustering, validasi cluster, visualisasi, dan interpretasi segmentasi pelanggan.
