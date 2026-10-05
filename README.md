# 🌿 KELOMPOK 3 — NDVI & CURAH HUJAN RESEARCH WORKSPACE

> **Dari data satelit mentah → data rapi → statistik → grafik → laporan.**
>
> Aplikasi desktop Python untuk membantu pengolahan data **NDVI** dan **curah hujan** secara terstruktur, berulang, dan mudah dipahami.

<div align="center">

**🛰️ Remote Sensing** · **🌱 Vegetation** · **🌧️ Rainfall** · **📊 Statistics** · **🖥️ Interactive GUI**

</div>

---

## 🧭 Tentang Proyek

**KELOMPOK 3** adalah aplikasi desktop berbasis Python yang dibuat untuk proyek **GUI Interaktif Data NDVI dan Curah Hujan**.

Aplikasi ini bukan tempat mengambil data satelit dari internet secara otomatis. Data disiapkan lebih dulu dari sumber resmi, kemudian aplikasi membaca berkas hasil pengumpulan tersebut untuk melakukan pengolahan dan visualisasi.

Secara sederhana:

```text
DATA SATELIT / DATA HUJAN
          ↓
   SPATIAL SUBSETTING
          ↓
       DATA MENTAH
          ↓
     KELOMPOK 3 GUI
          ↓
   VALIDASI & PARSING
          ↓
      PERHITUNGAN
          ↓
    STATISTIK TAHUNAN
          ↓
       GRAFIK
          ↓
   CSV / EXCEL / SUMMARY
```

---

# 🎯 Tujuan

Proyek ini dibuat supaya proses yang biasanya dilakukan secara manual bisa menjadi satu alur yang lebih rapi.

### Tujuan utamanya

- membaca data NDVI dalam **GeoTIFF**;
- membaca data curah hujan dalam **CSV**;
- memeriksa metadata dan kualitas data;
- menghitung **mean, maksimum, minimum, dan standar deviasi**;
- membuat ringkasan per tahun;
- membuat grafik NDVI, curah hujan, dan hubungan keduanya;
- menyediakan hasil dalam format yang mudah dibaca dan digunakan kembali.

---

# 🛰️ 1. Sumber Data

## 🌱 1.1 NDVI — HLS-VI 30 m

Data vegetasi yang digunakan dalam workflow proyek adalah **HLS-VI (Harmonized Landsat and Sentinel-2 Vegetation Indices)** pada resolusi **30 meter**, diakses melalui **NASA AppEEARS**.

AppEEARS merupakan layanan NASA untuk mengakses dan mentransformasikan data geospasial dengan parameter spasial, temporal, dan band/layer. AppEEARS menyediakan **area samples** berbasis polygon dan juga menyertakan data kualitas yang terkait dengan hasil ekstraksi. 

### Kenapa NDVI dipakai?

NDVI dipakai sebagai indikator sederhana untuk melihat kondisi/kehijauan vegetasi.

Rumus dasar NDVI:

$$
NDVI = \frac{NIR - Red}{NIR + Red}
$$

Karena NDVI adalah data raster, satu file dapat berisi banyak sekali nilai piksel.

```text
0.61  0.64  0.59  0.67
0.55  0.62  0.63  0.58
0.70  0.68  0.65  0.66
```

Aplikasi kemudian bekerja pada nilai-nilai tersebut untuk memperoleh statistik wilayah.

### Informasi penting

| Parameter | Nilai / Keterangan |
|---|---|
| Produk | HLS-VI |
| Variabel | NDVI |
| Resolusi spasial | 30 m |
| Bentuk data | Raster / GeoTIFF |
| Sumber akses | NASA AppEEARS |
| Periode proyek | 10 tahun, sesuai periode yang ditetapkan kelompok |

> **Catatan reproducibility:** nomor versi produk, task/request ID AppEEARS, tanggal permintaan, layer yang dipilih, polygon, dan parameter quality-control harus dicatat pada catatan dataset kelompok.

### Sumber resmi

