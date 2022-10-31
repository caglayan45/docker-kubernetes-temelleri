# DOCKER VE KUBERNETES TEMELLERİ

Merhaba, bu repo Docker ve Kubernetes'e giriş düzeyinde temel kavramları, temel komutları ve örnek uygulamaları içermektedir.


# Docker

#### Docker Nedir?
Docker en net tanımlamayla open source bir ‘container’ teknolojisidir. Aynı işletim sistemi üzerinde, yüzlerce hatta binlerce birbirinden izole ve bağımsız containerlar sayesinde sanallaştırma sağlayan bir teknolojidir. Web uygulamalarımızın kolayca kurulumunu, testini, çalışmasını ve deploymentını sağlar. Bunun yanında sunucu maliyetlerini önemli ölçüde azaltır.


#### Docker Daemon Nedir?
Hypervisor’ün dockerdaki karşılığıdır. Bütün CPU, RAM vb. gibi işletim sistemine ait işlerin yapıldığı bölümdür.

#### Docker Container Nedir?
Docker Daemon tarafından Linux çekirdeği içerisinde birbirinden izole olarak çalıştırılan process’lerin her birine verilen isimdir. Virtual Machine (Sanal Makina) analojisinde Docker’ı Hypervisor’e benzetirsek fiziksel sunucu üzerinde halihazırda koşturulmakta olan her bir işletim sisteminin (sanal sunucunun) Docker’daki karşılığı Container’dır.

#### Docker Image Nedir?
Containerlar layer halindeki Image’lardan oluşur. Docker Image ise containerlara kurulacak ve run edilecek olan uygulamaların veya OS’lerin image dosyalarıdır. Örnek verecek olursak mysql, mongodb, redis, ubuntu, mariadb.. Yüzlercesi mevcut. [DockerHub]'dan imageleri görebilirsiniz.

## 1-Temel Komutlar


Tüm docker image’lerini listeler
>docker images

Container’ı istenilen isim ile ayağa kaldırır
>docker run -it —name [containerName] [imageName]

Container’ı istenilen isim ve istenilen port bağdaştırarak ayağa kaldırır
>docker run -it -p 8080:80 —name [containerName] [imageName]

Çalışan container’ları listeler
>docker ps

Tüm containerları listeler
>docker ps -a

Tüm container’ların sadece container id bilgisini listeler
>docker ps -aq

İsmi ya da id bilgisi yazılmış olan conatiner’ı çalıştırır
>docker start [containerName / containerId]

Tüm container’ları çalıştırır
>docker start $(docker ps -aq)

İsmi ya da id bilgisi yazılmış olan conatiner’ı durdurur
>docker stop [containerName / containerId]

Tüm container’ları durdurur
>docker stop $(docker ps -aq)

Container’ı siler
>docker rm [containerName]

Image’i siler
>docker rmi [imageName]

Tüm image’leri siler
>docker rmi $(docker images -aq)

Tüm container’ları siler
>docker rm $(docker ps -aq)

Komut çalıştırıldığı anda hiçbir container tarafından kullanılmayan image’leri siler
>docker system prune -a

Çalışan container’ın içerisine girmemizi sağlar
>docker exec -it [containerName] bash

## 2-Dockerfile

#### Dockerfile Nedir?

Dockerfile, basit bir metin dosyasıdır. Uygulamaların image'ini oluşturmak için bu dosya kullanılır. **docker build** komutu ile bu dosya içerisindeki Instruction’lar (komutlar) Docker Daemon tarafından okunup adım adım çalıştırılarak image oluşturulur. Bu dosya içerisindeki her bir komut satırı bir katmana karşılık gelmektedir. Image'ler de Dockerfile’ın içerisindeki komut satırlarından yani katmanlardan oluşur. Dockerfile nasıl hazırlanır, temel olarak kullanılan komutlar nelerdir inceyelim.

Container’ın base image’i yazılır, baz alınacak image.
>FROM [imageName]

Image'i oluşturan kişinin adını ya da mail adresini yazmak için kullanılır.
>MAINTAINER [name]

Container içerisinde kurulması ya da çalıştırılması gereken şeyler olduğunda bu kısımda yazılır, genellikle yazılım paketlerini indirmek için kullanılır
>RUN [linux komutu]

Host üzerindeki dosyaları veya klasörleri kaynak adresinden imaj sistemindeki hedef adrese kopyalanması sağlar.
>COPY [from_path_want_to_copy] [to_path_inside_container_for_paste]

