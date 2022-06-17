## Panduan montiR

'montiR' (Model biOmass diNamik singkaT dI R) merupakan kumpulan model biomas dinamik untuk pengelolaan perikanan yang ditulis menggunakan R. Model surplus produksi dibangun atas asumsi equilibrium dan non-equilibrium yang dipopulerkan penggunaannya oleh Hilborn dan Walters (1992). Penjelasan mengenai latar belakang, kelebihan dan kekurangan dari tiap metode akan dijelaskan di panduan package ini sebagai pendahuluan untuk menjelaskan mengenai evolusi metode surplus produksi hingga didapatkan metode yang menghasilkan estimasi lebih baik dengan asumsi non-equilibrium yang dihitung dengan data fitting menggunakan observation error (Polacheck et al., 1993). 

Struktur serta fungsi yang terdapat pada package montiR dapat dilihat pada struktur berikut

[1. Metode sebelumnya](https://habeebollah.github.io/man_montiR.html#1-Metode-sebelumnya)

[1.a. Surplus production dengan asumsi equilibrium](https://habeebollah.github.io/man_montiR.html#1.a.-surplus-production-dengan-asumsi-equilibrium)

[1.b. Multiple regression dengan asumsi non-equilibrium](https://habeebollah.github.io/man_montiR.html#1.b.-surplus-produksi-dengan-asumsi-non-equilibrium-menggunakan-multiple-regression)

[2. Data fitting dengan asumsi non-equilibrium](https://habeebollah.github.io/man_montiR.html#2-surplus-produksi-dengan-asumsi-non-equilibrium-menggunakan-data-fitting)

[2.a. Data plotting](https://habeebollah.github.io/man_montiR.html#2.a.-data-plotting)

[2.b. Estimasi parameter surplus production](https://habeebollah.github.io/man_montiR.html#2.b.-estimasi-parameter-surplus-production-dengan-data-fitting)

[2.c. Menghitung reference point](https://habeebollah.github.io/man_montiR.html#2.c.-menghitung-reference-point-untuk-pengelolaan)

[2.d. Menghitung standard error dari reference point](https://habeebollah.github.io/man_montiR.html#2.d.-menghitung-standard-error-dari-reference-point)

[3. Analisis bayesian dengan asumsi non-equilibrium](https://habeebollah.github.io/man_montiR.html#3-meningkatkan-akurasi-pendugaan-stok-dengan-surplus-production-di-tingkat-spesies-menggunakan-bayesian)

[4. Membuat proyeksi pengelolaan](https://habeebollah.github.io/man_montiR.html#4-membuat-proyeksi-atas-kebijakan-reference-point-berdasar-tingkat-pemanfaatan-perikanan)



### 1. Metode Surplus Production yang tidak direkomendasikan 
### 1.a. Surplus Production dengan asumsi equilibrium
Pendekatan ini akan memberikan estimasi MSY dan Emsy yang tinggi (Hilborn and Walter, 1992; Polacheck, et al. 1993), sehingga sangat tidak disarankan untuk dijadikan sebagai panduan dalam pengambilan kebijakan perikanan. Overestimasi reference point pada kondisi ketika status perikanan sedang dalam kondisi overexploited akan memberikan ilusi bahwa stok ikan masih banyak, sehingga dapat merugikan pelaku perikanan karena jumlah tangkapan yang rendah dan merugikan stok ikan karena semakin tingginya pemanfaatan. 

Tool ini menggunakan asumsi equilibrium yang menghitung MSY dan Emsy dengan linear regression sederhana (Sparre dan Venema, 1998) ataupun dengan menggunakan metode non-linear (Punt dan Hilborn, 1996). Selain menghitung MSY dan Emsy, disarankan untuk juga menghitung confident interval dan r squared yang diterapkan pada metode linear regression. 

Metode ini akan selalu menghasilkan perhitungan MSY dan Emsy meskipun data yang digunakan berkualitas rendah dengan sedikit kontras (Hilborn dan Walters, 1992) ataupun memiliki data time series terbatas (Sparre dan Venema, 1998). menghasilkan Sebagaimana yang disebut oleh Hilborn dan Walters (1992), biasanya nilai r squared dari linear regression menunjukkan bahwa relasi antara CPUE dengan effort sangat tinggi sehingga memberikan kesan bahwa analisa yang dihasilkan dari metode ini benar.


### 1.b. Surplus produkction dengan asumsi non-equilibrium menggunakan multiple regression

Metode multiple regression merupakan metode selanjutnya yang digunakan untuk menghitung jumlah tangkapan ikan lestari (MSY) dan upaya penangkapan ikan lestari (Emsy) dengan model Schaefer. Metode ini menggunakan asumsi non-equilibrium dengan pendekatan least square (Walters and Hilborn, 1976) dan disebut menghasilkan bias dalam estimasi parameter surplus production, termasuk juga menghasilkan bias dalam proses lanjutan ketika parameter yang diestimasi digunakan untuk menghitung MSY dan Emsy (Uhler, 1979). Berdasar masukan ini, Hilborn dan Walters (1992) kemudian merevisi input yang digunakan dalam penghitungan multiple regression.

Penggunaan metode multiple regression ini sangat mudah dan cenderung akan mencari hubungan antar parameter yang diestimasi serta menghasilkan nilai r squared yang tinggi. Dapat dilihat bahwa metode multiple regression pasti akan menghasilkan estimasi, meskipun terkadang hasilnya terlihat kurang dapat dipercaya. Misalnya angka r (intrinsic growth parameter) yang minus seperti diatas. Hal ini terjadi karena metode surplus produksi disebut memiliki kelemahan jika menggunakan data yang memiliki tipe one way trip (Hilborn dan Walters, 1992).

### 2. Surplus produksi dengan asumsi non-equilibrium menggunakan data fitting

Metode time series fitting disebut sebagai metode yang lebih baik dibandingkan dengan dua metode lain (metode equilibrium dan multiple regression) yang digunakan untuk melakukan estimasi parameter dalam model surplus produksi (Hilborn dan Walter, 1992; Polacheck, et al. 1993; Punt dan Hilborn, 1996). Disini akan dibahas langkah yang disarankan untuk melakukan analisis dengan data fitting untuk meningkatkan akurasi perhitungan MSY, Bmsy dan Emsy.

Selanjutnya, metode ini lebih dikenal dengan sebutan Biomass Dynamic Model yang paling umum dibangun dari Schaefer (1954), Fox (1970) dan Pella-Tomlinson (1969). Saat ini `montiR` dibangun dengan model Schaefer yang dituliskan dengan

$ B_{t+1} = {B_{t} + rB_{t} (1- {B_{t} \over K}) - C_{t}} $

dimana:

$B_{t}$ = biomass yang dimanfaatkan pada awal tahun $t$

$r$ = laju pertumbuhan intrinsic

$K$ = carrying capacity

$C_{t}$ = jumlah tangkapan (volume) pada tahun $t$

dengan

$ I_{t+1} = {C_{t} \over E_{t}} = q B_{t} e^\epsilon $

dimana: 

$I_{t+1}$ = catch per unit of effort (CPUE)/indeks kelimpahan

$E_{t}$ = upaya penangkapan

$q$ = catchability

$\epsilon$ = observation error


Penggunaan montiR diawali dengan penyediaan data yang meliputi data tahun, data tangkapan serta data upaya yang dibuat dalam dataframe. Berikut adalah contoh untuk membuat dataframe sebagai input untuk analisis `montiR` menggunakan data tangkapan Yellowfin Tuna di East Pacific (Schaefer, 1957).

```markdown
df <- data.frame(year=c(1934:1955),
                 catch=c(60913,72294,78353,91522,78288,110417,114590,76841,41965,50058,64094,
                        89194,129701,160134,200340,192458,224810,183685,192234,138918,138623,140581),
                 effort=c(5879,6295,6771,8233,6830,10488,10801,9584,5961,5930,6397,9377,13958,
                        20381,23984,23013,31856,18726,31529,36423,24995,17806))
```


### 2.a. Data plotting

Langkah paling penting sebelum melakukan analisis data adalah memeriksa apakah data yang akan digunakan memenuhi persyaratan dan asumsi yang dibutuhkan untuk analisis biomass dynamic model, termasuk memilih jenis langkah apa yang harus dilakukan ketika data yang dibutuhkan tidak memenuhi asumsi. Para ahli statistik selalu memulai analisisnya dengan, "Plot your data!". 

Langkah untuk melihat grafik jumlah tangkapan (catch), upaya (effort) serta catch per unit of effort (CPUE)/indeks kelimpahan dari data yang dimiliki dapat dilakukan dengan mudah menggunakan kode dan contoh data yang tersedia sebagaimana berikut:

```markdown
plotInit(df=df.goodcontrast)
plotInit(df=df.onewaytrip)

```

Disini kita akan melihat dua jenis data yang biasanya terdapat pada perikanan, goodcontrast dan onewaytrip. Biomass dynamic model dengan menggunakan metode data fitting mensyaratkan data yang memiliki kontras yang cukup pada Catch per Unit Effort (CPUE) dengan pola menurun dan naik dan paling tidak memiliki 20 tahun entry untuk tangkapan dan upaya (pers comm, Ray Hilborn), dimana hal ini bisa dilihat pada contoh data `df.goodcontrast`. Contoh data yang tidak memiliki kontras yang baik dapat dilihat pada `df.onewaytrip`. Jenis data yang berbeda harus dianalisis menggunakan cara yang berbeda pula.

Pada tahap ini diharapkan data sudah melalui langkah data standardization yang biasanya diolah dengan menggunakan Generalized Linear Model.


### 2.b. Estimasi parameter surplus production dengan data fitting

Tool ini melakukan estimasi parameter K, B0, r, q dan menentukan jumlah tangkapan ikan lestari (MSY), biomassa ikan lestari (Bmsy), serta upaya penangkapan ikan lestari (Emsy) menggunakan data runut waktu dengan asumsi non-equilibrium untuk model Schaefer.

Proses estimasi diawali dengan mencari angka awal yang diperkirakan sesuai dengan parameter K, B0, r, q. Hal ini dilakukan dengan menduga angka parameter, kemudian dilihat apakah grafik yang dihasilkan dari parameter yang kita duga memberikan grafik yang sesuai dengan data yang akan kita lakukan pendugaannya. Misalnya kita akan menduga parameter surplus production dari data `df.goodcontrast`, maka cara estimasinya dilakukan dengan:

```markdown
library('montiR')

K <- 1000
B0 <- K
r <- 0.2
q <- 0.00025

inpars <- c(K, B0, r, q)
Par_init(inpars=inpars, df=df.goodcontrast)

```

Mencari angka awal ini dapat dilakukan terus menerus dengan merubah angka K, r dan q sehingga dirasa grafik hasil dari angka awal dirasa sudah sesuai (fit) dengan data yang akan kita analisis.

Setelah angka awal didapat, maka langkah selanjutnya adalah melakukan optimasi parameter melalui Maximum Likelihood Estimation dengan observation error. Observation error menggunakan asumsi bahwa terdapat kesalahan pada hubungan antara biomass dan indeks kelimpahan, sehingga parameter ini perlu untuk diestimasi. Indeks kelimpahan diasumsikan mengikuti distribusi log-normal untuk melakukan estimasi parameter K, B0, r, q dan sigma (observation error).

Proses estimasi parameter ini dilakukan dengan langkah sebagai berikut:

```markdown
library('montiR')

K <- 1000
B0 <- K
r <- 0.2
q <- 0.00025
sigma <- 0.1

inpars <- c(log(K), log(B0), log(r), log(q), log(sigma))
fit <- optim(par=inpars,
             fn=Par.min,
             df=df.goodcontrast,
             method="Nelder-Mead",
             OWT=FALSE, currentF = 0.7, weight = 0.5)

Par.vals <- data.frame(SPpar = c("K", "B0", "r", "q", "sigma"),
                       init_pars = c(K, B0, r, q, sigma),
                       fitted_pars = exp(fit$par))
Par.vals
```

Disini angka awal yang didapatkan dari proses sebelumnya kemudian disimpan sebagai inpars. Karena kita tahu bahwa parameter ini pasti bernilai positif, maka angka yang masuk disimpan dalam bentuk log. Setelah proses optimasi dengan perhitungan Maximum Likelihood Estimation selesai dilakukan, angka yang didapat masih dalam bentuk logarithmic sehingga perlu dilakukan backtransform. Angka awal dan angka akhir estimasi parameter K, B0, r, q dan sigma (observation error) hasil perhitungan maximum likelihood estimation dapat dilihat dengan meng-klik  `Par.vals`.

Tool ini sudah disesuaikan untuk kebutuhan data yang terbatas (dapat mengakomodasi hilangnya input data upaya penangkapan) serta sudah memperhitungkan kesalahan dalam pengambilan data (observation error). 

### 2.c. Menghitung reference point untuk pengelolaan

Tool ini menghasilkan jumlah stok ikan yang lestari (Bmsy), jumlah tangkapan ikan lestari (MSY) dan upaya penangkapan ikan lestari (Emsy) untuk model Schaefer dan Fox.

### 2.d. Menghitung standard error dari reference point

Tool ini mengestimasi jumlah stok ikan yang lestari (Bmsy), jumlah tangkapan ikan lestari (MSY) dan upaya penangkapan ikan lestari (Emsy) serta menghitung standard error menggunakan data runut waktu dengan asumsi non-equilibrium untuk model Schaefer dan Fox.

### 3. Meningkatkan akurasi pendugaan stok dengan surplus production di tingkat spesies menggunakan bayesian

Data prior setiap spesies untuk parameter pertumbuhan r diambil dari database fishbase dan sealifebase untuk mendukung pendugaan stok di tingkat spesies. Langkah lanjutan untuk melakukan estimasi parameter surplus production menggunakan pendekatan bayesian dan data runut waktu dengan asumsi non-equilibrium untuk model Schaefer dan Fox juga diberikan. Secara teori langkah ini akan menghasilkan pendugaan stok yang lebih akurat di tingkat spesies.

Kajian lebih lanjut menyebutkan bahwa package JABBA yang digunakan untuk melakukan analisis bayesian menghasilkan bias ketika menggunakan data yang memiliki kontras minimal semisal tipe one-way-trip (Sant’Ana, et. al., 2020).

### 4. Membuat proyeksi atas kebijakan reference point berdasar tingkat pemanfaatan perikanan

Tool ini akan membuat grafik proyeksi biomass per biomass at msy (B/Bmsy) dan fishing per fishing at msy (F/Fmsy) sebagai panduan untuk melihat kebijakan yang akan dibuat berdasarkan Bmsy, MSY dan Emsy sebagai reference point. Proyeksi dibuat dengan pendekatan deterministic secara default, dan terdapat opsi untuk tujuan stochastic.
