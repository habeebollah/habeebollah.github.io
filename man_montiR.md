## Panduan montiR

'montiR' (Model biOmass diNamik singkaT dI R) merupakan kumpulan model biomas dinamik untuk pengelolaan perikanan yang ditulis menggunakan R. Model surplus produksi dibangun atas asumsi equilibrium dan non-equilibrium yang dibangun atas konsep yang dibangun oleh Schaefer dan Fox. Penjelasan mengenai latar belakang, kelebihan dan kekurangan dari tiap metode serta contoh penggunaannya akan dijelaskan di panduan package ini. 

Struktur serta fungsi yang terdapat pada package montiR dapat dilihat pada gambar berikut

![Struktur](/img/montiR_fx.png)

[Linear Regression dengan asumsi equilibrium](https://habeebollah.github.io/man_montiR.html#1-surplus-production-dengan-asumsi-equilibrium)

[Multiple regression dengan asumsi non-equilibrium](https://habeebollah.github.io/man_montiR.html#2-surplus-produksi-dengan-asumsi-non-equilibrium-menggunakan-multiple-regression)

[Data fitting dengan asumsi non-equilibrium](https://habeebollah.github.io/man_montiR.html#3-surplus-produksi-dengan-asumsi-non-equilibrium-menggunakan-data-fitting)

[> Data plotting](https://habeebollah.github.io/man_montiR.html#3a-data-plotting)

[> Estimasi parameter surplus production](https://habeebollah.github.io/man_montiR.html#3b-estimasi-parameter-surplus-production-dengan-data-fitting)

[> Menghitung reference point](https://habeebollah.github.io/man_montiR.html#3c-menghitung-reference-point-untuk-pengelolaan)

[> Menghitung standard error dari reference point](https://habeebollah.github.io/man_montiR.html#3d-menghitung-standard-error-dari-reference-point)

[Analisis bayesian dengan asumsi non-equilibrium](https://habeebollah.github.io/man_montiR.html#4-meningkatkan-akurasi-pendugaan-stok-dengan-surplus-production-di-tingkat-spesies-menggunakan-bayesian)

[Membuat proyeksi pengelolaan](https://habeebollah.github.io/man_montiR.html#5-membuat-proyeksi-atas-kebijakan-reference-point-berdasar-tingkat-pemanfaatan-perikanan)


Penggunaan montiR diawali dengan penyediaan data yang meliputi data tahun, data tangkapan serta data upaya yang dibuat dalam dataframe seperti berikut:

```markdown
df <- data.frame(tahun=c(..., ..., ....),
                 tangkapan=c(..., ..., ....),
                 upaya=c(..., ..., ....))
```


### 1. Surplus Production dengan asumsi equilibrium
Pendekatan yang ditampilkan disini hanya untuk tujuan pembelajaran sebagai contoh model yang akan memberikan estimasi MSY dan Emsy yang tinggi (Hilborn and Walter, 1992; Polacheck, et al. 1993), sehingga sangat tidak disarankan untuk dijadikan sebagai panduan dalam pengambilan kebijakan perikanan. Overestimasi reference point pada kondisi ketika status perikanan sedang dalam kondisi overexploited akan memberikan ilusi bahwa stok ikan masih banyak, sehingga dapat merugikan pelaku perikanan karena jumlah tangkapan yang rendah dan merugikan stok ikan karena semakin tingginya pemanfaatan. 

Tool ini menggunakan asumsi equilibrium dengan analisa linear regression sederhana untuk menghitung MSY and Emsy termasuk confident interval dan r squared dengan adj R squared menggunakan model yang disusun oleh Schaefer dan Fox. Cara penghitungan menggunakan metode yang tersedia pada Sparre and Venema (1998) dengan contoh penggunaan dengan langsung memasukkan dataframe yang sudah disusun sesuai langkah diatas.

```markdown
library("montiR")
SF_Eq(df.javaTrawl)

$data
  year catch effort     CPUE.s    CPUE.f
1 1969  50.0    623 0.08025682 -2.522524
2 1970  49.0    628 0.07802548 -2.550720
3 1971  47.5    520 0.09134615 -2.393099
4 1972  45.0    513 0.08771930 -2.433613
5 1973  51.0    661 0.07715582 -2.561928
6 1974  56.0    919 0.06093580 -2.797934
7 1975  66.0   1158 0.05699482 -2.864795
8 1976  58.0   1970 0.02944162 -3.525346
9 1977  52.0   1317 0.03948368 -3.231868

$result
    analysis     Schaefer          Fox
1        MSY   66.0207509   60.9388243
2  MSY.CIlow   52.8471074   52.2093313
3  MSY.CIupp   87.9430572   73.1735762
4       Emsy 1241.2063030 1273.7079052
5 Emsy.CIlow  993.5385764 1091.2491142
6 Emsy.CIupp 1653.3510376 1529.4315838
7         r2    0.9278215    0.9661845
8     adj.r2    0.9175103    0.9613537
```

Disini dapat dilihat hasil analisis menggunakan model Schaefer dan Fox, berupa angka MSY dan Emsy serta rentang bawah dan atas untuk confident interval pada 95% dengan perhitungan r dan adjusted r squared. Sebagaimana yang disebut oleh Hilborn dan Walters (1992), nilai r menunjukkan bahwa relasi antara CPUE dengan effort sangat tinggi.


### 2. Surplus produksi dengan asumsi non-equilibrium menggunakan multiple regression

Metode multiple regression merupakan metode selanjutnya yang digunakan untuk menghitung jumlah tangkapan ikan lestari (MSY) dan upaya penangkapan ikan lestari (Emsy) dengan model Schaefer. Metode ini menggunakan asumsi non-equilibrium dengan pendekatan least square (Walters and Hilborn, 1976) dan disebutkan dapat menghasilkan bias dalam estimasi parameter surplus production, termasuk juga menghasilkan bias lanjutan ketika parameter yang diestimasi digunakan untuk menghitung MSY dan Emsy (Uhler, 1979). Berdasar masukan ini, Hilborn dan Walters (1992) kemudian merevisi input yang digunakan dalam penghitungan multiple regression.

Penggunaan metode multiple regression ini sangat mudah dan cenderung akan mencari hubungan antar parameter yang diestimasi serta menghasilkan nilai adjusted r squared yang tinggi. Contoh penggunaan package montiR dapat dilihat pada box berikut

```markdown
library("montiR")
Smreg(df.eastpacCatch)

  analysis      WH.1976       HW.1992
1        K 1.311046e+06  1.762816e+07
2        r 8.517976e-01 -2.310706e-01
3        q 8.021921e-06  1.306083e-06
4     Bmsy 6.555229e+05  8.814079e+06
5      MSY 2.791864e+05 -1.018337e+06
6     Emsy 5.309188e+04 -8.845938e+04
7       r2 2.809304e-01  3.907165e-01
8   adj.r2 2.010338e-01  2.831959e-01
```

Dapat dilihat bahwa metode multiple regression pasti akan menghasilkan estimasi, meskipun terkadang hasilnya terlihat kurang dapat dipercaya. Misalnya angka r yang minus seperti diatas. Hal ini terjadi karena metode surplus produksi disebut memiliki kelemahan jika menggunakan data yang memiliki tipe one way trip (Hilborn dan Walters, 1992).

### 3. Surplus produksi dengan asumsi non-equilibrium menggunakan data fitting

Metode time series fitting disebut sebagai metode yang lebih baik dibandingkan dengan dua metode lain (metode equilibrium dan multiple regression) yang digunakan untuk melakukan estimasi parameter dalam model surplus produksi (Hilborn and Walter, 1992; Polacheck, et al. 1993). Disini akan dibahas langkah yang disarankan untuk melakukan analisis dengan data fitting untuk meningkatkan akurasi perhitungan MSY, Bmsy dan Emsy.

### 3.a. Data plotting

Langkah paling penting sebelum melakukan analisis data adalah memeriksa apakah data yang akan digunakan memenuhi persyaratan dan asumsi yang dibutuhkan untuk analisis biomass dynamic model, termasuk memilih jenis langkah apa yang harus dilakukan ketika data yang dibutuhkan tidak memenuhi asumsi. Para ahli statistik selalu memulai analisisnya dengan, "Plot your data!". 

Langkah untuk melihat grafik dari data yang dimiliki dapat dilakukan dengan mudah menggunakan kode dan contoh data yang tersedia sebagaimana berikut:

```markdown
plotInit(df=df.goodcontrast)
plotInit(df=df.onewaytrip)

```

Disini kita akan melihat dua jenis data yang biasanya terdapat pada perikanan, goodcontrast dan onewaytrip. Biomass dynamic model dengan menggunakan metode data fitting mensyaratkan data yang memiliki kontras yang cukup pada Catch per Unit Effort (CPUE) dengan pola menurun dan naik dan paling tidak memiliki 20 tahun entry untuk tangkapan dan upaya, dimana hal ini bisa dilihat pada contoh data `df.goodcontrast`. Data yang tidak memiliki kontras yang baik dapat dilihat pada `df.onewaytrip`. Jenis data yang berbeda harus dianalisis menggunakan cara yang berbeda pula.

### 3.b. Estimasi parameter surplus production dengan data fitting

Tool ini melakukan estimasi parameter K, B0, r, q dan menentukan jumlah tangkapan ikan lestari (MSY), biomassa ikan lestari (Bmsy), serta upaya penangkapan ikan lestari (Emsy) menggunakan data runut waktu dengan asumsi non-equilibrium untuk model Schaefer dan Fox. Tool ini sudah disesuaikan untuk kebutuhan data yang terbatas (dapat mengakomodasi hilangnya input data upaya penangkapan) serta sudah memperhitungkan kesalahan dalam pengambilan data (observation error). 

### 3.c. Menghitung reference point untuk pengelolaan

Tool ini menghasilkan jumlah stok ikan yang lestari (Bmsy), jumlah tangkapan ikan lestari (MSY) dan upaya penangkapan ikan lestari (Emsy) untuk model Schaefer dan Fox.

### 3.d. Menghitung standard error dari reference point

Tool ini mengestimasi jumlah stok ikan yang lestari (Bmsy), jumlah tangkapan ikan lestari (MSY) dan upaya penangkapan ikan lestari (Emsy) serta menghitung standard error menggunakan data runut waktu dengan asumsi non-equilibrium untuk model Schaefer dan Fox.

### 4. Meningkatkan akurasi pendugaan stok dengan surplus production di tingkat spesies menggunakan bayesian

Data prior setiap spesies untuk parameter pertumbuhan r diambil dari database fishbase dan sealifebase untuk mendukung pendugaan stok di tingkat spesies. Langkah lanjutan untuk melakukan estimasi parameter surplus production menggunakan pendekatan bayesian dan data runut waktu dengan asumsi non-equilibrium untuk model Schaefer dan Fox sehingga menghasilkan pendugaan stok yang lebih akurat di tingkat spesies juga diberikan.

Kajian lebih lanjut menyebutkan bahwa package JABBA yang digunakan untuk melakukan analisis bayesian menghasilkan bias ketika menggunakan data yang memiliki kontras minimal semisal tipe one-way-trip (Sant’Ana, et. al., 2020).

### 5. Membuat proyeksi atas kebijakan reference point berdasar tingkat pemanfaatan perikanan

Tool ini akan membuat grafik proyeksi biomass per biomass at msy (B/Bmsy) dan fishing per fishing at msy (F/Fmsy) sebagai panduan untuk melihat kebijakan yang akan dibuat berdasarkan Bmsy, MSY dan Emsy sebagai reference point. Proyeksi dibuat dengan pendekatan deterministic secara default, dan terdapat opsi untuk tujuan stochastic.