Container’ın çalıştıracağı dizin yazılır, Container içerisinde belirtilen bu dizine geçiş yapılır. Eğer belirtilen dizin mevcut değilse öncelikle oluşturulur sonrasında bu dizine geçiş yapılır.
>WORKDIR [path_to_main_dir]

Container çalıştığında hangi portu dinlemesi gerektiğini bu komut aracağıyla belirtiriz.
>EXPOSE [portNumber]

Image çalıştırıldığında bu komut aracığıyla spesifik olarak verilen komutları ve parametreleri kullanarak yaşam döngüsüne devam eder.
>ENTRYPOINT ["related_run_command", "path_file_for_run"]

Environment Variable (Değişken) oluşturmak için kullanılan bir komuttur. Key – Value şeklinde kullanılır, iki çeşit kullanımı vardır.
>ENV key=value
ENV key value

Örnek Dockerfile:
>FROM alpine
RUN apk add -update nodes nodejs-npm
COPY . /src
WORKDIR /src
EXPOSE 8080
ENTRYPOUNT [“node”, “./app.js”]

## 3-Volumes
Volume’lar lokaldeki klasörü, container içerisindeki bir mantıksal klasör ile eşler. Container’larda kalıcı dosya saklamaya yarar.

Volume oluşturur
>docker create volume [volumeName]

Volume’ları listeler
>docker volume ls

Volume ile ilgili bilgi verir
>docker volume inspect [volumeName]

Volume siler
>docker rm [volumeName]

Oluşturulan volume docker run komutunda verilebilir 
Örnek run komutları:
>docker run -d —name testContainerName -v testVolumeName:/app testContainerName:latest

>docker run -d —name testContainerName -v d:/testLocalFolderForVolume:/app testContainerName:latest

>docker run -d —name testContainerName -v /Users/sahabt/testFolderForVolume:/app testContainerName:latest

## 4-Docker Compose Komutları

Birden fazla container tanımlama ve çalıştırmaya yarayan bir tool. YAML formatında yazılır.
NOT 1: bazen docker compose bazen ise docker-compose olarak kullanımı vardır.
NOT 2: up ile ayağa kaldırdığımız compose yaml’ı bir proje olarak tutulur.
NOT 3: bu başlık altında bahsedilen container'lar ve image'ler ilgili docker compose dosyası içerisindeki image'ler ve container'lardır.
NOT 4: bu komutlar docker compose yaml dosyasının bulunduğu dizinde çalıştırılmalıdır.

İmage’leri build eder
>docker compose build

Conatiner’ları başlatır/çalıştırır.
>docker compose start

Container’ları durdurur.
>docker compose stop

Container’ları build edip çalıştırır.
>docker compose up -d

Container’ları rebuild eder ve çalıştırır.
>docker compose up -d —build

Çalışan container’ları gösterir.
>docker compose ps

Container’ları siler.
>docker compose rm

Container’ları durdurur ve siler.
>docker compose down

Container’ların logunu gösterir.
>docker compose logs

Container içerisine girer.
>docker compose exec [containerName/containerId] bash

Çalışan projeleri (daha önce çalıştırılmış docker compose projelerini) gösterir
>docker compose ls

Aynı compose (port kullanımı varsa değiştirilmeli) yeni version (projenin yeni versiyonu) olarak yayınlanabilir. Ardından docker compose ls ile yeni isimle yayınlanan yeni versiyon da proje olarak görülebilir.
>docker compose -p [newVersionName] up -d

Yayınlanan yeni versiyon ismiyle birlikte down edilebilir.
>docker compose -p [versionName] down

## 5-Docker Compose YAML Komutları

Servislerin yazılacağı kısmın başlığıdır.
>services:

Services altında bulunan servislerin adıdır.
>test_app_name:

Pull edilecek image bu kısımda verilir. (Servisin altında)
>image: mariadb:10.6.4-focal

Container (servis) oluşmadan önce çalışacak komutlar yazılır
>command: npm install

Sunucu ya da container’ların koştuğu makine restart olduğunda ya da kapatılıp açıldığında servislerin tekrar çalıştırılmasını ya da çalışmamasını belirlediğimiz kısımdır.
Aynı zamanda hataya düştüğünde tekrar çalıştırılsın ya da durdurulmadığı sürece çalışsın şeklinde seçenekler de mevcut.
>restart: always
restart: no
restart: on-failure
restart: unless-stopped

