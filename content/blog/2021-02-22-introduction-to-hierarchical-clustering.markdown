---
title: Introduction to Hierarchical Clustering
author: Inayatus S & Nabiilah A F
github: https://github.com/inytss/hierarchical-clustering
date: '2021-02-22'
slug: introduction-to-hierarchical-clustering
categories:
  - R
tags:
  - Machine Learning
  - Unsupervised Learning
  - Hierarchical Clustering
description: ''
featured: 'hc.png'
featuredalt: ''
featuredpath: 'date'
linktitle: ''
type: post
---



Clustering merupakan salah satu metode Unsupervised Learning yang bertujuan untuk melakukan pengelompokan data berdasasrkan kemiripan/jarak antar data. Clustering memiliki karakteristik dimana anggota dalam satu cluster memiliki kemiripan yang sama atau jarak yang sangat dekat, sementara anggota antar cluster memiliki kemiripan yang sangat berbeda atau jarak yang sangat jauh. Menurut (Tan et al., 2006) dalam bukunya yang berjudul *Introduction to Data Mining*, metode clustering dibagi menjadi dua jenis, yaitu **Hierarchical Clustering** dan **Partitional Clustering**[^1]. 

**Partitional Clustering** umumnya bertujuan untuk mengelompokkan data menjadi beberapa cluster yang lebih kecil[^2]. Pada prosesnya, setiap cluster akan memiliki titik pusat cluster (*centroid*) dan mencoba menghitung setiap data yang paling dekat dengan centroid tersebut. Metode dalam partitional clustering diantaranya *k-means*, *fuzzy k-means*, dan *mixture modeling*.

<center> ![](/img/intro_hc/partitional.png){width="80%"} </center>

Sedangkan dalam **Hierarchical Clustering**, pengelompokan data dilakukan dengan membuat suatu bagan hirarki (**dendrogram**) dengan tujuan menunjukkan kemiripan antar data. Setiap data yang mirip akan memiliki hubungan hirarki yang dekat dan menbentuk cluster data. Bagan hirarki akan terus terbentuk hingga seluruh data terhubung dalam bagan hirarki tersebut. Cluster dapat dihasilkan dengan memotong bagan hirarki pada level tertentu. Beberapa metode dalam hierarchical clustering yaitu *single linkage*, *complete linkage*, *average linkage*, dan *ward's minimum variance*.

<center> ![](/img/intro_hc/hc.png){width="80%"} </center>

Pada kesempatan kali ini kita akan mendalami terkait **Hierarchical Clustering** serta aplikasinya untuk pengolahan data.

# Hierarchical Clustering

## Approach

Secara umum, *hierarchical clustering* dibagi menjadi dua jenis yaitu **agglomerative** dan **divisive**[^3]. Kedua metode ini dibedakan berdasarkan pendekatan dalam melakukan pengelompokkan data hingga membentuk dendrogram, menggunakan *bottom-up* atau *top-down manner*.

1. **Agglomerative Clustering** 

*Agglomerative clustering* biasa disebut juga sebagai agglomerative nesting (**AGNES**) dimana cara kerja dalam melakukan pengelompokan data menggunakan **bottom-up manner**. Prosesnya dimulai dengan menganggap setiap data sebagai satu cluster kecil (*leaf*) yang hanya memiliki satu anggota saja, lalu pada tahap selanjutnya dua cluster yang memiliki kemiripan akan dikelompokkan menjadi satu cluster yang lebih besar (*nodes*). Proses ini akan dilakukan terus menerus hingga semua data menjadi satu cluster besar (*root*). 

2. **Divisive hierarchical clustering**

*Divisive hierarchical clustering* biasa disebut juga sebagai divisive analysis (**DIANA**) di mana cara kerja dalam melakukan pengelompokan data menggunakan **top-down manner**. Prosesnya dimulai dengan menganggap satu set data sebagai satu cluster besar (*root*), lalu dalam setiap iterasinya setiap data yang memiliki karakteristik yang berbeda akan dipecah menjadi dua cluster yang lebih kecil (*nodes*) dan proses akan terus berjalan hingga setiap data menjadi satu cluster kecil (*leaf*) yang hanya memiliki satu anggota saja.

Berikut adalah ilustrasi mengenai bagaimana agglomerative dan divisive clustering bekerja.

<center> ![](/img/intro_hc/agnes-vs-diana.png){width="80%"} </center>

Selain memahami pendekatan dalam pembuatan dendrogram, mari memahami bagaimana setiap cluster dapat dibuat dan digabungkan.

## (Dis)similarity Measure

Tujuan dari clustering secara umum, baik hierarchical maupun partitional clustering adalah untuk membuat cluster yang memiliki karakteristik yang sama dalam satu anggota cluster dan memiliki karakteristik yang berbeda antar clusternya. Konsep inilah yang mengharuskan proses pembuatan cluster memperhatikan jarak / **(dis)similarity** / ukuran ketidakmiripan antar data. 

Terdapat beragam metode penghitungan (dis)similarity. Pemilihan metode (dis)similarity akan menentukan bagaimana kemiripan antar data dihitung. Itulah mengapa pemilihan metode (dis)similarity menjadi salah satu hal penting dalam pembuatan hierarchical clustering. 

Metode penghitungan (dis)similarity yang sering digunakan adalah *euclidean distance* dan *manhattan distance*, namun bisa saja menggunakan pengukuran jarak yang lain, bergantung pada data yang sedang kita analisis. Berikut ini formula dalam perhitungan (dis)similarity dari kedua metode tersebut:

1. **Euclidean distance**

`$$d_{xy} = \sqrt {\sum_{i=1}^{n}(x_i - y_i)^2}$$`

2. **Manhattan distance**

`$$d_{xy} = \sum_{i=1}^{n} |{(x_i - y_i)}|$$`

