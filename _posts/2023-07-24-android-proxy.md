---
title: "Proxy ile Android Uygulamaların Trafiğini İnceleme"
date: 2023-07-24 15:00 +0300
categories: [Android]
---

Birçok Android uygulaması Google servisleriyle çalışır. Bazı uygulamalar bu servisleri sadece gerekli işlemler için kullanırken; bazı uygulamalar konumunuzu, operatörünüzü, hatta telefonunuzun belirli bir andaki ses seviyesini içeren verileri öğrenmek için kullanabilir. Ya da uygulamanın kendi analitik servisleri mevcut olabilir. Bu durumda uygulamanın ne tür verileri gönderdiğini öğrenmek için uygulamanın trafiğini incelemek gerekir.

## Araçlar

• [Burp Suite Community Edition](https://portswigger.net/burp/communitydownload)

• Bir adet Android cihaz veya emülatör

## BurpSuite Ayarları

BurpSuite açın ve temporary project seçeneğiyle ilerleyin. Sonra Proxy sekmesine gelerek Proxy ayarlarını açın.

![BurpSuite Proxy](https://www.dropbox.com/scl/fi/jk7ilqkonvwad3ptd2xf2/BurpSuiteCommunity_m4TKXBaJim.png?rlkey=hv9mobes2geddpqvmnqtbsqo2&dl=1)

Yeni *proxy listener* ekle seçeneğine tıklayın. Burada port numarasını **8082** olarak ayarlayın. Bu noktada tüm arayüzlerden dinleme olarak ayarlayın, normal şartlarda bu ayar güvenli değil fakat şimdilik görmezden gelelim.

![BurpSuite Proxy Listener](https://www.dropbox.com/scl/fi/z29zp1btaoec4aa8tq53e/BurpSuiteCommunity_UOS0lA2OMY.png?rlkey=2mdlzdyo6fsewly7jnizqbdr2&dl=1)

Ayarları kaydettikten sonra şöyle gözükmeli.

![BurpSuite Proxy Listener](https://www.dropbox.com/scl/fi/788u04rejuf680nuyb61i/BurpSuiteCommunity_rxUn2VIH6u.png?rlkey=w7aiu6ml2zuatda0ix8rufg5m&dl=1)

Burada Import/export CA Certificate seçeneğine tıklayın. Çıkan pencerede sertifikayı DER formatında kaydedin. Bu sertifikayı Android cihaza yükleyeceğiz.

![BurpSuite Proxy Listener](https://www.dropbox.com/scl/fi/g0wrfn1n3wspaz2glenav/BurpSuiteCommunity_a4bHl1STpk.png?rlkey=w8dy97mtqktlztkmodjpx17t9&dl=1)

## Android Cihazı Ayarları

Kaydedilen .cer dosyasını Android cihaza atın. Dosyaya tıklayın ve istediğiniz bir isim vererek sertifikayı yükleyin. Android sistemi bilinmeyen bir kaynaktan sertifika yüklendi bildirimi verebilir. Yüklerken de uyarı veriyorsa kabul ederek devam edin. 

![Android System Notifications](https://www.dropbox.com/scl/fi/p5k8wxkfl2eppr1zjoru3/Screenshot_2023-07-24-14-52-57-890_com.android.settings.png?rlkey=g2q5qhssnwum0hmetet7zi3hh&dl=1){: .w-75}

>Emülatöre sertifika yüklerken uzantıyı .crt olarak değişmeniz gerekebilir.
{: .prompt-info}

Cihazın Wi-Fi ayarlarına gelerek ağa bağlı olduğunuz Wi-Fi basılı tutun. Açılan menüden Ağ ayarlarını değiştir seçeneğine tıklayın. Proxy ayarlarını manuel olarak ayarlayarak Hostname kısmına bilgisayarınızın yerel IP adresini yazın. Yerel IP öğrenmek için Windows'ta `ipconfig`, Linux'te `ifconfig` komutunu kullanabilirsiniz. Port kısmına ise BurpSuite'de ayarladığımız port numarasını yazın. Burada 8082 yazıyor olmalı.

![Android Proxy Settings](https://www.dropbox.com/scl/fi/rbwhladpssiy1izt47e15/IMG_20230724_144814.png?rlkey=jmqixd49fmppof1atnrzk26ef&dl=1){: .w-75}

## BurpSuite İncelemesi

BurpSuite'de Proxy sekmesine gelerek HTTP geçmişini açın. Cihaz herhangi bir adrese istek yapıyorsa burada görünmeli. Örneğin Brave tarayıcısı açıldığı anda `https://go-updater.brave.com/` adresine bir istek yapıyor. Bu kapatmanın mümkün olmadığı Brave güncelleme özelliği. İsteği ve cevabı incelersek ilgili istemcinin son sürümü kullanmakta olduğunu görebiliriz.

![BurpSuite Proxy History](https://www.dropbox.com/scl/fi/5ozgc19d4x4x35xuxg1kp/BurpSuiteCommunity_rXzvsOP6zu.png?rlkey=i487mrx0j4385j6bvwx0nzdwj&dl=1)

Kısacası olay bu. Örneğin açık kaynak olan Kiwi Browser'ın arama çubuğundan yapılan Yahoo ve Bing aramalarının verilerini gönderdiği [ortaya çıkmıştı](https://web.archive.org/web/20210605191305/https://github.com/kiwibrowser/src/issues/352), tabii bu en hafif örneklerden biri ve geliştiricinin savunması da var. Fakat bazı şirketler gerçekten sınır bilmiyor, fazlasıyla özel ve kişisel veriler topluyorlar ("*-Sözleşmeyi kabul ettiniz. -Evet?*"). Bu gibi durumlarda bunları tespit etmek ve mümkün olduğunca önlemek ise bize kalıyor.

> Uygulama bağlantı hatası veriyorsa muhtemelen sertifika pinlemesi vb. koruyucu mekanizmalara sahiptir. [apk-mitm](https://github.com/shroudedcode/apk-mitm), [android-unpinner](https://github.com/mitmproxy/android-unpinner) veya ReVanced `Override certificate pinning` patchini kullanarak erişim sağlayabilirsiniz.
{: .prompt-info}