# Cheatsheet Modul 2: Basic and Specialized Visualization Tools

Ringkasan sintaks untuk grafik dasar dan grafik khusus dari pandas dan Matplotlib, mengikuti urutan notebook 01 sampai 04.

| Notebook | Isi |
|---|---|
| 01 | Area plot, histogram, bar chart |
| 02 | Treemap dan pivot chart |
| 03 | Pie chart, box plot, scatter plot, bubble plot |
| 04 | Plotting langsung dengan Matplotlib |

## 1. Persiapan

```python
import numpy as np
import pandas as pd
import matplotlib as mpl
import matplotlib.pyplot as plt

mpl.style.use("ggplot")
```

## 2. Persiapan data (dataset imigrasi Kanada)

```python
# Nama kolom tahun dijadikan string agar mudah dipakai
df_can.columns = list(map(str, df_can.columns))

# Menjadikan nama negara sebagai index
df_can.set_index("Country", inplace=True)

# Daftar tahun dan kolom total
years = list(map(str, range(1980, 2014)))
df_can["Total"] = df_can[years].sum(axis=1)
```

## 3. Dua gaya penulisan Matplotlib

| Gaya | Ciri | Contoh |
|---|---|---|
| Scripting layer | Memakai `plt.` dan pembungkus `df.plot()` | `df.plot(kind="line")` |
| Artist layer | Memakai objek `fig` dan `ax` | `fig, ax = plt.subplots()` |

```python
# Artist layer
fig, ax = plt.subplots(figsize=(10, 6))
df.plot(kind="area", ax=ax)
ax.set_title("Judul")
ax.set_xlabel("Tahun")
ax.set_ylabel("Jumlah")
plt.show()
```

## 4. Menyimpan grafik ke folder outputs

Notebook berada di `notebooks/` dan hasil disimpan di `outputs/`, sehingga path relatifnya adalah `../outputs/`. Perintah `savefig` dijalankan sebelum `plt.show()`.

```python
plt.savefig("../outputs/area_plot_imigrasi_teratas.png", dpi=300, bbox_inches="tight")
plt.show()
```

---

# BAGIAN A: BASIC VISUALIZATION TOOLS (Notebook 01)

## 5. Area Plot

Digunakan untuk melihat tren kumulatif dan kontribusi setiap kategori dari waktu ke waktu.

```python
# Ambil 5 negara dengan total tertinggi, lalu transpose agar tahun menjadi index
df_top5 = df_can.sort_values("Total", ascending=False).head(5)
df_top5 = df_top5[years].transpose()

# Area plot bertumpuk (stacked adalah bawaan)
df_top5.plot(kind="area", figsize=(14, 8))

# Area plot tidak bertumpuk
df_top5.plot(kind="area", stacked=False, alpha=0.25, figsize=(14, 8))
```

| Parameter | Fungsi |
|---|---|
| `stacked` | `True` untuk bertumpuk, `False` agar area saling tumpang tindih |
| `alpha` | Transparansi antara 0 dan 1 |
| `figsize` | Ukuran gambar dalam inci (lebar, tinggi) |

Catatan: untuk memilih negara dengan total terendah, gunakan `.tail(5)` sebagai pengganti `.head(5)`.

## 6. Histogram

Digunakan untuk melihat sebaran frekuensi dari satu variabel numerik.

```python
# Mengetahui jumlah data per bin sebelum plotting
count, bin_edges = np.histogram(df_can["2013"])

# Histogram satu kolom
df_can["2013"].plot(kind="hist", figsize=(8, 5), xticks=bin_edges)

# Histogram beberapa kolom (tahun menjadi index, negara menjadi kolom)
df_t = df_can.loc[["Denmark", "Norway", "Sweden"], years].transpose()

# Bertumpuk
df_t.plot(kind="hist", bins=15, alpha=0.6, stacked=True, figsize=(10, 6))

# Tidak bertumpuk
df_t.plot(kind="hist", bins=15, alpha=0.6, stacked=False, figsize=(10, 6))
```

Pola yang sama berlaku untuk kelompok negara lain, misalnya Greece, Bulgaria, dan Albania.

| Parameter | Fungsi |
|---|---|
| `bins` | Jumlah kelompok interval, bawaan 10 |
| `xticks` | Menyelaraskan label sumbu x dengan batas bin |
| `stacked` | Menumpuk histogram antar kategori |
| `color` | Daftar warna, satu untuk setiap kolom |

Catatan: `df["2013"]` menghasilkan sebaran jumlah imigran antar negara pada tahun 2013. Setelah di-transpose, histogram menunjukkan sebaran nilai antar tahun untuk setiap negara.

## 7. Bar Chart

Digunakan untuk membandingkan nilai antar kategori atau antar periode.

