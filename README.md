## Unreal Engine 5 - Third Person Shooter Projesi
# Proje Adı: "Ezel Taşı"
> Bu proje, Unreal Engine 5 kullanılarak geliştirilmiş, hikaye anlatımı odaklı bir TPS (Third-Person Shooter) prototipidir. Proje, modüler silah ve sağlık sistemleri, davranış ağacı (Behavior Tree) tabanlı yapay zeka ve gelişmiş animasyon katmanlama tekniklerini içermektedir.
> Kocaeli Üniversitesi/Yazılım Geliştirme Laboratuvarı I dersi kapsamında geliştirilmiştir.*
## Oyunun Hikayesi ve Seviye Tasarımı:

*Oyunda oyuncuyu yönlendirmek ve atmosferi geliştirebilmek amacıyla replik ve trigger kullanımına başvurulmuştur.*
*Oyun başlangıcında oyuna başla ekranı sonrası başlayan sinematik ardından karakteri görürüz ve karakter, yerde yatan cesedi gördükten sonra "oradaki bizimkilerden mi" diyalogu oynar.*
*Oyuncu merkez bölgeye girince yerde birkaç kafa görür ve bu bölümde triggerlar ile replik girer ve tüfek sesi gelir*
*Oyuncu sesin geldiği tarafa gidince 3 tane düşman npc görür ve savaşır.* *Ardından oyuncu, yolun götürdüğü yerde bir değirmene doğru yol alır.* *Oyuncu, değirmenin arkasında düşmanlar olduğunu görünce onlarla da savaşır.*  *Bu bölgede karakterin ve de düşman npc'lerin bölgeye geliş amacı olan "Ezel Taşı" parlak bir şekilde yer alır: "Ezel Taşı'na sahip olan zamanın ve mekanın efendisi olur.* *Kimi efendi geleceği kendi çıkarları için düzenler, kimi ise geçmiş pişmanlıklarını.* *Ancak Ezel Taşı'nı kullanmanın bedeli anılardır.* *Her kullanımda anıların, sonunda benliğinin silinmesine yol açar."* *Oyuncu, düşmanları temizledikten sonra buraya asıl geliş amacı Ezel Taşını onlardan alır ve "gitme zamanı geldi" diyaloğu girer.* *Ardından bir beyaz ekran girer ve "sonunda annemi tekar görebileceğim" diyolağu repliği ile oyun biter.*

---
---


---
---

## 📖 İçindekiler

