
CONTAINERLAR DOGAR, BUYUR VE OLUR. E OLDUKLERINDE BEYINLERINDEKI BILGILERI BIR YERE YEDEKLEMEMIZ GEREKIYOR; BURADA DA DEVREYE VOLUMELER GIRIYOR.



![img](https://eus-www.sway-cdn.com/s/6OASzIg8Gr2Bj9ig/images/LXw_hdN9y_RqI7?quality=772&allowAnimation=true)

* Yukarıdaki görselde herhangi bir uygulama imaj haline getirildi sonrasında bu imajı kullanarak bir container olarak ayağa kaldırıldı.

* Burada uygulamamız çalışıyor ve sonuç verileri `application.log` dosyasına kaydediliyor.

* Diyelim ki sonrasında container ‘da bir sorun yaşadık ve bu yüzden yine aynı imajla yeni bir container oluşturduk.

* Ancak tüm önceki `application.log` dosyasının verileri eski container‘ın yazılabilir RW katmanında kalmış oldu.

* Dolayısıyla boş bir `application.log` dosyası ile karşılaşırız.

* Bu container eski container ‘ın yazılabilir RW katmanına erişim sağlayabilir mi? Hayır.

* Bu tamamen ayrı bir container ve ayrı bir yazılabilir RW katmanına sahiptir. Dolayısıyla,
    - harici bir disk alanına sahip değilseniz veya harici tutulan bir veritabanına kayıt yapılmıyorsa veriler kaybolur. 
    - Container yaşam süresinden daha uzun saklanması gereken verileriniz varsa bunları container içinde tutmayız. Bunun için Volume ‘ler kullanılır.

* Volume ‘leri container dışında tutmamız ve her yeni container ile
ilintilendirmemiz(mount), paylaşılabilir ve erişilebilir yapmamız gerekir.

## Volume Yapısı

* Docker Volume ‘ler aynen container ve image gibi docker objeleridir.

* Aynen container oluşturur gibi oluşturulur.

* Varsayılan olarak docker deamon veya engine çalışıtığı host makine üzerinde oluşturulurlar.

## Volume Nasıl Çalışır?

* İstersek NFS veya AWS EBS block sürücü olarakta kullanılabiliyor.

* Container sen başla fakat senin `/app` klasörün `vol:abc` isimli volume olsun.

* `/app` klasörüne yazılmış herbir dosya abc volumüne yazılsın ama sen container içindeki app klasörüne yazılmış bir dosya olarak gör ve kullan, şeklindeki bir mantıkla çalışmaktadır.

## Volume Nasıl Oluşturulur?

* Yeni bir volume oluşturmak için;

```bash
$ docker volume create firstvolume

# Volume'leri gorebilmek icin
docker volume ls

```
* Özelliklerini ayrıntılı olarak görebilmek için;
```bash
$ docker volume inspect firstvolume


# Sonucu
[
    {
        "CreatedAt": "2022-04-07T00:49:06Z",
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/var/lib/docker/volumes/birincivolume/_data", # Burada mountpoint altında volume içeriğinin saklandığı görülür ancak Docker Desktop VM üzerinde koştuğundan bunu direkt görmek biraz farklıdır. Ama Linux makinede bunu direkt makinenin dosya sistemi üzerinde görebilirsiniz.(https://library.netapp.com/ecmdocs/ECMP1217281/html/GUID-C6537E25-1E71-40F8-A1AF-F0DEED4C865D.html#:~:text=A%20volume%20mount%20point%20is,26%2Ddrive%2Dletter%20limitation.)
        "Name": "birincivolume",
        "Options": {},
        "Scope": "local" # means Volumes are stored in a part of the host filesystem which is managed by Docker 
    }
]
```
* Burada;
```bash
{

“Driver” : “local”

“Mountpoint” : “/var/lib/docker…”

…

}

```

* Genel kullanımı => `docker run -v <volume_name>:<container-içindeki klasör-adı> image_name ...`
Şeklindedir.

- ekbilgi: Elimdeki container baglamayi unuttum napicam dersen: https://stackoverflow.com/questions/28302178/how-can-i-add-a-volume-to-an-existing-docker-container (`Varolan container'dan yeni bir image olusturup o imajdan container turet!`)


#### Volume mount edilen ilk container ‘ı oluşturmak için

* Burada tam olarak napacagiz? Bir container'in icerisine directory(klasor), sonrasinda bir txt dosyasi olusturacagiz. Sonrasinda volume'leme yapacagiz. Son olarak container'i silince icinde olusturdugumuz dosya da silinmis mi silinmemis mi onu kontrol edecegiz.

```bash
$ docker container run -it -v firstvolume:/app ubuntu bash
# app klasoru mock klasor; volume'lerde degil container'larda gorunur.
# Volume'un icerisindeyiz, devam..
pwd
ls
```

* Şimdi bir `/app` klasörü altında `abc.txt` isimli bir dosya oluşturalım. Bunun için;

```bash
# Volume'un icerisindeyiz, devam..
cd /app

touch abc.txt

ls

# Bu dosyaya bir veri girelim;
# Volume'un icerisindeyiz, devam..
echo “This line is added from the first container” >> abc.txt
cat abc.txt # Saglama yapmak icin
ls && exit
```
* Dolayısıyla container kapandı veya stop konumuna geçti. Bunu görebilmek için;

```bash
$ docker ps -a

# veya

$ docker container ls -a
```
* Bu container <ID> ile silmek için;

```bash
# Ben hepsini sileyim istiyorum
$ docker container prune
```
* Şimdi volumü bağladığımız bu container tamamen silindi! Böylece container yaşam süresi sona erdiği için içindeki dosyalarda silindi. 
* Bize gerekli olan oluşturduğumuz dosyaya erişmek için volume varlığını kontrol edelim.

* Bunun için;
```bash
$ docker volume ls
```
* **Dolayısıyla container silinince volume ‘de beraberinde silinmez. Volume ‘ler container‘lardan bağımsız ve ayrı objelerdir.**

* Volume'ler Container ‘larla bağımsız bir varlığa sahiptirler.

* Şimdi yeni bir container oluşturalım ve volume içindeki dosyamızın varlığını kontrol edelim. Bunun için;

```bash
$ docker run -it -v firstvolume:/myVolume ubuntu bash
# myVolume klasoru mock klasor
# Volume'un icerisindeyiz suan
```
* Volume bağladığımız klasörün varlığını kontrol edelim. Bunun için;
```bash
# Volume'un icerisindeyiz, devam..
pwd
ls

…

cd /myVolume

ls

…

cat abc.txt

The line is add from the first container

exit
```

* Böylece container yaşam süresinden fazla bir süre için dosylarımıza erişebiliyoruz. İşte volume bize tam da bunu sağlamaktadır.

* Yani gerekli verilerimizi ve dosyalarımızı container dışında tutmamızı sağlar.

--------------------------------------------------------------

## BIR VOLUME'U BIRDEN FAZLA CONTAINER'A BAGLAMA

* Şimdi ikinci bir dosya oluşturalım. Bir volumü birden fazla container ‘a aynı anda bağlayalım.

```bash
docker ps -a 

# en son olusturdugumuz container'i ayaga kaldiralim
docker start ensondurdurdugumuzcontainerinidsi

# kaldirdigimiz container'i attached moda alalim(icinde degisiklik yapABILMEK icin)
docker attach CONTAINERID

# ls diyelim gozumuz gonlumuz acilsin
ls

# cd myVolume
cd myVolume/

ls

# ikinci dosyamizi olusturalim
touch secondFile.txt

ls

# Container'i calisir pozisyonda tutmak icin crtl p q ya bas
CIKTI: read escape sequence

# Kontrol edelim bakalim container hala calisir vaziyette mi
docker ps -a
```

* Ikinci volume ‘müzü de bu container ‘a da bağlayalım. Bunun için;

```bash
$ docker run -it -v  firstvolume:/techpro alpine sh # alpine farkli bir imaj ubuntu gibi bir OS bash yerine de sh kullandik bu da bash'in bir alternatifi

# container'in icindeyiz su an
…

ls

…

cd /techpro

ls
# Gordugunuz gibi alpine ile olusturdugumuz ikinci container da ayni volume ile islem yapabiliyor ki biz secondFile.txt yi gorebiliyoruz
…
```

* Şimdi burada yeni bir dosya oluşturalım ve diğer container shell ‘inden kontrol edelim. Bunun için;

```bash
echo “This line is added from the SECOND CONTAINER to thirdFile.” > thirdFile.txt

# Container'i calisir pozisyonda tutmak icin crtl p q ya bas
CIKTI: read escape sequence
```
* Şimdi birinci container üzerinden `thirdFile` üzerinde yazılı olan bilgiyi okuyabiliriz. Bunun için;

```bash
docker ps

docker attach BIRINCICONTAINERINIDSI

cat thirdFile.txt
```

## Boş ve Dolu Volume Davranışı

1. Eğer mevcut olmayan bir klasöre mount yapılırsa bu klasör container ‘da oluşturulur.(Yukaridaki orneklerimizde gorduk zaten; `techpro` klasoru mesela)

    - İçinde hangi dosyalar varsa container bunlara erişebilir. Yani içindeki dosyalar varsa ancako dosyalar container tarafından görülebilir. Boşsa zaten boş bir klasör olarak görünür.

2.  Klasör dolu Volume boş ise klasördeki dosyalar volume kopyalanır.

3. Her ikiside dolu ise Volume baskındır. Klasörde Volume ‘de hangi dosyalar varsa sadece o dosyalar görünür.

* Ama her koşulda container silinsede silinmesede Volume ‘e ne yazıldıysa bu kalıcı olarak saklanılmış olur. Zaten Volume ‘lerin amacıda budur.

```bash
# Volume olmadigi zaman dosyalarin ucacagina dair
$ docker run --rm -it techprodevops/test_app sh # rm: icindeki uygulama durdugu zaman containeri sistemden siler
# techprodevops/test_app sh: bu kurumun imaji
ls

exit

# Bu container exit olduğu için silindi!
```
* Bunu görmek için;

```bash
$ docker ps -a
```

* Yeni bir volume oluşturalım. Bunun için;

```bash
$ docker volume create test_volume
```

* Şimdi bunu yeni bir container oluşturup, bu container ‘a mount edelim. Bunun için;

```bash
$ docker container run --rm -it -v test_volume:/new_folder techprodevops/test_app sh

$ ls && cd usr

$ ls

…/techpro

$ ls techpro && exit
```

* Şimdi bu container silindi!
---------------------------------------------------------
#### 2. Eğer bir volume container ‘da hali-hazırda varolan bir klasöre mount edilirse;
        - Klasör dolu Volume boş ise klasördeki dosyalar volume kopyalanır.


* Şimdi boş bir Volume ‘ü içerisinde dosya olan bir klasöre mount edelim. Bunun için;

```bash
$ docker volume create testvolume2
$ docker container run --rm -it -v testvolume2:/usr/techpro techprodevops/test_app sh
# Yukarida ne diyoruz? bos olan test_volume da usr/techpro isimli yeni bir directory olustur ve techprodevops/test_app image'indan olusacak container'daki dosyalari test_volume/usr/techpro'ya dump et
$ ls
cd usr
cd techpro
..app1 app2

# Şimdi buradaki dosyalar test_volume kopyalandı!

$ exit
```

* Bunu görebilmek için yeni bir container oluşturalım. Bunun için;

```bash
$ docker run --rm -it -v testvolume2:/abc alpine sh

$ cd /abc && ls

$ ls
..app1 app2
```

* Dolayısıyla burada Volume boş olduğundan üzerine container ‘dan bağladığımız klasör altındaki tüm dosyalar kopyalanmış oldu.

* Şimdi bu dosyaları silelim ve klasörde neler var listeleyerek kontrol edelim.

```bash
$ rm -f app* && ls
ls
```

* Şimdi buraya “devops.txt” adında yeni bir dosya oluşturalım ve container ‘ı kapatalım. Bunun için;

```bash
$ touch devops.txt && exit
```

* Artık `testvolume2` içinde sadece 1 adet dosya var. Şimdi bunu dolu bir klasöre mount etmeyi deneyelim.
    - **Her ikiside dolu ise Volume baskındır. Klasörde Volume ‘de hangi dosyalar varsa sadece o dosyalar görünür.**
Bunun için;

```bash
$ docker run --rm -it -v testvolume2:/usr/techpro techprodevops/test_app sh

cd usr/techpro
ls
devops.txt
```

* Artık  `test_volume` içinde ne varsa `:/usr/techpro` klasöründe sadece o görünür ve erişilebilir! Gordugunuz gibi volume daha baskin cikti arkadaslar

- Remove all volumes.

```bash
docker volume prune -f
```