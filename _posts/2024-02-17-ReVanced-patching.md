---
title: ReVanced Bytecode ve Resource Patch Yazımı
date: 2024-02-17 11:33 +0300
categories: [Android]
---

Uygulamaların üzerinde değişiklik yapabiliyoruz ama bunlar sadece değiştirilen Android paketi halinde kapalı bir şekilde var oluyor. Yapılan değişiklikleri göstersek dahi, paylaşılan paket dosyasının sadece o değişikliklere sahip olduğu kesin değil. Bir ReVanced yaması, bir değişikliğin nasıl ve nereyi etkilediğini açık bir şekilde gösterir ve herkes yamaları uygulayabilir. Öncelikle, geliştirme ortamını hazırlayalım.


## Araçlar
- [Git](https://git-scm.com/)
- [Azul JDK 17](https://www.azul.com/downloads/?version=java-17-lts&package=jdk#zulu)
- [IntelliJ IDEA](https://www.jetbrains.com/idea/)


## Geliştirme Ortamı

Bir dizin oluşturup `revanced-cli` ve `revanced-patches` depolarını klonlayın. 

```shell
mkdir revanced && cd revanced

repositories=(
    "revanced-cli"
    "revanced-patches"
    "revanced-integrations" (isteğe bağlı)
    "revanced-patcher" (isteğe bağlı)
)

for repository in "${repositories[@]}" ; do
    git clone -b dev --single-branch --depth 1 https://github.com/revanced/$repository
done
```
Patches projesinin dizininde `./gradlew build` komutunu çalıştırın.


>Authentication hatası alıyorsanız [bağlantıdan](https://github.com/settings/tokens/new?scopes=read:packages&description=ReVanced) `read:packages` iznine sahip bir token oluşturun. Verilen tokeni kullanıcı dosyasında `.gradle` dizininde `gradle.properties` içerisine (yoksa oluşturarak) ekleyin.
{: .prompt-info}

```
gpr.user = <github kullanıcı adı>
gpr.key = <token>
```
{: file='~/.gradle/gradle.properties'}


>**API check failed for project revanced-patches** hatası alırsanız `./gradlew apidump` komutunu çalıştırıp tekrar build edin.
{: .prompt-info}

**<span style="color:green">BUILD</span> SUCCESSFUL** yazısını gördüyseniz kurulum tamamlandı.

### IntelliJ'de Kurulum

IDEA'da CLI projesi açılıp Gradle senkronizasyon işlemi tamamlandıktan sonra `MainCommand.kt` dosyasında üçgen sembolü gözüküyor olmalı.

![idea64](https://dl.dropbox.com/scl/fi/faw65okbf5a4ivrco7gmy/idea64_KG6vERfx3h.png?rlkey=wvskv2wmxeneui3sx8e60fiyr&dl=1){: .dark .w-75 .shadow .rounded-10 }
![idea64](https://dl.dropbox.com/scl/fi/j5a25i43171m68xoipwo9/idea64_RGBUJTG1bi.jpg?rlkey=91pzzm1r4de5b2kay61yyzos0&dl=1){: .light .w-75 .shadow .rounded-10 }

Ardından **Proje Yapısı > Modüller** kısmından patches projesini modül olarak ekleyin. 

![idea64](https://dl.dropbox.com/scl/fi/ibcgqeu21isdb5k3eq6u4/idea64_JMKvEW67tV.png?rlkey=mz8dur8mt77g7x43s07uadxb9&dl=1){: .dark .w-75 .shadow .rounded-10 }
![idea64](https://dl.dropbox.com/scl/fi/gh2nivevbh2cmm0093x0r/idea64_8uwxGiYaYZ.jpg?rlkey=sp5u6sws4skp8bdfha5o7m8cz&dl=1){: .light .w-75 .shadow .rounded-10 }

### Konfigürasyon

Pencereyi kapattıktan sonra Çalıştırma/Ayıklama Konfigürasyonları ayarlarından yeni bir Kotlin konfigürasyonu oluşturun. Ana sınıfı `app.revanced.cli.command.MainCommandKt` olarak ayarlayın.

![idea64](https://dl.dropbox.com/scl/fi/t0tje2si4qcdkekp9g5ai/idea6_2024-04-01.png?rlkey=83jya0vt59s7btghao1y19qlf&dl=1){: .dark .w-75 .shadow .rounded-10 }
![idea64](https://dl.dropbox.com/scl/fi/rmouqixyjf8yb4jgrtdoq/idea64_HXkaYiechy.jpg?rlkey=59lgj8wz799xgmfqw38hurlgz&dl=1){: .light .w-75 .shadow .rounded-10 }

Program komutları kısmını aynı terminalde CLI kullanıyormuş gibi yazıyoruz. <version> ibaresinin değişken olabileceğine ve dizin konumuna dikkat edin. Çalışma dizini `revanced-cli` projesi olarak ayarlanmışsa ona göre dizini ayarlayın.

```
patch
-p ..\revanced-patches\patches\build\libs\patches-<version>.rvp
..\to-be-patched.apk
```
Gradle türünde bir *Before launch* komutu ekleyin. Proje olarak patches projesini seçin ve komutlar kısmına `build` yazın.

![idea64](https://dl.dropbox.com/scl/fi/qroydu2wqkcjdbkxuotb8/idea64_aQO7T1dGyw.png?rlkey=qz0mrod24layc6gf2rwb3easj&dl=1){: .dark .shadow .w-50 .rounded-10}
![idea64](https://dl.dropbox.com/scl/fi/jja97ltvrl8bwmb5pw00c/idea64_eagsMDbuK1.jpg?rlkey=brf0vldp5h0wr9sfyt6xj8xr7&dl=1){: .light .shadow .w-50 .rounded-10}

### Debug Süreci

Projedeki patchlerden birine debug noktası koyarak düzgün çalışmakta olduğunu kontrol edin. Konfigürasyonda `to-be-patched.apk` yerine patchlemek istediğiniz Android paketi ismi ve `patches` projesinin versiyonu doğru şekilde yazılmış olmalı. Ben [revanced.app](https://revanced.app/patches?pkg=com.google.android.youtube) sitesindeki önerilen versiyonu kullanacağım, dolayısıyla konfigürasyon şu şekilde. Ayrıca APK nodpi olmalıdır.

```
patch
-b ..\revanced-patches\patches\build\libs\patches-5.2.1-dev.5.rvp
..\com.google.android.youtube_19.47.53.apk
```

Patchlemekte olduğunuz uygulamaya uygun olanlardan birine debug noktası koyup başarılı bir şekilde durduğunu gördüyseniz başka bir şeye ihtiyaç kalmadı.

## Patch Anatomisi

Patchlerin çoğu **BytecodePatch** ve **ResourcePatch** yöntemini kullanır. **Bytecode**, uygulamanın smali kodunda; **Resource**, Android'in uygulama kaynakları `(resources.arsc)` kısmında değişiklik yapmaya yarar. İkisi birlikte de kullanılabilir (özel ayarlara sahip olan patchlerde bunu görebilirsiniz). Sonradan eklenen **Hex** patch desteğiyse byte değerleri üzerinde değişikliği mümkün kılar. Byte seviyesinde değişiklik, uygulamanın farklı dillerde yazılmış decode edilemeyen kütüphaneler (native library) kullanması durumunda gerekiyor. Örneğin Unity IL2CPP yöntemini getirerek oyunların incelenmesini zorlaştırmıştır. 

>`RawResourcePatch` benzer isimdeki yöntemle aynı işe yarıyor, sadece `resources` dosyasını decode etmeden daha hızlı bir şekilde patchlemek için mevcut.
{: .prompt-info}

Bu rehberde temel düzeyinde **Bytecode** ve **Resource** patch işlemini anlatacağım.

### Parmak İzi

Patchleme işlemi gerçekleşmeden önce yapılması gereken, hedef dosyanın/fonksiyonun/satırların nerede olduğunu belirtmektir. Bu noktada parmak izi yöntemi devreye girer. Bir fonksiyona ait dönüş değeri `(void, int)`, erişim belirteçleri `(PUBLIC, STATIC)` ve aldığı parametreler gibi değerleri belirterek, değiştireceğimiz noktayı hedefleriz. 

Şu şekilde değerler belirtmiş olsaydık:

```kotlin
returnType = "V",
access = AccessFlags.PUBLIC,
parameters = listOf("Z"),
```
{: .nolineno}

Bu izden anlaşılan; `void` değer döndüren, `PUBLIC` erişimli ve `BOOLEAN` (smali kodunda Z) türünde parametre alan bir fonksiyon hedef alınmıştır. Geri dönen `fingerprint` türündeki nesne **BytecodePatch** constructor fonksiyonuna içerisine parametre olarak sunulur. 

```kotlin
object SomePatch : BytecodePatch(
    setOf(SomeFingerprint)
) {
    // ...
 }
```
{: .nolineno}

Ek bir bilgi olarak, parmak izi yöntemlerinin verimlilik sıralaması şu şekilde:

* **En Hızlı**: `[strings]` belirteciyle. Verilen dizelerden en az biri tam eşleşme sağlamalıdır.
* **Daha Hızlı**: `[accessFlags]`, `[returnType]` ve `[parameters]` seçenekleri sağlanarak.
* **Hızlı:** `[accessFlags]` ve `[returnType]` kombinasyonu.
* **En Yavaş**: Sadece `[custom]` ve `[opcodes]` kullanarak.

İz yazarken istediğiniz parametreleri kullanabilirsiniz.

### Patch İskeleti

Patch constructor fonksiyonunda ve içerisinde metadata bilgileri belirtilir.

```kotlin
val `patchName` = bytecodePatch(
    name = "Some patch",
    description = "Does some thing.",
) {
    compatibleWith("com.some.app")

    execute {
        ...
    }
}
```
{: .nolineno}

- **`name`**: Patch ismi. Belirteç olarak kullanılır. İsimsiz olursa *PatchBundleLoader* tarafından tanınmaz ama diğer patchler bağlılık olarak kullanabilir.
- **`description`**: Patch açıklaması.
- **`compatibleWith`**: Uygulamanın paket adı.

### Dizin Yapısı

Patchler, `revanced-patches/src/main/kotlin/app/revanced/patches/<uygulama-adı>` şeklinde düzene koyulur. Patch ismi işlevinden gelmektedir. Objektif bir dilde açıklama yazılır (örn. 'Shorts butonunu gizler'). Parmak izi, olabildiğince az ve öz yazılır. Verilen izin, birçok sürümde geçerli olacak şekilde oluşturulması patchi daha erişilebilir ve kapsamlı hale getirecektir.

## Patch Yazımı

Rehberde kullanılmak üzere yazdığım ufak bir `patchme` uygulaması bulunmakta. Bir metin kutusu ve butondan oluşuyor. İstenilen parolayı girdiğinizde doğru olduğunu belirten bir toast mesajı çıkarıyor. Bu uygulamayı patchleyerek her parolayı doğru kabul etmesini sağlayacağız.

### JADX ile İnceleme

Bağlantıdan [`patchme.apk`](https://www.dropbox.com/scl/fi/hru4anz7y0bo2t54zd0mm/patchme.apk?rlkey=lesb72xbuureic3e7osbri5x8&st=oivbgppl&dl=0) dosyasını indirin. Uygulamayı açıp göz atabilirsiniz. Okumaya devam etmeden hangi noktalarda açık olabileceğini düşünün.

Uygulamayı [JADX](https://github.com/skylot/jadx/releases/latest) ile açın. `dev.seaque.patchme` paketinin `MainActivity` dosyasında mantık kısmı bulunmakta. Burada, boolean türünde değer döndüren `isUnlocked` fonksiyonu göze çarpıyor. Parmak izinde bunu hedef alacağız. Parmak izi, smali koduna göre yazılır.

```smali
.method isUnlocked(Ljava/lang/String;)Z
    .registers 3
    .param p1, "input"    # Ljava/lang/String;

    .line 37
    const-string v0, "VWF!ac3Gq&"

    invoke-virtual {v0, p1}, Ljava/lang/String;->equals(Ljava/lang/Object;)Z

    move-result v0

    return v0
.end method
```
{: .nolineno}