```python
# Bar vertikal (nilai per tahun untuk satu negara)
df_iceland = df_can.loc["Iceland", years]
df_iceland.plot(kind="bar", figsize=(10, 6), rot=90)
plt.xlabel("Tahun")
plt.ylabel("Jumlah imigran")
plt.title("Imigrasi dari Iceland ke Kanada")

# Bar horizontal (15 negara teratas)
df_top15 = df_can.sort_values("Total", ascending=True).tail(15)["Total"]
df_top15.plot(kind="barh", figsize=(12, 8))
```

Anotasi panah dan teks pada bar chart:

```python
# Panah
plt.annotate(
    "",
    xy=(32, 70),
    xytext=(28, 20),
    xycoords="data",
    arrowprops=dict(arrowstyle="->", connectionstyle="arc3", color="blue", lw=2),
)

# Teks
plt.annotate("Krisis keuangan 2008 sampai 2011", xy=(28, 30), rotation=72.5, va="bottom", ha="left")
```

Catatan: pada bar chart, koordinat sumbu x berupa urutan batang (0 sampai 33), bukan nilai tahun. Angka pada contoh di atas hanya ilustrasi dan perlu disesuaikan dengan data.

Menampilkan nilai pada bar horizontal:

```python
for index, value in enumerate(df_top15):
    label = format(int(value), ",")
    plt.annotate(label, xy=(value - 47000, index - 0.10), color="white")
```

Selisih `47000` bergantung pada skala sumbu dan perlu disesuaikan.

---

# BAGIAN B: TREEMAP DAN PIVOT CHART (Notebook 02)

Catatan: isi bagian ini disusun dari topik pada nama notebook. Sesuaikan dengan pustaka dan kode yang dipakai pada notebook 02.

## 8. Treemap

Digunakan untuk menampilkan proporsi data hierarkis dalam bentuk persegi panjang bersarang. Luas persegi panjang mewakili besarnya nilai.

```python
import squarify

df_cont = df_can.groupby("Continent")["Total"].sum().sort_values(ascending=False)

plt.figure(figsize=(10, 6))
squarify.plot(sizes=df_cont.values, label=df_cont.index, alpha=0.8)
plt.axis("off")
plt.title("Imigrasi ke Kanada berdasarkan benua")
plt.show()
```

Alternatif interaktif dengan Plotly:

```python
import plotly.express as px

fig = px.treemap(df_can.reset_index(), path=["Continent", "Country"], values="Total")
fig.show()
```

## 9. Pivot chart

Pivot chart adalah grafik dari hasil ringkasan pivot table. Alurnya: ringkas data dengan `pivot_table`, lalu plot hasilnya.

```python
pivot = df_can.pivot_table(index="Continent", values="Total", aggfunc="sum")
pivot.plot(kind="bar", legend=False, figsize=(10, 6))
```

| Parameter `pivot_table` | Fungsi |
|---|---|
| `index` | Kolom yang menjadi baris ringkasan |
| `columns` | Kolom yang menjadi kategori di sisi kolom |
| `values` | Kolom yang dihitung |
| `aggfunc` | Fungsi agregasi, misalnya `sum`, `mean`, `count` |

---

# BAGIAN C: SPECIALIZED VISUALIZATION TOOLS (Notebook 03)

## 10. Pie Chart

Digunakan untuk menunjukkan proporsi setiap bagian terhadap keseluruhan. Cocok untuk jumlah kategori yang sedikit.

```python
# Agregasi total per benua
df_continents = df_can.groupby("Continent")["Total"].sum()

# Pie chart tanpa explode
df_continents.plot(
    kind="pie",
    figsize=(6, 6),
    autopct="%1.1f%%",
    startangle=90,
    shadow=True,
)
plt.axis("equal")
plt.title("Imigrasi ke Kanada berdasarkan benua, 1980 sampai 2013")
plt.show()
```

Versi yang lebih rapi agar label tidak saling menimpa:

```python
colors_list = ["gold", "yellowgreen", "lightcoral", "lightskyblue", "lightgreen", "pink"]
explode_list = [0.1, 0, 0, 0, 0.1, 0.1]  # jumlah elemen sama dengan jumlah kategori

df_continents.plot(
    kind="pie",
    figsize=(10, 6),
    autopct="%1.1f%%",
    startangle=90,
    labels=None,
    pctdistance=1.12,
    colors=colors_list,
    explode=explode_list,
)
plt.title("Imigrasi ke Kanada berdasarkan benua", y=1.12)
plt.axis("equal")
plt.legend(labels=df_continents.index, loc="upper left")
plt.show()
```

Untuk satu tahun tertentu, ganti kolom yang dijumlahkan:

```python
df_continents_2013 = df_can.groupby("Continent")["2013"].sum()
```

