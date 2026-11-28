# Cheatsheet: Advanced Visualization Tools dan Visualizing Geospatial Data

Contekan ini merangkum semua teknik yang dipakai di dua notebook:

- `01_advanced_visualization_tools.ipynb` (Waffle Chart, Word Cloud, Seaborn)
- `02_visualizing_geospatial_data.ipynb` (Folium: peta dasar, marker, choropleth)

Setiap bagian berisi template kode yang bisa langsung di-copy, tabel parameter, dan catatan kesalahan yang sering muncul.

---

## Daftar Isi

1. [Setup cepat](#1-setup-cepat)
2. [Alur kerja visualisasi](#2-alur-kerja-visualisasi)
3. [Pandas yang dipakai di notebook](#3-pandas-yang-dipakai-di-notebook)
4. [Matplotlib dasar](#4-matplotlib-dasar)
5. [Waffle Chart](#5-waffle-chart)
6. [Word Cloud](#6-word-cloud)
7. [Seaborn](#7-seaborn)
8. [Folium: peta dasar](#8-folium-peta-dasar)
9. [Folium: marker dan cluster](#9-folium-marker-dan-cluster)
10. [Folium: choropleth map](#10-folium-choropleth-map)
11. [Memilih jenis visualisasi](#11-memilih-jenis-visualisasi)
12. [Error umum dan solusinya](#12-error-umum-dan-solusinya)
13. [Glosarium](#13-glosarium)

---

## 1. Setup cepat

### Instalasi

```bash
pip install pandas numpy matplotlib seaborn pillow wordcloud pywaffle folium
```

### Import yang dipakai

```python
# Notebook 01
import matplotlib as mpl
import matplotlib.pyplot as plt
import numpy as np
import pandas as pd
import seaborn as sns
import wordcloud
from PIL import Image
from pywaffle import Waffle
from wordcloud import STOPWORDS, WordCloud

mpl.style.use("ggplot")  # gaya grafik ala ggplot
```

```python
# Notebook 02
import json
import urllib.request

import folium
import numpy as np
import pandas as pd
from folium import plugins
```

### Library apa untuk apa

| Library | Fungsi | Dipakai untuk |
|---|---|---|
| `pandas` | Mengolah tabel (dataframe) | Membaca CSV, filter, agregasi |
| `numpy` | Komputasi array | Mengubah gambar mask menjadi array |
| `matplotlib` | Dasar semua grafik | Figure, ukuran, simpan gambar |
| `seaborn` | Grafik statistik yang rapi | `countplot`, `barplot`, `regplot` |
| `pywaffle` | Membuat waffle chart | Proporsi antar kategori |
| `wordcloud` | Membuat awan kata | Visualisasi frekuensi kata |
| `PIL (Pillow)` | Membaca file gambar | Membuka gambar mask |
| `folium` | Peta interaktif (Leaflet) | Peta, marker, choropleth |

Cek versi yang terpasang:

```python
print("Matplotlib version:", mpl.__version__)
print("Seaborn version   :", sns.__version__)
print("WordCloud version :", wordcloud.__version__)
```

---

## 2. Alur kerja visualisasi

### Untuk grafik (Matplotlib, Seaborn, Waffle, Word Cloud)

```
Import library -> Baca data -> Siapkan data -> Buat plot -> Rapikan -> Simpan -> Tampilkan
```

1. **Baca data** dengan `pd.read_csv(...)`.
2. **Siapkan data**: pilih baris atau kolom, jumlahkan, kelompokkan.
3. **Buat plot** dengan fungsi dari library.
4. **Rapikan**: judul, label sumbu, ukuran gambar, font.
5. **Simpan** dengan `plt.savefig(...)`.
6. **Tampilkan** dengan `plt.show()`.

### Untuk peta (Folium)

```
Buat objek Map -> Tambahkan layer (marker / choropleth) -> Simpan ke HTML (save) -> Tulis nama variabel peta di baris terakhir sel
```

Peta Folium bersifat interaktif. Kamu bisa zoom, geser, dan klik langsung di notebook.

---

## 3. Pandas yang dipakai di notebook

Data utama: **Canada.csv** (195 baris x 39 kolom). Kolomnya: `Country`, `Continent`, `Region`, `DevName`, kolom tahun `1980` sampai `2013`, dan `Total`.

| Tujuan | Kode |
|---|---|
| Baca CSV (file lokal atau URL) | `df = pd.read_csv("path_atau_url.csv")` |
| Lihat 5 baris pertama | `df.head()` |
| Lihat jumlah baris dan kolom | `df.shape` |
| Jadikan kolom sebagai indeks | `df.set_index("Country", inplace=True)` |
| Pilih beberapa baris berdasarkan label | `df.loc[["Denmark", "Sweden", "Norway"], :]` |
| Pilih baris dan kolom tertentu | `df.loc[["Denmark", "Sweden"], years]` |
| Putar baris menjadi kolom (transpose) | `df.loc[[...], years].T` |
| Jumlahkan satu kolom | `df["Total"].sum()` |
| Jumlahkan per kolom (total per tahun) | `df[years].sum(axis=0)` |
| Jumlahkan per baris | `df_dsn.sum(axis=1)` |
| Nilai unik dalam kolom | `df["Continent"].unique()` |
| Ganti nilai teks | `df.replace("Northern America", "N-America")` |
| Rata-rata per kelompok | `df.groupby("Continent")["Total"].mean()` |
| Ambil N baris pertama | `df.iloc[0:100, :]` |
| Kembalikan indeks jadi kolom | `df.reset_index(inplace=True)` |
| Ganti nama semua kolom | `df.columns = ["years", "total"]` |
| Hapus nama indeks | `df.index.name = None` |
| Ubah dataframe jadi dictionary | `df.set_index("Country")["Total"].to_dict()` |

### List tahun (kolom tahun bertipe string)

```python
years = list(map(str, range(1980, 2014)))   # ['1980', '1981', ..., '2013']
```

### `axis=0` atau `axis=1`?

| Parameter | Arah hitung | Contoh |
|---|---|---|
| `axis=0` | Ke bawah, hasil satu angka per **kolom** | Total imigran per tahun |
| `axis=1` | Ke samping, hasil satu angka per **baris** | Total tiga negara pada tiap tahun (setelah transpose) |

### Resep: menyiapkan data untuk `regplot`

Seaborn butuh kolom `x` bertipe angka. Kolom tahun asli bertipe string, jadi perlu diubah.

```python
# 1. Total imigran per tahun
df_total = pd.DataFrame(df_canada[years].sum(axis=0))

# 2. Ubah indeks (tahun) menjadi angka
df_total.index = map(float, df_total.index)

# 3. Jadikan indeks sebagai kolom
df_total.reset_index(inplace=True)

# 4. Beri nama kolom yang jelas
df_total.columns = ["years", "total"]
```

Hasil akhirnya dataframe dengan dua kolom: `years` dan `total`.

---

## 4. Matplotlib dasar

| Kode | Fungsi |
|---|---|
| `mpl.style.use("ggplot")` | Memakai gaya grafik ggplot |
| `plt.figure(figsize=(15, 10))` | Mengatur ukuran gambar (lebar, tinggi dalam inci) |
| `plt.imshow(obj, interpolation="bilinear")` | Menampilkan gambar atau word cloud |
| `plt.axis("off")` | Menyembunyikan sumbu |
| `plt.savefig("path.png", dpi=200, bbox_inches="tight")` | Menyimpan gambar |
| `plt.show()` | Menampilkan gambar |

**Urutan wajib:** panggil `plt.savefig()` **sebelum** `plt.show()`. Kalau terbalik, file hasil bisa kosong.

| Parameter `savefig` | Arti |
|---|---|
| `dpi=200` | Kerapatan piksel. Makin besar, makin tajam dan makin besar file-nya |
| `bbox_inches="tight"` | Memotong margin putih berlebih dan menjaga legend tidak terpotong |

---

## 5. Waffle Chart

**Waffle chart** menampilkan proporsi dalam bentuk kotak-kotak. Satu kategori mendapat jumlah kotak sesuai porsinya. Cocok untuk dashboard dan perbandingan beberapa kategori dalam satu total.

### Template

```python
from pywaffle import Waffle

df_dsn = df_canada.loc[["Denmark", "Sweden", "Norway"], :]

fig = plt.figure(
    FigureClass=Waffle,
    rows=20,
    columns=30,
    values=df_dsn["Total"],
    cmap_name="tab20",
    legend={
        "labels": [f"{k} ({v})" for k, v in zip(df_dsn.index.values, df_dsn.Total)],
        "loc": "lower left",
        "bbox_to_anchor": (0.04, -0.1),
        "ncol": 3,
    },
)

plt.savefig("../outputs/waffle.png", dpi=200, bbox_inches="tight")
plt.show()
```

### Parameter

| Parameter | Arti | Contoh di notebook |
|---|---|---|
| `FigureClass=Waffle` | Memberi tahu Matplotlib untuk membuat waffle | Wajib ada |
| `rows`, `columns` | Jumlah baris dan kolom kotak. Total kotak = `rows x columns` | `20`, `30` (600 kotak) |
| `values` | Data yang dibagi ke kotak | `df_dsn["Total"]` |
| `cmap_name` | Skema warna Matplotlib | `"tab20"`, `"tab20c"` |
| `legend["labels"]` | Teks legend per kategori | `"Denmark (angka Total)"` |
| `legend["loc"]` | Posisi legend | `"lower left"` |
| `legend["bbox_to_anchor"]` | Geser legend (x, y) | `(0.04, -0.1)` |
| `legend["ncol"]` | Jumlah kolom legend | `3` |

### Trik label dengan list comprehension

```python
labels = [f"{k} ({v})" for k, v in zip(df_dsn.index.values, df_dsn.Total)]
```

`zip` menggabungkan nama negara (indeks) dan nilai `Total`. Hasilnya teks seperti `Denmark (angka Total)`.

> Catatan: notebook mengimpor `matplotlib.patches as mpatches` yang dibutuhkan untuk waffle manual. Dengan **pywaffle**, import itu tidak terpakai.

### Latihan yang dikerjakan

Waffle untuk China dan India: ganti `df_dsn` dengan `df_canada.loc[["China", "India"], :]`, pakai `cmap_name="tab20c"`, dan geser legend ke `(0.2, -0.1)`.

---

## 6. Word Cloud

**Word cloud** menampilkan kata dengan ukuran sesuai frekuensi. Kata yang sering muncul tampil lebih besar dan tebal.

### Template dasar

```python
from wordcloud import STOPWORDS, WordCloud

stopwords = set(STOPWORDS)       # daftar kata umum yang diabaikan
stopwords.add("said")            # tambah kata yang tidak informatif

wc = WordCloud(background_color="white", max_words=2000, stopwords=stopwords)
wc.generate(teks)                # teks = string panjang

plt.figure(figsize=(14, 18))
plt.imshow(wc, interpolation="bilinear")
plt.axis("off")
plt.savefig("../outputs/word_cloud.png", dpi=200, bbox_inches="tight")
plt.show()
```

### Mengunduh teks dari URL

```python
import urllib.request

alice_novel = urllib.request.urlopen(URL_TEKS).read().decode("utf-8")
```

### Parameter `WordCloud(...)`

| Parameter | Arti | Contoh |
|---|---|---|
| `background_color` | Warna latar | `"white"` |
| `max_words` | Batas jumlah kata berbeda yang ditampilkan | `2000` |
| `stopwords` | Himpunan kata yang dibuang | `stopwords` |
| `mask` | Array gambar sebagai cetakan bentuk | `alice_mask`, `canada_mask` |
| `contour_width` | Tebal garis tepi mask | `2` |
| `contour_color` | Warna garis tepi mask | `"lightgreen"` |

> `max_words=2000` membatasi jumlah kata berbeda yang tampil. Parameter ini **tidak** memotong teks menjadi 2000 kata pertama. Seluruh novel tetap diproses.

### Alur pengembangan di notebook

| Tahap | Perubahan | Hasil |
|---|---|---|
| 1 | `WordCloud()` bawaan | Kata umum seperti `said` mendominasi |
| 2 | Perbesar `figsize` | Kata kecil lebih terlihat |
| 3 | `stopwords.add("said")` | Kata yang lebih bermakna (Alice, little, Queen) |
| 4 | Tambah `mask=alice_mask` | Kata mengisi bentuk siluet Alice |
| 5 | Teks dari data imigrasi | Nama negara sebagai kata |
| 6 | `mask=canada_mask` plus kontur | Nama negara membentuk peta Kanada |

### Memakai mask

```python
alice_mask = np.array(Image.open(urllib.request.urlopen(URL_MASK)))   # dari URL
canada_mask = np.array(Image.open("../outputs/canada.png"))           # dari file lokal

wc = WordCloud(background_color="white", mask=alice_mask, stopwords=stopwords)
```

Area **putih** pada gambar dianggap kosong. Kata hanya mengisi area yang berwarna. Pakai gambar dengan latar putih bersih.

### Membuat teks dari data angka

Word cloud butuh teks, sedangkan data imigrasi berisi angka. Solusinya: ulang nama negara sebanyak porsi kontribusinya.

```python
max_words = 90
total_immigrants = df_canada["Total"].sum()
word_string = ""

for country in df_canada.index.values:
    if country.count(" ") == 0:          # hanya nama satu kata
        repeat_num_times = int(df_canada.loc[country, "Total"] / total_immigrants * max_words)
        word_string = word_string + ((country + " ") * repeat_num_times)

wordcloud_imm = WordCloud(background_color="white").generate(word_string)
```

Cara kerjanya:

- `Total / total_immigrants` menghasilkan porsi negara (angka 0 sampai 1).
- Dikali `max_words` menghasilkan berapa kali nama negara diulang.
- `int()` membulatkan ke bawah. Negara dengan kontribusi kecil dapat 0 pengulangan dan tidak tampil.
- Filter satu kata dipakai karena word cloud memecah teks per kata. Nama seperti "United Kingdom" akan terpecah menjadi dua kata.

---

## 7. Seaborn

Seaborn dibangun di atas Matplotlib dan menghasilkan grafik statistik dengan kode lebih singkat.

### Ringkasan tiga plot

| Plot | Fungsi | Sumbu Y | Catatan |
|---|---|---|---|
| `countplot` | Menghitung jumlah baris per kategori | Jumlah (count) | Seperti histogram untuk data kategorikal |
| `barplot` | Agregasi per kategori | Rata-rata (default) | Menampilkan confidence interval |
| `regplot` | Scatter plot plus garis regresi | Variabel numerik | Menampilkan tren |

### countplot

```python
plt.figure(figsize=(15, 10))
sns.countplot(data=df_canada1, x="Continent")
plt.savefig("../outputs/seaborn_count_plot_continent.png", dpi=200, bbox_inches="tight")
plt.show()
```

Satu baris data = satu negara. Jadi `countplot` pada `Continent` menghitung **jumlah negara per benua**.

### Memperpendek label yang terlalu panjang

```python
df_canada1 = df_canada.replace("Latin America and the Caribbean", "L-America")
df_canada1 = df_canada1.replace("Northern America", "N-America")
```

Perbesar `figsize` juga membantu agar label sumbu X tidak saling menimpa.

### barplot

```python
plt.figure(figsize=(15, 10))
sns.barplot(data=df_canada1, x="Continent", y="Total")
plt.show()
```

`barplot` mengelompokkan data per kategori, lalu menggambar nilai agregat (rata-rata). Garis tipis di atas batang adalah confidence interval.

Cara memverifikasi angkanya:

```python
df_canada1.groupby("Continent")["Total"].mean()
```

Hasil di notebook (rata-rata total imigran per negara dalam tiap benua):

| Benua | Rata-rata Total |
|---|---|
| Africa | 11.462,00 |
| Asia | 67.710,08 |
| Europe | 32.812,72 |
| L-America | 23.186,30 |
| N-America | 120.571,00 |
| Oceania | 3.941,00 |

### regplot (scatter plus garis regresi)

```python
sns.regplot(data=df_total, x="years", y="total")
```

Satu baris kode sudah menghasilkan titik data, garis regresi linear, dan area bayangan confidence interval.

#### Kustomisasi bertahap

```python
sns.regplot(data=df_total, x="years", y="total", color="green")                  # ganti warna
sns.regplot(data=df_total, x="years", y="total", color="green", marker="+")      # ganti bentuk titik
```

#### Versi lengkap

```python
plt.figure(figsize=(15, 10))
sns.set(font_scale=1.5)               # perbesar semua font
sns.set_style("whitegrid")            # latar putih dengan grid

ax = sns.regplot(
    data=df_total,
    x="years",
    y="total",
    color="green",
    marker="+",
    scatter_kws={"s": 200},           # ukuran titik
)
ax.set(xlabel="Year", ylabel="Total Immigration")
ax.set_title("Total Immigration to Canada from 1980 - 2013")

plt.savefig("../outputs/seaborn_regression_plot.png", dpi=200, bbox_inches="tight")
plt.show()
```

#### Parameter `regplot`

| Parameter | Arti | Contoh |
|---|---|---|
| `data` | Dataframe sumber | `df_total` |
| `x`, `y` | Nama kolom untuk sumbu X dan Y | `"years"`, `"total"` |
| `color` | Warna titik dan garis | `"green"` |
| `marker` | Bentuk titik | `"+"` |
| `scatter_kws` | Pengaturan khusus titik (dictionary) | `{"s": 200}` |

#### Mengatur tampilan

| Kode | Fungsi |
|---|---|
| `sns.set(font_scale=1.5)` | Memperbesar font tick, judul, dan label |
| `sns.set_style("ticks")` | Latar putih polos |
| `sns.set_style("whitegrid")` | Latar putih dengan garis grid |
| `ax.set(xlabel=..., ylabel=...)` | Mengubah label sumbu |
| `ax.set_title("...")` | Memberi judul |

> Tips tambahan: pengaturan `sns.set(...)` dan `sns.set_style(...)` berlaku **global**. Semua plot sesudahnya ikut berubah sampai kamu mengaturnya lagi. Pilihan style lain: `darkgrid`, `white`, `dark`.

#### Latihan yang dikerjakan: tiga negara Nordik

```python
nyears = list(map(str, range(1980, 2014)))

df_dsn = df_canada.loc[["Denmark", "Sweden", "Norway"], nyears].T   # tahun jadi baris
df_dsn = pd.DataFrame(df_dsn.sum(axis=1))                           # jumlahkan tiga negara
df_dsn.index = map(int, df_dsn.index)                               # tahun jadi angka
df_dsn.reset_index(inplace=True)
df_dsn.index.name = None
df_dsn.columns = ["years", "total"]

plt.figure(figsize=(15, 10))
sns.set(font_scale=1.5)
sns.set_style("whitegrid")
ax = sns.regplot(data=df_dsn, x="years", y="total", scatter_kws={"s": 200})
ax.set(xlabel="Year", ylabel="Total Immigration")
ax.set_title("Total Immigration from Denmark, Sweden, and Norway to Canada from 1980 - 2013")
plt.show()
```

---

## 8. Folium: peta dasar

**Folium** membuat peta interaktif berbasis Leaflet. Kamu mengolah data di Python, lalu Folium menggambarnya di peta.

### Peta paling sederhana

```python
world_map = folium.Map()
world_map          # tulis variabel di baris terakhir sel agar peta tampil
```

### Menentukan pusat dan zoom

```python
canada_map = folium.Map(location=[57.45323300961382, -101.66674931624746], zoom_start=4)
canada_map
```

| Parameter | Arti |
|---|---|
| `location=[lintang, bujur]` | Titik pusat peta, urutannya **latitude dulu, longitude kedua** |
| `zoom_start` | Level zoom awal. Makin besar, makin dekat |

### Level zoom yang dipakai di notebook

| `zoom_start` | Cakupan | Contoh di notebook |
|---|---|---|
| 2 | Seluruh dunia | Choropleth dunia |
| 4 | Negara besar | Kanada |
| 6 | Negara | Meksiko |
| 8 | Wilayah | Kanada (zoom dekat) |
| 12 | Kota | San Francisco |

### Koordinat yang dipakai

| Lokasi | `[latitude, longitude]` |
|---|---|
| Pusat dunia | `[0, 0]` |
| Kanada | `[57.45323300961382, -101.66674931624746]` |
| Meksiko | `[19.434041981675733, -99.13913907196424]` |
| San Francisco | `[37.77, -122.42]` |

### Gaya peta (tiles)

Folium punya tile bawaan. Notebook juga memakai tile Esri lewat URL.

```python
# Dark Gray Canvas
folium.Map(
    location=[57.45323300961382, -101.66674931624746],
    zoom_start=4,
    max_zoom=16,
    tiles="https://services.arcgisonline.com/ArcGIS/rest/services/Canvas/World_Dark_Gray_Base/MapServer/tile/{z}/{y}/{x}",
    attr="Esri, HERE, Garmin, © OpenStreetMap contributors",
)

# Light Gray Canvas (ganti World_Dark_Gray_Base menjadi World_Light_Gray_Base)
folium.Map(
    location=[19.434041981675733, -99.13913907196424],
    zoom_start=6,
    max_zoom=16,
    tiles="https://services.arcgisonline.com/ArcGIS/rest/services/Canvas/World_Light_Gray_Base/MapServer/tile/{z}/{y}/{x}",
    attr="Esri, HERE, Garmin, © OpenStreetMap contributors",
)
```

| Parameter | Arti |
|---|---|
| `tiles` | URL tile peta atau nama tile bawaan |
| `attr` | Teks atribusi sumber peta. Wajib ada saat memakai `tiles` berupa URL |
| `max_zoom` | Batas zoom maksimum |

| Gaya | Ciri | Cocok untuk |
|---|---|---|
| Default (OpenStreetMap) | Berwarna, detail | Navigasi umum |
| Dark Gray Canvas | Hitam putih, kontras tinggi | Menumpuk data di atas peta, sungai dan pesisir |
| Light Gray Canvas | Minimalis, latar terang | Tampilan bersih dan modern |

### Menyimpan peta ke HTML

```python
canada_map.save("../outputs/canada_dark_gray_map.html")
canada_map          # lalu tampilkan di notebook
```

| Aturan | Penjelasan |
|---|---|
| Panggil `save` pada objek peta | `nama_peta.save("path/file.html")` |
| Folder tujuan harus sudah ada | Contoh: `../outputs/` |
| Tulis ekstensi `.html` | `save` memakai nama persis seperti yang kamu tulis. Nama tanpa `.html`, misalnya `canada_light_gray`, menghasilkan file yang tidak langsung terbuka di browser |
| Panggil `save` setelah semua layer ditambahkan | Layer yang ditambahkan sesudah `save` tidak ikut masuk ke file |
| Buka hasilnya | Klik dua kali file HTML. Peta tetap interaktif |

---

## 9. Folium: marker dan cluster

### Data yang dipakai

Dataset insiden Kepolisian San Francisco 2016 (150.500 baris, 13 kolom). Notebook hanya memakai 100 baris pertama supaya peta ringan.

```python
df_incidents = pd.read_csv(URL_INSIDEN)
df_incidents = df_incidents.iloc[0:100, :]    # 100 insiden pertama
```

| Kolom utama | Arti |
|---|---|
| `Category` | Kategori kejahatan (dipakai untuk popup) |
| `X` | **Bujur** (longitude) |
| `Y` | **Lintang** (latitude) |

Karena `Y` adalah lintang, urutan `zip` selalu `zip(df.Y, df.X)`.

### Peta dasar San Francisco

```python
sanfran_map = folium.Map(location=[37.77, -122.42], zoom_start=12)
```

### Empat cara menampilkan titik

| Cara | Kode utama | Kapan dipakai |
|---|---|---|
| A. `FeatureGroup` plus `CircleMarker` | `folium.map.FeatureGroup()` | Mengelompokkan banyak lingkaran jadi satu layer |
| B. `Marker` plus tooltip | `folium.Marker(..., tooltip=label)` | Ingin ikon pin standar dengan keterangan saat hover |
| C. `CircleMarker` plus tooltip langsung | `CircleMarker(..., tooltip=label).add_to(map)` | Peta ramai, ingin lebih bersih (tanpa pin) |
| D. `MarkerCluster` plus popup | `plugins.MarkerCluster()` | Titik banyak dan rapat |

#### A. CircleMarker dalam FeatureGroup

```python
incidents = folium.map.FeatureGroup()

for lat, long in zip(df_incidents.Y, df_incidents.X):
    incidents.add_child(
        folium.vector_layers.CircleMarker(
            [lat, long],
            radius=5,
            color="yellow",
            fill=True,
            fill_color="red",
            fill_opacity=0.6,
        )
    )

sanfran_map.add_child(incidents)
```

#### B. Marker dengan tooltip

```python
for lat, long, label in zip(df_incidents.Y, df_incidents.X, df_incidents.Category):
    folium.Marker([lat, long], tooltip=label).add_to(sanfran_map)

sanfran_map.save("../outputs/sanfrancisco_incidents_two_marker_map.html")
```

Di notebook, sel ini juga menambahkan lingkaran biru lewat `FeatureGroup` (cara A).

#### C. CircleMarker dengan tooltip langsung

```python
sanfran_map = folium.Map(location=[37.77, -122.42], zoom_start=12)

for lat, long, label in zip(df_incidents.Y, df_incidents.X, df_incidents.Category):
    folium.vector_layers.CircleMarker(
        [lat, long],
        radius=5,
        color="yellow",
        tooltip=label,
        fill=True,
        fill_color="red",
        fill_opacity=0.6,
    ).add_to(sanfran_map)

sanfran_map.save("../outputs/sanfrancisco_incidents_circle_marker_map.html")
sanfran_map
```

#### D. MarkerCluster (keterangan lewat popup)

```python
from folium import plugins

sanfran_map = folium.Map(location=[37.77, -122.42], zoom_start=12)
incidents = plugins.MarkerCluster().add_to(sanfran_map)

for lat, long, label in zip(df_incidents.Y, df_incidents.X, df_incidents.Category):
    folium.Marker(location=[lat, long], icon=None, popup=label).add_to(incidents)

sanfran_map.save("../outputs/sanfrancisco_incidents_marker_cluster_map.html")
sanfran_map
```

Perilaku cluster:

- Zoom out penuh: semua titik menyatu menjadi satu cluster berisi `100`.
- Zoom in: cluster pecah menjadi cluster yang lebih kecil.
- Zoom paling dekat: muncul marker individual.

### Parameter `CircleMarker`

| Parameter | Arti |
|---|---|
| `[lat, long]` | Posisi titik |
| `radius` | Ukuran lingkaran |
| `color` | Warna **garis tepi** |
| `fill` | `True` agar lingkaran berisi warna |
| `fill_color` | Warna **isi** |
| `fill_opacity` | Transparansi isi (0 sampai 1) |
| `tooltip` | Teks yang muncul saat kursor diarahkan ke marker |
| `popup` | Teks yang muncul saat marker diklik |

| Parameter | Muncul saat | Dipakai di notebook |
|---|---|---|
| `tooltip` | Kursor diarahkan (hover) | Cara B dan C |
| `popup` | Marker diklik | Cara D (cluster) |

---

## 10. Folium: choropleth map

**Choropleth** adalah peta tematik. Setiap wilayah diwarnai sesuai nilai variabel, misalnya total imigran per negara.

### Bahan yang dibutuhkan

| Bahan | Isi | Di notebook |
|---|---|---|
| Data nilai | Dataframe dengan nama wilayah dan angka | `df_canada` (kolom `Country`, `Total`) |
| GeoJSON | File batas wilayah | `world_countries.json` |

### Langkah 1: unduh dan baca GeoJSON

```python
import json
import urllib.request

urllib.request.urlretrieve(URL_GEOJSON, "world_countries.json")

with open("world_countries.json", "r", encoding="utf-8") as f:
    world_geo = json.load(f)
```

### Langkah 2: cek struktur GeoJSON

```python
print(world_geo["type"])                          # FeatureCollection
print(len(world_geo["features"]))                 # 177 (jumlah negara)
print(world_geo["features"][0]["properties"])     # {'name': 'Afghanistan'}
```

Struktur ringkasnya:

```json
{
  "type": "FeatureCollection",
  "features": [
    {"type": "Feature", "properties": {"name": "Afghanistan"}, "geometry": "..."},
    "..."
  ]
}
```

Nama negara tersimpan di `feature.properties.name`. Jalur ini dipakai untuk `key_on`.

### Langkah 3 (opsional): siapkan teks tooltip

```python
total_per_negara = df_canada.set_index("Country")["Total"].to_dict()

for feature in world_geo["features"]:
    nama = feature["properties"]["name"]
    total = total_per_negara.get(nama)
    feature["properties"]["total_text"] = (
        f"{total:,.0f}" if total is not None else "Tidak ada data"
    )
```

`dict.get(nama)` mengembalikan `None` kalau nama tidak ditemukan, jadi kode tidak error. Negara tanpa data tampil sebagai "Tidak ada data".

### Langkah 4: buat choropleth

```python
world_map = folium.Map(location=[0, 0], zoom_start=2)

choropleth = folium.Choropleth(
    geo_data=world_geo,
    data=df_canada,
    columns=["Country", "Total"],
    key_on="feature.properties.name",
    fill_color="PRGn",
    fill_opacity=0.7,
    line_opacity=0.2,
    legend_name="Total Immigration to Canada",
).add_to(world_map)
```

### Langkah 5: tambahkan tooltip

```python
choropleth.geojson.add_child(
    folium.GeoJsonTooltip(
        fields=["name", "total_text"],
        aliases=["Negara:", "Total:"],
        sticky=True,
    )
)

world_map.save("../outputs/total_immigrations_choropleth_map.html")
world_map
```

### Parameter `folium.Choropleth`

| Parameter | Arti |
|---|---|
| `geo_data` | Data GeoJSON (batas wilayah) |
| `data` | Dataframe berisi nilai |
| `columns` | `[kolom_nama_wilayah, kolom_nilai]` |
| `key_on` | Jalur kunci di GeoJSON yang cocok dengan kolom nama di dataframe |
| `fill_color` | Skema warna (palet ColorBrewer) |
| `fill_opacity` | Transparansi isi |
| `line_opacity` | Transparansi garis batas |
| `legend_name` | Judul legend |

Parameter `GeoJsonTooltip`:

| Parameter | Arti |
|---|---|
| `fields` | Properti GeoJSON yang ditampilkan |
| `aliases` | Label tampilan untuk tiap field |
| `sticky` | `True` agar tooltip mengikuti kursor |

### Aturan `key_on`

- Format: `"feature.properties.<nama_kunci>"`.
- Peka huruf besar dan kecil. Tulis persis seperti di file GeoJSON.
- Isi nama di dataframe harus **sama persis** dengan nilai di GeoJSON. Nama yang berbeda ejaan membuat negara tidak terwarnai.
- Cara menemukan kunci: buka file GeoJSON atau cetak `world_geo["features"][0]["properties"]`.
- GeoJSON berisi 177 negara dan Canada.csv berisi 195 baris, jadi sebagian nama memang tidak akan cocok.

### Membaca hasil

Pada legend, warna menunjukkan besar total imigran. Notebook membacanya begini: makin gelap warna negara dan makin dekat ke hijau, makin tinggi jumlah imigrannya. Hasil notebook: imigrasi tertinggi selama 1980-2013 berasal dari **China, India, dan Filipina**, diikuti Polandia, Pakistan, dan US.

Palet `PRGn` berjalan dari ungu ke hijau. Palet lain seperti `YlOrRd` (kuning ke merah) juga bisa dipakai.

### Ide eksperimen

Buat choropleth untuk satu tahun tertentu atau satu dekade. Ganti kolom `"Total"` dengan kolom tahun, misalnya `"2013"`, atau jumlahkan beberapa kolom tahun lebih dulu.

---

## 11. Memilih jenis visualisasi

| Pertanyaan yang ingin dijawab | Pilihan | Fungsi |
|---|---|---|
| Berapa proporsi tiap kategori terhadap total? | Waffle chart | `Waffle` |
| Kata apa yang paling sering muncul? | Word cloud | `WordCloud` |
| Berapa banyak data di tiap kategori? | Count plot | `sns.countplot` |
| Berapa rata-rata nilai tiap kategori? | Bar plot | `sns.barplot` |
| Bagaimana tren atau hubungan dua variabel? | Regression plot | `sns.regplot` |
| Di mana kejadian berlangsung? | Peta dengan marker | `folium.Marker`, `CircleMarker` |
| Ada terlalu banyak titik dalam satu area? | Marker cluster | `plugins.MarkerCluster` |
| Bagaimana nilai berbeda antar negara atau wilayah? | Choropleth | `folium.Choropleth` |

---

## 12. Error umum dan solusinya

| Gejala | Penyebab | Solusi |
|---|---|---|
| `ModuleNotFoundError: pywaffle` (atau `wordcloud`, `folium`) | Library belum terpasang | `pip install pywaffle wordcloud folium`, lalu restart kernel |
| `FileNotFoundError: ../../data/raw/Canada.csv` | Path relatif tidak cocok dengan lokasi notebook | Cek lokasi kerja dengan `import os` lalu `os.getcwd()`, atau pakai URL |
| `FileNotFoundError` saat `savefig` | Folder `../outputs` belum ada | `os.makedirs("../outputs", exist_ok=True)` |
| `AttributeError: module 'urllib' has no attribute 'request'` | `urllib.request` belum diimpor | Tulis `import urllib.request` |
| File gambar hasil `savefig` kosong | `savefig` dipanggil setelah `show` | Panggil `savefig` lebih dulu |
| Label sumbu X saling menimpa | Nama kategori terlalu panjang | Perbesar `figsize` atau singkat label dengan `replace` |
| `KeyError` pada `df.loc[...]` | Nama negara salah ejaan, atau `set_index("Country")` belum dijalankan | Cek ejaan dan urutan sel |
| `regplot` gagal atau sumbu X aneh | Kolom tahun masih bertipe string | Ubah ke `int` atau `float` |
| Word cloud penuh kata umum | Stopwords kurang | `stopwords.add("kata")` |
| Mask word cloud tidak berpengaruh | Mask bukan array atau latar tidak putih | Pakai `np.array(Image.open(...))` dan gambar berlatar putih |
| Peta Folium tidak tampil | Variabel peta tidak berada di baris terakhir sel | Tulis `nama_peta` sendirian di baris terakhir |
| File HTML peta tidak terbuka di browser | Nama file di `save(...)` tidak berakhiran `.html` | Tambahkan `.html`, atau ganti nama file |
| File HTML peta tidak memuat sebagian layer | `save` dipanggil sebelum `add_child` atau `add_to` | Pindahkan `save` ke setelah semua layer ditambahkan |
| Teks marker tidak muncul saat diklik | Marker memakai `tooltip`, bukan `popup` | `tooltip` muncul saat hover, `popup` saat klik |
| `ValueError` soal attribution saat memakai `tiles` berupa URL | Parameter `attr` belum diisi | Tambahkan `attr="..."` |
| Tile peta kosong atau abu-abu polos | Tidak ada koneksi internet atau URL tile salah | Cek koneksi dan salin ulang URL tile |
| Marker muncul di lokasi yang salah | Latitude dan longitude tertukar | Urutan `[lat, long]`. Di dataset insiden: `Y` = lat, `X` = long |
| Negara tidak berwarna di choropleth | Nama di data tidak cocok dengan GeoJSON, atau `key_on` salah | Samakan ejaan nama dan periksa huruf besar kecil di `key_on` |
| Peta lambat atau berat | Terlalu banyak marker | Batasi data dengan `iloc[0:100]` atau pakai `MarkerCluster` |

---

## 13. Glosarium

| Istilah | Arti singkat |
|---|---|
| **dataframe** | Tabel data di pandas |
| **index** | Label baris dataframe |
| **aggregate** | Meringkas banyak nilai menjadi satu, misalnya `sum` atau `mean` |
| **categorical** | Data berupa kategori, misalnya nama benua |
| **waffle chart** | Grafik kotak-kotak untuk menampilkan proporsi |
| **word cloud** | Awan kata. Ukuran kata mengikuti frekuensi |
| **stopwords** | Kata umum yang diabaikan (the, and, said) |
| **mask** | Gambar cetakan bentuk untuk word cloud |
| **regression line** | Garis tren yang paling cocok dengan sebaran titik |
| **confidence interval** | Rentang ketidakpastian estimasi, tampil sebagai garis atau area bayangan |
| **latitude (lintang)** | Posisi utara-selatan |
| **longitude (bujur)** | Posisi timur-barat |
| **zoom level** | Tingkat pembesaran peta |
| **tiles** | Potongan gambar yang menyusun peta dasar |
| **marker** | Penanda titik di peta |
| **popup** | Kotak keterangan yang muncul saat marker diklik |
| **tooltip** | Keterangan singkat yang muncul saat kursor diarahkan ke marker atau wilayah |
| **cluster** | Kumpulan marker berdekatan yang digabung jadi satu angka |
| **choropleth** | Peta yang wilayahnya diwarnai sesuai nilai data |
| **GeoJSON** | Format file JSON yang menyimpan bentuk dan batas wilayah |
| **FeatureCollection** | Struktur GeoJSON berisi banyak wilayah (`features`) |