* [1. Proje Özeti](#1-proje-özeti)
* [2. Literatür Taraması ve Karşılaştırma](#2-literatür-taraması-ve-karşılaştırma)
* [3. Yazılımsal Mimariler ve Teknikler](#3-yazılımsal-mimariler-ve-teknikler)
    * [3.1. Bileşen Tabanlı (Component-Based) Mimari](#31-bileşen-tabanlı-component-based-mimari)
    * [3.2. Nesne Yönelimli Programlama (OOP) - Kalıtım](#32-nesne-yönelimli-programlama-oop---kalıtım)
    * [3.3. Yapay Zeka Mimarisi: Davranış Ağacı (BT/BB)](#33-yapay-zeka-mimarisi-davranış-ağacı-btbb)
    * [3.4. Olay-Yönetimli (Event-Driven) Programlama](#34-olay-yönetimli-event-driven-programlama)
* [4. Sistem Şemaları ve Blok Diyagramları](#4-sistem-şemaları-ve-blok-diyagramları)
    * [4.1. Blok Diyagramı: Silah Alma (Pickup) Sistemi](#41-blok-diyagramı-silah-alma-pickup-sistemi)
* [5. Tasarlanan Arayüz (UMG) Sayfaları](#5-tasarlanan-arayüz-umg-sayfaları)
* [6. Seviye Tasarımı ve Oynanış Akışı](#6-seviye-tasarımı-ve-oynanış-akışı)
* [7. Karşılaşılan Zorluklar ve Çözümler](#7-karşılaşılan-zorluklar-ve-çözümler)
* [8. Projenin Katkıları](#8-projenin-katkıları)
* [9. Yararlanılan Kaynaklar](#8-projenin-katkıları)
* [10. Kullanılan Assetler](#8-projenin-katkıları)
---

## 1. Proje Özeti

Proje, Unreal Engine 5'in default TPS şablonu üzerine inşa edilmiştir. Temel mekanikler arasında *Bileşen Tabanlı* bir silah sistemi (toplama, değiştirme, nişan alma), LineTrace tabanlı ateş etme, tam ve yarı otomatik atış modları, şarjör yenileme (Reload) ve çömelme (Crouch) bulunmaktadır.

Sistem, BP_ThirdPersonCharacter üzerinde merkezi bir mantıkla yönetilir. Animasyonlar (ABP_Manny), Layered Blend per Bone ve Transform (Modify) Bone teknikleri kullanılarak hem Locomotion (hareket) hem de üst vücut silah animasyonlarını (nişan alma, duruş) aynı anda harmanlayacak şekilde geliştirilmiştir.

Projenin temel farkı, **modüler BPC_Health sağlık bileşeni** ve **Behavior Tree (Davranış Ağacı)** ile yönetilen yapay zeka düşman (BP_Enemy) sistemidir. Hikaye anlatımı, TriggerBox'lar ile tetiklenen diyaloglar ve çevresel ipuçları (notlar) aracılığıyla sağlanır.

## 2. Literatür Taraması ve Karşılaştırma

Proje geliştirilirken sektördeki yaygın sistem mimarileri incelenmiş ve bu mimarilerle karşılaştırmalar yapılmıştır.

* *Silah Sistemleri: (Aktör vs. Bileşen)*
    * *Literatür:* Çoğu temel çalışma, özellikle eski olanlar silahları ayrı bir Actor olarak spawn edip karaktere Attach etme yöntemini kullanır. Bu, basit olsa da silah ile karakter arasındaki iletişimi (Casting) zorlaştırır ve performans maliyeti yaratır.
    * *Çalışmamız:* Projemiz, Pitchfork Academy'nin (2024) *Bileşen Tabanlı (Component-Based)* mimariyi benimser. Silahlar (BP_WeaponMaster), bir Actor değil, SkeletalMeshComponent'ten türetilmiş bir *Bileşen*'dir. Bu sayede, silah doğrudan karakterin bir parçası olur, animasyon ve mantık entegrasyonu kolaylaşır. Silahın dünyadaki hali (BP_PickupMaster) ise ayrı bir Actor olarak tasarlanmış, böylece sorumluluklar net bir şekilde ayrılmıştır.

* *Sağlık Sistemleri: (Doğrudan vs. Bileşen)*
    * *Literatür:* Yaygın yaklaşım, sağlık değişkenlerini (Health) ve hasar alma fonksiyonlarını doğrudan BP_Character ve BP_Enemy gibi ana sınıflara yazmaktır. Bu, kod tekrarına (PlayerHealth, EnemyHealth) ve bakım zorluğuna yol açar.
    * *Çalışmamız (Modüler Yaklaşım):* Projemizde **BPC_Health** adında bir *Blueprint Component* (Bileşen) tasarlanmıştır. Tüm hasar alma (HandleTakeAnyDamage), sağlık kontrolü ve ölüm olayı (OnDied Event Dispatcher) mantığı bu bileşendedir. Bu bileşen, hem BP_ThirdPersonCharacter'a hem de BP_Enemy'e eklenmiştir dolayısıyla tek sistemle iki farklı BP Sınıfı beslenmiştir. Bu sayede, sağlık mantığı tek bir yerden yönetilir ve yeniden kullanılabilirlik sağlanmıştır.

* *Yapay Zeka Mimarisi: (FSM vs. BT)*
    * *Literatür:* Basit yapay zekalar genellikle bir Enum (Durum Değişkeni) ve Switch nodları kullanılarak sonlu durum makineleri (Finite State Machine -FSM) ile yönetilir. Bu, durum sayısı arttıkça "Blueprint Spagetti" adı verilen karmaşık bir yapıya dönüşür.
    * *Çalışmamız (Standart):* Projemiz, endüstri standardı olan *Behavior Tree (Davranış Ağacı)* mimarisini kullanır. AI_Enemy_Controller (Beyin), BB_Enemy (Hafıza - Blackboard) ve BT_Enemy (Mantık - Behavior Tree) üçlüsü ile net bir sorumluluk ayrımı yapılmıştır. Bu, "Has Target" (Hedef Varsa) ve "Has No Target" (Hedef Yoksa) gibi karmaşık dallanmaların ve MoveToLastKnownLocation gibi taktiksel davranışların görsel ve yönetilebilir bir hiyerarşi ile tasarlanmasını sağlamıştır.

## 3. Yazılımsal Mimariler ve Teknikler

### 3.1. Bileşen Tabanlı (Component-Based) Mimari

Projenin en temel mimari kararıdır.
* **BPC_Health:** Sağlık sistemi, bir bileşen olarak tasarlanmıştır. Bu sayede bir Actor'un "hasar alabilir" olması için ona bu bileşeni eklemek yeterlidir.
* **BP_WeaponMaster:** Silahlar, SkeletalMeshComponent türevli bir bileşen olarak tasarlanmıştır. Bu, karakterin "silah kullanabilir" olmasını sağlar.

### 3.2. Nesne Yönelimli Programlama (OOP) - Kalıtım

Kod tekrarını önlemek için kalıtım (Inheritance) ilkesi benimsenmiştir:
* BP_PickupMaster (Ana Sınıf) → BP_WeaponPickup → BP_Pistol_A_Pickup / BP_Rifle_A_Pickup
* BP_WeaponMaster (Ana Sınıf) → BP_Pistol_A / BP_Rifle_A

### 3.3. Yapay Zeka Mimarisi: Davranış Ağacı (BT/BB)

Yapay zeka, üç ana parçadan oluşur:
1.  **AI_Enemy_Controller (Beyin):** BP_Enemy Actor'una atandığında, "Run Behavior Tree" komutu ile BT_Enemy'i çalıştırır.
2.  **BB_Enemy (Hafıza):** LastKnownLocation, StartLocation, TargetLocation, Target, hasTarget?  gibi AI'ın karar vermek için ihtiyaç duyduğu dinamik verileri tutar.
3.  **BT_Enemy (Mantık):** "Has Target" (Hedef Varsa) veya "Has No Target" (Hedef Yoksa) durumlarına göre dallanan mantıksal akıştır. Hedef varsa, mesafeyi kontrol eder (Check Distance), ateş eder (Shoot Player) ve oyuncunun son bilinen konumuna (MoveToLastKnownLocation) koşar.

### 3.4. Olay Yönetimli Programlama

Sistemler arası bağımlılığı azaltmak için Event Dispatcher'lar kullanılmıştır.
* *Örnek: Ölüm Mekaniği*
    1.  BPC_Health, sağlık 0 olduğunda CallOnDied isimli bir Event Dispatcher'ı çağırır.
    2.  BP_ThirdPersonCharacter, EventBeginPlay'de kendi sağlık bileşeninin CallOnDied olayına Bind Event (Olayı Bağla) yapar.
    3.  Sağlık bileşeni "öldüm" sinyalini yolladığında, karakter bu sinyali alır ve kendi ölüm mantığını (Ragdoll, Oyunu Yeniden Başlat vb.) tetikler.
    * Bu sayede sağlık bileşeninin, sahibinin (Player/AI) ne yapacağını bilmesine gerek kalmaz.

## 4. Sistem Şemaları ve Blok Diyagramları

----

### 4.1 Yerden Silah Alma Diyagramı
![Sistem Şeması](./Weapon%20Pickup%20Blok%20Diyagramı.drawio.png)

----


## 5. Tasarlanan Arayüz (UI) Sayfaları

Proje, oyuncu deneyimini desteklemek için aşağıdaki UI   Widget'larını kullanır:

* **WBP_Pickup_Interaction:** Oyuncu toplanabilir bir silahın (Pickup) kapsül alanına girdiğinde ekranda beliren ve 'E' tuşuna basılı tutulması gerektiğini belirten arayüz.
* **WBP_Crosshair:** Nişangah. BP_ThirdPersonCharacter içindeki "Aiming" (Nişan Alma) durumuna göre boyutu dinamik olarak değişir (küçülür/büyür).
* **WBP_Healthbar:** Oyuncunun can barı. BPC_Health bileşenindeki mevcut sağlığa bağlanmıştır.
* **WBP_AmmoCount:** Oyuncu bir silah aldığında beliren ve mevcut mermi/şarjör bilgisini gösteren arayüz.
* **WBP_AlertIcon:** AI (BP_Enemy), oyuncuyu fark ettiğinde (Alerted State) başında beliren ünlem (!) işareti widget'ı.
* **WBP_GameEnd (Hikaye):** Oyunun sonunda "Tebrikler" mesajını gösteren arayüz.

## 6. Seviye Tasarımı ve Oynanış Akışı

Oyun, mümkün olduğunca Low-Poly olan harita asset'inin özelleştirilmesiyle oluşturulan bir level'da geçmektedir. Hikaye anlatımı ve atmosfer, **ortam müziği** ve **TriggerBox**'lar ile tetiklenen diyaloglarla desteklenmiştir.



## 7. Karşılaşılan Zorluklar ve Çözümler

* *Problem 1: "Moonwalk" Sorunu*
    * *Sorun:* Karakter yürürken aynı anda silah tutma (Idle) animasyonunu oynatmaya çalıştığında, üst vücut pozu alt vücudun yürüme animasyonunu bozuyor ve karakter "Moonwalk" (geriye kayma) yapıyordu.
    * *Çözüm:* ABP_Manny animasyon grafiğinde Layered Blend per Bone düğümü kullanıldı. "Locomotion" (Idle/Walk/Run) sonucu ana poza bağlandı, silah duruşlarını içeren State Machine'ler ise "Upperbody" (üst vücut) kemik maskesi kullanılarak bu katmana harmanlandı.

* *Problem 2: Gelişmiş Nişan Alma (Aim Offset)*
    * *Sorun:* Nişan alma (Aim Offset) için Blendspace kullanmak, elde hazır blendspace bulunmaması ve her yön için el ile animasyon yapılması gerekti ve farklı silah duruşları (Pistol vs Rifle) ile birleştiğinde karmaşık ve yönetilemez bir hal aldı.
    * *Çözüm:* Blendspace'ler yerine, ABP_Manny'nin AnimGraph'ında Transform (Modify) Bone düğümü kullanıldı. Bu düğüm, karakterin nişan alma açısına göre (Kontrolcüden gelen veri) "Spine_03" (omurga) kemiğini prosedürel ve anlık olarak döndürerek dinamik bir nişan alma efekti yarattı.

* *Problem 3: Modüler Olmayan Sağlık Sistemi*
    * *Sorun:* Başlangıçta sağlık mantığı hem BP_ThirdPersonCharacter hem de BP_Enemy için ayrı ayrı yazılmıştı. Bu, büyük kod tekrarına ve karışıklığına yol açtı.
    * *Çözüm:* Tüm sağlık mantığı (HandleTakeAnyDamage, OnDied Dispatcher) BPC_Health adında tek bir *Blueprint Component*'e taşındı. Bu bileşen, hem oyuncuya hem de düşmana eklenerek sorun çözüldü.

* *Problem 4: Doğrusal Hikayede Yapay Zeka Devriyesi*
    * *Sorun:* BT_Enemy için Patrol (Devriye) mantığı yazılmıştı, ancak oyunun hikayesi stealth'ten çok arama-yok etme gibi daha doğrusal bir yapıya sahip olduğundan devriye gezen düşmanlar hikaye akışını ve planlamayı karıştırıyordu.
    * *Çözüm:* BT_Enemy'deki Patrol (Has No Target) dalı, hikaye gereği kasıtlı olarak kullanılmadı. Düşmanlar, seviyeye StartLocation'larına manuel olarak yerleştirildi ve BT_Service ile oyuncuyu gördüklerinde doğrudan LastKnownLocation'a saldırmaları sağlandı.

## 8. Projenin Katkıları

Bu proje, bir tutorial'ı temel almanın ötesinde, sistemleri modülerleştirmeyi ve entegre etmeyi öğretmiştir.
* *Teknik Kazanımlar:*
    * Blueprint Component (BPC_Health) kullanarak modüler ve yeniden kullanılabilir sistemler tasarlama.
    * Unreal Engine'in Behavior Tree (BT) ve Blackboard (BB) kullanarak yapay zeka mimarisi kurma.
    * Layered Blend per Bone ve Transform (Modify) Bone kullanarak karmaşık animasyon katmanlama (Animation Blending) sorunlarını çözme.
    * Event Dispatcher'lar aracılığıyla "Olay-Yönetimli" (Event-Driven) programlama mantığını kavrayarak sistemler arası bağımlılığı (Coupling) azaltma.
* *Tasarım Kazanımları:*
    * TriggerBox'lar ve ses ipuçları kullanarak çevresel hikaye anlatımı (Environmental Storytelling) tekniklerini uygulama.
    * Oyuncuyu yönlendiren ve taktiksel seçimler sunan (Vantage Point) seviye tasarımı temellerini atma.
    * Interaction olayını eski sistem blueprinlterdeki gibi tuşa atama yerine yeni IA_Interaction-IMC_Default bağlantılarıyla daha güvenli ve modüler kurmayı sağlama



## 9. Yararlanılan Kaynaklar:

* https://www.youtube.com/watch?v=H_Q57Yso9mM&t=19s [PitchforkAcademy - Youtube] - Modüler Silah Sistemi
* https://www.youtube.com/watch?v=Ez2xf0SkI5M&t=262s [Tic Toxic Studios] - NPC AI Karakter Takip
* https://forums.unrealengine.com/t/this-blueprint-self-is-not-a-playercontroller-therefore-target-must-have-a-connection/1955961
* https://dev.epicgames.com/documentation/en-us/unreal-engine/unreal-engine-build-tool-target-reference

## 10. Kullanılan Assetler:

* https://www.fab.com/listings/ced19ea1-31ed-437f-ae64-2b6b1561fede
* https://www.fab.com/listings/721b3a24-3a32-4d56-becb-ebc73f48ccee
* Drive Zip'i WeaponDark ve WeaponSilver silah assetleri
      
