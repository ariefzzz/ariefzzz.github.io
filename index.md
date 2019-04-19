# **Tutorial crawler menggunakan scrapy dan clushtering dokumen menggunakan k-mean**

## **1. scrapy**

> scrapy menurut [wikipedia](https://en.wikipedia.org/wiki/Scrapy) Scrapy (/ˈskreɪpi/ skray-pee) adalah framework gratis dan opensource python yang di desain untuk *web scrapping* dan juga bisa digunakan untuk mengekstrak data menggunakan API maupun seperti web crawler lain pada umumnya , saat ini scrapy dikelola oleh Scrapinghub Ltd.

## 1.1 pemasangan scrapy

pastikan sudah memasang [python](https://www.python.org/) versi terbaru dan melakukan centang pada pip 

![gambar 1](D:\kampus\semester 8\penambangan web\tutorialCrawler\gambar\gambar 1.PNG)

agar bisa menggunakan perintah pip di cmd

setelah memasang python , maka install scrapy mengggunakan perintah `pip install scrapy`

jika gagal menginstall scrapy silahkan install terlebih dahulu yang diminta scrapy bisa saja .net framework ataupun visual studio 14 tergantung keadaan komputer masing masing 

## 1.2 membuat project scrapy

membuat project scrapy pertama buka folder tempat kita akan membuat project , disana tekan shift+klikKanan kemudian pilih  **open command window here** maka  akan muncul cmd dan ketik disana `scrapy startproject olx` seperti berikut

![1555651284646](C:\Users\zainal\AppData\Roaming\Typora\typora-user-images\1555651284646.png)

maka akan tergenerate project scrapy seperti berikut ![1555651471600](C:\Users\zainal\AppData\Roaming\Typora\typora-user-images\1555651471600.png)

setelah itu gunakan perintah `cd tugasAkhir` untuk berpindah directory ke tugasAkhir dan jalankan perintah berikut untuk membuat crawlernya `scrapy genspider tugasAkhir pta.trunojoyo.ac.id`

## 1.3 mengatur domain

domain digunakan untuk

## 1.4 mengatur startPage

## 1.5 mengatur data yang akan diambil

## 1.6 crawl data

## 2. migrasi csv ke sqlite

## 3. clushtering data

## Project layout

```java
mkdocs.yml    # The configuration file.
docs/
    index.md  # The documentation homepage.
    ...       # Other markdown pages, images and other files.
```
