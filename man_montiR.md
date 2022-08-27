## Panduan montiR

'montiR' (Model biOmass diNamik singkaT dI R) merupakan kumpulan model biomas dinamik untuk pengelolaan perikanan yang ditulis menggunakan R. Model surplus produksi dibangun atas asumsi equilibrium dan non-equilibrium yang dipopulerkan penggunaannya oleh Hilborn dan Walters (1992). Penjelasan mengenai latar belakang, kelebihan dan kekurangan dari tiap metode akan dijelaskan di panduan package ini sebagai pendahuluan untuk menjelaskan mengenai evolusi metode surplus produksi hingga didapatkan metode yang menghasilkan estimasi lebih baik dengan asumsi non-equilibrium yang dihitung dengan data fitting menggunakan observation error (Polacheck et al., 1993). 

### 1. Metode Surplus Production yang tidak direkomendasikan 
#### 1.a. Surplus Production dengan asumsi equilibrium
Pendekatan ini akan memberikan estimasi MSY dan Emsy yang tinggi (Hilborn and Walter, 1992; Polacheck, et al. 1993), sehingga sangat tidak disarankan untuk dijadikan sebagai panduan dalam pengambilan kebijakan perikanan. Overestimasi reference point pada kondisi ketika status perikanan sedang dalam kondisi overexploited akan memberikan ilusi bahwa stok ikan masih banyak, sehingga dapat merugikan pelaku perikanan karena jumlah tangkapan yang rendah dan merugikan stok ikan karena semakin tingginya pemanfaatan. 

Tool ini menggunakan asumsi equilibrium yang menghitung MSY dan Emsy dengan linear regression sederhana (Sparre dan Venema, 1998) ataupun dengan menggunakan metode non-linear (Punt dan Hilborn, 1996). Selain menghitung MSY dan Emsy, disarankan untuk juga menghitung confident interval dan r squared yang diterapkan pada metode linear regression. 

Metode ini akan selalu menghasilkan perhitungan MSY dan Emsy meskipun data yang digunakan berkualitas rendah dengan sedikit kontras (Hilborn dan Walters, 1992) ataupun memiliki data time series terbatas (Sparre dan Venema, 1998). Sebagaimana yang disebut oleh Hilborn dan Walters (1992), biasanya nilai r squared dari linear regression menunjukkan bahwa relasi antara CPUE dengan effort sangat tinggi sehingga memberikan kesan bahwa analisa yang dihasilkan dari metode ini benar secara statistik.


#### 1.b. Surplus production dengan asumsi non-equilibrium menggunakan multiple regression

Metode multiple regression merupakan metode selanjutnya yang digunakan untuk menghitung jumlah tangkapan ikan lestari (MSY) dan upaya penangkapan ikan lestari (Emsy) dengan model Schaefer. Metode ini menggunakan asumsi non-equilibrium dengan pendekatan least square (Walters and Hilborn, 1976) dan disebut menghasilkan bias dalam estimasi parameter surplus production, termasuk juga menghasilkan bias dalam proses lanjutan ketika parameter yang diestimasi digunakan untuk menghitung MSY dan Emsy (Uhler, 1979). Berdasar masukan ini, Hilborn dan Walters (1992) kemudian merevisi input yang digunakan dalam penghitungan multiple regression.

Penggunaan metode multiple regression ini sangat mudah dan cenderung akan mencari hubungan antar parameter yang diestimasi serta menghasilkan nilai r squared yang tinggi. Dapat dilihat bahwa metode multiple regression pasti akan menghasilkan estimasi, meskipun terkadang hasilnya terlihat kurang dapat dipercaya. Misalnya angka r (intrinsic growth parameter) yang minus. Hal ini terjadi karena metode surplus produksi disebut memiliki kelemahan jika menggunakan data yang memiliki tipe one way trip (Hilborn dan Walters, 1992).

### 2. Surplus production dengan asumsi non-equilibrium menggunakan data fitting

Metode time series fitting disebut sebagai metode yang lebih baik dibandingkan dengan dua metode lain (metode equilibrium dan multiple regression) yang digunakan untuk melakukan estimasi parameter dalam model surplus produksi (Hilborn dan Walter, 1992; Polacheck, et al. 1993; Punt dan Hilborn, 1996). Disini akan dibahas langkah yang disarankan untuk melakukan analisis dengan data fitting untuk meningkatkan akurasi perhitungan MSY, Bmsy dan Emsy.

Pendekatan ini selanjutnya lebih dikenal dengan sebutan Biomass Dynamic Model, dimana variasinya kebanyakan menggunakan perhitungan dari Schaefer (1954), Fox (1970) dan Pella-Tomlinson (1969). Saat ini `montiR` dibangun dengan model Schaefer yang dituliskan dengan

$ B_{t+1} = {B_{t} + rB_{t} (1- {B_{t} \over K}) - C_{t}} $

dimana:

$B_{t}$ = biomass yang dimanfaatkan pada awal tahun $t$

