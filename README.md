# Laporan Bulanan Produk berdasarkan Total Pendapatan

```sql
-- Buat tabel sementara
WITH report_monthly_orders_product_agg AS (
  SELECT
    FORMAT_DATE('%Y-%m', DATE(oi.created_at)) AS year_month,
    p.id AS product_id,
    p.name AS product_name,
    p.category,
    p.brand,
    COUNT(*) AS total_items_sold,
    SUM(p.retail_price) AS total_revenue
  FROM
    `bigquery-public-data.thelook_ecommerce.order_items` AS oi
  JOIN
    `bigquery-public-data.thelook_ecommerce.products` AS p
    ON oi.product_id = p.id
  WHERE
    oi.status = 'Complete'
  GROUP BY
    year_month, product_id, product_name, category, brand
)

-- Hasil akhir
SELECT
  *
FROM
  report_monthly_orders_product_agg
ORDER BY
  year_month,
  total_revenue DESC;
```


# Penjelasan
1. WITH report_monthly_orders_product_agg AS (...):

* Menggunakan Common Table Expression (CTE) untuk mendefinisikan tabel sementara bernama report_monthly_orders_product_agg. Ini memungkinkan kita untuk merujuk ke hasil query ini dalam pernyataan SELECT berikutnya.

2. SELECT FORMAT_DATE('%Y-%m', DATE(oi.created_at)) AS year_month:

* Mengonversi tanggal created_at dari tabel order_items menjadi format YYYY-MM untuk mengelompokkan data penjualan per bulan.
3. p.id AS product_id, p.name AS product_name, p.category, p.brand:

* Mengambil informasi produk dari tabel products, termasuk ID produk, nama, kategori, dan merek.
4. COUNT(*) AS total_items_sold:

* Menghitung jumlah total item yang terjual untuk setiap produk per bulan.
5. SUM(p.retail_price) AS total_revenue:

* Menjumlahkan harga jual (retail_price) dari setiap produk yang terjual untuk mendapatkan total pendapatan per produk per bulan.
6. FROM bigquery-public-data.thelook_ecommerce.order_items AS oi:

* Mengambil data dari tabel order_items dengan alias oi.
7. JOIN bigquery-public-data.thelook_ecommerce.products AS p ON oi.product_id = p.id:

* Menggabungkan tabel order_items dan products berdasarkan product_id untuk mendapatkan detail produk yang terkait dengan setiap item pesanan.
8. WHERE oi.status = 'Complete':

* Memfilter hanya pesanan dengan status 'Complete' untuk memastikan bahwa hanya penjualan yang berhasil yang dihitung.
9. GROUP BY year_month, product_id, product_name, category, brand:

* Mengelompokkan data berdasarkan bulan-tahun dan detail produk untuk melakukan agregasi pada total_items_sold dan total_revenue.
10. ORDER BY year_month, total_revenue DESC:

* Mengurutkan hasil akhir berdasarkan year_month dan kemudian total_revenue secara menurun untuk menampilkan produk dengan pendapatan tertinggi di urutan teratas setiap bulan.
