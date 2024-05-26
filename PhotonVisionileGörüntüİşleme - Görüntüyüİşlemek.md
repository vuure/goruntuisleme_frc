# PhotonVision ile Görüntü İşleme - Görüntüyü İşlemek
PhotonVision ile görüntü işlemeyi seçmek, yapabileceğiniz en mantıklı hamlelerden biridir. Bu kısımda nasıl kurulacağını vs. anlatmayacağım, lütfen Co-Processorun ve kütüphanelerin kurulumundan sonra burayı okumaya devam edin.

Eğer kurulumunuzu doğru yaptıysanız, robotunuza bağlı olduğunuz bilgisayarda http://photonvision.local:5800 adresini tarayıcınızda açtığınızda aşağıdaki gibi bir arayüzün sizi karşılamış olması lazım.

![Uygulama Ekran Görüntüsü](https://wiki.team3176.com/uploads/images/gallery/2023-11/image.png)

Program açıldığında yapmanız gereken ilk şey, ayarlardan takım numaranızı ayarlamaktır. Bu, öncesinde de bahsettiğimiz NetworkTables'ın çalışması için gereklidir. PhotonVision'un sayfasından belirtilen diğer ayarları da yaptıysanız artık görüntü işlemeye geçebiliriz!

# Görüntü İşleme Yöntemleri
Görüntü işleme için PhotonVision üzerinde 'Threshold' denilen 4 temel yolumuz vardır.
## Reflektif Bant ve Renkli Cisimler
Reflektif Bantlar ve Renkli Cisimler için görüntü işleme, FRC'nin en eski görüntü işleme tekniklerindendir. Ancak 2023 sezonundan sonra, 2024 sezonu itibari ile reflektif bantlar artık kullanılmamaktadır. Renkli cisimlerin algılanması ise hâlen aktif olarak kullanılır.

Peki neden direkt objeyi algılamak yerine nesneyi rengine ve cismine göre ayırt etmek bazı durumlarda daha mantıklı? Temel olarak, performans açısından. Zaten neredeyse bütün senelerde oyun parçalarının renkleri ve(ya) şekilleri, sahada normal olarak bulunulması beklenen renklerden ve cisimlerden farklıdır. Bu sebeple, renk ve cisim algılaması daha yüksek performansta, beklenenden daha düşük hata payıyla gerçekleşir. Örneğin 2024 sezonundaki Nota oyun parçası, saha içinde ve çevresinde pek bulunmayan bir renk (Turuncu) ve şekil (Halka) kombinasyonu olduğundan, bir yapay zeka modeli eğitmekten ziyade nesne ve şekil algılamak daha hızlı ve yüksek performanslı bir işlemdir. O zaman nasıl çalışıyor bu algılama?

Öncelikle renkleri filtreleyerek başlamamız lazım. Bu işleme 'Maskeleme' denir, kamerada sadece istediğiniz ayardaki piksellerin gözükmesini sağlar. Objemizin renk tonunu -yani rengini- ayarlayabilmek için 'Hue', doygunluğunu ayarlamak için 'Saturation', parlaklığını ayarlamak için ise 'Value' değişkenlerini ayarlamamız gerekir. Bunun belirli bir formülü yoktur, deneyerek bulmanız lazım. En iyi sonuçlar için ayarlamayı yarışmada kullanmayı planladığınız kamerayla yapmanız, ışıklandırmayı yarışma ortamına benzer yapmanız, ayarlamayı yaptığınız koşulların (Arkaplan objeleri, nesnenin üzerinde bulunduğu zemin, kamera açısı vs.) yarışma koşullarına benzer veya aynı olmasına dikkat etmeniz çok büyük önem arz etmektedir. 

Eğer renginizi 'maskelediyseniz', sırada objenin şeklini ayarlamak var. Bunun için yine Threshold ayarlarındaki obje seçme kısmından objenizi seçmeli, bunun Tuning'ini (ayarlamasını) yapmalısınız. Burada en önemli kısım hata payını yeterli oranda ayarlamaktır. Unutmayın ki, 2024 senesindeki oyun parçası, kamera yerle belli bir açı yaptığından tam daire değil eliptik bir şekilde gözükmektedir. Bu yüzden hata payını eliptik şekilleri de algılayacak miktarda, deneyerek ayarlamanız lazım.

Peki ya bu sistem şekilleri nasıl algılıyor? Bunun iki yolu var. İlki, PhotonVision'un da kullandığı, kenarları-köşeleri saymak. Nasıl sayıyor kısmında işler biraz derinleşiyor, ki PhotonVision bile bu işlemlerin bazıları için OpenCV kütüphanesini kullanıyor. Ancak Amerika'yı yeniden keşfetmeye gerek yok! Ayarladığınız maskelemeye göre maskelemeden geçen piksellerin oluşturduğu şekilleri, hata payına göre 'yumuşattıktan' (Örneğin hata payı ayarına göre aralarında belirli bir açıdan daha az olan iki kenarı tek bir kenar olarak algıladıktan) sonra kenar-köşe sayısını sayarak bize nesneyi söylediğini bilmek çokça yeterli. İkinci yol ise, şekilleri bir yapay zeka modeline öğrettikten sonra algılamak. İlk yöntem de saymak için bazı yerlere makine öğrenmesini kullanır ancak ikinci yöntem tamamen bunun üzerinedir.

## Apriltagler
Aprilagler, FRC'deki en popüler lokalizasyon ve görüntü işleme tekniklerindendir. İlk cümlede de değim gibi; lokalizasyon, açı ayarlama, otomatik konumlandırma gibi uçsuz bucaksız bir kullanım alanına sahiptirler. Peki Apriltaglerin aslında bu işin orijinali olmadığını biliyor muydunuz? 

Aslında FRC'de kullandığımız Aprilagler, 36h11 adı verilen, Bütün Apriltaglerin bir alt sınıfıdır. Apriltagler ise aslında AruCo Marker denilen bir İşaretleyici ('Marker'ın direkt olarak Türkçe çevirisi) grubunun alt dalıdır. Aşağıda gördüğünüz fotoğraf, AruCo Marker işaretçi familyasının bütün alt dallarını gösterir: 

![Uygulama Ekran Görüntüsü](https://pub.mdpi-res.com/sensors/sensors-22-08548/article_deploy/html/images/sensors-22-08548-g003.png?1667884697)

Peki neden Apriltag? Aslında Apriltag seçilme nedeni, hem yeterli sayıya kadar kapasitesi olmasından, hem de 36h11 ailesinin birim kare kapladığı alana kıyasla gayet yeterli olmasından ötürü robotlar tarafından uzak mesafelerden algılanabilmesidir.

Apriltagler ile, robotun Apriltag'e göre ister 2 boyutlu ister 3 boyutlu bütün bilgilerine ulaşabiliriz. Örneğin robotun Apriltag'e göre X, Y ve Z eksenindeki açıları, dikey mesafesi, yatay mesafesi, en kısa mesafesi... bu bilgiler, özellikle otonom kısımda robotun hareketini sağlamamıza büyük ölçüde katkı sunmaktadır. Mesela, Apriltagleri kullanarak 2024 sezonunda robotunuzun Speaker Apriltagine göre mesafesine veyahut açısına bağlı olarak; kolunuz varsa kolununz yükseliği, varsa taretinizin yüksekliği ve açısı gibi bilgileri otonom olarak ayarlayabilirdiniz. Peki, Apriltager nasıl algılanıyor?

Apriltagler, temsil ettikleri sayıyı Binary türünde barındırırlar. Bu yüzden Apriltaglerin algılanması QR kodlarınkiyle benzerlik gösterir. Apriltagin algılanmasında sırası ile aşağıdaki işlemler uygulanır:

Öncelikle görüntü, renkler silinip sadece siyah-beyaz kalacak şekilde maskelenir. Daha sonrasında, siyah-beyaz hâle dönüştürülen görüntünün çözünürlüğü düşürülerek algılama algoritmasının daha hızlı çalışması hedeflenir. Sonrasında, elimizdeki görüntüdeki pikseller üç gruba sınıflandırılır: "Aşırı Koyu", "Aşırı Aydınlık" ve "Belirsiz". Sıradaki aşama olan 'segmentasyonda', "Belirsiz" olarak sınıflandırılan pikseller elimine edilir, "Aşırı koyu" ve "Aşırı Aydınlık" olarak sınıflandırılan pikseller saklanır. "Algılama" aşamasında ise saklanan görüntüdeki olası köşeleri algılayarak dörtgen oluşturmaya çalışan bir algoritma çalışır. Algılanan bu dörtgenlerden 'Decode' edilebilenler, yani barındırdığı Binary türünden veri deşifre edilebilenler edilir, edilemeyenler elenir. En sonda ise deşifre edilen bu veri amaç doğrultusunda en baştaki orijinal görüntünün üstüne yazılabilir, algılanan alanın merkez koordinatı belirlenebilir, bunun bir sınırı yoktur. 

3 boyutlu algılama ise bundan daha karmaşıktır ve belirli matriks hesaplamaları gerektirir. Bu yüzden bu işlemin nasıl gerçekleştiğinin derinlerine girmeden yüzeysel olarak anlatmak, uygulamasının detaylarına girmek uygun olacaktır. Basitçe, yaptığımız kalibrasyon doğrultusunda farklı açılardan kalibrasyon kağıdının (birazdan göreceğiz) konumunu kaydederek sisteme konumlara göre nasıl gözüktüğünü kaydediyoruz, aynı durumda bir Apriltag gördüğü zaman da kalibrasyon bilgisine göre gerekli değerlere ulaşıyoruz. Ancak insanların aksine kameralar, derinlik ve gölgelendirme gibi kavramları algılayamadıkları için Apriltagi olması gerekenden başaşağı veya aynalı (normal durumun X ve Y eksenine göre simetriği şeklinde) gösterebilir. Bu durumdan kaçınmak için kalibrasyonu olabildiğince birazdan anlatacağım gibi olması gerekene uygun yapabilir, veya başaşağı dönme gibi durumların yaşanmayacağından emin olduğunuz için bu şekilde bir algılanma yaşandığında düzeltmesi için bir algoritma yazabilirsiniz. Ancak bu, ileride başaşağı dönülmesi gereken oyun temaları olursa işe yaramaz.

Bu sistemi PhotonVision'da kullanabilmek için öncelikle az önce de bahsettiğim kamera kalibrasyonunuzu yapmanız lazım. Öncelikle kullanacağınız sisteme en uygun çözünürlüğü seçin. Genellikle ne yüksek ne düşük, 480x600 gibi çözünürlükler tercih edilir. Daha sonrasında istediğiniz kalibrasyon kağıdı ayarlarını yapın, genellikle 1 inç 8x8 chessboard tercih edilir. Sonrasında bu kağıdın mürekkebi silik olmayan bir yazıcıdan çıktısını alın ve kalibrasyonu başlatın. Olabildiğince çok ve değişik açılardan kalibre edin. Buradaki değişik kısmı önemli, çünkü ne kadar fazla farklı kalibrasyon bilgisi olursa, az önceki paragrafta bahsettiğim 3B algılama sorunları azalır. 

Elde edilen veriler ekranın sol altındaki Targets kısmında görülebilir

# Özet
Bu kısımda Reflektif Bant, Renkli Cisimler ve Apriltagleri görüntü işleme ile nasıl algılayabileceğimizi öğrendik. Verilerin robot kodunda kullanılmış hâlinin basit bir örneğini görmek isterseniz "GoruntuKodlariJava" klasörüne bakabilirsiniz.