- NASA AppEEARS: https://appeears.earthdatacloud.nasa.gov/
- LP DAAC HLS-VI documentation: https://lpdaac.usgs.gov/products/hls_30m/v002/
- HLS-VI User Guide: https://lpdaac.usgs.gov/documents/2088/HLS_VI_User_Guide_V2.pdf

---

## ✂️ 1.2 Pemotongan Area / Spatial Subsetting

Sebelum dianalisis, data perlu dibatasi pada area penelitian.

Secara konsep:

```text
AREA DATA BESAR
       ↓
BATAS WILAYAH PENELITIAN
       ↓
AMBIL PIXEL DI DALAM AREA
       ↓
DATA SIAP ANALISIS
```

Polygon/batas administrasi berfungsi sebagai **batas wilayah analisis**, sehingga statistik yang dihitung aplikasi tidak tercampur dengan wilayah di luar lokasi penelitian.

### ⚠️ Catatan tentang GeoAdmin

Istilah **GeoAdmin** pada workflow proyek perlu didokumentasikan bersama layer/batas yang benar-benar dipakai. Portal resmi **geo.admin.ch** adalah geoportal Federal Administration of Switzerland. Karena lokasi penelitian proyek berada di Indonesia, README ini **tidak menganggap geo.admin.ch sebagai sumber resmi batas administrasi Indonesia tanpa bukti layer yang memang digunakan**.

Untuk reproduksi, simpan minimal:

```text
Nama layer batas   : ...
Sumber              : ...
Format              : SHP / GeoJSON / GPKG / lainnya
Tanggal akses       : ...
CRS                 : ...
Nama wilayah        : ...
``` 

Sumber resmi geo.admin.ch: https://www.geo.admin.ch/en/

---

# 🌧️ 2. Curah Hujan — GPM IMERG Final Daily

Data curah hujan pada workflow proyek menggunakan **GPM IMERG Final Daily** yang diakses melalui **NASA Giovanni**.

IMERG adalah produk presipitasi multisatelit dari misi Global Precipitation Measurement (GPM). Dokumentasi NASA menjelaskan bahwa algoritma IMERG menggabungkan berbagai estimasi presipitasi satelit serta informasi gauge dan menghasilkan beberapa tahap/jenis produk, termasuk **Early, Late, dan Final Run**. Final Run menggunakan data gauge bulanan pada tahap akhirnya dan ditujukan untuk penggunaan riset. 

### Kenapa data hujan ini dipakai?

Karena penelitian ingin mempunyai variabel kedua yang mewakili **presipitasi/hujan**, sehingga perubahan NDVI dan pola hujan dapat dilihat dalam periode yang sama.

### Informasi penting

| Parameter | Nilai / Keterangan |
|---|---|
| Produk | GPM IMERG Final |
| Temporal | Daily |
| Variabel | Precipitation |
| Sumber analisis | NASA Giovanni |
| Bentuk hasil proyek | Time series CSV |
| Resolusi grid IMERG | 0,1° |

NASA menjelaskan bahwa data IMERG/GIS mempertahankan resolusi spasial sekitar **0,1°** dari produk aslinya. Nilai ini jauh lebih kasar daripada NDVI HLS 30 m, sehingga keduanya tidak boleh dianggap memiliki resolusi spasial yang sama. 

### Giovanni

Giovanni adalah lingkungan web NASA untuk **menampilkan dan menganalisis parameter geofisika** serta menyediakan informasi provenance/data lineage. 

Sumber resmi:

- NASA Giovanni: https://giovanni.gsfc.nasa.gov/giovanni/
- Giovanni User Guide: https://giovanni.gsfc.nasa.gov/giovanni/doc/UsersManualworkingdocument.docx.html
- GPM IMERG documentation: https://gpm.nasa.gov/resources/documents/imerg-v07-daily-and-grand-climatology-products-documentation
- IMERG V07 ATBD: https://gpm.nasa.gov/resources/documents/imerg-v07-atbd
- IMERG GIS/GeoTIFF documentation: https://gpm.nasa.gov/resources/documents/imerg-gis-geotiff-documentation