Ada beberapa pengukuran (dis)similarity yang lain yang bisa digunakan yaitu menggunakan **correlation-based distance**. Correlation-based distance biasa digunakan ketika kita ingin mengetahui bentuk (dis)similarity pada suatu data yang bergerak "naik" atau "turun" secara bersamaan. Pengukuran (dis)similarity ini sering digunakan untuk melakukan analisis ekspresi gen atau dalam dunia marketing, ketika kita ingin melakukan *customer segmentation* berdasarkan kesamaan barang yang dibeli oleh pelanggan tanpa memperhatikan banyak barang yang mereka beli.

*Euclidean distance* dan *manhattan distance* cenderung memiliki konsep yang berkebalikan dengan correlation-based distance, data yang akan dikelompokkan bersama merupakan data yang memiliki karakteristik nilai yang sama, entah sama besarnya atau sama kecilnya. Pengukuran ini biasa digunakan pada kasus *customer segmentation* yang memperhatikan banyaknya pembelian dari pelanggan, segmentasi daerah yang memiliki kasus COVID tinggi/rendah, dan lain sebagainya[^4]. 

Pada R, kita dapat menggunakan fungsi `dist()` untuk menghitung (dis)similarity antar data. Secara default, fungsi `dist()` akan menghitung *euclidean distance* antar data. 

Selanjutnya, nilai (dis)similarity antar data ini akan dibentuk menjadi **distance matrix**. Kemudian, *distance matrix* tersebut akan diolah untuk penyusunan dendrogram.

## Linkage Method

Dalam hierarchical clustering, selain menghitung (dis)similarity antar data, diperlukan juga cara untuk menghitung (dis)similarity antar cluster sehingga dapat terbentuk dendrogram dari cluster-cluster yang dekat. Proses penggabungan cluster-cluster kecil menjadi satu dendrogram utuh dilakukan melalui beberapa pendekatan **Linkage Method**. Berikut ini linkage method yang sering digunakan pada agglomerative approach:

1. **Complete Linkage** / **Maximum Linkage**
2. **Single Linkage** / **Minimum Linkage**
3. **Average Linkage**
4. **Centroid Linkage**
5. **Ward's minimum Variance**

### Complete/Maximum Linkage

Pengukuran (dis)similarity atau jarak antar cluster dilakukan dengan mengukur terlebih dahulu jarak antar tiap observasi dari cluster yang berbeda (**pairwise distances**). Jarak paling tinggi (*maximum distance*) akan menjadi ukuran (dis)similarity antar cluster. Kemudian, dendrogram akan terbentuk dari cluster-cluster yang memiliki (dis)similarity paling kecil. Hal ini membuat dendrogram yang terbentuk menjadi lebih terpisah antar clusternya (terbentuk cluster yang "compact").

Berikut formula jarak antar cluster menggunakan complete linkage:

`$$d_{12} = \max_{ij} d(X_i, Y_j)$$`

di mana:

* `\(X_1, X_2, ..., X_k\)` : observasi pada cluster 1
* `\(Y_1, Y_2, ..., Y_k\)` : observasi pada cluster 2
* `\(d(X, Y)\)` : jarak antara data pada cluster 1 dengan data pada cluster 2

### Single/Minimum Linkage

Pengukuran (dis)similarity atau jarak antar cluster dilakukan dengan mengukur terlebih dahulu jarak antar tiap observasi dari cluster yang berbeda pairwise distances. Jarak paling kecil (*minimum distance*) akan menjadi ukuran (dis)similarity antar cluster. Dendrogram akan terbentuk dari cluster-cluster yang memiliki (dis)similarity paling kecil. Hal ini membuat dendrogram yang terbentuk menjadi lebih "loose" atau berdekatan antar clusternya.

Berikut formula jarak antar cluster menggunakan single linkage:

`$$d_{12} = \min_{ij} d(X_i, Y_j)$$`

### Average Linkage

Pengukuran (dis)similarity atau jarak antar cluster dilakukan dengan mengukur terlebih dahulu jarak antar tiap observasi dari cluster yang berbeda *pairwise distances*. Kemudian, dihitung rata-rata jarak dari *pairwise distance* tersebut dan nilai tersebut akan menjadi ukuran (dis)similarity antar cluster. Dendrogram akan terbentuk dari cluster-cluster yang memiliki (dis)similarity paling kecil. Umumnya metode ini akan menghasilkan cluster yang tidak terlalu "loose" maupun "compact".

Berikut formula jarak antar cluster menggunakan average linkage:

`$$d_{12} = \frac{1}{kl} \sum_{i=1}^{k}\sum_{j=1}^{l} d(X_i, Y_j)$$`

### Centroid Linkage

Perhitungan (dis)similarity atau jarak antar cluster dilakukan dengan mengukur jarak antar centroid pada dua cluster. Perhitungan centroid disini menggunakan rata-rata pada suatu variabel x. Dendrogram yang terbentuk akan berdasarkan cluster dengan jarak antar centroid paling kecil.

Berikut formula jarak antar cluster menggunakan centroid linkage:

`$$d_{12} = d(\bar X, \bar Y)$$`

### Ward's Minimum Variance

Pada metode ini, di tiap iterasinya akan dibentuk cluster-cluster yang kemudian dihitung nilai *within sum of square* tiap cluster (WSS). WSS dapat diartikan sebagai jumlah dari jarak tiap observasi ke nilai tengah cluster. Cluster-cluster yang menghasilkan within sum of square terkecil akan diambil kemudian digabungkan hingga membentuk satu dendrogram utuh.

Berikut adalah ilustrasi untuk kelima jenis linkage di atas[^5]:

<center> ![](/img/intro_hc/linkage.png){width="80%"} </center>