| Parameter | Fungsi |
|---|---|
| `autopct` | Format persentase pada setiap irisan |
| `startangle` | Sudut awal irisan pertama |
| `pctdistance` | Jarak label persentase dari pusat |
| `explode` | Menarik irisan keluar dari pusat |
| `labels=None` | Menghilangkan label di sekitar pie, diganti legend |

## 11. Box Plot

Digunakan untuk melihat sebaran data melalui median, kuartil, rentang, dan outlier.

```python
# Satu negara
df_japan = df_can.loc[["Japan"], years].transpose()
df_japan.plot(kind="box", figsize=(8, 6))

# Statistik numerik yang melandasi box plot
df_japan.describe()

# Dua negara dalam satu gambar
df_ci = df_can.loc[["China", "India"], years].transpose()
df_ci.plot(kind="box", figsize=(8, 6))

# Box plot horizontal
df_ci.plot(kind="box", vert=False, color="blue", figsize=(10, 4))

# 15 negara teratas
df_top15_t = df_can.sort_values("Total", ascending=False).head(15)[years].transpose()
df_top15_t.plot(kind="box", vert=False, figsize=(12, 8))
```

Membuat beberapa grafik berdampingan (subplots):

```python
fig = plt.figure()
ax0 = fig.add_subplot(1, 2, 1)
ax1 = fig.add_subplot(1, 2, 2)

df_ci.plot(kind="box", color="blue", vert=False, figsize=(20, 6), ax=ax0)
df_ci.plot(kind="line", figsize=(20, 6), ax=ax1)
plt.show()
```

Cara membaca box plot:

| Elemen | Makna |
|---|---|
| Garis tengah kotak | Median (Q2) |
| Batas kotak | Kuartil pertama (Q1) dan kuartil ketiga (Q3) |
| Whisker | Rentang data di luar kotak, bawaan 1,5 kali IQR |
| Titik di luar whisker | Outlier |

## 12. Scatter Plot

Digunakan untuk melihat hubungan antara dua variabel numerik.

```python
# Total imigran per tahun
df_tot = pd.DataFrame(df_can[years].sum(axis=0))
df_tot.index = list(map(int, df_tot.index))
df_tot.reset_index(inplace=True)
df_tot.columns = ["year", "total"]

# Scatter plot
df_tot.plot(kind="scatter", x="year", y="total", figsize=(10, 6), color="darkblue")
plt.title("Total imigrasi ke Kanada, 1980 sampai 2013")
plt.xlabel("Tahun")
plt.ylabel("Jumlah imigran")
```

Menambahkan garis regresi linear:

```python
x = df_tot["year"]
y = df_tot["total"]
fit = np.polyfit(x, y, deg=1)   # fit[0] = slope, fit[1] = intercept

plt.plot(x, fit[0] * x + fit[1], color="red")
plt.annotate("y={0:.0f} x + {1:.0f}".format(fit[0], fit[1]), xy=(2000, 150000))
plt.show()
```

Variasi untuk gabungan beberapa negara (Denmark, Norway, Sweden):

```python
total_dns = df_can.loc[["Denmark", "Norway", "Sweden"], years].sum(axis=0)
df_dns = pd.DataFrame({"year": list(map(int, total_dns.index)), "total": total_dns.values})
df_dns.plot(kind="scatter", x="year", y="total", figsize=(10, 6), color="darkblue")
```

Catatan: `reset_index()` diperlukan agar tahun menjadi kolom biasa dan dapat dipanggil sebagai `x="year"`.

## 13. Bubble Plot

Scatter plot dengan variabel ketiga yang diwakili oleh ukuran titik.

```python
# Siapkan data: tahun sebagai kolom, negara sebagai kolom
df_can_t = df_can[years].transpose()
df_can_t.index = list(map(int, df_can_t.index))
df_can_t.index.name = "Year"
df_can_t.reset_index(inplace=True)

# Fungsi normalisasi min-max agar ukuran titik berada pada rentang yang wajar
def normalize(series):
    return (series - series.min()) / (series.max() - series.min())

norm_brazil = normalize(df_can_t["Brazil"])
norm_argentina = normalize(df_can_t["Argentina"])

# Dua bubble plot dalam satu sumbu
ax0 = df_can_t.plot(
    kind="scatter", x="Year", y="Brazil", figsize=(14, 8),
    alpha=0.5, color="green", s=norm_brazil * 2000 + 10, xlim=(1975, 2015),
)
ax1 = df_can_t.plot(
    kind="scatter", x="Year", y="Argentina",
    alpha=0.5, color="blue", s=norm_argentina * 2000 + 10, ax=ax0,
)

ax0.set_ylabel("Jumlah imigran")
ax0.set_title("Imigrasi dari Brasil dan Argentina, 1980 sampai 2013")
ax0.legend(["Brazil", "Argentina"], loc="upper left", fontsize="x-large")
```