---

# 🧩 3. Kenapa NDVI dan Hujan Dipakai Bersama?

Kedua data mempunyai fungsi yang berbeda.

| Data | Menjawab pertanyaan |
|---|---|
| 🌱 NDVI | Bagaimana kondisi vegetasi? |
| 🌧️ Curah hujan | Bagaimana pola presipitasi? |

Kemudian keduanya dapat dibandingkan berdasarkan waktu:

```text
2016 → NDVI + Hujan
2017 → NDVI + Hujan
2018 → NDVI + Hujan
...
2025 → NDVI + Hujan
```

Tujuan perbandingan ini adalah melihat **pola dan hubungan statistik**, bukan otomatis menyatakan bahwa hujan menjadi satu-satunya penyebab perubahan NDVI.

---

# 🧮 4. Metode Pengolahan Data

## 4.1 Alur besar

```text
┌───────────────────────┐
│ 1. Kumpulkan data     │
└───────────┬───────────┘
            ↓
┌───────────────────────┐
│ 2. Tentukan wilayah   │
│    dan periode        │
└───────────┬───────────┘
            ↓
┌───────────────────────┐
│ 3. Spatial subsetting │
│    / clipping         │
└───────────┬───────────┘
            ↓
┌───────────────────────┐
│ 4. Simpan data        │
│    GeoTIFF + CSV      │
└───────────┬───────────┘
            ↓
┌───────────────────────┐
│ 5. Baca dengan GUI    │
└───────────┬───────────┘
            ↓
┌───────────────────────┐
│ 6. Quality control    │
└───────────┬───────────┘
            ↓
┌───────────────────────┐
│ 7. Statistik tahunan  │
└───────────┬───────────┘
            ↓
┌───────────────────────┐
│ 8. Grafik & korelasi  │
└───────────┬───────────┘
            ↓
┌───────────────────────┐
│ 9. Export CSV/XLSX    │
└───────────────────────┘
```

---

# 🧑‍💻 5. Cara Aplikasi Membaca GeoTIFF

GeoTIFF bukan sekadar gambar. Selain nilai piksel, raster dapat membawa informasi seperti jumlah band, ukuran raster, CRS, transform/georeferencing, dan NoData.

Aplikasi menggunakan **Rasterio** untuk membuka dataset raster.

Secara sederhana:

```python
with rasterio.open(file_path) as src:
    data = src.read(...)
```

Rasterio mendokumentasikan `rasterio.open()` sebagai cara membuka dataset untuk membaca/menulis dan menyediakan atribut geospasial seperti CRS, transform, jumlah band, ukuran, dan NoData. 

### Kenapa Rasterio?

Karena file GeoTIFF perlu dibaca sebagai **data raster yang mempunyai referensi geografis**, bukan diperlakukan seperti gambar PNG biasa.

Sumber:

- Rasterio Quickstart: https://rasterio.readthedocs.io/en/stable/quickstart.html
- Rasterio API: https://rasterio.readthedocs.io/en/stable/api/rasterio.html

---

# ⚡ 6. Kenapa Bisa Memproses Raster Besar?

Aplikasi tidak harus membaca seluruh raster sekaligus.

Pada kode, pemrosesan dilakukan dengan **window/chunk**.

```text
GeoTIFF besar
     ↓
+---------+
| WINDOW  |
+---------+
| WINDOW  |
+---------+
| WINDOW  |
+---------+
     ↓
Hitung per bagian
     ↓
Gabungkan statistik
```

Kode menggunakan:

```python
Window(col, row, width, height)
```

Rasterio menyediakan windowed reading agar sebagian raster dapat dibaca berdasarkan bagian tertentu. Hal ini berguna untuk raster besar yang tidak selalu nyaman dimuat sekaligus ke RAM. 

Sumber: https://rasterio.readthedocs.io/en/stable/topics/windowed-rw.html

---

# 🔢 7. Perhitungan NDVI di Python

