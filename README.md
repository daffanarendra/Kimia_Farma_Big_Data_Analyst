# Kimia Farma Performance Analytics 2020-2023

Tools :
 - BigQuery
 - Google Looker Studio

## Project Background
Kimia Farma is the first pharmaceutical industry company in Indonesia, established by the Dutch East Indies government in 1817. As a Big Data Analytics Intern at Kimia Farma, your duties will encompass a series of challenges that require a deep understanding of data and analytical capabilities. One of your main projects will be evaluating Kimia Farma's business performance from 2020 to 2023. The following are the tasks
you have to do:

## Importing Datasets to BigQuery
1. Obtain the provided datasets:
   - kf_final_transaction.csv
   - kf_inventory.csv
   - kf_kantor_cabang.csv
   - kf_product.csv
2. Establish a fresh project on BigQuery adhering to the specified naming conventions.
3. Import the datasets into BigQuery, assign a new table name for each dataset being uploaded, and enable auto-detection for the schema to facilitate automatic creation.

## Create Data Mart
create an analysis table based on the aggregation results of the four previously imported tables. The following are the mandatory columns in the table:
- transaction_id : transaction ID code,
- date: date the transaction was made,
- branch_id : Kimia Farma branch ID code,
- branch_name : Kimia Farma branch name,
- kota: Kimia Farma branch city,
- provinsi: Kimia Farma branch province,
- rating_cabang: consumer assessment of the Chemistry branch Pharma
- customer_name : Name of the customer who performed the action transaction,
- product_id : drug product code,
- product_name : drug name,
- actual_price : drug price,
- discount_percentage : Percentage of the discount given on medicine,
- gross_profit_percentage : What the profit percentage should be accepted from the drug under the following conditions:

      -Price <= IDR 50,000 -> 10% profit
      -Price > IDR 50,000 - 100,000 -> 15% profit
      -Price > IDR 100,000 - 300,000 -> 20% profit
      -Price > IDR 300,000 - 500,000 -> 25% profit
      -Price > IDR 500,000 -> 30% profit,
- nett_sales : price after discount,
- nett_profit: profit obtained by Kimia Farma,
- rating_transaksi: consumer assessment of transactions which is conducted.

<details><summary>SQL Query</summary>

```sql
-- Create Datamart Design --
create table kimia_farma.Analyst_Table as
SELECT
  ft.transaction_id,
  ft.date,
  ft.branch_id,
  kc.branch_name,
  kc.kota,
  kc.provinsi,
  kc.rating as rating_cabang,
  ft.customer_name,
  p.product_id,
  p.product_name,
  ft.price as actual_price,
  ft.discount_percentage,
  case
    when ft.price <=50000 then 0.1
    when ft.price >50000-100000 then 0.15
    when ft.price >100000-300000 then 0.2
    when ft.price >300000-500000 then 0.25
    when ft.price >50000 then 0.3
    else 0.3
  end as persentase_gross_laba,
  ft.price*(1-ft.discount_percentage) as nett_sales,
  (ft.price*(1-ft.discount_percentage)*
    case
    when ft.price <=50000 then 0.1
    when ft.price >50000-100000 then 0.15
    when ft.price >100000-300000 then 0.2
    when ft.price >300000-500000 then 0.25
    when ft.price >50000 then 0.3
    else 0.3
  end) as nett_profit,
  ft.rating as rating_transaksi
from
  `kimia_farma.kf_final_transaction` as ft
left join
  `kimia_farma.kf_kantor_cabang` AS KC ON  ft.branch_id=kc.branch_id
left join
  `kimia_farma.kf_product` AS p ON ft.product_id = p.product_id
;
```
<p align="center">
  <img src="/folder/data_mart.JPEG">
  <br>Fig.1. Data Mart Design</br>





