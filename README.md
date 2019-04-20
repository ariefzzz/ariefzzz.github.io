# ariefzzz.github.io
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

domain digunakan untuk membatasi lingkup situs yang dijelajahi oleh crawler ,

buka file **tugasAkhir.py** yang berada di folder spiders lalu atur `allowed_domains = ['pta.trunojoyo.ac.id']` untuk membatasi link yang dijelajahi crawler tetap berada di domain `pta.trunojoyo.ac.id`

## 1.4 mengatur startPage

startpage berguna memberi tahu  crawler link awal tempat dia mulai menjelajah seperti berikut `start_urls = ['https://pta.trunojoyo.ac.id/welcome/index/4']` isi di dalam list bisa lebih dari 1 link , saya mulai melakukan crawl mulai halaman 4 karena halaman 1-3 strukturnya berbeda dengan halaman 4-seterusnya sehingga agar mudah saya crawl mulai halaman 4 

## 1.5 mengatur rules

tambahkan rule berikut di baris setelah startUrl

```python
rules = (
 Rule(LinkExtractor(allow=(), restrict_xpaths=('//*[@id="end"]/ol/li[8]/a',)),callback="parse_item",follow=True),)
```

rule ini bisa diartikan untuk link di alamat xpath `'//*[@id="end"]/ol/li[8]/a'` di parse ke fungsi parse_item , dan `follow=True` berarti di dalam halaman link tadi jika ada alamat xpath `'//*[@id="end"]/ol/li[8]/a'` maka jelajahi juga sampai alamat xpath tersebut tidak exist

xpath bisa dicopy melalui inspect element

untuk mengenal lebih dalam xpath anda bisa membaca beberapa artikel berikut:

- https://medium.com/@achmadsyah/tentang-aku-kamu-xpath-f15f67b98733
- https://id.wikipedia.org/wiki/XPath

## 1.6 mengatur data yang akan diambil

setelah rule mengatur tombol mana yang mengarah ke pagging berikutnya maka link content tiap pagging harus di extract  , disini saya mengextraknya berdasarkan css `'a.gray.button::attr(href)'`  digambar berikut bisa dilihat css yang menuju halaman detail

![1555660038677](C:\Users\zainal\AppData\Roaming\Typora\typora-user-images\1555660038677.png)

css tadi di extract di method parse_item kemudian tiap link yang didapat di parse ke method `parse_detail_page`

```python
def parse_item(self, response):
        print('Processing..' + response.url)

        item_links = response.css('a.gray.button::attr(href)').extract()
        # print(item_links)
        for a in item_links:
            print("membaca artikel .... "+a)
            yield scrapy.Request(a, callback=self.parse_detail_page)
```

setelah link menuju halaman detail di parse maka berikutnya adalah mengextract data yang ingin diambil melalui method `parse_detail_page`  , 

cara mengextract judul penulis sebenarnya sama saja menggunakan css maupun xpath , tapi direkomendaskan menggunakan css jika struktur web tersebut mendukung css yang tertata , sedangkan imbuhan `.extract()[0]` digunakan untuk ambil data didalamnya karena return dari `response.css()` maupun `response.xpath()` adalah list, 

jika ingin berexperiment dengan xpath ataupun css agar data yang diambil sesuai keinginan maka gunakan perintah `scrapy shell https://pta.trunojoyo.ac.id/welcome/index/4` di cmd dan uji xpath atau  css kalian dengan perintah `response.css()` ataupun `response.xpath()` jika data yang ditampilkan sesuai maka gunakan xpath ataupun css tersebut

di method ini selain mengekstrack data juga pembersihan data ,beberapa data memilikikata yang tidak ingin saya ambil seperti **Penulis : nama** sedangkan yang ingin saya ambil hanya **nama** saja maka saya melakukan replace di variable penulis

kemudian item adalah data yang akan kita simpan ke csv atributnya apa saja tinggal kita sesuaikan

```python
 def parse_detail_page(self, response):
        judul = response.css('a.title::text').extract()[0].strip()
        penulis = response.xpath('//*[@id="content_journal"]/ul/li/div[2]/div[1]/span/text()').extract()[0]
        abstrak = response.xpath('//*[@id="content_journal"]/ul/li/div[4]/div[2]/p/text()').extract()[0]
        url = response.url

        penulis = penulis.replace('Penulis : ', ''

        item = ItemTugasAkhir()
        item['judul'] = judul
        item['penulis'] = penulis
        item['abstrak'] = abstrak
        item['url'] = url
        yield item
```

jangan lupa juga meng edit **items.py** agar field yang di parse oleh `parse_detail_page` sesuai dengan parameter di  `ItemTugasAkhir()` 

```python
import scrapy


class PtatrunojoyoItem(scrapy.Item):
    # define the fields for your item here like:
    # name = scrapy.Field()
    pass

class ItemTugasAkhir(scrapy.Item):
    # define the fields for your item here like:
    judul = scrapy.Field()
    penulis = scrapy.Field()
    abstrak = scrapy.Field()
    url = scrapy.Field()
```