Jika raster yang diberikan sudah berisi NDVI, aplikasi dapat menggunakan band NDVI tersebut.

Jika raster berisi Red dan NIR, aplikasi mempunyai fungsi:

```python
def compute_ndvi(nir, red):
    ...
```

dan menghitung:

$$
NDVI = \frac{NIR-Red}{NIR+Red}
$$

Implementasinya menggunakan operasi array NumPy sehingga perhitungan dapat dilakukan pada banyak nilai piksel sekaligus.

NumPy memang dirancang untuk operasi numerical/scientific computing dan array multidimensi. 

Sumber: https://numpy.org/

---

# 🧹 8. Quality Control NDVI

Aplikasi memeriksa beberapa hal sebelum statistik dibuat, antara lain:

- file benar-benar tersedia;
- raster mempunyai band;
- tanggal akuisisi dapat dikenali bila tersedia;
- band NDVI / Red / NIR dapat ditentukan;
- nilai invalid dapat ditangani;
- NoData dapat dikeluarkan dari perhitungan;
- scale/offset raster dapat diperiksa;
- hasil yang ambigu dapat diberi status/peringatan.

Tujuannya sederhana:

> **jangan menghitung statistik dari pixel yang seharusnya tidak ikut.**

---

# 📅 9. Pengelompokan Berdasarkan Tahun

Setelah setiap file selesai diproses, hasil diberi informasi tanggal/tahun.

Contoh:

```text
NDVI_2019_01.tif → 2019
NDVI_2019_02.tif → 2019
NDVI_2020_01.tif → 2020
```

Kemudian hasil dikelompokkan:

```text
2019
 ├─ file 1
 └─ file 2

2020
 └─ file 1
```

Dari kelompok tersebut dibuat statistik tahunan.

Fungsi utama di source:

```python
group_ndvi_by_year()
summarize_ndvi_year()
combine_year_by_year()
```

---

# 🌧️ 10. Cara Aplikasi Membaca CSV Hujan

CSV dibaca menggunakan **pandas**.

Alurnya:

```text
CSV
 ↓
pandas.read_csv()
 ↓
cari kolom waktu
 ↓
cari kolom precipitation
 ↓
parse tanggal
 ↓
ubah rainfall menjadi angka
 ↓
quality control
 ↓
statistik
```

Source aplikasi memiliki fungsi:

```python
detect_rainfall_columns()
compute_rainfall_stats()
process_rainfall_csv_worker()
```

Pandas menyediakan `read_csv()` untuk membaca CSV menjadi `DataFrame`, yaitu struktur tabel yang kemudian mudah diproses untuk analisis. 

Sumber:

- Pandas documentation: https://pandas.pydata.org/docs/
- `read_csv`: https://pandas.pydata.org/docs/reference/api/pandas.read_csv.html

---

# 📊 11. Statistik yang Dihasilkan

## NDVI

Aplikasi menghitung statistik seperti:

- **Mean** — nilai rata-rata;
- **Minimum** — nilai terendah;
- **Maximum** — nilai tertinggi;
- **Standard deviation / SD** — ukuran penyebaran nilai.

## Curah hujan

Aplikasi menghitung antara lain:

- mean;
- minimum;
- maximum;
- median;
- standard deviation;
- total;
- jumlah sampel;
- rentang waktu.

### Contoh tabel hasil

| Tahun | Mean NDVI | Min | Max | SD | Mean Hujan | SD Hujan |
|---:|---:|---:|---:|---:|---:|---:|
| 2016 | … | … | … | … | … | … |
| 2017 | … | … | … | … | … | … |
| 2018 | … | … | … | … | … | … |
| ... | ... | ... | ... | ... | ... | ... |
| 2025 | … | … | … | … | … | … |

> Nilai pada tabel di atas hanya contoh format, bukan hasil pengukuran.

---

# 📈 12. Cara Aplikasi Membuat Grafik

Setelah data berada dalam bentuk angka/tabel, aplikasi mengirimkannya ke **Matplotlib**.

Alur sederhananya:

