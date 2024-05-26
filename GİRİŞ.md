# FRC Görüntü İşleme Temelleri
Görüntü İşleme, temelde bilgisayarların görmesini sağlayan, "Computer Vision" (Bilgisayar Görmesi) diye adlandırılan işlemin uygulamasıdır. Gerçek hayatta belli kalıplar altında bulunmaksızın birçok çeşidi olsa da, FRC'de kullandığımız belli başlı teknikler vardır. Cymughs Robotics için hazırladığım bu belgede de yöntemleri normalde kullanılan şekilde anlatıp uygulamalı projelerini FRC temelli gerçekleştireceğim.

# Kullanılan Cihazlar
Görüntü işleme yapan bir cihaz, tercihe bağlı olarak işlemciyi veya ekran kartını, fakat çoğunlukla işlemciyi kullanır. FRC'de RoboRIO, gerçek hayatta ise Arduino gibi kartlar bu işleme uygun olmadığı için (görüntüyü işleyecek yeterli işlemci gücüne sahip olmadıklarından) 'Co-Processor (Yardımcı İşlemci' denilen geliştirme kartları tercih edilir.

Geliştirme kartları genel olarak düşünüldüğünde FRC için ikiye ayrılır

## Plug-In (Tak Çalıştır) Geliştirme Kartları
Bu tarz geliştirme kartları, adından da anlaşılacağı üzere donanımsal ve elektronik, bazen de yazılımsal anlamda hazırlık ve kurulum istemeyen, hemen takıp kullanmaya yönelik kartlardır. Bunun FRC'deki en önemli temsilcisi, en popüler ve en güçlü Co-Processorlardan olan Limelight modelleridir 

![Logo](https://limelightvision.io/cdn/shop/products/L2SCALED_1080x.png?v=1674663124)

Limelight'lar modeline göre '120FPS 1280x800' gibi yüksek performans değerlerine ulaşabilirler

## Artıları

- Hızlı ve yüksek performans
- Tak-Çalıştır olmasından kaynaklı vakit kaybetmeden kullanma 
## Eksileri
- Yüksek Maliyet (400 dolara kadar)
- Kullanıma hazır olduğundan dolayı takımdaki öğrencilerin Co-Processor kurulumunun belli başlı kısımlarını öğrenememesi

## Diğer Co-Processorlar
Tak-Çıkar olanların yanında hem maliyet açısından uygun, hem de kendi kodunuzu çalıştırabileceğiniz Co-Processorlar da vardır. Bunların belli bir marka olmasına gerek yoktur, çoğumuzun bildiği Raspberry Pi'lar olabilir, ancak bir mini bilgisayar bile kullanabilirsiniz! FRC için tek kısıtlama (değişmiş olabilir) 600 doların altında bir maliyeti olmasıdır.

Bu Co-Processorlarda; kamera, elektronik ve yazılım sistemini kendinizin hazırlaması gerekmektedir.

![Logo](https://www.arducam.com/wp-content/uploads/2020/02/raspberry-pi-camera-pinout-camera-2.png)

Burada dikkat edilmesi gereken en önemli şeylerden biri, kamera seçimidir. Kullanım alanınıza uygun çözünürlüklü, uygun FPS değerini sağlayan bir kamera kullanmalısınız. Hatta ihtiyaç durumuna göre sıralı ve global çekimli kameralar arasında da seçim yapabilirsiniz.

## Artıları
- Düşük maliyet
- Custom olmasından kaynaklı donanımsal ve yazılımsal anlamda istediğiniz özellikleri yapabilme imkanı
## Eksileri
- Bazı modellerde düşük performans
- Daha kolay hasar alımı
- Daha fazla uğraş ve zaman gerektirmesi

# İletişim Protokolleri
Eğer hangi Co-Processoru kullanmaya karar verdiyseniz, sırada Co-Processor ile Motor Kontrol Kartınız (FRC için RoboRIO, kısaca Rio) arasındaki iletişimi sağlamak var. Robot kodunuzda elde ettiğiniz verileri kullanabilmeniz için bunları Rio'ya göndermeniz lazım. FRC'de kullandığınız görüntü işleme uygulamasına göre bu işlem otomatik, görüntü işleme yazılımının kütüphanesi aracılığıyla, gerçekleştirilebiliyor ancak nasıl çalıştığını bilmekte fayda var. FRC'de bunun için NetworkTables kütüphanesini kullanıyoruz.

Aşağıda gördüğünüz şema, NetworkTables'ın nasıl çalıştığını anlatıyor:

![Uygulama Ekran Görüntüsü](https://user-images.githubusercontent.com/55664403/81491807-1082b400-9258-11ea-9c78-776d219f0a99.png)

Bu kısım çok detaylı ve derin olduğundan, Obje Algılamaya kadar buraya girmeyeceğim. O kısma kadar yapacağımız bütün işlemlerde, PhotonVision kütüphanesi iletişimi bizim için halledecek.

# Görüntü İşleme
İşlemcimizi seçtik, donanımsal her şeyimizi hallettik, sıra geldi görüntüyü işlemeye. Bunun için önümüzde üç yol var:
## Limelight Yazılımı İle Görüntü İşleme
Eğer bir Limelight cihaz aldıysanız, [sitelerinde bulunan](https://docs.limelightvision.io/docs/docs-limelight/getting-started/summary), kendilerinin de 'Zero-Code' olarak adlandırdığı bu dökümantasyon aracılığıyla, yine kendilerine ait olan bir arayüz üzerinden görüntü işleme yapabilirsiniz
## Kendi Programınızı Yazma
Eğer hiçbir programın size yeterli seçeneği vermediğini düşünüyorsanız, veya bu programlarda bulunmayan bir şeyi kendiniz kodlamak istiyorsanız, Co-Processorunuza kendi kodunuzu yazabilirsiniz. Makine Öğrenimi için en yaygın olan kodlama dili Python'dur.

Ancak bu, çeşitli zorluklara yol açabilir. Matriks hesaplamak için gerekli matematik bilgisi ileri seviye ve üniversite düzeyinde olduğundan, kendi programınızı yazarken bir takım zorluklarla karşılaşabilir, yeterli zamanınız yoksa yetiştiremeyebilirsiniz.

## PHOTONVISION!!!
Photonvision, FRC'de en çok tercih edilen ve kullanım alanı en geniş görüntü işleme programıdır. İleri seviye matematik işlemlerini üstlenirken, istediğiniz projeyi ve özelliği programlamak için gerekli özgürlüğü tanır. Biz de yazımızın geri kalanında PhotonVision üzerinden ilerleyeceğiz

Photonvision, çeşitli gönüllüler tarafından geliştirilen, FRC için bir görüntü işleme programıdır. İsterseniz dökümantasyonuna göz atabilirsiniz, ancak ileriki bölümlerde her şeyi anlattığımdan ihtiyacınız olmayacak.
