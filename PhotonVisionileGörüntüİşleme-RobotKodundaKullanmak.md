# PhotonVision ile Görüntü İşleme - Robot Kodunda Kullanmak
Görüntü işlememizi yaptığımıza göre, sıra bunu robot kodumuzda kullanarak istediğimiz projeyi gerçekleştirmeye geldi. Bu dosyada basit bir Apriltag hedefe kilitlenme kodunu "Timed Based" sistemde yazacağız, sonra bunu "Command Based" sisteme geçireceğiz.

Eğer "Command Base" sistem hakkında yazdıklarımı merak ediyorsanız, ilk soruya verdiğim cevaptaki linkteki "Command Based Nedir?" adlı yazımı okuyabilirsiniz.

Öncelikle, PhotonVision kütüphanelerini projemize indirmemiz lazım. Bunun için PhotonVision dökümantasyonundaki adımları takip edebilirsiniz. PhotonVision kütüphanemizi indirdikten sonra;
```
import org.photonvision.PhotonCamera;
```
kod satırı ile kodumuza yükleyebiliriz. Böylece artık "PhotonCamera" sınıfı içindeki bütün metodları kodumuzda kullanabiliriz. 

Bundan sonra kullanacağımız kamera öğemizi tanımlıyoruz;
```
PhotonCamera cam = new PhotonCamera("photonvision");
```
yukarıdaki kod satırında "cam" değişken adı yerine kullanmak istediğiniz değişken adını, "photonvision" yerine de PhotonVision arayüzünüzdeki kamera adını giriniz. PhotonVision arayüzündeki kamera adınıza sağ üstte yer alan "Camera" bölümünden erişebilirsiniz.

Şimdi ise sıra PhotonVision'un gönderdiği değerleri almaya geldi. bu örneğimizde Apriltag kullanacağımızdan, PhotonVision arayüzünde Apriltag Thresholdunun seçili olduğundan emin olmalıyız. Sonrasında ise algılamanın bize sağladığı verileri almak için; 

```
var result = cam.getLatestResult();
```
kod satırını yazıyorum. Bu, 'cam' değişkenine tanımlı bilgilere erişmemi sağlayacak.

Şimdi ise Apriltag'e kilitlenmek için hangi değerlere ihtiyacım olduğunu tepit edeceğim. Aşağıda 3B sistemdeki açı değerlerinin karşılık geldiği isimleri belirten bir tablo var:

![Logo](https://docs.photonvision.org/en/latest/_images/camera-coord.png)

Bu tabloya bakıldığında, bizim isteğimiz robotun Apriltag'e Z ekseninde sabitlenmesi olduğu için "Yaw" açı değerini kullanacağız. Fakat bundan önce, robot kodunun çökmemesi için öncesinde de bahsettiğimiz gibi Apriltag bulunup bulunmadığını kontrol ederek algoritma açığını kapatmamız lazım. Bu yüzden öncelikle;
```
if (result.hasTargets()){
    //Robot Code
} else {
    //Robot Code
}
     
```
kodlarını kodumuza ekliyoruz. Buradan sonra Yaw açı değerini almak için öncelikle algılanan görüntü içinden en iyi Apriltag değerini almalı, sonrasında bu Apriltag değerinin yaw değerini almalıyız. Bunun için; 
```
PhotonTrackedTarget target = result.getBestTarget();
double robotYaw = target.getYaw();
```
kodlarını "If Statement"ımızın içine ekleyebiliriz. Eğer kilitlenme işlemini yapmayı planlıyorsak, şasemiz %90 ihtimalle baktığı yönden bağımsız hareket edebilen, yani ya mecanum ya swerve şasedir. Swerve kodlaması hakkında pek bir bilgim olmadığı için bu örnekte mecanum şase üzerinden ilerleyeceğim. Apriltag'in konumuna göre yönü ayarlayabilmek için robotun Apriltag ile açısına bağlı olarak bir dönüş hızı tanımlamamız lazım, bunun için de en mantıklı ve kullanışlı yol PID ve FeedBack denklemleridir. Bunların ne olduğunu bildiğinizi düşünerek geçiyorum, bu örnekte ikisi arasından biraz daha popüler olan PID denklemini kullanacağız. Deneme-Yanılma ile bulduğumuz PID değerlerini şase için bir PID kontrolcüsüne tanımlıyorum;
```
PIDController turnController = new PIDController(0.1, 0, 0.1);
```
Daha sonrasında ise yine "If Statement"ımızın içine mecanum şasemizi kontrol etmek için gerekli olan kodu ekleyip PID hesaplamasını da yazıyorum.
```
m_robotDrive.driveCartesian(-leftYaxis, -rightXaxis, turnController.calculate(robotYaw, 0), gyroRotation2D);
```
Burada temiz kod yazma ilkelerine aykırı bir hareket yaptığım görülebilir. Eğer PID hesaplamasını bir değişkene atasaydım okunurluk ve anlaşılırlığı arttırabilirdim. Ancak ufak bir örnek olduğu için bunu şimdilik geçiyorum. 

Yukarıdaki işlemleri yaptıktan sonra Apriltag olmadığında yapmasını istediğim şeyleri kodlamak için "Else Statement"ım içine de mecanum kontrol kodunu;
```
m_robotDrive.driveCartesian(-leftYaxis, -rightXaxis, leftXaxis, gyroRotation2D);
```
ekleyerek kodumu tamamlıyorum.

Bu örnekte algılamayı öğrendiğimiz Apriltagleri robot kodumuza eklemeyi öğrendik, ufak bir de proje gerçekleştirdik. Joystick, motor kontrolcüsü gibi değişkenleri tanımlamayı bir önceki linkte gösterdiğim, siz de o soruyu sorarak bir nevi bildiğinizi kabul ettiğiniz için göstermedim. Bu kodun tam hâlini "GoruntuİslemeJava" dosyası içinde bulabilirsiniz.
