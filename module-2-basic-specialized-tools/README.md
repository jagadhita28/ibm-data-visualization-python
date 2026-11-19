# Modul 2: Basic and Specialized Visualization Tools

Modul ini merupakan bagian dari kursus **Data Visualization with Python** (IBM). Fokusnya adalah membuat grafik dasar dan grafik khusus menggunakan pandas dan Matplotlib dengan dataset imigrasi ke Kanada tahun 1980 sampai 2013.

## Cakupan materi

| Notebook | Topik |
|---|---|
| 01 | Basic visualization tools: area plot, histogram, bar chart |
| 02 | Treemap dan pivot chart |
| 03 | Specialized visualization tools: pie chart, box plot, scatter plot, bubble plot |
| 04 | Plotting langsung dengan Matplotlib |

## Struktur folder

```
module-2-basic-specialized-tools/
├── notebooks/
│   ├── 01_basic_visualization_tools.ipynb
│   ├── 02_treemaps_pivot_charts.ipynb
│   ├── 03_specialized_visualization_tools.ipynb
│   └── 04_plotting_directly_with_matplotlib.ipynb
├── outputs/
├── README.md
└── cheatsheets.md
```

Folder `notebooks/` berisi seluruh kode analisis, sedangkan folder `outputs/` berisi grafik hasil dalam format PNG. Ringkasan sintaks setiap jenis grafik tersedia pada [cheatsheets.md](cheatsheets.md).

## Dataset

Data imigrasi ke Kanada berdasarkan negara asal untuk periode 1980 sampai 2013, bersumber dari United Nations Population Division yang disediakan dalam materi kursus.

## Hasil visualisasi

| Notebook | Topik | File hasil |
|---|---|---|
| 01 | Area plot | [area_plot_imigrasi_teratas.png](outputs/area_plot_imigrasi_teratas.png)<br>[area_plot_imigrasi_teratas_025.png](outputs/area_plot_imigrasi_teratas_025.png)<br>[area_plot_imigrasi_terendah.png](outputs/area_plot_imigrasi_terendah.png) |
| 01 | Histogram | [histogram_imigrasi_2013.png](outputs/histogram_imigrasi_2013.png)<br>[stacked_histogram_imigrasi_denmark_norway_sweden.png](outputs/stacked_histogram_imigrasi_denmark_norway_sweden.png)<br>[unstacked_histogram_imigrasi_denmark_norway_sweden.png](outputs/unstacked_histogram_imigrasi_denmark_norway_sweden.png)<br>[stacked_histogram_imigrasi_greece_bulgaria_albania.png](outputs/stacked_histogram_imigrasi_greece_bulgaria_albania.png)<br>[unstacked_histogram_imigrasi_greece_bulgaria_albania.png](outputs/unstacked_histogram_imigrasi_greece_bulgaria_albania.png) |
| 01 | Bar chart | [bar_plot_imigrasi_iceland.png](outputs/bar_plot_imigrasi_iceland.png)<br>[bar_arrow_imigrasi_iceland.png](outputs/bar_arrow_imigrasi_iceland.png)<br>[bar_arrow_text_imigrasi_iceland.png](outputs/bar_arrow_text_imigrasi_iceland.png)<br>[barh_plot_15_top_country_with_annotate.png](outputs/barh_plot_15_top_country_with_annotate.png) |
| 02 | Treemap dan pivot chart | [treemaps_example.png](outputs/treemaps_example.png)<br>[pivot_charts_example.png](outputs/pivot_charts_example.png) |
| 03 | Pie chart | [pie_charts_immigration_by_continent.png](outputs/pie_charts_immigration_by_continent.png)<br>[pie_charts_immigration_by_continent_2013.png](outputs/pie_charts_immigration_by_continent_2013.png)<br>[pie_chart_without_explode.png](outputs/pie_chart_without_explode.png) |
| 03 | Box plot | [box_plot_japan_immigrants.png](outputs/box_plot_japan_immigrants.png)<br>[box_plot_china_india_immigrants.png](outputs/box_plot_china_india_immigrants.png)<br>[horizontal_box_plot.png](outputs/horizontal_box_plot.png)<br>[box_plot_top_15_immigrant.png](outputs/box_plot_top_15_immigrant.png)<br>[subplots_immigrant_china_india.png](outputs/subplots_immigrant_china_india.png) |
| 03 | Scatter plot | [scatter_plot_total_immigrants.png](outputs/scatter_plot_total_immigrants.png)<br>[scatter_with_line_plot_total_immigrants.png](outputs/scatter_with_line_plot_total_immigrants.png)<br>[scatter_plot_immigrants_denmark_norway_sweden.png](outputs/scatter_plot_immigrants_denmark_norway_sweden.png) |
| 03 | Bubble plot | [bubble_plots_immigrant_brazil_argentina.png](outputs/bubble_plots_immigrant_brazil_argentina.png)<br>[bubble_plots_immigrant_china_india.png](outputs/bubble_plots_immigrant_china_india.png) |
| 04 | Plotting langsung | Belum tersedia |

## Cara menjalankan

```bash
git clone https://github.com/jagadhita28/ibm-data-visualization-python.git
cd ibm-data-visualization-python
git checkout module-2
cd module-2-basic-specialized-tools
jupyter lab
```

Buka notebook pada folder `notebooks/` secara berurutan dari 01 sampai 04, lalu jalankan seluruh sel dari atas ke bawah. Grafik akan tersimpan ke folder `outputs/`.

## Pustaka yang digunakan

- pandas
- NumPy
- Matplotlib
- openpyxl (membaca file Excel)
- squarify (treemap)