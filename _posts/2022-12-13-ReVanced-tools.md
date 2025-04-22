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

Bu gönderi, ReVanced patchlerini üç farklı şekilde uygulamayı anlatmakta. 

| Yöntem                                                           |        Platform(lar) |
| :--------------------------------------------------------------- | -------------------: |
| [ReVanced CLI](https://github.com/revanced/revanced-cli)         | PC, Android (Termux) |
| [ReVanced Manager](https://github.com/revanced/revanced-manager) |              Android |
| [Revancify](https://github.com/decipher3114/Revancify)           |     Android (Termux) |
| ~~ReVanced Builder~~                                             |      ~~PC, Android~~ |

## CLI

>En hızlı yöntem.
{: .prompt-info}

Ortam için gerekenler:

- [Zulu JDK 17](https://www.azul.com/downloads/?package=jdk#zulu)
- [ReVanced CLI](https://github.com/revanced/revanced-cli/releases/latest)
- [ReVanced Patches](https://github.com/revanced/revanced-patches/releases/latest)
- İstenilen APK

İstediğiniz bir terminalde patch işlemi:

```bash
java -jar .\revanced-cli-*-all.jar patch \
.\some.apk \
--patches .\patches-*.rvp
```

Varsayılan ayarlarda kullanılan gizli seçenekler:

* **`--out`**: Output dizini.
* **`--temporary-files-path`**: Geçici dosyaların bulunacağı dizin.
* **`--keystore`**: İmzada kullanılan key dosyasının dizini.

## Manager

[Uygulamayı](https://github.com/revanced/revanced-manager/releases/latest) yükleyin. Uyarılarda evet butonuna tıklayın. SD Kart ikonundan indirilen APK'yi içe aktarın.

![ReVanced Manager Screenshot](https://dl.dropbox.com/scl/fi/rx3r060857v309gjz6onx/Screenshot_1694081282.jpg?rlkey=lofjr70d1jtycakilp2xk3pyx&dl=1){: .post-image .height-25}

Patchleri seçin, isteğe bağlı parametre alabilen patchlerde uygun değeri yazın ve patch butonuna tıklayın. Doğrusu hepsi bundan ibaret, Manager ilk zamanlarına kıyasla sağlam hale geldiği için yazacak bir şey kalmadı. 

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

Burası patchlemeden önce seçenekler belirtebileceğiniz kısım. Bazı patchler seçeneklere göre şekillenir, örneğin Spotify tema patchinin seçeneğine istediğiniz HEX değeri girebilirsiniz.

![Revancify Patch Options](https://dl.dropbox.com/scl/fi/aehelmztr4lb942jsdek1/revancify-patchoptions.png?rlkey=m95l6rv7788axptj2wbdwaabl&dl=1)

Patch Selection menüsünden istediklerinizi seçtikten sonra ilk seçenekle devam edin, Revancify her şeyi otomatik indirecektir. 

![Revancify Patch](https://dl.dropbox.com/scl/fi/g7stidbur40jszcil3v4o/revancify-patch.gif?rlkey=uh3y2x36qrl27gekqkggtx6po&dl=1){: .post-image}

Bu şekilde işlem bittikten sonra APK `0/storage/emulated/Revancify` dizininde belirecektir.