Linkage method akan menentukan rupa dari dendrogram yang terbentuk. Telah dilakukan diskusi terkait beragam linkage method dan pemilihannya [disini](https://stats.stackexchange.com/questions/195446/choosing-the-right-linkage-method-for-hierarchical-clustering)

## Interpretasi Dendrogram

Setelah menghasilkan dendrogram dari hierarchical clustering, sudah sepatutnya kita perlu mampu membaca dendrogram tersebut. Sebagai contoh, berikut adalah gambar kedekatan beberapa instrumen musik yang digambarkan dengan dendrogram:

<center> ![](/img/intro_hc/dendrogram.jpg){width="80%"} </center>

Dendrogram sejatinya merupakan struktur yang menggambarkan kemiripan antar data. Tiap data pada awalnya diletakan di suatu level dasar dimana tiap data berdiri sendiri sebagai satu cluster tunggal. Pada contoh di atas, tiap instrumen musik merupakan satu cluster tunggal di bagian dasar dendrogram. 

Kemudian, data-data terdekat akan digabungkan menjadi satu cluster dan dihubungkan dengan suatu garis. Contohnya cluster instrumen "piccolo" & "flute" dan cluster instrumen "basoon" & "clarinet". Garis tersebut dimulai dari titik data dan bersatu di titik tertentu. 

**Panjang garis antar data (dihitung tegak lurus dari titik data hingga titik kedua garis bersatu) mewakilkan nilai (dis)similarity antar data**. Disini kita bisa menyimpulkan bahwa "piccolo" dengan "flute" memiliki kedekatan yang lebih tinggi dibandingkan "basoon" dengan "clarinet". Kita juga bisa mengetahui bahwa "flute" memiliki kedekatan yang lebih tinggi dengan "clarinet" dibandingkan dengan "trumpet". Hal ini karena "flute" dan "clarinet" memiliki panjang garis antar data yang lebih pendek dibandingkan antara "flute" dan "trumpet".

Pada partitional clustering yang mungkin lebih umum kita kenal (contohnya k-means), pengguna perlu menentukan jumlah `k` cluster yang ingin dibentuk. Namun, pada hierarchical clustering hal tersebut tidak diwajibkan. Hal ini berangkat dari tujuan awal hierarchical clustering yang fokus pada mengetahui kedekatan antar data (bukan mengetahui partisi antar data).

Meskipun begitu, **kita tetap dapat membuat cluster-cluster data dengan memotong dendrogram pada nilai (dis)similarity tertentu**. Pada contoh dendrogram di atas, kita membuat partisi berupa cluster "strings", "woodwind", "brass", dan "percussion".

Layaknya menentukan kedekatan antar data, kita juga dapat menetukan kedekatan antar cluster dengan memperhitungkan panjang garis antar data. Dari dendrogram di atas, kita dapat menarik insight bahwa cluster "woodwind" lebih dekat dengan cluster "strings" dibandingkan dengan cluster "brass" dan "percussion".

Bila disimpulkan, **semakin panjang garis antar data maka semakin berbeda antar data/cluster tersebut, dan semakin pendek garis antar data maka semakin mirip antar data/cluster tersebut**. Dari pemahaman ini, kita juga bisa menentukan manakah data yang berdekatan/berjauhan dengan data yang sedang kita analisis.

Hal lain yang dapat diperhatikan dalam analisis cluster adalah adanya kemungkinan untuk memperoleh cluster yang hanya terdiri dari satu atau sedikit sekali anggota. Ketika hal tersebut terjadi, kita perlu melakukan pengecekan kembali pada data yang kita miliki. Hal ini bisa disebabkan karena adanya data yang cukup berbeda dengan yang lainnya atau biasa disebut **outlier / anomali**. 

## Alur Pengerjaan

Secara umum, berikut adalah langkah-langkah yang dilakukan untuk melakukan hierarchical cluster analysis, menggabungkan konsep-konsep yang sudah kita pahami sebelumnya:

1. Menyiapkan data dimana data yang digunakan adalah data bertipe numerik agar dapat digunakan untuk penghitungan jarak.
2. Menghitung (dis)similarity atau jarak antar data yang berpasangan pada dataset. Metode penghitungan (dis)similarity dapat dipilih berdasarkan data. Nilai (dis)similarity tersebut kemudian akan disusun menjadi distance matrix.
3. Membuat dendrogram dari distance matrix menggunakan linkage method tertentu. Kita juga dapat mencoba beberapa linkage method kemudian memilih dedrogram paling baik.
4. Menentukan dimana akan melakukan pemotongan tree (dengan nilai (dis)similarity tertentu). Disinilah tahap dimana cluster akan terbentuk.
5. Melakukan interpretasi dari dendrogram yang telah didapat.

## Pros & Cons

Berikut adalah beberapa kelebihan dan kekurangan hierarchical clustering yang juga dapat dipertimbangkan sebelum dipakai untuk analisis data.

**Kelebihan:**

* Mampu menggambarkan kedekatan antar data dengan dendrogram.
* Cukup mudah untuk pembuatannya.
* Dapat menentukan banyak cluster yang terbentuk setelah dendrogram terbentuk.

**Kekurangan:**

* Tidak dapat menganalisis data kategorik secara langsung (terhambat pada penghitungan jarak yang hanya bisa dilakukan untuk data numerik, sehingga data kategorik perlu dipre-process terlebih dahulu).
* Tidak diperuntukkan untuk menghasilkan jumlah cluster optimal yang mutlak (jumlah cluster dapat berubah-ubah tergantung level pemotongan dendrogram).
* Sensitif terhadap data yang memiliki skala berbeda (sehingga data perlu dinormalisasi/standarisasi terlebih dahulu).
* Sensitif terhadap outlier.
* Cukup berat komputasinya untuk data berukuran besar.

# Study Case: Analisis Data `USArrests`

Pada artikel ini, kita akan coba mengaplikasikan cara kerja hierarchical clustering AGNES untuk menganalisis kedekatan Negara Bagian AS berdasarkan tingkat kriminalitasnya. Kita akan menggunakan dataset `USArrest` yang menyimpan informasi statistik terkait jumlah kriminalitas `assault`, `murder` dan `rape` per 100,000 penduduk pada 50 negara bagian Amerika Serikat pada tahun 1973, berikut data populasi penduduk tiap negara bagian tersebut. 

## Dataset

Pertama, kita load terlebih dahulu data yang akan digunakan.


```r
head(USArrests)
```

```
#>            Murder Assault UrbanPop Rape
#> Alabama      13.2     236       58 21.2
#> Alaska       10.0     263       48 44.5
#> Arizona       8.1     294       80 31.0
#> Arkansas      8.8     190       50 19.5
#> California    9.0     276       91 40.6
#> Colorado      7.9     204       78 38.7
```

## Preparasi Data

Sebelum membuat clustering, kita perlu menyiapkan dataset yang digunakan. Dari tampilan data sebelumnya, kita telah mengetahui bahwa tipe data pada tiap kolom sudah tepat/sesuai konteks kolomnya. Perlu diperhatikan bahwa, analisis clustering didasari oleh analisis jarak, sehingga dibutuhkan variable dengan tipe **numerik**. Variabel dengan tipe kategorik tidak terlalu bagus untuk dimasukkan pada analisis clustering. Pada dateset kita, semua variable bertipe numerik sehingga cocok untuk analisis clustering. 

Selanjutnya kita dapat cek apakah terdapat **missing value** pada data. Dari hasil di bawah, kita mengetahui bahwa tidak ada missing value pada data dan data dapat dianalisis lebih lanjut.


```r
# cek missing value
anyNA(USArrests)
```

```
#> [1] FALSE
```

Selanjutnya, kita dapat melakukan perhitungan (dis)similarity data. Namun, perhitungan (dis)similarity / jarak amat sensitif terhadap perbedaan skala antar variable data. Oleh karena itu, kita perlu melakukan standarisasi data terlebih dahulu agar skala pada tiap variable serupa. Pada data `USArrest` setiap variabel belum memiliki skala yang sama sehingga perlu dilakukan standarisasi. 


```r
# cek skala tiap variable data
summary(USArrests)
```

```
#>      Murder          Assault         UrbanPop          Rape      
#>  Min.   : 0.800   Min.   : 45.0   Min.   :32.00   Min.   : 7.30  
#>  1st Qu.: 4.075   1st Qu.:109.0   1st Qu.:54.50   1st Qu.:15.07  
#>  Median : 7.250   Median :159.0   Median :66.00   Median :20.10  
#>  Mean   : 7.788   Mean   :170.8   Mean   :65.54   Mean   :21.23  
#>  3rd Qu.:11.250   3rd Qu.:249.0   3rd Qu.:77.75   3rd Qu.:26.18  
#>  Max.   :17.400   Max.   :337.0   Max.   :91.00   Max.   :46.00
```

Dalam melakukan standarisasi data, kita bisa menggunakan **z-score standarization**. Formula z-score standarization adalah sebagai berikut:

`$$Z-Score = \frac{x - \mu}{\sigma}$$`
 
 di mana:
 
* `x`: observasi data
* `\(\mu\)`: rata-rata variabel
* `\(\sigma\)`: standar deviasi pada variabel tersebut
 
Pada R, kita bisa melakukan z-score standarization menggunakan fungsi `scale()`. Hasil standarisasi data akan kita simpan dalam objek `us_z`.


```r
# z-score standardization
us_z <- scale(USArrests) 
head(us_z)
```

```
#>                Murder   Assault   UrbanPop         Rape
#> Alabama    1.24256408 0.7828393 -0.5209066 -0.003416473
#> Alaska     0.50786248 1.1068225 -1.2117642  2.484202941
#> Arizona    0.07163341 1.4788032  0.9989801  1.042878388
#> Arkansas   0.23234938 0.2308680 -1.0735927 -0.184916602
#> California 0.27826823 1.2628144  1.7589234  2.067820292
#> Colorado   0.02571456 0.3988593  0.8608085  1.864967207
```

## Mengukur (Dis)similarity

Setelah membersihkan dan menyiapkan data, selanjutnya kita dapat melakukan **perhitungan (dis)similarity data**. Metode yang akan digunakan pada kasus ini yaitu *euclidean distance*, karena kita akan menjadikan besaran nilai pada tiap variable data sebagai karakteristik yang membedakan tiap observasinya (negara bagian).


```r
# menghitung distance
us_dist <- dist(x = us_z, method = "euclidean")
```

Fungsi `dist()` secara otomatis menghitung (dis)similarity antar observasi data. Objek hasil `dist()` tersebut nantinya dapat langsung dimasukkan kedalam fungsi clustering dan otomatis diolah sebagai **distance matrix**, namun berikut kurang lebih tampilannya dalam bentuk distance matrix.


```
#>             Alabama   Alaska  Arizona Arkansas California
#> Alabama    0.000000 2.703754 2.293520 1.289810   3.263110
#> Alaska     2.703754 0.000000 2.700643 2.826039   3.012541
#> Arizona    2.293520 2.700643 0.000000 2.717758   1.310484
#> Arkansas   1.289810 2.826039 2.717758 0.000000   3.763641
#> California 3.263110 3.012541 1.310484 3.763641   0.000000
```

## Modeling {.tabset}

Setelah persiapan data selesai, maka saatnya membuat clustering. Pembuatan clustering yang akan dicoba adalah 5 metode AGNES clustering yang telah dijelaskan pada bab **"Linkage Method"** sebelumnya. Hal ini bertujuan untuk membandingkan rupa dendrogram yang dihasilkan oleh tiap metode.

Pembuatan AGNES clustering pada R dapat menggunakan fungsi `hclust()`. Fungsi ini akan meminta 2 argumen untuk dimasukkan yaitu:

* `d`: objek distance / distance matrix
* `method`: linkage method yang digunakan, dengan pilihannya diantaranya: 
  + "ward.D2"
  + "single"
  + "complete" 
  + "average"
  + "centroid"

Selain linkage method yang kita bahas di atas, terdapat juga linkage method yang lain. Dokumentasi lebih lengkap mengenai linkage method yang disediakan `hclust()` ada [disini](https://www.rdocumentation.org/packages/stats/versions/3.6.2/topics/hclust), dan bacaan lebih lanjut terkait penjelasan linkage method tersebut dapat dilihat [disini](https://stats.stackexchange.com/questions/195446/choosing-the-right-linkage-method-for-hierarchical-clustering).

* **Complete Linkage**


```r
us_hc_complete <- hclust(d = us_dist, method = "complete")
```

* **Single Linkage**


```r
us_hc_single <- hclust(d = us_dist, method = "single")
```

* **Average Linkage**


```r
us_hc_avg <- hclust(d = us_dist, method = "average")
```

* **Centroid Linkage**


```r
set.seed(100)
us_hc_centroid <- hclust(d = us_dist, method = "centroid")
```

* **Ward's Minimum Variance**


```r
us_hc_ward <- hclust(d = us_dist, method = "ward.D2")
```

Pada setiap metode clustering tersebut akan membentuk sebuah dendrogram. Dendrogram dapat divisualisasikan menggunakan fungsi `plot()` biasa atau bisa menggunakan fungsi `fviz_dend()` dari package **factoextra**.

### Dendrogram Complete Linkage


```r
library(factoextra)
fviz_dend(us_hc_complete, cex = 0.5, 
          main = "Cluster Dendrogram Complete Linkage")
```

<img src="/blog/2021-02-22-introduction-to-hierarchical-clustering_files/figure-html/unnamed-chunk-12-1.png" width="768" style="display: block; margin: auto;" />

Pada dendrogram tersebut, setiap observasi yang memiliki kemiripan akan dihubungkan oleh sebuah garis dan bersatu pada suatu titik. Panjang garis tersebut disebut juga **cophenetic distance** dan berbeda-beda untuk tiap observasi. Panjang garis tersebut merupakan "Height" (y-axis) yang menggambarkan (dis)similarity/jarak antar kedua observasi.

Hasil yang terbentuk dari dendrogram complete linkage menunjukkan dendrogram yang cukup **compact**, dimana setiap cluster terlihat memiliki **partisi yang cukup jelas**. Hal ini akan mempermudah saat pemotongan dendrogram untuk membentuk banyak cluster. 

Untuk melakukan pengelompokan pada pada dendrogram, kita bisa melakukan pemotongan dendrogram pada nilai (dis)similarity tertentu untuk mendapatkan sejumlah cluster yang terbentuk. Untuk melakukan pemotongan dendrogram, R menyediakan fungsi `cutree()` dan kita dapat mendefinisikan `h` yaitu nilai (dis)similarity yang ingin dipakai, atau `k` yaitu banyaknya cluster yang ingin kita bentuk.

Pada metode complete linkage ini akan kita coba potong menjadi 4 cluster.


```r
# cutree untuk menghasilkan cluster data
complete_clust <- cutree(us_hc_complete,  k = 4)
head(complete_clust)
```

```
#>    Alabama     Alaska    Arizona   Arkansas California   Colorado 
#>          1          1          2          3          2          2
```

Berikut ini banyak anggota dari masing-masing cluster yang terbentuk.


```r
table(complete_clust)
```

```
#> complete_clust
#>  1  2  3  4 
#>  8 11 21 10
```

Anggota pada masing-masing cluster cukup merata dengan cluster yang paling banyak memiliki anggota adalah cluster 3. Berikut hasil akhir cluster yang terbentuk apabila dilihat dari dendrogram.


```r
fviz_dend(us_hc_complete, k = 4, k_colors = "jco", rect = T, 
          main = "Complete Linkage Cluster")
```

<img src="/blog/2021-02-22-introduction-to-hierarchical-clustering_files/figure-html/unnamed-chunk-15-1.png" width="768" style="display: block; margin: auto;" />

Cara membaca cluster pada dendrogram sebagai berikut :

* Cluster 1 digambarkan oleh dendrogram berwarna merah.
* Cluster 2 digambarkan oleh dendrogram berwarna abu-abu.
* Cluster 3 digambarkan oleh dendrogram berwarna kuning.
* Cluster 4 digambarkan oleh dendrogram berwarna biru.

Salah satu cara yang dapat digunakan untuk memvalidasi cluster tree yaitu dengan menghitung korelasi antara **cophenetic distance** dengan **jarak original** masing-masing observasi. Semakin kuat nilai korelasi jarak antar observasi, maka cluster tersebut merepresentasikan data yang dimiliki.

Untuk melihat korelasinya, maka kita perlu menghitung cophenetic distance terlebih dahulu menggunakan fungsi `cophenetic()`.


```r
complete_coph <- cophenetic(us_hc_complete)
cor(complete_coph, us_dist)
```

```
#> [1] 0.6979437
```

### Dendrogram Single Linkage


```r
fviz_dend(us_hc_single, cex = 0.5, 
          main = "Cluster Dendrogram Single Linkage")
```

<img src="/blog/2021-02-22-introduction-to-hierarchical-clustering_files/figure-html/unnamed-chunk-17-1.png" width="768" style="display: block; margin: auto;" />

Hasil dendrogram yang terbentuk pada model single linkage terlihat lebih lebar dimana antar anggota cluster yang berbeda pun terlihat memiliki Height / (dis)similarity yang rendah. Cluster yang terbut terlihat rapat dan sangat dekat sehingga **partisi dari tiap cluster tidak tervisualisasikan dengan maksimal**. Hal ini dikarenakan proses pembuatan hirarki dendrogram menggunakan minimum (dis)similarity pada setiap clusternya.

Sangat sulit untuk menentukan banyak cluster yang akan terbentuk apabila setiap observasi sangat rapat. Apabila dicoba untuk membentuk 4 cluster, maka berikut ini hasil cluster yang terbentuk.


```r
single_clust <- cutree(us_hc_single, k = 4)
head(single_clust)
```

```
#>    Alabama     Alaska    Arizona   Arkansas California   Colorado 
#>          1          2          1          1          3          1
```

Banyak anggota pada masing-masing cluster adalah sebagai berikut.


```r
table(single_clust)
```

```
#> single_clust
#>  1  2  3  4 
#> 46  1  2  1
```

Anggota pada cluster yang terbentuk sangat berbeda secara signifikan. Cluster 1 memiliki anggota yang paling banyak hampir sebanyak total observasi pada data. Hal ini yang dikarenakan terlalu rapatnya antar observasi sehingga sulit dipisahkan perbedaannya.


```r
fviz_dend(us_hc_single, k = 4, k_colors = "jco", rect = T, 
          main = "Single Linkage Cluster")
```

<img src="/blog/2021-02-22-introduction-to-hierarchical-clustering_files/figure-html/unnamed-chunk-20-1.png" width="768" style="display: block; margin: auto;" />

Cara membaca cluster pada dendrogram yaitu  sebagai berikut :

* Cluster 1 digambarkan oleh dendrogram berwarna merah.
* Cluster 2 digambarkan oleh dendrogram berwarna biru.
* Cluster 3 digambarkan oleh dendrogram berwarna abu-abu.
* Cluster 4 digambarkan oleh dendrogram berwarna kuning.

Apabila kita cek validasi dari cluster tree yang terbentuk, korelasi yang diperoleh rendah, artinya cluster yang terbentuk kurang merepresentasikan kondisi data yang ada. Sehingga menggunakan single linkage untuk data `USArrest` kurang relevan.


```r
single_coph <- cophenetic(us_hc_single)
cor(single_coph, us_dist)
```

```
#> [1] 0.541272
```

### Dendrogram Average Linkage


```r
fviz_dend(us_hc_avg, cex = 0.5, main = "Cluster Dendrogram Average Linkage")
```

<img src="/blog/2021-02-22-introduction-to-hierarchical-clustering_files/figure-html/unnamed-chunk-22-1.png" width="768" style="display: block; margin: auto;" />

Hasil dendrogram yang terbentuk pada model average linkage terlihat hampir mirip seperti complete linkage, yaitu terlihat compact. Model average linkage biasa digunakan karena menghasilkan nilai validasi cluster tree yang lebih baik dibandingkan metode linkage yang lain. 


```r
avg_coph <- cophenetic(us_hc_avg)
cor(avg_coph, us_dist)
```

```
#> [1] 0.7180382
```

Apabila dendrogramnya dipotong untuk membentuk 4 cluster, berikut hasil yang diperoleh.


```r
avg_clust <- cutree(us_hc_avg, k = 4)
table(avg_clust)
```

```
#> avg_clust
#>  1  2  3  4 
#>  7  1 12 30
```

Anggota cluster paling banyak diperoleh pada cluster 4 sebanyak 30 cluster. Namun terdapat cluster yang hanya memiliki 1 anggota saja yaitu pada cluster 2. Hal ini dapat dijadikan indikasi bahwa observasi pada cluster 2 yaitu "Alaska" merupakan anomali data. 

Berikut visualisasi dendrogram yang terbentuk pada masing-masing cluster.


```r
fviz_dend(us_hc_avg, k = 4, k_colors = "jco", rect = T, main = "Average Linkage Cluster")
```

<img src="/blog/2021-02-22-introduction-to-hierarchical-clustering_files/figure-html/unnamed-chunk-25-1.png" width="768" style="display: block; margin: auto;" />

Cara membaca cluster pada dendrogram yaitu  sebagai berikut :

* Cluster 1 digambarkan oleh dendrogram berwarna abu-abu.
* Cluster 2 digambarkan oleh dendrogram berwarna kuning.
* Cluster 3 digambarkan oleh dendrogram berwarna merah.
* Cluster 4 digambarkan oleh dendrogram berwarna biru.

### Dendrogram Centroid Linkage


```r
fviz_dend(us_hc_centroid, cex = 0.5, main = "Cluster Dendrogram Centroid Linkage")
```

<img src="/blog/2021-02-22-introduction-to-hierarchical-clustering_files/figure-html/unnamed-chunk-26-1.png" width="768" style="display: block; margin: auto;" />

Dendrogram yang terbentuk sangat berbeda dengan dedrogram pada model sebelumnya. Terdapat ketidak teraturan distance pada dendrogram di beberapa cabang, dimana nilai distance antar cluster yang lebih rendah dibandingkan nilai distance antar observasinya. Kondisi ini disebut juga **inversion**.

Hal ini dikarenakan pada centroid linkage terdapat pembentukan centroid atau pusat cluster yang "memusatkan" seluruh jarak observasi pada cluster. Sehingga ketika dihitung nilai (dis)similarity antar cluster, dapat dihasilkan nilai (dis)similarity yang lebih rendah dibandingkan (dis)similarity antar observasinya. Dalam kata lain, similarity dapat meningkat selama proses clustering. Hal inilah yang perlu diperhatikan bila menggunakan centroid method. Diskusi lebih lanjut dapat dilihat [disini](https://stats.stackexchange.com/questions/217519/centroid-linkage-clustering-with-hclust-yields-wrong-dendrogram).

Meskipun begitu, apabila dilihat dari nilai korelasi distancenya menunjukkan nilai yang sedikit cukup merepresentasikan data yang ada. 


```r
centroid_coph <- cophenetic(us_hc_centroid)
cor(centroid_coph, us_dist)
```

```
#> [1] 0.6074717
```

Berikut ini anggota cluster pada 4 cluster yang terbentuk.


```r
centroid_clust <- cutree(us_hc_centroid, k = 4)
table(centroid_clust)
```

```
#> centroid_clust
#>  1  2  3  4 
#> 47  1  1  1
```

Anggota cluster didominasi pada cluster 1 sebanyak 47 observasi, namun untuk cluster yang lain hanya memiliki 1 observasi saja. Berikut visualiasi cluster yang terbentuk untuk masing-masing dendrogramnya.


```r
fviz_dend(us_hc_centroid, k = 4, k_colors = "jco", rect = T, 
          main = "Centroid Linkage Cluster")
```

<img src="/blog/2021-02-22-introduction-to-hierarchical-clustering_files/figure-html/unnamed-chunk-29-1.png" width="768" style="display: block; margin: auto;" />

Hasil partisi cluster yang terbentuk tidak sesuai dengan yang seharusnya. Berikut deskripsi anggota cluster yang seharusnya:

* Cluster 1 terdiri dari negara New Jersey hingga California.
* Cluster 2 beranggotakan North California.
* Cluster 3 beranggotakan Alaska.
* Cluster 4 beranggotakan Vermont.

Sepertinya terdapat kesulitan dalam melakukan pemotongan otomatis pada dendrogram yang mungkin dikarenakan oleh adanya *inversion*.

### Dendrogram Ward's Minimum Variance


```r
fviz_dend(us_hc_ward, cex = 0.5, 
          main = "Cluster Dendrogram Ward's Minimum Variance")
```

<img src="/blog/2021-02-22-introduction-to-hierarchical-clustering_files/figure-html/unnamed-chunk-30-1.png" width="768" style="display: block; margin: auto;" />

Dendrogram yang diperoleh dari metode ward's minimum variance terlihat **terpartisi dengan sangat baik**. Pada tiap cluster, terlihat nilai height yang rendah atau similarity yang amat tinggi antar observasinya. Hal ini dikarenakan cara kerja metode ward's minimum variance yang meminimumkan nilai within sum of squared (wss) tiap cluster. Umumnya, dendrogram metode ini menghasilkan partisi yang baik dan dikatkan yang paling menyerupai hasil *k-means clustering* (salah satu metode partitional clustering).

Berdasarkan nilai korelasi distance untuk metode ward's, diperoleh 0.69, yang mana dari segi nilai tidak terlalu besar, namun untuk melakukan partisi model cukup mudah.


```r
ward_coph <- cophenetic(us_hc_ward)
cor(ward_coph, us_dist)
```

```
#> [1] 0.6975266
```

Berikut ini anggota cluster pada 4 cluster yang terbentuk.


```r
ward_clust <- cutree(us_hc_ward, k = 4)
table(ward_clust)
```

```
#> ward_clust
#>  1  2  3  4 
#>  7 12 19 12
```

Anggota cluster yang diperoleh pada model ward's cukup mirip seperti model complete linkage. Anggota cluster yang paling sedikit yaitu pada cluster 1. 


```r
fviz_dend(us_hc_ward, k = 4, k_colors = "jco", rect = T, 
          main = "Ward's Minimum Variance Cluster")
```

<img src="/blog/2021-02-22-introduction-to-hierarchical-clustering_files/figure-html/unnamed-chunk-33-1.png" width="768" style="display: block; margin: auto;" />

Cara membaca cluster pada dendrogram sebagai berikut :

* Cluster 1 digambarkan oleh dendrogram berwarna biru
* Cluster 2 digambarkan oleh dendrogram berwarna kuning
* Cluster 3 digambarkan oleh dendrogram berwarna merah
* Cluster 4 digambarkan oleh dendrogram berwarna abu-abu

### Dendrogram Terpilih

Dari kelima dendrogram yang sudah dibuat, kita dapat memilih salah satu yang ingin digunakan. Pemilihannya dapat berdasarkan hasil validasi cluster tree menggunakan korelasi **cophenetic distance** dengan **jarak original** masing-masing observasi yang tadi telah dihitung:


```r
data.frame(complete = cor(complete_coph, us_dist),
          single = cor(single_coph, us_dist),
          average = cor(avg_coph, us_dist),
          centroid = cor(centroid_coph, us_dist),
          ward = cor(ward_coph, us_dist)) %>% 
  tidyr::pivot_longer(cols = colnames(.),names_to = "method", values_to = "correlation")
```

```
#> # A tibble: 5 x 2
#>   method   correlation
#>   <chr>          <dbl>
#> 1 complete       0.698
#> 2 single         0.541
#> 3 average        0.718
#> 4 centroid       0.607
#> 5 ward           0.698
```

Dapat terlihat bahwa nilai korelasi yang dihasilkan **average linkage** paling tinggi dibanding metode lainnya, disusul oleh **complete linkage**, dan **ward linkage**. Mempertimbangkan pula dendrogram yang dihasilkan, dendrogram dari average linkage menghasilkan cluster outlier yang hanya berisi negara bagian Alaska, dan ditakutkan membuat hasil clustering tidak optimal. Oleh karena itu, akan dipilih metode **complete linkage** dengan pertimbangan hasil correlation yang tinggi dan menghasilkan dendrogram yang cukup mudah diinterpretasikan.

## Validasi Cluster

Dalam hierarchical clustering, menentukan banyaknya cluster optimal yang harus dibentuk terbilang cukup sulit. Secara umum, untuk menentukan cluster yang baik terdapat dua pengukuran yang sering dilakukan:

1. **Internal measures**

Internal measures merupakan pengukuran untuk melakukan cluster validation berdasarkan informasi internal yang terdapat pada data. Metode yang biasa digunakan pada internal measure yaitu **connectivity**, **dunn index**, dan **silhouette**. Nilai connectivity berkisar antara 0-inf, dimana cluster yang baik memiliki connectivity yang kecil. Sementara itu, nilai silhouette dan dunn index diharapkan tinggi untuk mengatakan bahwa clustering semakin baik.

2. **Stability measures**

Stability measures merupakan pengukuran validation cluster yang lebih mendalam dibandingkan internal measures. Stability mengukur tingkat konsistensi dari hasil cluster yang terbentuk apabila terdapat satu variabel yang dihilangkan dalam satu waktu. Pengukuran yang dilakukan pada stability yaitu:

* **Average proportion of non-overlap (APN)**: mengukur proporsi "rata-rata jumlah observasi pada cluster yang sama" antara dendrogram yang dibuat menggunakan data yang lengkap dan data yang sudah dihilangkan satu variabelnya.
* **Average distance (AD)**: jarak rata-rata antar observasi dalam cluster yang sama, pada pembuatan cluster dengan data yang lengkap dan data yang sudah dihilangkan satu variabelnya.
* **Average distance between means (ADM)**: jarak rata-rata antar pusat cluster pada pembuatan cluster dengan data yang lengkap dan data yang sudah dihilangkan satu variabelnya.
* **Figure of merit (FOM)**: rata-rata variansi intra-cluster pada variabel yang dihapus, dimana clustering dilakukan menggunakan sisa kolom yang tidak terhapus.

> Nilai dari APN, ADM, dan FOM measure stability diatas memiliki range 0-1, di mana semakin kecil nilainya, maka akan semakin konsisten hasil clustering yang terbentuk. Sedangkan nilai AD berkisar pada range 0-inf, semakin kecil nilainya juga semakin baik hasil clusternya[^6].
Bahasan lebih jauh mengenai cluster validation dapat dibaca [disini](https://www.datanovia.com/en/lessons/cluster-validation-statistics-must-know-methods/).

Untuk melakukan validasi cluster, R menyediakan package `clValid` dengan fungsi yang digunakan adalah `clValid()`. Kita akan coba cek jumlah cluster optimal berdasarkan internal measures dan stability measures menggunakan data `us_z` yang sudah dilakukan standarisasi.

Fungsi `clValid()` memiliki beberapa argumen diantaranya:

* `obj`: data yang akan digunakan dalam clustering
* `nClust`: banyak cluster yang akan di evaluasi
* `clMethods`: metode clustering yang digunakan, beberapa diantaranya yang bisa digunakan yaitu "hierarchical, "kmeans", "diana", "fanny", "som", "model", "sota", "pam", "clara", dan "agnes". Bisa menggunakan lebih dari satu metode yang akan di evaluasi.
* `validation`: pengukuran validasi cluster yang digunakan, beberapa diantaranya yaitu "internal", "stability", dan "biological". Bisa menggunakan lebih dari satu metode yang akan di evaluasi.
* `metric`: pengukuran (dis)similarity yang digunakan, beberapa yang bisa digunakan yaitu "euclidean", "correlation", dan "manhattan".
* `method`: metode hierarchical clustering yang digunakan, beberapa yang bisa digunakan yaitu "ward", "single", "complete", dan "average". 


```r
library(clValid)
# internal measures
internal <- clValid(us_z, nClust = 3:5, 
                    clMethods = "agnes", 
                    validation = "internal", 
                    metric = "euclidean",
                    method = "complete")
summary(internal)
```

```
#> 
#> Clustering Methods:
#>  agnes 
#> 
#> Cluster sizes:
#>  3 4 5 
#> 
#> Validation Measures:
#>                           3       4       5
#>                                            
#> agnes Connectivity  11.8730 24.7817 25.1067
#>       Dunn           0.2648  0.1622  0.1709
#>       Silhouette     0.3692  0.3160  0.3174
#> 
#> Optimal Scores:
#> 
#>              Score   Method Clusters
#> Connectivity 11.8730 agnes  3       
#> Dunn          0.2648 agnes  3       
#> Silhouette    0.3692 agnes  3
```


```r
# stabilitas measures
stability <- clValid(us_z, nClust = 3:4, 
                     clMethods = "agnes", 
                     validation = "stability", 
                     metric = "euclidean",
                     method = "complete")
# hanya menampilkan skor optimal
optimalScores(stability)
```

```
#>         Score Method Clusters
#> APN 0.2311732  agnes        4
#> AD  1.6246247  agnes        4
#> ADM 0.5947000  agnes        4
#> FOM 0.7425723  agnes        4
```

Berdasarkan hasil validasi cluster, internal measures menghasilkan jumlah cluster terbaik di angka 3, sementara stability measures menghasilkan angka 4. **Penggunaan cluster sebanyak 3 atau 4 bisa dijadikan opsi untuk melakukan partisi data**. Nilai yang lebih baik dapat ditentukan berdasarkan kemudahan pengguna untuk melakukan interpretasi (misalnya profiling/analisis karakteristik) untuk tiap clusternya.

## Kesimpulan

Kita telah melakukan tahap demi tahap pembuatan hierarchical clustering dari pembacaan data hingga pembentukan dendrogram, berikut pemotongan dendrogramnya untuk clustering data. Berikut adalah tampilan dendrogram terpilih menggunakan pemotongan 4 cluster dan beberapa insight yang dapat diambil.


```r
fviz_dend(us_hc_complete, k = 4, k_colors = "jco", rect = T, 
          main = "Complete Linkage Cluster")
```

<img src="/blog/2021-02-22-introduction-to-hierarchical-clustering_files/figure-html/unnamed-chunk-37-1.png" width="768" style="display: block; margin: auto;" />

Terdapat 2 cluster besar dan tiap cluster besar dapat dipecah kembali menjadi 2 cluster yang lebih kecil. Kemiripan antara negara bagian berdasarkan tingkat kriminalitasnya dapat terlihat, dan pasangan negara bagian yang paling serupa adalah Iowa & New Hampshire.

# Aplikasi Hierarchical Clustering

Hierarchical Clustering memiliki keunggulan yang tidak dimiliki oleh metode clustering lain, yaitu dapat merepresentasikan kedekatan antar tiap data dengan dendrogramnya. Hal ini amat bermanfaat khususnya dalam **analisis network/komunitas** atau deteksi data dari suatu komunitas. Oleh karena itu hierarchical clustering banyak dilakukan pada kasus-kasus berikut:

1. [Social Network Community Detection](https://www.hindawi.com/journals/complexity/2017/3719428/)
2. [Analisis Evolusi Mahluk Hidup - Biodiversity of Butterflies](https://www.floridamuseum.ufl.edu/science/at-last-butterflies-get-a-bigger-better-evolutionary-tree/)
3. [Analisis Penyebaran Penyakit - Nextrain for COVID19 Tracking](https://academic.oup.com/bioinformatics/article/34/23/4121/5001388)

# Referensi

[^1]: Tan, P.N., Steinbach, M., Kumar, V. (2006) Introduction to Data Mining. Boston: Pearson Education.

[^2]: Fred, A.L.N. dan Leitao, J.M.N. (2000) Partitional vs Hierarchical Clustering Using a Minimum Grammar Complexity Approach, di F.J. Ferri et al. (Eds.): SSPR&SPR 2000, LNCS 1876, hal. 193-202. Berlin: Springer-Verlag

[^3]: University of Cincinnati Business Analytics. UC Business Analytics R Programming Guide, Bab [Hierarchical Clustering](https://uc-r.github.io/hc_clustering)

[^4]: [(Dis)similarity Measure](https://www.datanovia.com/en/lessons/clustering-distance-measures/)

[^5]: Rhys, H.I. (2020) [Machine Learning with R, the tidyverse, and mlr](https://livebook.manning.com/book/machine-learning-for-mortals-mere-and-otherwise/chapter-17/). USA: Manning Publications Co.
