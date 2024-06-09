---
title: "Windows PATH Değişkenleri Karmakarışık Olduğunda"
date: 2023-11-30 13:35 +0300
categories: [İşletim Sistemi]
---

![sudo make me a sandwich](https://xkcd.com/store/sudo_shirt_thumb.png){: .dark .shadow }
![sudo make me a sandwich](https://imgs.xkcd.com/comics/sandwich.png){: .light .shadow  w='330' }

Bir program yüklediğinizde, bu yazılımı çalıştırmanın kolay bir yolunun olması gerekir. Masaüstündeki simgeye tıklamak, başlat menüsü kısayollarını kullanmak gibi. Fakat komut araçları yüklediğinizde, bunlar genellikle tek bir exe dosyası olduğundan istediğiniz bir ana dizinde toplu bir şekilde saklarsınız. Çalıştırmak için terminal penceresinde adlarıyla çağırmanız gerekir. Bunun için ise işletim sistemine bu araçları ve nerede olduklarını tanıtmanız gerekir. Bu noktada devreye PATH girer.

Linux ve diğer Unix, macOS ve Windows biçimlerinin tümü, kullanıcının ortamıyla ilişkili bir PATH kullanır. Yürütülebilir bir programı ismine başvurarak çalıştırmayı denediğinizde, komut yorumlayıcısı PATH'deki dizinlerde arama yaparak bu adda yürütülebilir bir program arar.

Bazen programları yüklerken (ilk akla gelenler git ve Python) PATH değişkenini ekle seçeneğini görmüşsünüzdür. Çoğu zaman işe yarar ve zararsız bir yöntem, eğer ortam değişkenlerini kurcalamıyorsanız. Aksi halde değişkenler listesini okumak örnekteki gibi bir eziyete dönüşebilir.

![Path variables](https://www.architectryan.com/static/42eb044d39582f04f1f213e17e4fcb30/32056/edit_path_variable.png){: .shadow w='520' h='400'}

Okurken ne kadar sağa ve sola baktığınızı fark ettiniz mi? Herhangi bir hiyerarşi ve düzen yok, kullanıcı dosyaları mı önce geliyor, sistem dosyaları mı? Aylar önce silinmiş bir programa ait bir değer bulunuyor olabilir. Bir de alttaki örneğe bakalım.

![Path variables](https://i.imgur.com/VScWPbp.png){: .shadow w='520' h='400'}

İlk bakışta src dizininin geliştirme ortamı için gereksinimler olduğunu anlıyoruz. Sonra JAVA ve Android dizinleri, sonra da kullanıcı bazındaki klasörler ve komut satırı araçlarının toplandığı USERTOOLS. Okuması çok daha kolay değil mi? Hayır mı? Peki... Evet diyenlerle devam edebiliriz.

## Derleyip Toplamak

Etkisi büyük ama uygulaması oldukça basit. Öncelikle kullanmak istediğiniz ana dizinleri belirleyin. Java yüklemişseniz önceden `JAVA_HOME` adında bir değişken görebilirsiniz. Bizim yapacağımız şey de tam olarak bu. **\<ad\>** için kullanıcı değişkenleri kısmında yeni butonuna basıp ismi ve dizini belirtin. PATH düzenleme kısmında bu isme, sağına ve soluna yüzde sembolü ekleyerek ulaşacağız.

> Bu sayede tüm değerler tek bir ana değere bağlı, hepsinin yerini değiştirmek gerekirse sadece ana değeri değiştirmemiz yeterli olacak.
{: .prompt-info}

Yukarıdaki görüntüde `USERTOOLS` olan değer `C:/tools` dizinine işaret ediyor. Güzel tarafı Windows aramaya yazarak da direkt bu dizini açabiliyorsunuz.

![Windows Search](https://i.imgur.com/cS7fbKq.png){: .shadow w='580'}

`%APPDATA%`, `%WINDIR%` ve `%TEMP%` gibi örnekler ise Windows'ta hâli hazırda bulunan ortam değişkenleri. İşletim sisteminin tasarım kararlarını inceleyip ihtiyaca göre uygulamanın ufak bir örneğini görmüş olduk.