Alt kısımda belirtilen servis üzerinde çalışmasını gerektiğini belirtir, önce belirtilen servis ayağa kalkar.
>depends_on:
   - db

Değişken tanımlayabildiğimiz, değer atayabildiğimiz ve compose ile servisleri ayağa kaldırırken “docker compose up -d -e DEBUG=0“ şeklinde dışarıdan parametre geçebilmemizi sağlar.
>environment:
  - DEBUG=1
  - TESTENV=testValue

Servisin çalışacağı portun ve dışarıdan ulaşılacak portun belirlediği kısım (sol taraftaki dışarıdan ulaşacağımız port, sağ taraftaki de container'ın uygulamasının çalışacağı port numarasıdır).
>ports:
  - “8080:80”

2 kısımda kullanılabilir, bir servis altında ilgili servisin hangi network’de çalışması isteniyorsa ilgili network adı verilerek, bir de services altında network’leri tanımlamak amacıyla
Servis altında kullanımı;
>networks:
  - frontend

Services altında kullanımı (network tanımlama);
>networks:
  frontend:
  backend:

2 kısımda kullanılabilir, bir servis altında ilgili serviste bir volume ataması (mapping) yapılmak isteniyorsa ilgili volume adı verilerek, bir de services altında volume’leri tanımlamak amacıyla
Servis altında kullanımı;
>volumes:(lokal yol belirterek volume ataması)
  - ./db:/etc/data
volumes:(tanımlanmış bir volume ataması)
  - db-data:/etc/data

Services altında kullanımı (volume tanımlama);
>volumes:
  db-data:

## 6-Contariner Registry ve DockerHub

####Container Registry Nedir?

Container'lar için merkezi depodur (central repositories).
Private ya da Public olarak kullanılabilir.
Varsayılan depo DockerHub'dır.
AWS, GCP ve Azure, Container Registry'i servis olarak sunar.

İlk olarak konsoldan docker hub'a login olmak gerekir
>docker login -u [username] -p [password]

Önceden build edilmiş projeye etiket ekleme
>docker tag [tagName] [username]/[imageName]:latest

Docker Hub'a image pushlama
>docker push [username]/[imageName]:latest

Docker Hub'dan image çekme
>docker pull [imageName]:latest

Docker Hub Örnek Pushlama;
>docker build -t csancaktar/express:v1 .
docker push csancaktar/express:v1
docker rmi csancaktar/express:v1
docker pull csancaktar/express:v1

# Kubernetes (K8s)

Sıkça "buluta yönelik işletim sistemi" olarak tanımlanan Kubernetes, containerize edilmiş uygulama ve hizmet kümelerini yönetmek amacıyla tasarlanmış, açık kaynaklı bir platformdur. Google tarafından GO dilinde geliştirilmiş Cloud Native Computing Foundation tarafından desteklenen mevcut konteyner haline getirilmiş uygulamalarınızı otomatik deploy etmek, sayılarını arttırıp azaltmak gibi işlemler ile birlikte yönetmenizi sağlayan bir Konteyner kümeleme (container cluster) aracıdır. Master (Control Plane) ve Nodes (Workers) yapısı vardır. 

## Cluster


Cluster, belirli bir konfigürasyon ile yapılandırılmış olan, aynı amaç doğrultusunda beraber ya da yedekli şekilde çalışan sunucu kümelerine verilen isimdir. Kubernetes'in en büyük bileşenidir. Node'lar birliği olarak da ifade edilebilir. Bundan anlaşıldığı üzere Node'lar (Master ve Workers) Cluster içerisinde yer alır.

## Master Node


Cluster'ın kontrol merkezi olarak düşünülebilir. Tüm cluster’ı yönetmekten, izlemekten, değişiklikler yapmaktan ve oluşan olaylara cevap vermekten sorumludur. Nodelar ise esas işi yapan bileşenlerdir. Yaptıkları işleri, durumlarını masterlara sürekli olarak iletirler. Master Node ile api server (kube-apiserver) üzerinden haberleşilir. Restful API JSON ile manifest(konfigürasyon) dosyaları Master Node’a iletilir. Birçok bileşenden oluşur, bunlar; kube-apiserver, etcd, kube-controller-manager ve kube-scheduler'dır.

### kube-apiserver

Kubernetes'in beyni olarak bilinir. 443 portunda çalışır. **kubectl** komutu ile kullanılabilir. 

### Cluster Store (etcd)

Kubernetes'in hafızası olarak bilinir. NoSQL yapıda veritabanı olarak kullanılır. Key value yapısıyla çalışır. Cluster'ın durumu ve konfigürasyonları burada saklanır. 

### kube-controller-manager

Bu bileşen kubernetes sistemine verilen manifest dosyası ile desired state dediğimiz istenilen durumu korumak ve tüm konfigurasyonu istenilen durumda tutmaktan sorumludur (node sayıları, Enpointler, dns vs.). Hemen her fonksiyon/işlem için bir controller bulunabilir (endpoints, namespaces, replication vs.), desired state bilgileri cluster store’da (etcd) saklanır, herhangi bir durumda bu hedeflenen yapıdan uzaklaşılırsa (örnek olarak 1 makine terminate olma durumu vb.) kontrolör içerisinde bulunan desired state yapısı bunu tespit edip terminate olan makineyi otomatik olarak oluşturmaktadır.

### kube-scheduler

Organizatör de denebilir. Bu servis işlemlerin zamanlaması için kullanılır ve nodelara görevler atar. Belirlenen pod’un hangi node üzerinde çalıştırılacağına karar verme mekanizmasıdır. Kubelet’i harekete geçirerek, ilgili olan pod ve içerisinde yer alan konteyner oluşturulmuş olur.

## Worker Nodes


Cluster'da yer alan gerçek makinelere verilen isimdir. Kubernetes ekosisteminde iş yüklerinin çalıştığı yerlerdir. Master Node'dan gelen direktiflere göre işlemleri gerçekleştirirler. Kubelet, Container engine ve kube-proxy (load balancer) bileşenleri worker'lar üzerinde çalışır. 

### kubelet

Kubelet bir anlamda Node'un kendisidir. Node'u bir agent olarak da düşünebiliriz. Node kubernetes cluster’a kubelet ile üye olur. Podların başlatılmasından kubelet sorumludur. Ve node içerisindeki olaylar için Master’a bilgilerin iletilmesinden sorumludur.

### Container Engine

Container engine container yönetiminden sorumludur. Containerların başlatılması, durdurulması, container image'lerinin alınması vb. işlemleri yapar. Kubernetes içerisinde iki adet container engine desteklenir, rkt ve Docker. Genellikle docker tercih edilir.

### kube-proxy (load balancer)

Bu bileşen kubernetes network’ünden sorumludur. Pod IP adreslerini yönetir, load balancingden sorumludur. Kubernetes'de pod içerisindeki tüm container'lar tek IP yi paylaşırlar.

## Pod

Kubernetes sisteminde ki en atomik computing birimi Podlardır. Bunları sanallaştırma ekosisteminde ki sanal makinalar, docker ekosisteminde ki containerlar olarak düşünebilirsiniz. Nodelar ve Masterlar podları host etmek ve organize etmek için vardır. Docker için container ne ise K8s için Pod odur. Her Pod’un bir IP’si vardır. K8s Pod’ların içinde Container’lar çalıştırır. 1 Pod yalnızca 1 Node üzerinde çalışabilir.

<u>Pod Durumları (Pod State);</u>
- **Pending:** Pod, Kubernetes kümesi tarafından kabul edildi, ancak bir veya daha fazla container kurulmadı ve çalışmaya hazır hale getirilmedi.
- **Running:** Pod bir düğüme bağlandı ve tüm containerlar oluşturuldu. Çalışıyor.
- **Succeeded:** Pod'daki tüm containerlar başarıyla sonlandırıldı ve yeniden başlatılmayacak.
- **Failed:** Pod'daki tüm containerlar sonlandırıldı ve en az bir adet container başarısızlıkla sonlandı. Yani container ya sıfırdan farklı bir durumla çıktı ya da sistem tarafından sonlandırıldı.
- **Unknown:** Bazı nedenlerden dolayı Pod'un durumu elde edilemedi. Bu aşama tipik olarak, Pod'un çalışması gereken Node ile iletişimdeki bir hata nedeniyle oluşur. İletişim hatası da denebilir.
- **CrashLoopBackOff:** Pod tekrar tekrar çalışıp durduğunda bir süre sonra bu hataya düşer (Pod'un durumu CrashLoopBackOff olur).

### Pod Komutları

YAML dosyası hazırlanmış bir Pod yaratır
>kubectl creafe -f [podName.yml/podName.yaml]

İsmi ve image'i yazılan pod'u çalıştırır, ve 3600 sanite uyutur
>kubectl run [podName] --image=[imageName] -- /bin/sh -c "sleep 3600"

Pod’ları namespace bilgileriyle beraber listeler
>kubectl get pods -A

İsmi yazılan pod'un yaml dosyasını son parametrede yolu ve adı bulunan yaml dosyasına kaydeder (export işlemi).
>kubectl get pod [podName] -o yaml > file.yaml

Yazılan namespace altındaki podları listeler
>kubectl get pods -n [namespaceName]

Pod’ların bilgilerini listeler
>kubectl get pods -o wide

İsmi yazılan pod hakkında bilgiler getirir
>kubectl describe pod [podName]

İsmi yazılan podu siler
>kubectl delete pod [podName]

İsmi yazılan pod'un içerisine girer(sh ya da bash)
>kubectl exec -it [podName] -- sh/bash

## Init Containers

Declarative yaml içerisinde bulunur, Pod'lar oluşmadan önce init containerlar oluşur ve Pod'ların ayağa kalkabilmesi için gerekli bağlantı testi vb. kontrolleri sağlarlar. [Init Containers] içerisinde örnek kullanımı mevcuttur.

## Selectors

Key-Value yapısıyla çalışır. Objelerinizi ya da kaynaklarınızı gruplamak ve tanımlamak için onlara etiket (label) ataması yapmanızı sağlar. Örnek kullanıma [Selectors]'den ulaşılabilir.

## Multi Containers Pods

Pod'lar içerisinde birden fazla container kullanımı da mevcuttur. [Multi Containers] kısmında örnek kullanım mevcuttur.

## ReplicaSet

Pod'ların replica larını oluşturur (yaml içerisinde 3 replica bilgisi verildiyse, aynı anda Pod'dan 3 adet oluşur), herhangi bir pod crash olup ya da herhangi bir sebepten dolayı durduğunda, silindiğinde replicaset sayesinde K8S tarafından otomatik olarak yeniden oluşturulur.

ReplicaSet yaratır
>kubectl apply -f [definition.yaml]

ReplicaSet'leri gösterir
>kubectl get rs

ReplicaSet hakkında bilgi verir
>kubectl describe rs [replicaSetName]

Silme komutu, birisi yaml adı parametresiyle diğeri de replicaSet name parametresiyle
>kubectl delete -f [definition.yaml]
kubectl delete rs [replicaSetName]

## Deployments

Deployment ReplicaSet’in yanında rolling update yapma imkanı veren ilgili podu ve replica sayılarını belirttiğimiz bir Kubernetes objesidir. Deployment oluşturduğunda, arka planda replicaset oluşturur fakat bu replicaset'lerle direkt olarak etkileşimde bulunulamaz. Birden fazla ReplicaSet kullanabildiği için de rolling update yapma şansına sahiptir. Aynı şekilde bir önceki versiyona rollback yapma imkanı da verir. Her Mikroservis için bir deployment oluşturulur. Örnek olarak 3 replica’lı bir deploymenti 5 replicaya da çıkartabiliriz ya da 2 replicaya da düşürebiliriz. Hatta çeşitli metrikler dinleyerek auto-scaling yeteneği de kazandırabiliriz.
Rolling update için örnek kullanım [RollingUpdate] kısmında verilmiştir.
Blue-Green deployment senaryosu için örnek kullanım [Blue-Green Deployment] kısmında verilmiştir.

Imperative ve Declarative deploy oluşturma
>kubectl create deploy [deploymentName] --image=[imageName] --replicas=3 --port=80
kubectl apply -f [definition.yaml]

Deployment'ları listeler
>kubectl get deploy

İsmi yazılan deployment hakkında bilgi verir
>kubectl describe deploy [deploymentName]

Replicaset'leri listeler
>kubectl get rs

Deployment'a yapılan güncellemenin durumunu gösterir
>kubectl rollout status

İsmi yazılan deployment'ın geçmiş bilgilerini getirir. (geçmiş updateler vb.)
>kubectl rollout history deployment [deploymentName]

İsmi yazılan deployment'a yapılan update'i geri almak için kullanılır
>kubectl rollout undo [deploymentName]

İsmi yazılan deployment'ı parametre olarak geçilen revizyona geri almak için kullanılır
>kubectl rollout undo [deploymentName] --to-revision=[revision]

Deploymentları yaml ismi ya da deployment ismi yazarak kaldırır
>kubectl delete -f [definition.yaml]
kubectl delete deploy [deploymentName]

## DeamonSet

DeamonSet'ler tanımlandığında yaml'da belirtilen podu, tüm node'larda ya da selector ile seçilmiş belirli node'larda ayağa kaldırır, bu selector'e dahil olacak şekilde yeni bir node eklendiğinde bu pod otomatik olarak orada da ayağa kalkacaktır. Tolerans ile master node harici tüm nodlarda çalış şeklinde konfigürasyon verilebilir. Örnek kullanımı [DeamonSet] kısmında mevcuttur.


## StatefulSet

Örnek kullanım [StatefulSet] kısmında verilmiştir.

## Job ve CronJob

Job bir ya da birden fazla Pod'un belirli bir işi belirli bir tekrarda yapacağı işlemler Job olarak tanımlanır. Örnek kullanım [Job] kısmında verilmiştir.

CronJob ise periyodik bir şekilde çalışacak, yapılacak işlemler için kullanılır. Örnek kullanım [CronJob] kısmında verilmiştir.

## Services

Servisler ClusterIP, NodePort ve LoadBalancer olarak kullanılırlar. Pod'lara erişme yoludur. Pod'ların IP'si güvenilmez ve sağlıksızdır fakat servislerin IP leri tam tersi güvenilir ve sağlıklı çalışır. Pod'ların aksine statik ip adresleri, statik DNS isimleri vardır. **[serviceName].[namespace].svc.cluster.local** ile ulaşılabilir.

### ClusterIP
ClusterIP K8S'de varsayılan servistir. Cluster içerisinden erişim sağlanabilir, dışarıdan erişim sağlanamaz. Başka bir pod önce ClusterIP'ye ulaşır, ClusterIP'nin selector'ler ya da label'lar ile eşleştiği podlara ClusterIP üzerinden erişim sağlar. ClusterIP tanımlama aşamasında port ve targetPort olarak 2 adet port bilgisi girilir. Port, servisin dinlediği (çalıştığı) port bilgisini içerir, targetPort ise eşleştiği Pod'ların container portlarıyla aynı olmalı (targetPort üzerinden podlara erişim sağlanmaktadır). Örnek kullanım [ClusterIP] kısmında verilmiştir.

### NodePort
NodePort ClusterIP'den türetilmiştir. ClusterIP'ye göre daha fonksiyoneldir. Cluster içerisinden ve dışarıdan erişim sağlanabilir. ClusterIP'deki gibi port ve targetPort bilgileri vardır bunlara ekstra olarak nodePort ve protocol bilgisi de tanımlamak gerekir. NodePort bilgisi cluster dışından erişim için kullanılacak port bilgisini içermelidir. Statik olarak tanımlanmadığı takdirde K8S otomatik olarak bir tanımlama gerçekleştirir. 30000 ile 32767 arasında tanımlanmalıdır. Node'ların public ip adresleri olmak zorundadır. Node IP + nodePort ile servise dışarıdan erişim sağlanabilir. Örnek kullanım [NodePort] kısmında verilmiştir.

Bir podu dışarıya açmak için servis oluşturur
>kubectl expose po [podName] --port=[portNumber] --target-port=[targetPortNumber] --type=NodePort

Bir deployment'ı dışarıya açmak için servis oluşturur
>kubectl expose deploy [deployName] --port=[portNumber] --target-port=[targetPortNumber] --type=NodePort --name frontend

İsmi yazılan yaml dosyasındaki servisi deploy eder
>kubectl apply -f [definition.yaml]

Servislerin listesini getirir
>kubectl get svc

Servislerin listesini ekstra bilgilerle getirir
>kubectl get svc -o wide

Servis hakkında bilgi verir
>kubectl describe svc [serviceName]

Yaml dosyasından deploy edilmiş servisi siler
>kubectl delete -f [definition.yaml]

İsmi yazılan servisi siler
>kubectl delete svc [serviceName]

### LoadBalancer
LoadBalancer aslında bulut sürümlerine has bir servis. Örnek kullanım [LoadBalancer] kısmında verilmiştir.

## Volumes

Persistant Volume (**PV**):
Kaynaklar (dosya ve klasörleriniz) için depo sunar. Cluster tarafında kullanılabilir. 

Persistant Volume Claim (**PVC**):
PVC'ler PV'ler ile bire bir eşleşirler, PV talep ederler. Bir ya da daha fazla pod PVC'leri kullanabilir. Pod'lar içerisindeki tüm container'lar aynı volume'u paylaşırlar.

Birçok PV plugini vardır:
[PV Plugin'leri(Görsel)](/getting-started/docker-kubernetes-temelleri/Assets/pvPlugins.png)

Sarı ile üstü çizili olan HostPath (PV) tek bir node tarafından test amaçlı kullanılır, dosyaları herhangi bir bulut ortamını kullanmadan lokal ortamda saklar.

PV'lerde Recalim Policy özelliği dikkat edilmesi gereken önemli kısımlardandır. **Delete** olarak seçildiğinde, volume serbest bırakıldığında ya da Pod'lar silindiğinde volume içerisindeki verilerinizi de siler. Varsayılan olarak delete gelmektedir. **Retain** olarak seçildiğinde ise Pod'lar silinse de verileriniz saklanmaya devam eder.

Dinamik olarak tanımlanmak istendiğinde sarı ile üstü çizili olmayanlardan (cloud providerlardan) birisi ile çalışılabilir. Statik yöntemde 1 volume 1 node olarak test amaçlı kullanılabiliyorken, dinamik yöntemde bir volume'u birden fazla node ve pod kullanabilir. Dinamik yöntemde PV olarak StorageClass (**SC**) kullanılır.

<u>Volume'lerde erişim modları da mevcuttur;</u>
**ReadWriteMany:** bir ve birden fazla pod tarafından okuma ve yazma yetkisidir.
**ReadOnlyMany:** bir ve birden fazla pod tarafından okuma yetkisidir.
**ReadWriteOnce:** yalnızca bir pod tarafından okuma ve yazma, diğer podlar için sadece okuma yetkisidir.

<u>PV durumları;</u>
**Available:** Claim atanmamış ve kullanılmayan bir volume.
**Bound:** Claim ile eşleştirilmiş diğer kullanımlara kapalı bir volume.
**Released:** Claim'i kaldırılmış ve tekrar claim atanmamış bir volume.
**Failed:** Claim atamada ya da herhangi bir kısımda hata almış bir volume.

Örnek volume kullanımı [Volumes] kısmında verilmiştir.

PV,SC ve PVC Cheat Sheet;
PV,SC ve PVC tanımlamak için kullanılır
>kubectl applf -f [definition.yaml]

PV'leri listeler
>kubectl get pv

SC'leri listeler
>kubectl get sc

PVC'leri listeler
>kubectl get pvc

İsmi yazılan PV hakkında bilgi verir
>kubectl describe pv [pvName]

İsmi yazılan SC hakkında bilgi verir
>kubectl describe sc [scName]

İsmi yazılan PVC hakkında bilgi verir
>kubectl describe pvc [pvcName]

Yaml dosyası ismi yazılan PV,SC ya da PVC'leri siler
>kubectl delete -f [definition.yaml]

İsmi yazılan PV'yi siler
>kubectl delete pv [pvName]

İsmi yazılan SC'yi siler
>kubectl delete sc [scName]

İsmi yazılan PVC'yi siler
>kubectl delete pvc [pvcName]

## ConfigMaps

Configmap, uygulamalarımızda kullandığımız konfigürasyonların dışarıda bir noktaya konularak tek noktadan kolayca yönetilmesine olanak sağlayan bir kubernetes objesidir. Key-Value yapısıyla çalışır. Yaml dosyaları ya da text dosyaları olarak ya da dosyalama (klasörler ve dosyalar) sistemi olarak oluşturulabilir. ConfigMap dosyalarında, herhangi bir değişiklik yapıldığında, container'lar restart edilene kadar değişikikler uygulanmaz. Örnek volume kullanımı [ConfigMaps] kısmında verilmiştir.

Imperative yol ile configmap oluşturma
>kubectl create configmap literal-example --from-literal="city=Istanbul" --from-literal=state=Kadıköy

Declarative yol ile configmap oluşturma
>kubectl apply -f [cm.yaml]

Dosyadan çekerek configmap oluşturma
>kubectl create cm [name] --from-file=myconfig.txt

Klasörden çekerek configmap oluşturma
>kubectl create cm [name] --from-file=config/

Configmap'leri listeler
>kubectl get cm

İsmi yazılan configmap'i bir yaml dosyasına kaydeder
>kubectl get cm [name] -o YAML

İsmi yazılan configmap'i siler
>kubectl delete -f [cm.yaml]

## Secrets

Parola, kullanıcı adı, token gibi bilgileri güvenli bir şekilde depolayacağınız alan Secret’tır. Burada depoladığımız bilgilere verdiğimiz isimle uygulamamız içinde kullanabiliriz. Secret ConfigMaps'e benzer ancak özel olarak gizli verileri tutmayı amaçlar. Hem ConfigMaps hem de Secret, verileri bir anahtar değer (key, value) çifti olarak depolar. En önemli fark, Secret’ın verileri base64 biçiminde depolamasıdır, ConfigMaps'in ise verileri düz metin olarak depolamasıdır. Anahtarlar, parolalar, hizmet hesapları kimlik bilgileri, db bağlantı dizesi vb. gibi bazı kritik verileriniz varsa, her zaman Secret’ı tercih etmelisiniz. Örnek volume kullanımı [Secrets] kısmında verilmiştir.

Imperative yol ile secret oluşturma
>kubectl create secret generic [secretName] --from-literal=STATE=Kadıköy

Declarative yol ile secret oluşturma
>kubectl apply -f [secret.yaml]

Secret'ları listeler
>kubectl get secrets

İsmi yazılan secret'ı getirir
>kubectl get secrets [secretName] -o YAML

İsmi yazılan ya da yaml dosyası yazılan secret'ı siler
>kubectl delete -f [secret.yaml]
kubectl delete secrets [secretName]

## Probes

Podların  durumlarını takip edebilmek için kullanılan yapı.

<u>**Startup Probe;**</u>
Pod'un içindeki Container'daki uygulamaların başarılı bir şekilde çalışıp çalışmadığını kontrol eder.

<u>**Readiness Probe;**</u>
Pod, request almaya, bir işlem yapmaya hazır mı? Pod'a trafik akabilecek mi? Kontrollerini sağlar.

<u>**Liveness Probe;**</u>
Pod sağlıklı çalışıyor mu? Pod beklendiği gibi çalışıyor mu? Kontrollerini sağlar. Örnek kullanım [Liveness Probe] kısmında verilmiştir.

Probe'ların **Success**, **Failure** ve **Unknown** olmak üzere 3 return'ü vardır.

<u>Probe'ların 3 yöntemi vardır;</u>
**Execaction:** Container içinde bir aksiyon alma, bir execution gerçekleştirme
**Tcpsocketraction:** Container'ın IP ve port bilgileri kullanılarak TCP kontrolü gerçekleştirme
**Httpgetaction:** Container'a http isteği gerçekleştirme

[DockerHub]: <https://hub.docker.com/search?q=&type=image>
[Init Containers]: <https://github.com/caglayan45/docker-kubernetes-temelleri/tree/main/Init%20Containers>
[Selectors]: <https://github.com/caglayan45/docker-kubernetes-temelleri/tree/main/Selectors>
[Multi Containers]: <https://github.com/caglayan45/docker-kubernetes-temelleri/tree/main/Multi%20Containers%20Pods>
[DeamonSet]: <https://github.com/caglayan45/docker-kubernetes-temelleri/tree/main/DaemonSet>
[StatefulSet]: <https://github.com/caglayan45/docker-kubernetes-temelleri/tree/main/StatefulSet>
[Job]: <https://github.com/caglayan45/docker-kubernetes-temelleri/tree/main/Job>
[CronJob]: <https://github.com/caglayan45/docker-kubernetes-temelleri/tree/main/CronJob>
[RollingUpdate]: <https://github.com/caglayan45/docker-kubernetes-temelleri/tree/main/Rolling%20Updates>
[Blue-Green Deployment]: <https://github.com/caglayan45/docker-kubernetes-temelleri/tree/main/Blue-Green%20Deployments>
[ClusterIP]: <https://github.com/caglayan45/docker-kubernetes-temelleri/tree/main/ClusterIP>
[NodePort]: <https://github.com/caglayan45/docker-kubernetes-temelleri/tree/main/NodePort>
[LoadBalancer]: <https://github.com/caglayan45/docker-kubernetes-temelleri/tree/main/Load%20Balancer>
[Volumes]: <https://github.com/caglayan45/docker-kubernetes-temelleri/tree/main/Volumes>
[ConfigMaps]: <https://github.com/caglayan45/docker-kubernetes-temelleri/tree/main/ConfigMaps>
[Secrets]: <https://github.com/caglayan45/docker-kubernetes-temelleri/tree/main/Secrets>
[Liveness Probe]: <https://github.com/caglayan45/docker-kubernetes-temelleri/tree/main/LivenessProbe>