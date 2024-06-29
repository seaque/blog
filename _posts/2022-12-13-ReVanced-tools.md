---
title: ReVanced Uygulama Yöntemleri
date: 2022-12-13
categories: Android
# image:
#   path: 
#   width: 600   # in pixels
#   height: 300   # in pixels
#   alt:
---

ReVanced projesi, Vanced'e göre çok daha geniş kapsamlı olduğundan işin teknik kısmıyla ilgilenmeyen kullanıcılar için biraz karışık gelebilir. Bu yazı, ReVanced patchlerini üç farklı şekilde uygulamayı anlatmakta. 

| Yöntem | Platform(lar) 
| :- | -: |
| [ReVanced CLI](https://github.com/revanced/revanced-cli) | PC, Android (Termux)
| [ReVanced Manager](https://github.com/revanced/revanced-manager/releases) | Android
| [Revancify](https://github.com/decipher3114/Revancify)| Android (Termux)
| ~~ReVanced Builder~~ | ~~PC, Android~~

## CLI

>Manager'a göre avantajı biraz daha hızlı olması, özellikle telefonunuzun işlemcisi iyi değilse 10 dakika bile sürebiliyor. Ayrıca `options.json` dosyasıyla ek değişiklikler yapılabiliyor.
{: .prompt-info}

Geliştirme ortamı için gerekenler:

- [Zulu JDK 17](https://www.azul.com/downloads/?package=jdk#zulu)
- [ReVanced CLI](https://github.com/revanced/revanced-cli/releases/latest)
- [ReVanced Patches](https://github.com/revanced/revanced-patches/releases/latest)
- [ReVanced Integrations](https://github.com/revanced/revanced-integrations/releases/latest)
- İstenilen APK

Java dizinini PATH değişkenlerine eklemeyi unutmayın. Ayrıca indirdiğiniz APK bundle olmamalı.

Tüm dosyaları bir klasörde toplayın. Bu noktada `options.json` adlı dosya oluşturup içeriğine aşağıdaki yazıyı eklerseniz tema eski grimsi karanlık mod olacaktır. Fakat pek güzel gözükmüyor, sadece örnek olarak verdim. Options dosyası [*custom-double-tap-length*](https://github.com/inotia00/revanced-patches/blob/revanced-extended/src/main/kotlin/app/revanced/patches/youtube/layout/doubletaplength/patch/DoubleTapLengthPatch.kt) gibileri için işe yarıyor, istediğiniz değerleri ekleyebilirsiniz.

```json
[{
  "patchName" : "Theme",
  "options" : [ {
    "key" : "darkThemeBackgroundColor",
    "value" : "#212121"
  }, {
    "key" : "lightThemeBackgroundColor",
    "value" : "@android:color/white"
  }]
}]
```

Her şey hazır olduğuna göre patchleme işlemini gerçekleştirebiliriz.

```powershell
java -jar .\revanced-cli-2.20.1-all.jar patch `
.\com.google.android.youtube_18.05.40.apk `
-o .\app.revanced.youtube_18.05.40.apk `
-b .\revanced-patches-2.167.0.jar `
```
- `patch`: Patch yapılacak APK
- `-o`: Çıktı dosyası adı (herhangi bir şey yazılabilir)
- `-b`: Java patch dosyası

>Google uygulamalarında hesaplarınızı kullanmak için [MicroG](https://github.com/TeamVanced/VancedMicroG/releases/tag/v0.2.24.220220-220220001) de gerekiyor.

Çıkan dosyayı ADB aracılığıyla ya da normal yöntemle yükleyebilirsiniz.

Sadece istenilen patchleri dahil etmek için `--exclusive` seçeneğini kullanabilirsiniz.

```powershell
--exclusive `
-i copy-video-url `
-i disable-shorts-on-startup ` 
-i disable-zoom-haptics ` 
-i general-ads
```
{: .nolineno}

Ya da belirli patchleri hariç tutmak için `--exclude (-e)` seçeneğini kullanabilirsiniz.

```powershell
-e custom-branding `
-e always-autorepeat `
-e premium-heading
```
{: .nolineno}

Tüm seçenekler [şuradaki](https://revanced.app/patches) listede bulunmakta.

## Manager

[Uygulamayı](https://github.com/revanced/revanced-manager/releases/tag/v0.0.57) yükleyin.

Play Store'dan yüklenmiş uygulamaları seçerek deneyebilirsiniz fakat hata verebilir, bu durumda non-bundle olanı indirip depolamadan içeri aktarmak gerek.

![ReVanced Manager Screenshot](https://dl.dropbox.com/scl/fi/rx3r060857v309gjz6onx/Screenshot_1694081282.jpg?rlkey=lofjr70d1jtycakilp2xk3pyx&dl=1){: .post-image}

>armeabi v7 mimarisine sahip cihazlarda uyarı verebilir fakat aldırmayın, bazı cihazlarda sorunsuz patchlenebiliyor.
{: .prompt-warning}

## Revancify

![Revancify Open Screen](https://dl.dropbox.com/scl/fi/qnktohy9e241ehgb9ti9h/revancify-openscreen.png?rlkey=iatblpnvxw7c6odee0xcp4t8n&dl=1){: .post-image}

[Termux](https://f-droid.org/en/packages/com.termux/) uygulamasını yükleyin ve aşağıdaki kod parçacığını yapıştırın.

```bash
curl -sL "https://raw.githubusercontent.com/decipher3114/Revancify/main/install.sh" | bash
```
{: .nolineno}

Gerekli dosyalar indikten sonra `revancify` komutuyla menüyü açabilirsiniz. Burada birkaç seçenekle karşılacaksınız. 

Uygulama seçme menüsünde mevcut uygulamalar ve versiyonları.

![Revancify App Select](https://dl.dropbox.com/scl/fi/ptgqrsmyoxrdb25trsst1/revancify-appselect.png?rlkey=sc1b5gg1c1vi0luw4izlzlcbb&dl=1)

Kaynak seçme bölümünde diğer patch kaynaklarını seçebiliyorsunuz. Farklılıklar için kaynakların sayfalarına bakmanız gerekiyor. Örneğin **inotia00** kaynağı sadece YouTube, YouTube Music ve Reddit uygulamalarını içeriyor.

![Revancify Change Source](https://dl.dropbox.com/scl/fi/zz3xoi4ptf13dbi762cpz/revancify-changesource.png?rlkey=mtvzr6voqp76x6zavaw85aaaa&dl=1)

Burası patchlemeden önce seçenekler belirtebileceğiniz kısım. Bazı patchler seçeneklere göre şekillenir, örneğin Spotify tema patchinin seçeneğine istediğiniz değeri girebilirsiniz.

![Revancify Patch Options](https://dl.dropbox.com/scl/fi/aehelmztr4lb942jsdek1/revancify-patchoptions.png?rlkey=m95l6rv7788axptj2wbdwaabl&dl=1)

Patch Selection menüsünden istediklerinizi seçtikten sonra ilk seçenekle devam edin, Revancify her şeyi otomatik indirecektir. 

![Revancify Patch](https://dl.dropbox.com/scl/fi/g7stidbur40jszcil3v4o/revancify-patch.gif?rlkey=uh3y2x36qrl27gekqkggtx6po&dl=1){: .post-image}

Bu şekilde işlem bittikten sonra APK `0/storage/emulated/Revancify` dizininde bulunabilir. Nâm-ı diğer telefonun ana dizini.