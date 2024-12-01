---
title: Android Modlamaya Giriş
date: 2023-01-14
categories: [Android]
image:
  path: https://cdn.pixabay.com/photo/2018/05/03/21/49/android-3372580_960_720.png
  width: 900   # in pixels
  height: 300   # in pixels
  alt: Android Logo. Raphael Silva, Pixabay.
---

> Mobil uygulama geliştirme, geleceği tanımlamaktadır. Her yeni nesil telefon, artan işlem hızı ve gücü, daha iyi ekranlar ve daha uzun pil ömrü getirir. Bilgi işlem çağının şafağında, uzmanlar bilgisayarların sayıca büyüyüp küçüleceğini iddia ettiler, ancak eğilim tam tersineydi. Mobil platformlar bu trendin bir uzantısıdır. Artık MP3 çalarınız, e-postanız, tarayıcınız, GPS'iniz, YouTube'unuz, sosyal ağlarınız cebinizde. Mobil gelişme henüz çok genç ve *Way of the Cracker* yolculuğunuza başlarsanız, koruma yöntemlerinin nasıl doğduğunu, büyüdüğünü, geliştiğini ve her adımda nasıl kırıldığını gözlemleyebilirsiniz.

<p align="right" style="color: tan;">– lohan0. (2010). Way of the Android Cracker.</p>

Bu yazıda Android uygulamaların üzerinde ters mühendislik yaparak modlamanın en basit hâli gösterilecek. Sonunda APK olarak paketlenmiş bir uygulamayı çeşitli araçlarla açmayı, incelemeyi, yorumlamayı ve modifiye ederek tekrar paketlemeyi öğrenmiş olacaksınız.

## Ön Gereksinimler
- Basit düzeyde programlama bilgisi
- Komut istemcisi kullanımı
- Herhangi bir derleyici ya da editör

