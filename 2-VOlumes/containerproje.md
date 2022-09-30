* Volumes'den geldik buraya

# Hands-on Docker-03_3 : Bind Mounts - Practice Website

## Learning Outcomes

-   Start a container as an nginx web server
-   Install a development environment from scratch

## Stage -1 

- For AWS EC2 add a security rule for protocol HTTP port 80 and show Nginx Web Server is running on Docker Machine.

```bash
# Acik olmasi gereken portlar
Custom TCP 8080 Anywhere
TCP 22 Anywhere
```

- Run the `nginx` container in detached mod, name it as `web_server`, and open <public-ip> on browser and this will bring the nginx default page.

```bash
docker run -d --name web_server --rm -p 8080:80 nginx
# -d yani detached modda calis
# -p 8080:80 yani portlari birbirine maruz birak?
# --rm yani container islemini tamamladiktan sonra sil
# nginx imaji kullanacagiz

Burada localhosta(http://localhost:8080/) girip Welcome to nginx! yazisini gormen lazim. Saniyeler icerisinde bir web sayfasini ayaga kaldirdik, iste dockerin guzelligi bu..
```

- Attach the `nginx` container, show the index.html in the /usr/share/nginx/html directory.

```bash
docker exec -it web_server bash # varolan container'inin icerisinde <bash> komutunu calistirmak icin exec; yani webserver in icerisine bash shell ile gir demis oluyoruz biryerde
# -it interactive mod idi hatirlarsak
root@312gthvrh4h5:/# cd /usr/share/nginx/html
root@312gthvrh4h5:/usr/share/nginx/html# ls
50x.html  index.html
root@312gthvrh4h5:/usr/share/nginx/html# cat index.html
# container'in icinden cikmadan devam
```


## Stage -2 
* Ek bilgi: (https://stackoverflow.com/questions/68775869/support-for-password-authentication-was-removed-please-use-a-personal-access-to)
- Share and clone the repository link as : https://github.com/talfik2/javascript-projects

    ```bash
    # container'dayiz
    mkdir website && cd website
    apt-get update -y
    apt-get upgrade -y
    apt-get install git
    git clone https://github.com/talfik2/javascript-projects
    # Git Hatirlatma Pratigi
    git init
    git add .
    git status
    git commit -m "Docker'daki commitimiz"
    git remote add origin https://github.com/<kendikullaniciadlari>/deneme.git
    git log
    git remote set-url origin https://<githubtoken>@github.com/<username>/<repositoryname>.git
    # alternatif olarak: git remote add origin https://github.com/talfik3/deneme.git
    git push --set-upstream origin master
    ls
    cd javascript-projects/reviews
    ls
    ```

- Burada ne yapmak istiyoruz? Icinde bulundugumuz directorydeki dosyalari nginx'in icerisine tasiyip daha tatli bir index.html gormek istiyoruz.

```bash
# Container'dan cikma
cp ./* /usr/share/nginx/html/
# Localhost:8080 i tekrar kontrol et
exit
```

## Stage -3 


- Remove all containers.

```bash
docker stop -f web_server
# As we run our container in detached mode, it is going to remove itself once it is not working
```
