# Segmentasi Pelanggan Online Retail II dengan RFM dan Clustering

Proyek ini melakukan segmentasi pelanggan pada dataset **Online Retail II** menggunakan fitur **RFM** (*Recency, Frequency, Monetary*) dan beberapa algoritma clustering. Hasil akhir tidak diambil dari satu algoritma secara manual, tetapi dipilih dari tabel validasi internal melalui variabel `Cluster_Final`.

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

Pemilihan `k = 2` didasarkan pada **Silhouette Method**, karena nilai rata-rata silhouette tertinggi berada pada `k = 2`. Dengan jumlah cluster ini, segmentasi dapat dibaca sebagai dua kelompok besar pelanggan: pelanggan lebih aktif/bernilai tinggi dan pelanggan kurang aktif/bernilai rendah.

## Dataset

File dataset yang digunakan:

```text
online_retail_II.xlsx
```

Sheet yang digabungkan:

```text
Year 2009-2010
Year 2010-2011
```

Data dibersihkan dengan menghapus transaksi yang tidak memiliki `Customer ID`, transaksi retur, serta transaksi dengan `Quantity` atau `Price` yang tidak valid.

## Fitur RFM

| Fitur | Deskripsi |
|---|---|
| Recency | Selisih hari sejak transaksi terakhir pelanggan |
| Frequency | Jumlah invoice unik pelanggan |
| Monetary | Total nilai belanja pelanggan |

Sebelum clustering, fitur RFM ditransformasi dan distandarisasi menggunakan `scale()` agar perbedaan skala antarfitur tidak mendominasi hasil clustering.

## Alur Analisis

1. Import dan penggabungan dua sheet transaksi.
2. Pemeriksaan missing value (`NA`).
3. Cleaning data transaksi.
4. Pembentukan fitur RFM per pelanggan.
5. Transformasi dan scaling fitur RFM.
6. Penentuan jumlah cluster optimal menggunakan Elbow Method dan Silhouette Method.
7. Penerapan beberapa algoritma clustering.
8. Validasi hasil clustering menggunakan metrik internal.
9. Pemilihan algoritma terbaik ke dalam `Cluster_Final`.
10. Interpretasi profil cluster.

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

Validasi menggunakan internal criteria karena dataset tidak memiliki label asli segmentasi pelanggan.

| Metrik | Arah Evaluasi | Fungsi R |
|---|---|---|
| Silhouette Width | Lebih besar lebih baik | `silhouette()` |
| Shadow Value | Lebih besar lebih baik | `cluster.stats()` |
| Davies-Bouldin Index | Lebih kecil lebih baik | `index.DB()` |
| Calinski-Harabasz Index | Lebih besar lebih baik | `cluster.stats()` |

Notebook membuat tabel `validasi_semua`, lalu menghitung ranking untuk tiap metrik:

```text
Rank.Silhouette
Rank.Davies.Bouldin
Rank.Calinski.Harabasz
Rank.Total
```

Algoritma dengan `Rank.Total` terbaik dipilih sebagai `Cluster_Final`. Pada hasil notebook saat ini, algoritma yang terpilih adalah **K-Means**.

## Rangkuman Hasil Validasi

| Metode | N | Cluster | Noise | Silhouette | Davies-Bouldin | Calinski-Harabasz | Rank Total |
|---|---:|---:|---:|---:|---:|---:|---:|
| K-Means | 5.878 | 2 | 0 | 0.4381 | 0.9693 | 6205.27 | 5 |
| Hierarchical Average | 5.878 | 2 | 0 | 0.5815 | 0.7459 | 151.62 | 7 |
| PAM / K-Medoids | 5.878 | 2 | 0 | 0.4340 | 0.9765 | 6161.99 | 8 |
| GMM | 5.878 | 2 | 0 | 0.2924 | 1.0982 | 3056.20 | 11 |
| DBSCAN | 5.819 | 2 | 59 | 0.2784 | 1.1334 | 2902.03 | 14 |

DBSCAN memiliki 59 data noise. Untuk validasi internal DBSCAN, noise dikeluarkan dari perhitungan metrik agar evaluasi hanya membandingkan anggota cluster.

## Interpretasi Cluster Final

Interpretasi dilakukan berdasarkan profil rata-rata RFM pada `Cluster_Final`.

| Pola RFM | Makna Segmen | Rekomendasi |
|---|---|---|
| Recency rendah, Frequency tinggi, Monetary tinggi | Pelanggan aktif dan bernilai tinggi | Program loyalitas, rekomendasi personal, penawaran eksklusif |
| Recency tinggi, Frequency rendah, Monetary rendah | Pelanggan kurang aktif atau bernilai rendah | Kampanye reaktivasi, diskon terbatas, reminder produk |

Nomor cluster dapat berubah tergantung hasil algoritma, sehingga interpretasi sebaiknya selalu membaca tabel profil RFM, bukan hanya angka label cluster.

## External Criteria

External criteria seperti **Cluster Accuracy**, **Cluster Purity**, dan **Rand Index** hanya dapat digunakan jika tersedia label asli. Karena dataset ini tidak memiliki label asli segmentasi pelanggan, bagian tersebut bersifat opsional/template.

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
