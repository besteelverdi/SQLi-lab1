# Lab: SQL injection UNION attack, determining the number of columns returned by the query

Bu yazıda, PortSwingger tarafından oluşturulan SQLi lablarının ilkini inceleyeceğiz.

Lab yönergesi:


This lab contains an SQL injection vulnerability in the product category filter. The results from the query are returned in the application's response, so you can use a UNION attack to retrieve data from other tables. The first step of such an attack is to determine the number of columns that are being returned by the query. You will then use this technique in subsequent labs to construct the full attack.

To solve the lab, determine the number of columns returned by the query by performing an SQL injection UNION attack that returns an additional row containing null values.

![image](https://user-images.githubusercontent.com/70814577/112756177-b237fe80-8fec-11eb-8f36-5328f36ac07c.png)


Sızmadan yapmadan önce, sızacağınız sunucunun hataya açık konumları, genellikle kullanıcıya izin verdiği sürece kullanıcı adı, şifre alanları, arama, … gibi bir şeyler girmesine (giriş) izin veren yerleri belirlenmelidir. Bu laboratuvar, ürün kategorisi filtresinde bir SQL injection güvenlik açığı içermektedir.(Ürün kategorisi kusuru)

To solve the lab, determine the number of columns returned by the query by performing an SQL injection UNION attack that returns an additional row containing null values. (Bunu çözmek için kategori filtresi sorgusu tarafından döndürülen sütun sayısını bulmamız gerekmektedir.) 

Sütün sayısını belirlerken UNION kullanıyoruz. UNION operatörü, 2 veya daha fazla SELECT deyiminden 2 sonuç kümesini birleştirmek için kullanılır dolayısıyla SELECT ifadelerindeki tekrarlanan satırları silecektir. UNION'da her SELECT deyimi, sonuç kümesinde karşılık gelen veri türüyle aynı sayıda sütuna sahip olmalıdır.Yani, yalnızca UNION SELECT veri türünde doğru sayıda sütuna sahip olduğunda, sorgu TRUE olarak sonuçlandırılır, aksi takdirde FALSE döner ve sorgu başarısız olur.

Hataya açık konum ve problem belirlendikten sonra BurpSuite uygulaması açılarak, kategori seçerek adresdeki değişim gözlemlenir.
![image](https://user-images.githubusercontent.com/70814577/112756183-b9f7a300-8fec-11eb-86cf-7c41c6453dc7.png)
![image](https://user-images.githubusercontent.com/70814577/112756187-bcf29380-8fec-11eb-95a0-4754ee5f6c3b.png)

Ekran görüntülerinde de görüldüğü gibi sitenin ana adresinin sabit olduğu, tıkladığınız kategoriye göre filtreleme yapılmaktadır. https://ac1a1f8c1e2c42f3802c29d8009f00ca.web-security-academy.net/filter?category=Food+%26+Drink

Burada, ürünü kategoriye göre filtrelemek için, kullanıcı tarafının sunucuya bir GET isteği /filter?category=/Food+%26+Drink filtresi gönderdiğini görüyoruz, bu nedenle giriş Food & Drink’dir.

GET/filter?category=Food+%26+Drink isteğinin yanına '+UNION+SELECT+NULL-- satırını ekleyerek filtre kategorisi sorgusunun sonucunu SELECT boş sorgusunun sonucuyla (1 sütun) birleştiriyoruz. Sonuç olarak hata alıyoruz. Yani bu da bize kategori sorgusu tarafından döndürülen sütun sayısının birden fazla küme olduğunu göstermektedir. Sorgumuz sonuç döndürene kadar sırasıyla daha fazla sayıda sütun, 2 sütun, 3 sütun … denemeye devam ediyoruz.

![image](https://user-images.githubusercontent.com/70814577/112756203-d267bd80-8fec-11eb-83f5-023fa21b334b.png)
![image](https://user-images.githubusercontent.com/70814577/112756209-d693db00-8fec-11eb-84c2-7f4b8d42cfcf.png)
![image](https://user-images.githubusercontent.com/70814577/112756213-d98ecb80-8fec-11eb-804e-bfc4220bb804.png)
![image](https://user-images.githubusercontent.com/70814577/112756215-dbf12580-8fec-11eb-8500-f17257c87337.png)

Sorgunun '+UNION+SELECT+NULL,NULL,NULL-- yani 3 sütünda bittiği görülmektedir.