```text
Data numerik
    ↓
Figure
    ↓
Axes
    ↓
Plot / Bar / Scatter / Histogram
    ↓
Tampilan GUI
```

Matplotlib menyediakan objek `Figure` dan `Axes` untuk membuat berbagai jenis visualisasi. 

Sumber:

- Matplotlib documentation: https://matplotlib.org/stable/
- Matplotlib Quickstart: https://matplotlib.org/stable/users/explain/quick_start.html

---

# 🔗 13. Grafik Korelasi

Untuk melihat hubungan NDVI dan hujan, aplikasi dapat membuat **scatter plot**.

```text
        NDVI
          ↑
       •     •
    •    •
  •   •
 └────────────────→ Rainfall
```

Jika diperlukan, koefisien korelasi Pearson dapat digunakan sebagai angka ringkas hubungan linear.

Namun:

> **Korelasi tidak otomatis berarti sebab-akibat.**

Perubahan NDVI juga dapat dipengaruhi faktor lain seperti tutupan lahan, musim, kualitas observasi, awan, kondisi tanah, dan proses lingkungan lainnya.

---

# 🖥️ 14. Kenapa Ada GUI?

GUI memakai **Tkinter**, yaitu antarmuka Python ke toolkit **Tcl/Tk**.

Dengan GUI, pengguna tidak perlu menulis semua nama file dan perhitungan langsung di terminal.

Cukup:

```text
[ Upload TIFF ]
[ Upload CSV  ]
[ Load Folder ]

       ↓

   PROSES DATA

       ↓

 [ Statistik ]
 [ Grafik    ]
 [ Export    ]
```

Tkinter merupakan antarmuka GUI standar Python untuk Tcl/Tk. 

Sumber: https://docs.python.org/3/library/tkinter.html

---

# 🧵 15. Kenapa Aplikasi Tidak Langsung Freeze Saat Memproses Banyak File?

Source aplikasi menggunakan pemrosesan berbasis **worker/thread** untuk pekerjaan yang lama, kemudian hasilnya dikirim kembali ke GUI melalui queue/callback.

Gambarannya:

```text
MAIN GUI THREAD
      │
      ├── tombol
      ├── grafik
      └── tampilan progress

           ↕

       WORKER THREAD
      │
      ├── baca GeoTIFF
      ├── hitung NDVI
      ├── baca CSV
      └── hitung statistik
```

Tujuan desain ini adalah menjaga antarmuka tetap responsif selama batch processing berlangsung.

> Ini adalah penjelasan arsitektur aplikasi; efektivitas respons tetap bergantung pada ukuran data dan beban komputer.

---

# 📤 16. Kenapa Bisa Export ke CSV dan Excel?

Setelah hasil pengolahan berubah menjadi struktur tabel, hasil dapat diekspor.

### CSV

Format sederhana untuk data tabel dan mudah dibaca oleh banyak software.

### XLSX

Untuk workbook Excel, aplikasi memakai **openpyxl**.

OpenPyXL adalah library Python untuk membaca/menulis file Excel 2010+ dengan format seperti `.xlsx`. 

Sumber: https://openpyxl.readthedocs.io/en/stable/

---

# 🧱 17. Struktur Teknis Program

Source utama merupakan aplikasi Python monolitik yang menggabungkan fungsi analisis, ekspor, dan GUI.

Fungsi analitis penting yang terdapat pada source:

```text
extract_date_from_string()
extract_acquisition_date()
find_band_index()
detect_sensor()
band_scale_offset()
compute_ndvi()
sample_band_values()
decide_ndvi_plan()
build_metadata()
build_statistics()
write_xlsx()
write_rainfall_xlsx()
process_tiff_worker()

detect_rainfall_columns()
compute_rainfall_stats()
process_rainfall_csv_worker()

combine_stats()
group_ndvi_by_year()
summarize_ndvi_year()
combine_rainfall_stats()
combine_year_by_year()

class RajwaXalyaApp
```