$r$ = laju pertumbuhan intrinsic

$K$ = carrying capacity

$C_{t}$ = jumlah tangkapan (volume) pada tahun $t$

dengan

$ I_{t+1} = {C_{t} \over E_{t}} = q B_{t} e^\epsilon $

dimana: 

$I_{t}$ = catch per unit of effort (CPUE)/indeks kelimpahan pada tahun $t$

$E_{t}$ = upaya penangkapan pada tahun $t$

$q$ = catchability

$\epsilon$ = observation error


Penggunaan `montiR` diawali dengan penyediaan data yang meliputi data tahun, data tangkapan serta data upaya yang dibuat dalam dataframe. Berikut adalah contoh untuk membuat dataframe sebagai input untuk analisis `montiR` menggunakan data tangkapan Yellowfin Tuna di East Pacific (Schaefer, 1957).

```markdown
df <- data.frame(year=c(1934:1955),
                 catch=c(60913,72294,78353,91522,78288,110417,114590,76841,41965,50058,64094,
                        89194,129701,160134,200340,192458,224810,183685,192234,138918,138623,140581),
                 effort=c(5879,6295,6771,8233,6830,10488,10801,9584,5961,5930,6397,9377,13958,
                        20381,23984,23013,31856,18726,31529,36423,24995,17806))
```


#### 2.a. Data plotting

Langkah paling penting sebelum melakukan analisis data adalah memeriksa apakah data yang akan digunakan memenuhi persyaratan dan asumsi yang dibutuhkan untuk analisis biomass dynamic model, termasuk memilih jenis langkah apa yang harus dilakukan ketika data yang dibutuhkan tidak memenuhi asumsi. Para ahli statistik selalu memulai analisisnya dengan, "Plot your data!". 

Langkah untuk melihat grafik jumlah tangkapan (catch), upaya (effort) serta catch per unit of effort (CPUE)/indeks kelimpahan dari data yang dimiliki dapat dilakukan dengan mudah menggunakan kode dan contoh data yang tersedia sebagaimana berikut:

```markdown
plotInit(df=df.goodcontrast0)
plotInit(df=df.onewaytrip0)

```

Disini kita akan melihat dua jenis data yang biasanya terdapat pada perikanan, goodcontrast dan onewaytrip. Biomass dynamic model dengan menggunakan metode data fitting mensyaratkan data yang memiliki kontras yang cukup pada Catch per Unit Effort (CPUE), ditunjukkan dengan adanya representasi pola turun dan naik serta paling tidak memiliki 20 tahun entry untuk tangkapan dan upaya (pers comm, Ray Hilborn). Contoh dari data yang memiliki kontras yang cukup dapat dilihat pada `df.goodcontrast0` dan `df.namibianCatch`, dimana contoh plot dari `df.goodcontrast0` dapat dilihat berikut:

![Tipe data dengan good contrast](/img/img_goodcontrast0.png)

Contoh data yang tidak memiliki kontras yang baik dapat dilihat pada `df.onewaytrip0` dan `df.eastpacCatch`. Dapat dilihat bahwa data tangkapan dan upaya memiliki pola meningkat dengan tidak memiliki pola menurun sebagaimana yang ditunjukkan dari plot `df.onewaytrip0` berikut:
![Tipe data one way trip](/img/img_onewaytrip0.png)

Jenis data yang berbeda harus dianalisis menggunakan cara yang berbeda pula. Hal ini akan dijelaskan lebih lanjut di bagian selanjutnya.

Pada tahap ini diharapkan data sudah melalui langkah data standardization yang biasanya diolah dengan menggunakan Generalized Linear Model.


#### 2.b. Estimasi parameter surplus production dengan data fitting

Tool ini melakukan estimasi parameter K, B0, r, q dan menentukan jumlah tangkapan ikan lestari (MSY), biomassa ikan lestari (Bmsy), serta upaya penangkapan ikan lestari (Emsy) menggunakan data runut waktu dengan asumsi non-equilibrium untuk model Schaefer.

Proses estimasi diawali dengan mencari angka awal yang diperkirakan sesuai dengan parameter K, B0, r, q. Hal ini dilakukan dengan menduga angka parameter, kemudian dilihat apakah grafik yang dihasilkan dari parameter yang kita duga memberikan grafik yang sesuai dengan data yang akan kita lakukan pendugaannya. Misalnya kita akan menduga parameter surplus production dari data `df.namibianCatch`, maka cara estimasinya dilakukan dengan mencari angka awal terlebih dahulu dengan:

```markdown
library('montiR')

K <- 1700
B0 <- K
r <- 0.3
q <- 0.0003

inpars <- c(K, B0, r, q)
Par_init(inpars=inpars, df=df.namibianCatch)

```

