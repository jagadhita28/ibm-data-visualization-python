# Module 3: Advanced Visualization Tools dan Visualizing Geospatial Data

Bagian dari repositori `ibm-data-visualization-python`, folder `module-3-advanced-viz-geospatial`. Latihan visualisasi data dengan Python ini terdiri dari dua notebook. Notebook pertama membahas teknik visualisasi lanjutan (Waffle Chart, Word Cloud, Seaborn). Notebook kedua membahas visualisasi data geospasial dengan peta interaktif (Folium).

Dokumen pendamping:

- [`cheatsheet.md`](./cheatsheet.md): contekan kode, tabel parameter, dan daftar error umum.
- `README.md` (file ini): penjelasan proyek, dataset, cara menjalankan, dan isi tiap notebook.

---

## Daftar Isi

1. [Gambaran umum](#1-gambaran-umum)
2. [Tujuan pembelajaran](#2-tujuan-pembelajaran)
3. [Struktur proyek](#3-struktur-proyek)
4. [Dataset](#4-dataset)
5. [Instalasi](#5-instalasi)
6. [Cara menjalankan](#6-cara-menjalankan)
7. [Notebook 01: Advanced Visualization Tools](#7-notebook-01-advanced-visualization-tools)
8. [Notebook 02: Visualizing Geospatial Data](#8-notebook-02-visualizing-geospatial-data)
9. [File output](#9-file-output)
10. [Latihan di dalam notebook](#10-latihan-di-dalam-notebook)
11. [Temuan utama](#11-temuan-utama)
12. [Catatan teknis](#12-catatan-teknis)
13. [Troubleshooting](#13-troubleshooting)
14. [Referensi dan kredit](#14-referensi-dan-kredit)

---

## 1. Gambaran umum

| | Notebook 01 | Notebook 02 |
|---|---|---|
| **File** | `01_advanced_visualization_tools.ipynb` | `02_visualizing_geospatial_data.ipynb` |
| **Fokus** | Grafik lanjutan untuk proporsi, teks, dan statistik | Peta interaktif |
| **Library utama** | `matplotlib`, `seaborn`, `pywaffle`, `wordcloud`, `Pillow` | `folium`, `pandas`, `numpy` |
| **Teknik** | Waffle chart, word cloud, countplot, barplot, regplot | Peta dasar, tile kustom, marker, cluster, choropleth |
| **Data** | Imigrasi ke Kanada 1980-2013, novel *Alice's Adventures in Wonderland* | Insiden Kepolisian San Francisco 2016, imigrasi ke Kanada 1980-2013, GeoJSON dunia |
| **Hasil** | 13 gambar PNG di `outputs/` | 6 file peta interaktif di `outputs/` |

Seluruh narasi notebook ditulis dalam bahasa Indonesia. Istilah teknis tetap memakai bahasa Inggris.

---

## 2. Tujuan pembelajaran

Setelah menyelesaikan kedua notebook, kamu mampu:

- [ ] Membuat **waffle chart** dengan `pywaffle` untuk menampilkan proporsi antar kategori.
- [ ] Membuat **word cloud** dari teks, menambahkan stopwords, dan membentuknya dengan **mask**.
- [ ] Mengubah data numerik menjadi teks agar bisa divisualisasikan sebagai word cloud.
- [ ] Memakai **Seaborn** untuk `countplot`, `barplot`, dan `regplot`.
- [ ] Mengatur tampilan grafik: ukuran gambar, warna, marker, font, dan style latar.
- [ ] Membuat peta interaktif dengan **Folium**, lengkap dengan pusat peta, level zoom, dan gaya tile.
- [ ] Menempatkan **marker**, **popup**, dan **marker cluster** berdasarkan koordinat.
- [ ] Membuat **choropleth map** dengan file GeoJSON dan menambahkan tooltip.
- [ ] Menyimpan peta Folium ke file HTML supaya bisa dibuka di browser.

---

## 3. Struktur proyek

Path di dalam notebook bersifat relatif terhadap lokasi file `.ipynb`:

| Path di kode | Fungsi | Dipakai di |
|---|---|---|
| `../../data/raw/Canada.csv` | Sumber data imigrasi | Notebook 01 |
| `../outputs/` | Folder penyimpanan gambar dan peta hasil | Notebook 01 dan 02 |
| `../outputs/canada.png` | Gambar mask peta Kanada untuk word cloud | Notebook 01 |
| `world_countries.json` | File GeoJSON, diunduh ke folder kerja notebook | Notebook 02 |

Struktur folder yang cocok dengan path tersebut:

```
ibm-data-visualization-python/
├── data/
│   └── raw/
│       └── Canada.csv
└── module-3-advanced-viz-geospatial/
    ├── notebooks/
    │   ├── 01_advanced_visualization_tools.ipynb
    │   ├── 02_visualizing_geospatial_data.ipynb
    │   └── world_countries.json        (diunduh oleh notebook 02)
    ├── outputs/
    │   ├── canada.png                  (mask, disiapkan manual)
    │   ├── ... 13 gambar PNG dari notebook 01
    │   └── ... 6 file peta dari notebook 02
    ├── cheatsheet.md
    └── README.md
```

> Nama folder `notebooks/` mengikuti pola path relatif di kode (`../outputs` dan `../../data`). Sesuaikan kalau nama folder notebook-mu berbeda.
>
> `canada.png` harus kamu siapkan sendiri. Notebook hanya membacanya, tidak mengunduhnya.

---

## 4. Dataset

Semua file unduhan dari IBM Skills Network memakai alamat dasar berikut:

```
https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-DV0101EN-SkillsNetwork/
```

### 4.1 Imigrasi ke Kanada 1980-2013 (`Canada.csv`)

- **Sumber asli:** [UN International Migration Flows, revisi 2015](https://www.un.org/development/desa/pd/data/international-migration-flows).
- **Bentuk data:** 195 baris dan 39 kolom, satu baris mewakili satu negara asal.
- **Versi:** dataset yang sudah dibersihkan.
- **Total imigran 1980-2013:** 6.409.153 orang.
- **Lokasi file:** `Data%20Files/Canada.csv` di alamat dasar. Notebook 01 membaca dari file lokal, notebook 02 membaca dari URL.

| Kolom | Isi |
|---|---|
| `Country` | Nama negara asal |
| `Continent` | Benua |
| `Region` | Wilayah |
| `DevName` | Kategori negara (Developing regions atau Developed regions) |
| `1980` sampai `2013` | Jumlah imigran per tahun (34 kolom, nama kolom bertipe string) |
| `Total` | Jumlah imigran seluruh tahun |

### 4.2 Novel *Alice's Adventures in Wonderland* (`alice_novel.txt`)

- Teks karya Lewis Carroll untuk latihan word cloud.
- Diunduh langsung ke memori lewat `urllib` (tidak disimpan sebagai file).
- Lokasi: `Data%20Files/alice_novel.txt` di alamat dasar.
- Mask bentuk Alice dan kelinci: `labs/Module%204/images/alice_mask.png` di alamat dasar.

### 4.3 Insiden Kepolisian San Francisco 2016

- **Sumber asli:** portal data publik San Francisco (SFPD Incident Reports).
- **Bentuk data:** 150.500 baris dan 13 kolom. Notebook hanya memakai **100 baris pertama**.
- **Anonimisasi:** alamat dan lokasi dipindahkan ke tengah blok atau persimpangan.
- **Lokasi file:** `Data%20Files/Police_Department_Incidents_-_Previous_Year__2016_.csv` di alamat dasar.
- Dataset ini sudah tidak tersedia di situs aslinya karena pembaruan sistem kepolisian. Notebook memakai salinan dari IBM.

| Kolom | Isi |
|---|---|
| `IncidntNum` | Nomor kejadian |
| `Category` | Kategori kejahatan atau kejadian |
| `Descript` | Deskripsi kejadian |
| `DayOfWeek` | Hari terjadinya kejadian |
| `Date` | Tanggal |
| `Time` | Waktu |
| `PdDistrict` | Distrik kepolisian |
| `Resolution` | Hasil penyelesaian (pelaku ditangkap atau tidak) |
| `Address` | Alamat terdekat |
| `X` | **Bujur** (longitude) |
| `Y` | **Lintang** (latitude) |
| `Location` | Tuple lintang dan bujur |
| `PdId` | ID departemen kepolisian |

### 4.4 GeoJSON batas negara dunia (`world_countries.json`)

- Format: `FeatureCollection` dengan **177 negara**.
- Setiap negara menyimpan nama di `properties.name`.
- Lokasi: `Data%20Files/world_countries.json` di alamat dasar.
- Notebook 02 mengunduhnya dengan `urllib.request.urlretrieve`.

---

## 5. Instalasi

Notebook dijalankan dengan **Python 3.12**. Versi library yang tercatat di notebook 01:

| Library | Versi |
|---|---|
| Matplotlib | 3.11.2 |
| Seaborn | 0.13.2 |
| WordCloud | 1.9.6 |

Buat virtual environment (opsional tapi disarankan), lalu pasang library:

```bash
python -m venv .venv

# Windows
.venv\Scripts\activate

# macOS atau Linux
source .venv/bin/activate

pip install pandas numpy matplotlib seaborn pillow wordcloud pywaffle folium jupyterlab
```

---

## 6. Cara menjalankan

1. Siapkan folder dan file sesuai [struktur proyek](#3-struktur-proyek):
   - Letakkan `Canada.csv` di `data/raw/`.
   - Buat folder `outputs/` dan taruh `canada.png` di dalamnya.
2. Jalankan Jupyter:
   ```bash
   jupyter lab
   ```
3. Buka notebook, lalu jalankan sel dari atas ke bawah (**Run All** atau `Shift + Enter` per sel).
4. Notebook 01 butuh koneksi internet untuk mengunduh novel dan mask Alice.
5. Notebook 02 butuh koneksi internet untuk mengunduh data, GeoJSON, dan tile peta. Notebook ini juga menyimpan peta ke `../outputs/`, jadi folder tersebut harus sudah ada.

Urutan notebook: kerjakan **01** dulu, lalu **02**. Keduanya berdiri sendiri, tetapi notebook 02 memakai dataset imigrasi yang sudah dikenalkan di notebook 01.

---

## 7. Notebook 01: Advanced Visualization Tools

### Peta isi

| Bagian | Isi | Alat |
|---|---|---|
| Import Libraries | Setup library dan cek versi | `matplotlib`, `seaborn`, `wordcloud` |
| Fetching Data | Baca `Canada.csv`, jadikan `Country` sebagai indeks | `pandas` |
| Waffle Charts | Proporsi imigran antar negara | `pywaffle` |
| Word Clouds | Awan kata dari novel dan dari data imigrasi | `wordcloud`, `Pillow` |
| Plotting with Seaborn | Plot kategorikal | `countplot`, `barplot` |
| Regression Plots | Tren total imigrasi dan garis regresi | `regplot` |

### 7.1 Import libraries

- Mengatur gaya grafik dengan `mpl.style.use("ggplot")`.
- Mengimpor `numpy`, `pandas`, `PIL.Image`, `seaborn`, dan `wordcloud`.
- Mencetak versi library untuk memastikan lingkungan sudah benar.

### 7.2 Fetching data

```python
df_canada = pd.read_csv("../../data/raw/Canada.csv")
df_canada.set_index("Country", inplace=True)
```

Setelah `Country` menjadi indeks, mencari data satu negara cukup dengan `df_canada.loc["Denmark"]`.

### 7.3 Waffle charts

**Ide:** satu kategori mendapat jumlah kotak sesuai porsinya dari total. Grafik ini mudah dibaca untuk perbandingan proporsi dan cocok untuk dashboard.

Langkah di notebook:

1. Pilih tiga negara: Denmark, Sweden, Norway.
2. Buat waffle 20 baris x 30 kolom (600 kotak) dengan `FigureClass=Waffle`.
3. Buat label legend lewat list comprehension, misalnya `"Denmark (angka Total)"`.
4. Simpan gambar dengan `dpi=200` dan tampilkan.
5. Latihan: ulangi untuk China dan India dengan skema warna `tab20c`.

### 7.4 Word clouds

**Ide:** makin sering sebuah kata muncul, makin besar tampilannya.

Bagian ini berkembang bertahap:

| Tahap | Yang dilakukan | Hasil |
|---|---|---|
| 1 | Unduh novel Alice, buat `WordCloud()` bawaan | Awan kata pertama, kata `said` ikut mendominasi |
| 2 | Perbesar ukuran gambar | Kata yang jarang muncul lebih jelas |
| 3 | Tambahkan `said` ke `stopwords` | Kata yang lebih bermakna: Alice, little, Queen |
| 4 | Pakai `mask=alice_mask` | Kata membentuk siluet Alice dan kelinci |
| 5 | Ubah data imigrasi jadi teks | Nama negara diulang sesuai kontribusinya |
| 6 | Pakai mask peta Kanada plus kontur hijau | Nama negara membentuk peta Kanada |

**Cara mengubah angka menjadi teks:**

```python
repeat_num_times = int(df_canada.loc[country, "Total"] / total_immigrants * max_words)
word_string = word_string + ((country + " ") * repeat_num_times)
```

Negara dengan kontribusi besar diulang lebih sering, sehingga tampil lebih besar. Hanya nama negara satu kata yang dipakai, karena nama dua kata akan terpecah menjadi dua kata terpisah. Hasilnya menunjukkan bahwa mayoritas imigran berasal dari 15 negara yang tampil di awan kata.

### 7.5 Plotting with Seaborn

Seaborn membuat grafik statistik yang rapi dengan kode ringkas.

**Categorical plots**

| Plot | Pertanyaan yang dijawab | Hasil |
|---|---|---|
| `countplot` | Berapa banyak negara di tiap benua? | Batang jumlah negara per benua |
| `barplot` | Berapa rata-rata imigran per negara di tiap benua? | Batang rata-rata dengan confidence interval |

Label benua yang panjang (`Latin America and the Caribbean`, `Northern America`) diganti menjadi `L-America` dan `N-America` agar tidak saling menimpa. Angka pada `barplot` diverifikasi dengan `groupby("Continent")["Total"].mean()`.

| Benua | Rata-rata total per negara |
|---|---|
| Africa | 11.462,00 |
| Asia | 67.710,08 |
| Europe | 32.812,72 |
| L-America | 23.186,30 |
| N-America | 120.571,00 |
| Oceania | 3.941,00 |

### 7.6 Regression plots

**Ide:** `regplot` menggambar scatter plot sekaligus garis regresi linear.

Persiapan data (total imigran per tahun):

```python
years = list(map(str, range(1980, 2014)))
df_total = pd.DataFrame(df_canada[years].sum(axis=0))
df_total.index = map(float, df_total.index)
df_total.reset_index(inplace=True)
df_total.columns = ["years", "total"]
```

Kustomisasi yang dicoba, berurutan:

1. `regplot` polos.
2. Warna hijau: `color="green"`.
3. Bentuk titik `+`: `marker="+"`.
4. Ukuran gambar `figsize=(15, 10)`.
5. Ukuran titik `scatter_kws={"s": 200}`, judul, dan label sumbu.
6. Font lebih besar: `sns.set(font_scale=1.5)`.
7. Style latar: `"ticks"` (putih polos) dan `"whitegrid"` (putih dengan grid).

Latihan: regression plot total imigrasi dari Denmark, Sweden, dan Norway selama 1980-2013, dengan langkah transpose (`.T`), penjumlahan per baris (`sum(axis=1)`), dan penamaan ulang kolom.

---

## 8. Notebook 02: Visualizing Geospatial Data

### Peta isi

| Bagian | Isi | Alat |
|---|---|---|
| Exploring Datasets | Pengenalan dua dataset | - |
| Importing Libraries | Import `numpy`, `pandas`, `folium` | `folium` |
| Introduction to Folium | Peta dasar, pusat, zoom, gaya tile | `folium.Map` |
| Maps with Markers | Titik insiden di San Francisco | `CircleMarker`, `Marker`, `MarkerCluster` |
| Choropleth Maps | Peta dunia berwarna menurut total imigrasi | `folium.Choropleth` |

### 8.1 Mengapa Folium

- Dibuat khusus untuk data geospasial.
- Hasilnya **interaktif** (zoom, geser, klik), cocok untuk dashboard.
- Gratis. Beberapa library lain, misalnya plotly, dapat membatasi jumlah panggilan API.
- Berbasis Leaflet.js dengan tile bawaan dari OpenStreetMap, Mapbox, dan Esri.

### 8.2 Introduction to Folium

**Peta dunia**

```python
world_map = folium.Map()
```

**Pusat dan zoom.** Lokasi ditentukan lewat lintang dan bujur. Makin tinggi `zoom_start`, makin dekat tampilan peta.

| Peta | `location` | `zoom_start` |
|---|---|---|
| Kanada (rendah) | `[57.4532, -101.6667]` | 4 |
| Kanada (dekat) | `[57.4532, -101.6667]` | 8 |
| Meksiko | `[19.4340, -99.1391]` | 6 |

**Gaya tile**

| Gaya | Ciri |
|---|---|
| Default | Peta OpenStreetMap berwarna |
| Esri Dark Gray Canvas | Hitam putih berkontras tinggi, cocok untuk menumpuk data dan menjelajahi sungai dan pesisir |
| Esri Light Gray Canvas | Minimalis dengan latar terang, tampak bersih dan modern. Nama negara tampil dalam bahasa Inggris |

Tile kustom memakai parameter `tiles` (URL), `attr` (atribusi), dan `max_zoom=16`.

Kedua peta Kanada disimpan ke folder `outputs/` lewat `canada_map.save(...)`:

| Peta | File hasil |
|---|---|
| Dark Gray Canvas | `canada_dark_gray_map.html` |
| Light Gray Canvas | `canada_light_gray` (tersimpan tanpa ekstensi `.html`) |

Latihan: peta Meksiko dengan gaya Light Gray Canvas, `zoom_start=6`.

### 8.3 Maps with markers

Data: 100 insiden pertama dari dataset SFPD 2016, dengan pusat peta San Francisco `[37.77, -122.42]` dan `zoom_start=12`.

Notebook mencoba beberapa cara, dari yang paling dasar sampai paling rapi:

| Langkah | Cara | Hasil |
|---|---|---|
| 1 | `FeatureGroup` berisi `CircleMarker` (isi merah, tepi kuning) | 100 lingkaran di peta (tidak disimpan ke file) |
| 2 | `FeatureGroup` lingkaran biru, ditambah `folium.Marker` dengan `tooltip=Category` | Setiap titik punya pin dan keterangan kategori kejahatan. Disimpan sebagai `sanfrancisco_incidents_two_marker_map.html` |
| 3 | `CircleMarker` dengan `tooltip` langsung | Peta lebih bersih tanpa pin. Disimpan sebagai `sanfrancisco_incidents_circle_marker_map.html` |
| 4 | `plugins.MarkerCluster` dengan `popup` | Titik berdekatan digabung menjadi cluster berangka. Disimpan sebagai `sanfrancisco_incidents_marker_cluster_map.html` |

Perbedaan `tooltip` dan `popup`:

| Parameter | Muncul saat | Dipakai di langkah |
|---|---|---|
| `tooltip` | Kursor diarahkan ke marker (hover) | 2 dan 3 |
| `popup` | Marker diklik | 4 |

Perilaku cluster:

- Saat zoom out penuh, seluruh titik menyatu menjadi satu cluster berangka **100**.
- Saat zoom in, cluster pecah menjadi cluster yang lebih kecil.
- Pada zoom terdekat, marker tampil satu per satu.

### 8.4 Choropleth maps

**Ide:** wilayah diwarnai menurut nilai variabel, sehingga perbedaan antar wilayah terlihat sekilas.

Alur di notebook:

1. Baca `Canada.csv` dari URL (195 baris x 39 kolom).
2. Unduh `world_countries.json` dan baca dengan `json.load`.
3. Periksa struktur GeoJSON: tipe `FeatureCollection`, 177 fitur, kunci nama negara di `properties.name`.
4. Buat dictionary `total_per_negara`, lalu simpan teks `total_text` (angka berformat ribuan atau "Tidak ada data") ke properti tiap negara.
5. Buat `folium.Map(location=[0, 0], zoom_start=2)`.
6. Tambahkan `folium.Choropleth(...)` dengan parameter utama berikut:

| Parameter | Nilai | Arti |
|---|---|---|
| `geo_data` | `world_geo` | Batas negara |
| `data` | `df_canada` | Data imigrasi |
| `columns` | `["Country", "Total"]` | Kolom nama dan nilai |
| `key_on` | `"feature.properties.name"` | Penghubung nama negara di data dan di GeoJSON |
| `fill_color` | `"PRGn"` | Skema warna |
| `fill_opacity` | `0.7` | Transparansi isi |
| `line_opacity` | `0.2` | Transparansi garis batas |
| `legend_name` | `"Total Immigration to Canada"` | Judul legend |

7. Tambahkan `GeoJsonTooltip` agar nama negara dan total tampil saat kursor diarahkan ke negara.
8. Simpan peta ke `../outputs/total_immigrations_choropleth_map.html`, lalu tampilkan.

**Cara membaca legend:** makin gelap warna negara dan makin dekat ke hijau, makin tinggi jumlah imigrannya.

**Hasil:** imigrasi tertinggi selama 33 tahun (1980-2013) berasal dari **China, India, dan Filipina**, diikuti Polandia, Pakistan, dan US.

Saran eksplorasi dari notebook: buat choropleth untuk tahun tertentu atau satu dekade, lalu bandingkan dengan pola keseluruhan periode.

---

## 9. File output

Notebook 01 menyimpan 13 gambar ke folder `../outputs/`:

| File | Berasal dari bagian |
|---|---|
| `waffle_proportions_immigrant_denmark_sweden_norway.png` | Waffle chart tiga negara Nordik |
| `waffle_proportions_immigrant_china_india.png` | Waffle chart China dan India |
| `basic_word_clouds.png` | Word cloud novel Alice (ukuran diperbesar) |
| `stopwords_said_word_clouds.png` | Word cloud setelah `said` jadi stopword |
| `masking_word_clouds.png` | Word cloud dengan mask Alice |
| `total_immigrations_word_clouds.png` | Word cloud nama negara |
| `masking_total_immigrations_word_clouds.png` | Word cloud nama negara dengan mask Kanada |
| `seaborn_count_plot_continent.png` | Countplot benua |
| `seaborn_bar_plot_total_continent.png` | Barplot total per benua |
| `seaborn_regression_plot_total_immigrants.png` | Regplot dengan font besar |
| `seaborn_regression_plot_total_immigrants_ticks_bg.png` | Regplot dengan style `ticks` |
| `seaborn_regression_plot_total_immigrants_whitegrid_bg.png` | Regplot dengan style `whitegrid` |
| `seaborn_regression_plot_total_immigrants_three_countries.png` | Regplot Denmark, Sweden, Norway |

Notebook 02 menyimpan 6 peta interaktif ke folder yang sama. File HTML bisa dibuka langsung di browser dan tetap bisa di-zoom serta diklik.

| File | Isi |
|---|---|
| `canada_dark_gray_map.html` | Peta Kanada dengan tile Dark Gray Canvas, zoom 4 |
| `canada_light_gray` | Peta Kanada dengan tile Light Gray Canvas, zoom 4 (tersimpan tanpa ekstensi `.html`) |
| `sanfrancisco_incidents_two_marker_map.html` | Insiden San Francisco dengan pin marker dan tooltip kategori |
| `sanfrancisco_incidents_circle_marker_map.html` | Insiden San Francisco dengan `CircleMarker` dan tooltip kategori |
| `sanfrancisco_incidents_marker_cluster_map.html` | Insiden San Francisco dengan marker cluster |
| `total_immigrations_choropleth_map.html` | Choropleth dunia total imigrasi ke Kanada 1980-2013 |

File lain di folder `outputs/` adalah `canada.png`, yaitu mask peta Kanada yang kamu siapkan sendiri. File `world_countries.json` tersimpan di folder kerja notebook 02.

---

## 10. Latihan di dalam notebook

| No | Notebook | Soal | Pendekatan |
|---|---|---|---|
| 1 | 01 | Waffle chart kontribusi China dan India | `df_canada.loc[["China", "India"], :]`, waffle `tab20c` |
| 2 | 01 | Dataframe total imigran per tahun 1980-2013 | `df_canada[years].sum(axis=0)`, lalu rapikan menjadi `df_total` |
| 3 | 01 | Regression plot imigrasi Denmark, Sweden, Norway | Transpose, `sum(axis=1)`, `regplot` dengan style `whitegrid` |
| 4 | 02 | Peta Meksiko | `folium.Map(location=[19.434, -99.139], zoom_start=6)` |
| 5 | 02 | Peta Meksiko dengan latar terang | Tile Esri Light Gray Canvas, `zoom_start=6` |

---

## 11. Temuan utama

- Total imigran dari seluruh negara selama 1980-2013: **6.409.153**.
- Negara asal terbesar: **China, India, Filipina**, disusul Polandia, Pakistan, dan US.
- Rata-rata total imigran per negara paling tinggi ada di **N-America** (120.571), lalu **Asia** (67.710) dan **Europe** (32.813). Oceania paling rendah (3.941).
- Kata yang paling sering muncul di novel Alice: **Alice**, **said**, **little**, **Queen**. Kata `said` dibuang karena kurang informatif.
- Dari 150.500 insiden SFPD 2016, 100 insiden pertama dipakai untuk latihan peta marker.

---

## 12. Catatan teknis

- **`max_words` pada WordCloud** (di notebook disebut "2000 maksimal kata") membatasi jumlah kata berbeda yang ditampilkan. Seluruh novel tetap diproses.
- **Urutan `savefig` dan `show`:** simpan gambar sebelum `plt.show()` supaya file tidak kosong.
- **`sns.set(...)` dan `sns.set_style(...)`** berlaku global. Semua plot sesudahnya ikut berubah.
- **`tooltip` dan `popup` Folium:** `tooltip` muncul saat kursor diarahkan, `popup` muncul saat marker diklik. Notebook memakai `tooltip` untuk pin dan `CircleMarker`, dan `popup` untuk marker cluster.
- **Urutan koordinat:** `[latitude, longitude]`. Di dataset insiden, kolom `Y` adalah latitude dan `X` adalah longitude.
- **Pencocokan nama di choropleth:** nama negara di dataframe harus sama dengan `properties.name` di GeoJSON. Jumlahnya berbeda (195 baris data, 177 negara GeoJSON), jadi sebagian negara bisa tidak terwarnai.
- **Peta Folium** dapat tidak tampil di pratinjau notebook pada beberapa platform, misalnya GitHub. Notebook 02 sudah menyimpan peta ke `outputs/` dalam format HTML, jadi buka file tersebut di browser.
- **Nama file saat `save`:** Folium menulis file persis sesuai nama yang kamu berikan. Peta Kanada terang tersimpan sebagai `canada_light_gray` tanpa `.html`. Tambahkan `.html` agar file langsung terbuka di browser.
- **Urutan `save`:** file HTML hanya berisi layer yang sudah ditambahkan ke peta saat `save` dipanggil. Panggil `save` setelah semua `add_child` dan `add_to` selesai.
- **Hanya 100 insiden** dipakai agar peta ringan. Memuat semua 150.500 titik akan memperlambat browser.

---

## 13. Troubleshooting

Daftar lengkap error dan solusinya ada di bagian **Error umum dan solusinya** pada [`cheatsheet.md`](./cheatsheet.md). Ringkasannya:

| Masalah | Solusi cepat |
|---|---|
| `ModuleNotFoundError` | `pip install pywaffle wordcloud folium`, lalu restart kernel |
| `FileNotFoundError` untuk `Canada.csv` atau `outputs` | Cek struktur folder dan lokasi notebook |
| Peta tidak tampil | Tulis variabel peta di baris terakhir sel |
| Negara tidak berwarna di choropleth | Samakan ejaan nama dan periksa `key_on` |
| Gambar hasil kosong | Panggil `savefig` sebelum `show` |
| File peta tidak terbuka di browser | Tambahkan `.html` pada nama file di `save(...)` |

---

## 14. Referensi dan kredit

Materi dan dataset mengikuti lab dari kursus IBM Skills Network (kode DV0101EN), dengan penyesuaian bahasa dan kode pada notebook ini.

**Dokumentasi library**

- [pandas](https://pandas.pydata.org/)
- [NumPy](https://numpy.org/)
- [Matplotlib](https://matplotlib.org/)
- [Seaborn](https://seaborn.pydata.org/) dan [`regplot`](https://seaborn.pydata.org/generated/seaborn.regplot.html)
- [PyWaffle](https://github.com/ligyxy/PyWaffle)
- [word_cloud](https://github.com/amueller/word_cloud/) oleh Andreas Mueller
- [Folium](https://github.com/python-visualization/folium/)

**Sumber data**

- [UN International Migration Flows (revisi 2015)](https://www.un.org/development/desa/pd/data/international-migration-flows)
- [SFPD Police Department Incident Reports](https://data.sfgov.org/Public-Safety/Police-Department-Incident-Reports-Historical-2003/tmnf-yvry)