Pola yang sama dipakai untuk membandingkan China dan India dengan mengganti nama kolom.

| Parameter | Fungsi |
|---|---|
| `s` | Ukuran titik (luas dalam satuan poin kuadrat) |
| `alpha` | Transparansi agar titik yang bertumpuk tetap terlihat |
| `ax` | Menggambar beberapa seri pada sumbu yang sama |

---

# BAGIAN D: PLOTTING LANGSUNG DENGAN MATPLOTLIB (Notebook 04)

Catatan: isi bagian ini disusun dari topik pada nama notebook. Sesuaikan dengan kode yang dipakai pada notebook 04.

## 14. Memanggil fungsi Matplotlib tanpa `df.plot()`

Pembungkus `df.plot()` memanggil fungsi Matplotlib di belakang layar. Pemanggilan langsung memberi kendali penuh atas setiap elemen grafik.

```python
years_int = list(map(int, years))
fig, ax = plt.subplots(figsize=(10, 6))
```

| Jenis grafik | Pemanggilan langsung |
|---|---|
| Line | `ax.plot(x, y, color="red", linewidth=2, marker="o")` |
| Area bertumpuk | `ax.stackplot(x, [y1, y2], labels=["A", "B"], alpha=0.6)` |
| Bar vertikal | `ax.bar(kategori, nilai)` |
| Bar horizontal | `ax.barh(kategori, nilai)` |
| Histogram | `ax.hist(data, bins=15, alpha=0.6)` |
| Pie | `ax.pie(nilai, labels=label, autopct="%1.1f%%")` |
| Box plot | `ax.boxplot(data)` |
| Scatter dan bubble | `ax.scatter(x, y, s=ukuran, alpha=0.5)` |

Contoh lengkap:

```python
fig, ax = plt.subplots(figsize=(10, 6))
ax.plot(years_int, df_can.loc["India", years], label="India", color="red")
ax.plot(years_int, df_can.loc["China", years], label="China", color="blue")
ax.set_title("Imigrasi dari India dan China")
ax.set_xlabel("Tahun")
ax.set_ylabel("Jumlah imigran")
ax.legend()
fig.tight_layout()
fig.savefig("../outputs/line_plot_china_india.png", dpi=300, bbox_inches="tight")
plt.show()
```

Beberapa grafik dalam satu gambar:

```python
fig, axes = plt.subplots(1, 2, figsize=(14, 5))
axes[0].hist(df_can["2013"], bins=10)
axes[1].boxplot(df_can["2013"])
axes[0].set_title("Histogram")
axes[1].set_title("Box plot")
fig.tight_layout()
```

---

## 15. Memilih jenis grafik

| Tujuan analisis | Grafik yang sesuai |
|---|---|
| Tren total dan kontribusi per kategori | Area plot |
| Sebaran frekuensi satu variabel | Histogram |
| Perbandingan nilai antar kategori atau periode | Bar chart |
| Proporsi data hierarkis | Treemap |
| Ringkasan data berdasarkan kategori | Pivot chart |
| Proporsi bagian terhadap keseluruhan | Pie chart |
| Median, kuartil, dan outlier | Box plot |
| Hubungan dua variabel numerik | Scatter plot |
| Hubungan tiga variabel numerik | Bubble plot |

## 16. Kesalahan yang sering terjadi

1. Lupa mengubah nama kolom menjadi string, sehingga `df["2013"]` memunculkan `KeyError`.
2. Lupa melakukan `transpose()`, sehingga garis atau area yang muncul adalah negara, bukan tahun.
3. Memakai `sum(axis=1)` pada DataFrame yang masih memuat kolom non-numerik. Pilih kolom tahun terlebih dahulu.
4. Label sumbu x tidak sesuai dengan batas bin pada histogram. Gunakan `xticks=bin_edges`.
5. Area plot bertumpuk menutupi kategori lain. Gunakan `stacked=False` dan `alpha` rendah.
6. Pie chart dengan terlalu banyak kategori kecil sehingga label bertabrakan. Gunakan `labels=None` dan legend.
7. Scatter plot gagal karena tahun masih berupa index. Jalankan `reset_index()` terlebih dahulu.
8. Titik bubble terlalu besar karena nilai belum dinormalisasi. Gunakan normalisasi min-max sebelum diberikan ke parameter `s`.
9. File grafik tidak muncul di `outputs/` karena `savefig` dijalankan setelah `plt.show()`. Panggil `savefig` terlebih dahulu.
10. Anotasi pada bar chart meleset karena koordinat x diisi dengan tahun. Gunakan urutan batang.