## 1.7 crawl data

untuk crawling data bisa menggunakan perintah `scrapy crawl --nolog TugasAkhir -o data.csv -t csv`  data hasil crawling akan disimpan di data.csv

proses crawling seperti berikut tunggu hingga selesai

![1555664423165](C:\Users\zainal\AppData\Roaming\Typora\typora-user-images\1555664423165.png)

setelah selesai data akan menjadi seperti berikut :

![1555674935243](C:\Users\zainal\AppData\Roaming\Typora\typora-user-images\1555674935243.png)

## 2. migrasi csv ke sqlite

saya menggunakan tools [db browser for sqlite](https://sqlitebrowser.org/) disini sangat mudah  klik new database ketika muncul  pop up untuk mengisi field apa saja yang ada di db close saja 

![1555675137009](C:\Users\zainal\AppData\Roaming\Typora\typora-user-images\1555675137009.png)

setelah itu di menu file pilih import table form csv lali pilih file csv tadi dan lakukan proses migrasi

![1555675466884](C:\Users\zainal\AppData\Roaming\Typora\typora-user-images\1555675466884.png)

tunggu hingga proses selesai 

 ![1555675509234](C:\Users\zainal\AppData\Roaming\Typora\typora-user-images\1555675509234.png)

## 3. clushtering data

dalam step ini diasumsikan data telah menjadi sqlite dengan struktur seperti ini

![1555681519832](C:\Users\zainal\AppData\Roaming\Typora\typora-user-images\1555681519832.png)

## 3.1 koneksi database

saya menggunakan library sqlite3 untuk melakukan koneksi dengan db , code untuk melakukan koneksi sebagai berikut

```python
import sqlite3 
def koneksi(db_file):
	try:
		conn = sqlite3.connect(db_file)
		return conn
	except Error as e:
		print(e)
	return None

database = "data DUMMY.sqlite"
#pastikan nama file database benar dan berada 1 directory dengan file python
conn = koneksi(database)
```



## 3.2 menjadikan data  terstruktur

field yang akan diolah adalah field  deskripsi , untuk merubahnya menjadi data terstruktur ada beberapa step yang harus dilakukan yaitu :

- lowercase 
- stopword
- stemming
- tokenisasi

seperti contoh program berikut :

```python
def keDataTerstruktur(text):
	text = lowerCase(text)
	text = stopword(text)
	text = stemming(text)
	text = tokenisasi(text)
	return text	
```

adapun isi dari masing-masing fungsi dan penjelasnnya  sebagai berikut :

```python
def lowerCase(text):
	text = text.lower()
    #mengubah keseluruhan text menjadi lowercase
	return text
def stopword(text):
	# Ambil Stopword bawaan
	stop_factory = StopWordRemoverFactory().get_stop_words()
	print(stop_factory)
	more_stopword = ['diatur', 'perjodohan']
	
	# Merge / menggabungkan stopword bawaan dan tambahan
	data = stop_factory + more_stopword
	
	dictionary = ArrayDictionary(data)
	str = StopWordRemover(dictionary)
	
	hasil = str.remove(text)
	# print(hasil)
	
	return hasil
def stemming(text):
	# create stemmer
	factory = StemmerFactory()
	stemmer = factory.create_stemmer()
	#melakukan stemming , atau mengubah kata menjadi kata dasar
	hasil = stemmer.stem(text)
	return hasil
def tokenisasi(text):
    
    #memecah kata berdasarkan spasi
	text = text.split()
	return text

```

setelah membuat code yang mengubah suatu text menjadi data terstruktur  sekarang adalah cara mengambi data dari database dan memasukkannya ke dalam fungsi `def keDataTerstruktur(text)` tadi , dengan menggunakan koneksi `conn` yang telah ada kita ambil datanya dengan menggunakan code

```python
def prosesDataTerstruktur(conn):
	cur = conn.cursor()
    #execute query select
	cur.execute("SELECT * FROM data")
	rows = cur.fetchall()
	print("prosesDataTerstruktur", end="", flush=True)
	for row in rows:
        #mengakses variable daftaKata
		global daftarKata
        #row[0] atau deskripsi di parse ke fungsi keDataTerstruktur()
		text = keDataTerstruktur(row[0])
        #mengumpulkan kata dari semua dokumen digunakan nanti di vsm
		daftarKata = daftarKata.union(set(text))
		
		text = ' '.join(text)
		#proses menyimpan row[3] adalah nim yang dijadikan primary key
        simpanDataTerstruktur(conn , text ,row[3])
        
		print(". ", end="", flush=True)
	print("done")
```

data yang telah menjadi terstruktur akan disimpann di database mennggunakan kode berikut:

```python
#memastikan field dataTerstruktur ada dalam database
def cekKolomDataTerstruktur(conn):
	cur = conn.cursor()
	cur.execute("PRAGMA table_info(data);")
	rows = cur.fetchall()
	for row in rows:
		# print(row[1])
		if row[1] =='dataTerstruktur':
			return True
			end
	cur.execute("ALTER TABLE data ADD COLUMN dataTerstruktur TEXT;")
	return False
def simpanDataTerstruktur(conn , text , url):
	task = (str(text) , str(url))
	sql = "UPDATE data SET dataTerstruktur = ? WHERE url = ?"
	cur = conn.cursor()
	cur.execute(sql,task)
	return cur.lastrowid
```

maka file database setelah dilakukan proses ini akan menjadi 

![1555745362681](C:\Users\zainal\AppData\Roaming\Typora\typora-user-images\1555745362681.png)

## 3.3 vsm

setelah menjalankan method `def prosesDataTerstruktur(conn):` maka variable globa `daftarKata` akan berisi seluruh kata (yang telah menjadi data terstruktur) dari seluruh dokumen yang ada di database maka proses vsm adalah menghitung berapa banyak masing masing kata terkandung di dalam dokumen , metode untuk menghitung berapa jumlah kata di sebuah text sebagai berikut

```python
def keVsm(text):
	global daftarKata
	text = text.split()
	tmp = []
	
    #melakukan perulangan sepanjang 'daftarKata' untukmenghitung masing masing kata ada berapa pada 'text'
	for kata in daftarKata:
		jumlahKata = text.count(kata)
		tmp = tmp+[jumlahKata]

	print('panjang vsm = ', len(tmp))
	return tmp
```

berikutnya hanya perlu mengambil data terstruktur di database dan parse ke method  `def keVsm(text):`

```python
def prosesVsm (conn):
	cur = conn.cursor()
	cur.execute("SELECT * FROM data")
	rows = cur.fetchall()
	print("prosesVsm", end="", flush=True)
	for row in rows:
        #row[4] yaitu data terstruktur di parse ke method keVsm()
		kata = keVsm(row[4])
		#hasil return adalah list maka harus menggunakan fungsi join agar menjadi string , sebernarnya bisa menggunakan variable lain selain kata , tapi karena variable kata tidak digunakan lagi maka masukkan saja ke variable itu 
        kata = ' '.join(str(x) for x in kata)
		#fungsi penyimpanan vsm
        simpanDataVsm(conn , kata ,row[3])
		print(". ", end="", flush=True)
	print("done")
```

untuk fungsi penyimpanan dan cek field kurang lebih sama dengan penyimpanan dan cek field data terstruktur hanya perlu penyesuaian sedikit

## 3.4 kmean

step dasar kmean sebagai berikut

1. tentukan jumlah clushter
2. random pusat clushter sebanyak jumlah clushter
3. kemudian cari jarak masing-masing pusat clushter ke setiap anggota  lain
4. jarak terdekat adalah  anggota clushter tersebut
5. hitung rata-rata seluruh anggota masing-masing clushter , rata-rata tsb adalah pusat clushter baru
6. ulangi langkah 3,4,5 sampai tidak ada anggota yang berpindah clushter atau pusat clushter == pusat clushter baru

jika ditulis dalam code versi saya akan seperti ini

```python
def kmean(conn,k,url,vsm):
	#1. k adalah jumlah clushter
    global centerCluster , clushter , centerDataCluster
	newClushter=[]
	iterasi = 0
    #2.random pusat clushter
	centerCluster = list(random(k,len(vsm)))
	print("centerCluster = ",centerCluster)
	for x in range(0,k):  
		centerDataCluster.append(getCenterData(conn,centerCluster[x]))
	while(True): 
		iterasi+=1
		
		for dataKe in vsm:
			#3. tmp jarak menampung jarak data ke masing masing center
			tmpJarak=[]
			jcenter=0
			for centerKe in centerDataCluster:
				tmpJarak.append(manhattanDistance(dataKe,centerKe))
			#4. kemudian diambil jarak terendah
			cls = tmpJarak.index(min(tmpJarak))
			newClushter.append(cls+1)
        # pengecekan apakah ada perubahan anggota clushter
		if(newClushter == clushter):
			break
		else:
			clushter = newClushter
			newClushter = []

			print(clushter)
			NewCenter = []
			for x in range(1,k+1):			
				tmp = [0] * len(vsm[0])
				jumlah = 0
				for y in clushter:
					if(y==x):

						tmp = aPlusB(tmp,vsm[y]) 
						jumlah+=1
				#5. hitung rata-rata pusat clushter baru
                NewCenter.append(mean(tmp,jumlah))
			centerDataCluster = NewCenter
	return clushter
```

 hasil kmean akan seperti berikut

![1555754273918](C:\Users\zainal\AppData\Roaming\Typora\typora-user-images\1555754273918.png)

sekian penjelasan saya kurang lebihnya mohon maaf   , terimakasih

full code ada di repo berikut  :