### Araçlar
- [Android Emülatörü](https://developer.android.com/design-for-safety/privacy-sandbox/download#:~:text=In%20Android%20Studio%2C%20go%20to,appears%2C%20and%20select%20Create%20device.)
- [Java Development Kit](https://www.oracle.com/java/technologies/downloads/)
- [JADX](https://github.com/skylot/jadx)
- [APK Easy Tool](https://forum.xda-developers.com/t/discontinued-windows-apk-easy-tool-v1-60-2022-06-23.3333960/)
- [crackme0.apk](https://www.dropbox.com/s/fm7exio5fwmlrlz/crackme0.apk?dl=0)
- [ADB Tools](https://developer.android.com/studio/releases/platform-tools) *(İsteğe Bağlı)*

> Bu yazıdaki her şey eğitimsel amaçlıdır. APK dosyalarını değiştirmek ve paylaşmak önerilmez.
{: .prompt-info}

## crackme0.apk

Emülatörü kurup açtıktan sonra indirmiş olduğunuz **crackme0.apk** dosyasını sürükleyerek ya da `adb install crackme0.apk` komutuyla yükleyin ve uygulamayı açın. Bir giriş formu ve bir buton bulunan basit bir uygulamayla karşılacaksınız. Herhangi bir değeri girin, **"Invalid serial!"** bilgilendirmesi çıkacaktır. 

![Android-Modding-Screenshot](https://dl.dropbox.com/s/2ze9495c2di38l9/Screenshot_1677066964.png){: .post-image}

Buna benzer bilgilendirmeler başlangıç noktası belirlemek için önemli, çoğu uygulama herhangi bir türde geçerliliği bu mesajın oluşturulduğu yere yakın yapar.

## Java Kodunun İncelenmesi

**crackme0.apk** dosyasını JADX ile açın. İhtiyacımız olan yer:

**Source code > com.lohan.crackme0 > Main**

Koda şöyle bir göz attığımızda 66. satırda `validateSerial` adlı bir fonksiyon görüyoruz. `generateHash` kısmında ne yapıldığı çok önemli değil. Bizi asıl ilgilendiren kısım fonksiyonun ana şekli.

```java
public int validateSerial(String serial) {
    try {
    } catch (Exception e) {
        e.printStackTrace();
    }
    if (generateHash(getMobileID()).equals(serial)) {
        return 1;
    }
    return 0;
}
```
{: .nolineno}

Girilen serial doğruysa 1, yanlışsa 0 değeri döndürülüyor. Bu durumda en basit çözüm bence son satırdaki değeri 1'e çevirmek ve girilen tüm değerleri geçerli kılmak. Unutmayın ki bir kontrolü etkilemenin birden fazla yolu olabilir. Fakat bu yazı için en kısa yolu seçelim.

## Decompile

Ne yapacağımızı belirlediğimize göre decode kısmına geçebiliriz. APK Easy Tool programını açın ve File/folder kısmına **crackme0.apk** dosyasını sürükleyin. Ayarlardan çıkarılacak klasörün hedef dizinini ayarlayabilirsiniz.

> Kolaylık açısından APK Easy Tool kullanıyorum, fakat **Log output >>** açarsanız arka planda neler yapıldığını görebilirsiniz. 
{: .prompt-info}

crackme0 adlı bir klasör oluşacak. Bu klasörden `crackme0\smali\com\lohan\crackme0` dizinine giderseniz birçok smali uzantılı dosya göreceksiniz. Smali kodunun ne olduğunu ekteki alıntı en iyi şekilde açıklamakta.

> Bir uygulama kodu oluşturduğunuzda APK dosyası, ikili Dalvik byte kodunun olduğu bir .dex dosyası içerir. Bu platformun aslında anladığı tek biçimdir. Bununla birlikte ikili kodu okumak veya değiştirmek kolay değildir, bu nedenle okunabilir hâle dönüştürmek için araçlar vardır. Byte kodun okunabilen en yaygın biçimi Smali olarak bilinir.

<p align="right"><a href="https://stackoverflow.com/a/30837786">– Antimony</a></p>

## Decode

`Main.smali` dosyasını açın. Satırları kodun içerisindeki `.line` yazan kısımlardan anlayabilirsiniz. Dosyada biraz aşağı indiğimde `.method public validateSerial(Ljava/lang/String;)I` şeklinde bir şey görüyorum, aradığımız bu olmalı. Metnin ilk yarısı anlaşılabiliyor, validateSerial adında public bir fonksiyon olduğunu söyleyebiliriz. Yalnız dikkat çeken kısım parantez içi. Dalvik Bytecode kaynaklarından bazı harflerin ne anlama geldiğini açıklayan bir tabloya ulaştım.

| code | value
| :-- | ----: |
| D | double
| J | long
| V | void (only for return type)
| I | int 
| Z | bool
| Lclassname; | instance from the class
| B | byte
| S | short
| C | char
| F | float

Sondaki `I` harfi, fonksiyonun int türünde bir değer döndürdüğünü gösteriyor. Aldığı parametre ise Java dilinin String sınıfından bir nesne. Son kısım karışık geldiyse, bir programlama dilinde `String a;` yazdığınızda String sınıfından bir nesne oluşturmakta olduğunuzu hatırlayın.

Peki, fonksiyonun içerisine baktığımızda birçok şey görüyoruz. Bunun nedeni smali kodunda her bir aşamanın en ufak parçalarına bölünerek temsil edilmesi. Fonksiyonun Java hâline tekrar bakarsak if bloğu içinde bir kontrol mekanizması, onun dışında ise `return 0;` satırı vardı. Burada `:cond_0` yazan kısım dikkat çekiyor. 

```ruby
if-eqz v1, :cond_0

.line 68
const/4 v1, 0x1

.line 73
:goto_0
return v1

.line 69
:catch_0
move-exception v1

move-object v0, v1

.line 70
.local v0, "e":Ljava/lang/Exception;
invoke-virtual {v0}, Ljava/lang/Exception;->printStackTrace()V

.line 73
.end local v0    # "e":Ljava/lang/Exception;
:cond_0
const/4 v1, 0x0
```
{: .nolineno file='Main.smali'}

İki `:cond_0` arasındaki satırlar pek anlaşılır olmayabilir, fakat tam altındaki `const/4 v1, 0x0` Java kodundaki son satırı hatırlatıyor. [Android Open Source Project - Dalvik bytecode](https://source.android.com/docs/core/runtime/dalvik-bytecode) sayfasına bakarsak, `const/4 vA, #+B` tabirinin virgülün sağdaki değerini sol registera taşımaya yaradığını görürüz. 

Özetle, serial geçerliyse **v1** registera **1 (true)**; değilse **0 (false)** değeri döndürülüyor. Peki false döndürülen yeri 1 yapsak? 

`const/4 v1, 0x0` → `const/4 v1, 0x1`

## Recompile

Yapacağımız değişiklikleri bitirdikten sonra dosyayı tekrar paketleyebiliriz. APKEasyTool üzerinden klasörü seçerek ya da üzerine sürükleyerek ekleyin. Başka bir ayarı değiştirmeden Compile seçeneğine basın. 

![Android-Modding-Screenshot](https://dl.dropbox.com/s/7prw488qfrmv9tj/sign.jpg)

**🛈 Sign Successful** yazısını gördükten sonra programı kapatabilirsiniz. Emülatörü açın ve önceden yüklenmiş crackme0 uygulaması duruyorsa silin, sonra da recompile etmiş olduğunuz uygulamayı yükleyerek deneme yapın.

![Android-Modding-Screenshot](https://dl.dropbox.com/s/p3bbvpaair9hwl3/Screenshot_1678188628.jpg){: .post-image}