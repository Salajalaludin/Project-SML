# Segmentasi Pelanggan Online Retail II dengan RFM dan Clustering

Proyek ini menganalisis dataset **Online Retail II** dari UCI Machine Learning Repository untuk melakukan segmentasi pelanggan menggunakan pendekatan **RFM** (*Recency, Frequency, Monetary*) dan metode clustering.

## Ringkasan

Analisis dilakukan dengan menggabungkan dua sheet transaksi Online Retail II, membersihkan data, membentuk fitur RFM pada level pelanggan, lalu membandingkan metode **K-Means** dan **Gaussian Mixture Model (GMM)**.

Jumlah cluster optimal yang digunakan adalah:

```text
k = 2
```

Pemilihan `k = 2` didasarkan pada metode **Silhouette**, karena nilai silhouette tertinggi berada pada `k = 2`.

## Dataset

File dataset yang digunakan:

```text
online_retail_II.xlsx
```

Dataset berisi transaksi toko online Inggris pada periode 2009-2011. Dua sheet yang digunakan:

```text
Year 2009-2010
Year 2010-2011
```

Ringkasan jumlah data:

```text
Data awal                  : 1.067.371 transaksi
Setelah cleaning            :   805.549 transaksi
Customer unik setelah clean :     5.878 pelanggan
Data RFM                    :     5.878 pelanggan
```

## Fitur RFM

Fitur yang digunakan untuk clustering:

| Fitur | Deskripsi |
|---|---|
| Recency | Jarak hari sejak transaksi terakhir pelanggan |
| Frequency | Jumlah transaksi unik pelanggan |
| Monetary | Total nilai belanja pelanggan |

Sebelum clustering, fitur RFM distandarisasi menggunakan `scale()`.

## Metode

Metode clustering yang dibandingkan:

| Metode | Keterangan |
|---|---|
| K-Means | Clustering berbasis centroid |
| GMM | Clustering berbasis model probabilistik Gaussian |

Validasi cluster menggunakan:

| Metrik | Kriteria |
|---|---|
| Silhouette Width | Lebih besar lebih baik |
| Davies-Bouldin Index | Lebih kecil lebih baik |
| Calinski-Harabasz Index | Lebih besar lebih baik |

## Hasil Utama

Berdasarkan hasil validasi, **K-Means** menghasilkan performa yang lebih baik dibandingkan GMM.

Ringkasan hasil validasi:

| Metode | Silhouette | Davies-Bouldin | Calinski-Harabasz |
|---|---:|---:|---:|
| K-Means | 0.4381 | 0.9693 | 4393.74 |
| GMM | 0.2924 | 1.0982 | 1123.55 |

Segmentasi akhir menggunakan hasil **K-Means** dengan `k = 2`.

## Interpretasi Cluster

| Cluster | Segmen | Deskripsi | Rekomendasi |
|---|---|---|---|
| 1 | Pelanggan Aktif / Bernilai Tinggi | Recency rendah, Frequency tinggi, dan Monetary tinggi | Program loyalitas, rekomendasi personal, dan penawaran eksklusif |
| 2 | Pelanggan Tidak Aktif / Bernilai Rendah | Recency tinggi, Frequency rendah, dan Monetary rendah | Kampanye reaktivasi, diskon terbatas, dan reminder produk relevan |

## Struktur File

```text
.
├── online_retail_II.xlsx
├── online_retail_II_SMLpraUAS.Rmd
├── online_retail_II_SMLpraUAS.ipynb
├── online_retail_II_SMLpraUAS.pdf
└── README.md
```

## Cara Menjalankan

Pastikan package R berikut sudah terpasang:

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
  "knitr",
  "rmarkdown"
))
```

Render laporan dari RStudio atau terminal R:

```r
rmarkdown::render("online_retail_II_SMLpraUAS.Rmd")
```

Atau jalankan notebook:

```text
online_retail_II_SMLpraUAS.ipynb
```

## Output

Output utama proyek:

```text
online_retail_II_SMLpraUAS.pdf
```

Laporan berisi proses import data, cleaning, pembentukan RFM, penentuan jumlah cluster, validasi cluster, visualisasi, serta interpretasi segmentasi pelanggan.