> Nama class pada source lama dapat tetap berbeda karena proyek ini mempertahankan basis aplikasi awal. **Branding aplikasi untuk submission adalah KELOMPOK 3.**

---

# 📦 18. Library / Import yang Digunakan

| Import | Fungsi sederhana |
|---|---|
| `os` | operasi sistem/file |
| `re` | pencarian pola teks |
| `sys` | akses fungsi/konfigurasi Python |
| `json` | membaca/menulis struktur JSON |
| `math` | operasi matematika |
| `time` | waktu/profiling sederhana |
| `uuid` | ID unik hasil proses |
| `queue` | komunikasi worker ↔ GUI |
| `logging` | pencatatan log |
| `threading` | menjalankan pekerjaan latar |
| `traceback` | informasi error |
| `datetime` | tanggal dan waktu |
| `pathlib.Path` | manajemen path |
| `typing` | type hints |
| `numpy` | array & perhitungan numerik |
| `pandas` | tabel/CSV/time series |
| `rasterio` | membaca raster GeoTIFF |
| `Window` | membaca potongan raster |
| `Resampling` | resampling saat membaca raster |
| `rio_transform` | transformasi koordinat |
| `tkinter` | GUI |
| `ttk` | widget GUI |
| `filedialog` | pemilihan file/folder |
| `messagebox` | dialog pesan |
| `matplotlib` | plotting |
| `Figure` | wadah grafik |
| `FigureCanvasTkAgg` | menampilkan Matplotlib di Tkinter |
| `NavigationToolbar2Tk` | toolbar navigasi grafik |

### Dependency inti

```text
rasterio
numpy
pandas
openpyxl
matplotlib
```

`tkinter` termasuk dalam instalasi Python pada banyak distribusi desktop dan digunakan sebagai toolkit GUI. 

---

# 🔬 19. Algoritma Ringkas

## Algoritma A — NDVI

```text
MULAI
  ↓
Pilih file GeoTIFF
  ↓
Buka dengan Rasterio
  ↓
Baca metadata
  ↓
Cari tanggal akuisisi
  ↓
Tentukan apakah raster sudah NDVI
  atau perlu Red + NIR
  ↓
Baca raster per window
  ↓
Buang NoData / nilai invalid
  ↓
Jika perlu:
NDVI = (NIR - Red) / (NIR + Red)
  ↓
Hitung mean, min, max, SD
  ↓
Simpan hasil
  ↓
Selesai
```

## Algoritma B — Curah Hujan

```text
MULAI
  ↓
Pilih CSV
  ↓
Baca dengan Pandas
  ↓
Cari kolom waktu
  ↓
Cari kolom precipitation
  ↓
Parse tanggal
  ↓
Parse nilai hujan
  ↓
Buang baris yang tidak valid
  ↓
Urutkan waktu
  ↓
Hitung statistik
  ↓
Kelompokkan menurut tahun
  ↓
Simpan hasil
  ↓
Selesai
```

## Algoritma C — Gabungan

```text
NDVI tahunan
     +
Rainfall tahunan
     ↓
Samakan tahun
     ↓
Buat tabel gabungan
     ↓
Buat grafik
     ↓
Buat scatter / korelasi
     ↓
Export
```

---

# 📐 20. Contoh Cara Berpikir Statistik

Misalkan dalam satu tahun terdapat beberapa nilai NDVI:

```text
0.50
0.60
0.70
```

Mean:

$$
\bar{x}=\frac{0.50+0.60+0.70}{3}=0.60
$$

Untuk hujan, misalnya:

```text
10 mm
20 mm
30 mm
```

Mean:

$$
\bar{x}=20\text{ mm}
$$

Aplikasi melakukan pekerjaan seperti ini secara otomatis untuk data yang jumlahnya jauh lebih banyak.

---

# 🧪 21. Quality Control yang Harus Diperhatikan

Sebelum hasil digunakan dalam laporan ilmiah, tetap periksa:

### NDVI