Mencari angka awal ini dilakukan dengan merubah angka K, r dan q sehingga dirasa grafik hasil dari angka awal (Estimation) dirasa sudah sesuai (fit) dengan data yang akan kita analisis (Observation). Sebagai panduan pada tahapan ini, angka r seharusnya berada pada rentang antara 0-1.3 dan angka q biasanya berada pada rentang antara 0-0.1.

Setelah angka awal didapat, maka langkah selanjutnya adalah melakukan optimasi parameter melalui Maximum Likelihood Estimation dengan observation error. Observation error menggunakan asumsi bahwa terdapat kesalahan pada hubungan antara biomass dan indeks kelimpahan, sehingga parameter ini perlu untuk diestimasi. Indeks kelimpahan diasumsikan mengikuti distribusi log-normal untuk melakukan estimasi parameter K, B0, r, q dan sigma (observation error).

Proses estimasi parameter ini dilakukan dengan langkah sebagai berikut:

```markdown
library('montiR')

K <- 1700
B0 <- K
r <- 0.3
q <- 0.0003
sigma <- 0.1
inpars <- c(log(K), log(B0), log(r), log(q), log(sigma))

> melakukan optimasi 
fit <- optim(par=inpars,
             fn=Par.min,
             df=df.namibianCatch,
             method="Nelder-Mead")

Par.vals <- data.frame(SPpar = c("K", "B0", "r", "q", "sigma"),
                       init_pars = c(K, B0, r, q, sigma),
                       fitted_pars = exp(fit$par))
Par.vals
  SPpar init_pars  fitted_pars
1     K   1.1e+03 2.326238e+03
2    B0   1.1e+03 1.105251e+03
3     r   1.3e+00 5.182160e-01
4     q   1.8e-03 8.317058e-04
5 sigma   1.0e-01 2.200941e-01

> memeriksa apakah sudah menghasilkan grafik yang fit
Par_init(inpars=exp(fit$par), df=df.namibianCatch)
```

Disini angka awal yang didapatkan dari proses sebelumnya kemudian disimpan sebagai inpars. Karena kita tahu bahwa parameter ini pasti bernilai positif, maka angka awal disimpan dalam bentuk log sebelum masuk didalam function. Setelah proses optimasi dengan perhitungan Maximum Likelihood Estimation selesai dilakukan, angka yang didapat masih dalam bentuk logarithmic sehingga perlu dilakukan backtransform. Angka awal dan angka akhir estimasi parameter K, B0, r, q dan sigma (observation error) hasil perhitungan maximum likelihood estimation dapat dilihat dengan melihat `Par.vals` dan grafik hasil optimasi dapat dilihat menggunakan function `Par_init`.

![Perbandingan fitting dari data Observation dan Estimation](/img/img_namibianCatchfitted.png)

Data frame `Par.vals` menyimpan informasi angka awal (`init_pars`) dan angka hasil optimasi (`fitted_pars`). Sebagai panduan untuk memeriksa hasil parameter, angka K biasanya selalu lebih tinggi dibanding B0, angka r seharusnya berada pada rentang antara 0-1 dan angka q biasanya berada pada rentang antara 0-0.1. Jika angka hasil optimasi masih belum sesuai dengan panduan sederhana ini, angka awal dapat di-constrain menggunakan batas bawah dan batas atas serta merubah metode optimasi menjadi "L-BFGS-B". 

Untuk data yang memiliki tipe one way trip, input yang digunakan dalam optimasi perlu disesuaikan terlebih dahulu. Penjelasan lebih lanjut untuk metode ini akan ditulis beberapa waktu kedepan. Selain itu, tool ini sudah disesuaikan untuk kebutuhan data yang terbatas (dapat mengakomodasi hilangnya input data upaya penangkapan) serta sudah memperhitungkan kesalahan dalam pengambilan data (observation error). 

#### 2.c. Menghitung reference point untuk pengelolaan

Hasil optimasi diatas menghasilkan parameter biomass dynamic model, yang perlu dihitung lebih lanjut untuk menghasilkan angka MSY, Bmsy dan Emsy. Angka-angka tersebut dapat dihasilkan menggunakan

```markdown
Bmsy <- K/2
MSY <- (r*K)/4
Emsy <- r/(2*q)
```

#### 2.d. Menghitung standard error dari reference point

Tool ini mengestimasi jumlah stok ikan yang lestari (Bmsy), jumlah tangkapan ikan lestari (MSY) dan upaya penangkapan ikan lestari (Emsy) serta menghitung standard error menggunakan data runut waktu dengan asumsi non-equilibrium untuk model Schaefer dan Fox.

### 3. Membuat proyeksi atas kebijakan reference point berdasar tingkat pemanfaatan perikanan

Tool ini akan membuat grafik proyeksi biomass per biomass at msy (B/Bmsy) dan fishing per fishing at msy (F/Fmsy) sebagai panduan untuk melihat kebijakan yang akan dibuat berdasarkan Bmsy, MSY dan Emsy sebagai reference point. Proyeksi dibuat dengan pendekatan deterministic secara default, dan terdapat opsi untuk tujuan stochastic.
