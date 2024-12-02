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

**Dataset :**
[https://console.cloud.google.com/marketplace/product/bigquery-public-data/thelook-eco
mmerce?project=cool-benefit-286000](https://console.cloud.google.com/marketplace/product/bigquery-public-data/thelook-eco)

# Penjelasan
1. **WITH report_monthly_orders_product_agg AS (...)**:

* Menggunakan Common Table Expression (CTE) untuk mendefinisikan tabel sementara bernama report_monthly_orders_product_agg.

2. **SELECT FORMAT_DATE('%Y-%m', DATE(oi.created_at)) AS year_month**:

* Mengonversi tanggal created_at dari tabel order_items menjadi format YYYY-MM untuk mengelompokkan data penjualan per bulan.

3. COUNT(*) AS total_items_sold:

* Menghitung jumlah total item yang terjual untuk setiap produk per bulan.
  
4. **SUM(p.retail_price) AS total_revenue**:

* Menjumlahkan harga jual (retail_price) dari setiap produk yang terjual untuk mendapatkan total pendapatan per produk per bulan.
  
5. FROM bigquery-public-data.thelook_ecommerce.order_items AS oi:

* Mengambil data dari tabel order_items dengan alias oi.
  
6. **JOIN bigquery-public-data.thelook_ecommerce.products AS p ON oi.product_id = p.id**:

* Menggabungkan tabel order_items dan products berdasarkan product_id.
  
7. **WHERE oi.status = 'Complete'**:

* Memfilter hanya pesanan dengan status 'Complete' yang di ambil. Selain itu, tidak di ambil.
  
8. **GROUP BY year_month, product_id, product_name, category, brand**:

* Mengelompokkan data berdasarkan bulan-tahun dan detail produk untuk melakukan agregasi pada total_items_sold dan total_revenue.
  
9. **ORDER BY year_month, total_revenue DESC**:

* Mengurutkan hasil akhir berdasarkan year_month dan kemudian total_revenue paling besar.