- apakah band yang digunakan benar;
- apakah scale factor sesuai metadata produk;
- apakah NoData sudah benar;
- apakah tanggal akuisisi benar;
- apakah kualitas/awan sudah diperhatikan sesuai produk sumber;
- apakah semua file memang berasal dari wilayah dan periode yang sama.

### Curah hujan

- apakah kolom precipitation benar;
- apakah unit sesuai produk;
- apakah waktu terbaca dengan benar;
- apakah ada data kosong;
- apakah ada duplikasi waktu;
- apakah total hujan dihitung sesuai arti temporal datanya.

### Gabungan

- pastikan tahun NDVI dan hujan benar-benar sejajar;
- jangan menganggap perbedaan spasial 30 m vs 0,1° sebagai sama;
- jangan menyimpulkan kausalitas hanya dari korelasi.

---

# 🗂️ 22. Format Data yang Diharapkan

## NDVI

```text
.tif
.tiff
```

GeoTIFF yang berisi NDVI atau raster yang memungkinkan aplikasi menentukan Red/NIR.

## Rainfall

```text
.csv
```

Format minimal yang dapat dibaca:

```csv
time,mean_GPM_3IMERGDF_07_precipitation
2016-01-01,8.2
2016-01-02,12.4
2016-01-03,0.0
```

Aplikasi melakukan deteksi nama kolom secara otomatis dan mendukung fallback berdasarkan pola nama/tipe data.

---

# 🚀 23. Menjalankan Aplikasi

## Install dependency

```bash
pip install -r requirements.txt
```

Isi dependency proyek:

```text
rasterio
numpy
pandas
openpyxl
matplotlib
```

## Jalankan

```bash
python KELOMPOK_3.py
```

atau sesuai nama entry-point paket yang digunakan:

```bash
python app.py
```

---

# 🧾 24. Output yang Diharapkan

Contoh hasil proyek:

```text
output/
├── NDVI_....csv
├── NDVI_....xlsx
├── rainfall_....csv
├── rainfall_....xlsx
└── summary_....xlsx
```

Isi output dapat mencakup:

- hasil per file;
- statistik NDVI;
- statistik rainfall;
- ringkasan tahunan;
- tabel gabungan;
- visualisasi;
- metadata;
- warning/log.

---

# 🧠 25. Kenapa Aplikasi Ini Berguna?

Tanpa aplikasi:

```text
buka file
→ hitung
→ catat
→ buka file berikutnya
→ hitung lagi
→ catat lagi
→ ulang terus
```

Dengan aplikasi:

```text
UPLOAD
  ↓
PROCESS
  ↓
STATISTICS
  ↓
GRAPH
  ↓
EXPORT
```

Jadi inti aplikasi adalah **mengurangi pekerjaan berulang** dan membuat proses pengolahan lebih konsisten.

---

# ⚠️ 26. Batasan Ilmiah

Aplikasi ini adalah **research-support / data-processing tool**, bukan model prediksi bencana atau mesin pengambil keputusan.

Hasil aplikasi tetap bergantung pada kualitas data input.

Secara khusus:

1. NDVI HLS dan IMERG memiliki resolusi spasial berbeda.
2. Data hujan merupakan estimasi produk satelit, bukan pengukuran satu rain gauge lokal.
3. Korelasi tidak membuktikan sebab-akibat.
4. Interpretasi ekologis harus mempertimbangkan faktor lain.
5. Parameter dataset harus dicatat secara lengkap untuk reproduksibilitas.

---

# 📚 27. Daftar Sumber Resmi

## Data & platform

1. **NASA AppEEARS**  
   https://appeears.earthdatacloud.nasa.gov/

2. **LP DAAC — HLS 30 m / HLS-VI**  
   https://lpdaac.usgs.gov/products/hls_30m/v002/

3. **HLS-VI User Guide**  
   https://lpdaac.usgs.gov/documents/2088/HLS_VI_User_Guide_V2.pdf

4. **NASA Giovanni**  
   https://giovanni.gsfc.nasa.gov/giovanni/

5. **Giovanni User Guide**  
   https://giovanni.gsfc.nasa.gov/giovanni/doc/UsersManualworkingdocument.docx.html

