# PhotonVision ile Görüntü İşleme - Oluşabilecek Sorunlar ve Olası Çözümleri

Elbette yapacağımız hiçbir program kusursuz olmayacak. Bu sorunları aşmanın en temel yolu, kuvvetli bir araştırma kapasitenizin olması ve tecrübedir. Elbette kimse aradığı sorunu bir anda bulup, buna uygun çözümü araştırmıyor. Bu süreç biraz sancılı olabilir.

Burada bağlantı ve ağ sorunlarına değinmeyeceğim. Daha çok Apriltagler ve Obje Algılama ile alakalı sorunlar olacak.

# Apriltagler ile İlgili Sıkıntılar

Apriltagler, yapısı dahili ile sorunlara çok açık bir platform. Bunların en başında ise algılanmama geliyor. Bu durumda birçok sebep olabilir. Öncelikle çözünürlüğünüzü kontrol edin, eğer görüntünün gözükmeyeceği kadar düşükse veya coprocessoru kastıracak kadar yüksekse Apriltag algılaması gerçekleşmeyebilir. Buna bir diğer sebep de dikkat hatalarıdır. Algılamaya çalıştığınız Apriltag'in 36h11 ailesinden emin olun, Apriltag Thresholdunun seçili olduğundan emin olun. Thresholdunuzun algılama ayarlarındaki Apriltag türünün 36h11 seçili olduğuna emin olun. Eğer bunlardan hiçbiri sorununuzu çözmediyse cihazınızı flashlamayı/PhotonVision'u yeniden yüklemeyi deneyebilirsiniz.

Bir diğer sorun da hareket halinde iken algılanmamadır. Bunun önüne geçmek için en önemli önlem global çekimli bir kamera kullanmanızdır. Global çekimli kameralar sıralı çekimli kameraların aksine görüntüyü yukarıdan aşağıya doğru değil, hepsini bir anda oluşturur. Bu sayede hareket bulanıklığını (Motion Blur) azaltarak daha duru bir görüntü elde edilir, Apriltagler algılanabilir. Eğer global çekimli kameranız varsa ve yine aynı sorunları yaşıyorsanız saniyedeki kare sayınızı (FPS) kontrol edin. 30 FPS altı değerler sağlıklı bir algılama sağlamayabilir. Eğer bu sorununuzu çözmediyse Apriltag algılama hata payı değerini yükseltmeyi deneyin. Ancak dikkat edin, hata payını aşırı arttırırsanız kamera 'Segmentasyon' sırasında elediği bazı Apriltag adayı pikselleri işleme dahil ederek hatalı algılamaya, yani Apriltag olmamasına rağmen varmış gibi algılanmasına sebebiyet verebilir. 3B algılamalardan kaynaklı oluşacak sıkıntıları "PhotonVision ile Görüntü İşleme - Görüntüyü İşleme" dosyasında anlatmıştım.

Eğer Apriltagten gelen yatay, dikey ve endirekt uzaklık mesafe bilgileri yanlış ise kalibrasyonunuzda bir sıkıntı var demektir. Eğer cihazınız çok kastığı için düzgün bir kalibrasyon yapamıyorsanız, başka bir cihazda aynı kamera ve çözünürlük değerlerinde kalibrasyon yapıp kullanacağınız cihaza aktarabilirsiniz. 

Eğer Apriltagleri sadece merkezlendirme gibi 2B işlem gerektiren görevlerde kullanacaksanız, 3B modda algılayarak yapmak çeşitli performans düşüklüklerine sebebiyet verebilir. Hangi amaç doğrultusunda kullanacağınızı seçip ona göre kullanılacakları hesaba katmak en doğrusu olacaktır. 

Robot kodunuza Apriltagleri eklerken sadece algılandığında yapılacakları yazdığınız bir kod hiçbir türlü çalışmaz. Kodunuza;
```bash
  if(targets.hasTargets()){
    //robot code
  } else {
    //robot code
  }
```
tarzı bir ifade eklemeniz, robotunuza Apriltag yokken ne yapmasını söylerek algoritma açığını kapatır, böylece robot kodunuz çökmez. 

Pratik yapmak ve belge okumak çok önemlidir. Ne kadar fazla tecrübe edinirseniz yapılacak hatalara karşı çözüm bulma imkanınız o kadar artar.

# Renkli Cisim Algılamada Yaşanabilecek Sıkıntılar
Renkli cisim algılama, pek fazla sıkıntı yaşayabileceğiniz bir alan değildir. PhotonVision kısmında algılama için kod yazmanıza gerek olmadığından, aklıma gelen muhtemel sorunlara değineceğim.

Eğer Photonvisionda algılamak istediğiniz dışındaki renkler de o cisim olarak algılanıyorsa, algılanacak minimum piksel alanını değiştirebilirsiniz, böylece eğer algılayacağınız cisim hatalı algılanan cisme göre görece büyükse bu sorun ortadan kalkar. Minimum algılanma alanını, herhangi bir karışıklık olmasa bile o renkteki 1-2 piksellik alanların algılanmaması için ufak bir değere atamanız gereklidir. Ancak eğer ilk bahsettiğim bir durumdaki gibi karışıklık sebebiyle yapıyorsanız, bu belli bir mesafeden nesnenizin kapladığı piksel alanının azalması ve algılanmaması anlamında gelir. Hem renk olarak hem şekil olarak aynı ancak boyut olarak farklı iki cisminiz varsa ve uzaktaki cisimleri de algılamanız gerekiyorsa, maaleseff renkli cisim algılamayı daha fazla kullanamazsınız, obje algılamayı kullanmanız gerekir. 