6. **NASA GPM — IMERG V07 Daily / Climatology Documentation**  
   https://gpm.nasa.gov/resources/documents/imerg-v07-daily-and-grand-climatology-products-documentation

7. **NASA GPM — IMERG V07 ATBD**  
   https://gpm.nasa.gov/resources/documents/imerg-v07-atbd

8. **NASA GPM — IMERG GIS / GeoTIFF Documentation**  
   https://gpm.nasa.gov/resources/documents/imerg-gis-geotiff-documentation

9. **geo.admin.ch**  
   https://www.geo.admin.ch/en/

## Software / libraries

10. **Rasterio documentation**  
    https://rasterio.readthedocs.io/en/stable/

11. **NumPy documentation**  
    https://numpy.org/

12. **Pandas documentation**  
    https://pandas.pydata.org/docs/

13. **Matplotlib documentation**  
    https://matplotlib.org/stable/

14. **OpenPyXL documentation**  
    https://openpyxl.readthedocs.io/en/stable/

15. **Python Tkinter documentation**  
    https://docs.python.org/3/library/tkinter.html

---

# 📝 28. Data Provenance Checklist

Agar laporan kelompok mudah dipertanggungjawabkan, isi data berikut untuk setiap produk:

```text
[ ] Nama produk
[ ] Provider / lembaga
[ ] Collection / version
[ ] Variabel / band
[ ] Resolusi spasial
[ ] Resolusi temporal
[ ] Rentang tanggal
[ ] Wilayah / koordinat
[ ] Batas polygon yang digunakan
[ ] CRS
[ ] Unit
[ ] Scale factor
[ ] Quality flag / QA
[ ] Tanggal akses
[ ] URL sumber
[ ] Nama file hasil download
```

---

# 🧪 29. Reproducibility Recipe

Untuk mengulang penelitian, seseorang seharusnya dapat mengikuti urutan:

```text
1. Tentukan wilayah
2. Tentukan periode
3. Siapkan polygon/batas wilayah
4. Request HLS-VI di AppEEARS
5. Simpan hasil NDVI
6. Lakukan spatial subset/clipping
7. Request GPM IMERG Final Daily di Giovanni
8. Tentukan area/bounding box
9. Simpan time series hujan
10. Masukkan GeoTIFF + CSV ke KELOMPOK 3
11. Jalankan analisis
12. Periksa warning/metadata
13. Export CSV/XLSX
14. Validasi hasil sebelum masuk laporan
```

---

# 💡 30. Satu Kalimat untuk Presentasi

> **“KELOMPOK 3 adalah aplikasi Python yang mengubah data NDVI berbentuk GeoTIFF dan data curah hujan berbentuk time series CSV menjadi informasi yang lebih mudah dipahami melalui statistik tahunan, grafik, perbandingan, dan export hasil.”**

---

# 🏁 Kesimpulan

```text
SATELIT
   ↓
DATA
   ↓
CLIP / SUBSET
   ↓
BERSIHKAN
   ↓
KELOMPOK 3
   ↓
HITUNG
   ↓
PLOT
   ↓
ANALISIS
   ↓
LAPORAN
```

**KELOMPOK 3** menyatukan tahapan pengolahan data ke dalam satu workspace sehingga pengguna dapat berpindah dari data raster dan time series menuju statistik, visualisasi, dan output terstruktur tanpa harus mengulang proses manual satu per satu.

---

<div align="center">

### 🌱 NDVI · 🌧️ Rainfall · 🛰️ Remote Sensing · 📊 Data Analysis

**KELOMPOK 3**

</div>

> **Catatan:** README ini mendokumentasikan workflow, kode, library, data, dan sumber yang diketahui dari proyek. Parameter yang hanya diketahui dari proses pengambilan data kelompok—misalnya koordinat final, polygon final, task ID AppEEARS, dan nama layer clipping—harus diisi dari catatan asli kelompok agar dokumentasi benar-benar reproducible.
