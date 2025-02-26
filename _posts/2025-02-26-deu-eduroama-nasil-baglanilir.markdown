---
layout: post
title:  "Arch Linux'da DEU Eduroam'a Nasıl Bağlanılır"
---

Arch'da wifi ağlarına bağlanmak bazen çok uğraştırıcı bir iş olabiliyor. 
NetworkManager veya iwctl üzerinde biraz yetkinlikle çoğu ağa kolayca bağlanmak mümkün.
Ama eduroamlara bağlanmak normal ağlara bağlanmaktan biraz daha zor, çünkü <em>802.1X kimlik doğrulama</em> adı verilen bir algoritma kullanıyorlar.
Bu blogda, belki başkaları benim kadar uğraşmak zorunda kalmaz diye size kendi Arch setupımda nasıl okulumun eduroamına bağlandığımı anlatacağım.

Ben Arch linux ve Awesome WM kullanıyorum, network için ise de NetworkManager ve iwd kullanıyorum. Os'iniz farketmeksizin son ikisini kullanarak eduroama bağlanabilirsiniz.
Öncelikle okulun sunduğu <u>[(CAT)yapılandırma yardımcı aracı](https://cat.eduroam.org/)</u>'nı indirmeniz gerekiyor. Normalde bu yazılımın bağlanabilmemiz için bütün yapılandırmayı yapması gerekiyor, ama benim durumumda tek başına yeterli değildi. 

Bu programı çalıştırdıktan sonra 802.1X kimlik doğrulama yüzünden bir *wpa_supplicant* dosyası yazmamız gerekiyor.
Sıradaki iş, /var/lib/iwd/eduroam.8021x konumuna bir dosya oluşturun ve aşağıdaki yazıyı kendi bilgilerinizle yapıştırın. 
```
[Security]
EAP-Method=TTLS
EAP-Identity=<isim-soyisim>@ogr.deu.edu.tr
EAP-Anonymous-Identity=anonymous@deu.edu.tr
EAP-TTLS-Phase2-Method=Tunneled-PAP
EAP-TTLS-Phase2-Identity=<isim-soyisim>@ogr.deu.edu.tr
EAP-TTLS-Phase2-Password=<şifre>
EAP-TTLS-CACert=/var/lib/iwd/eduroam.pem
```
Çalıştırdığımız eduroam yükleyici ~/.config/cat_installer/ca.pem konumuna bir sertifika yerleştiriyor. Eğer iwd'yi root olarak çalıştırıyorsanız bu dosyayı bir root konumuna kopyalamanız gerekiyor. Aşağıdaki komut bu işe yarıyor. 
```
sudo cp ~/.config/cat_installer/ca.pem /var/lib/iwd/eduroam.pem
```
Sonunda NetworkManager ve iwd'yi yaptığımız değişiklikleri görmek için yeniden başlatmalıyız. 
```
sudo systemctl restart NetworkManager
sudo systemctl restart iwd 
```
Ve bu kadardı! Şimdi nmcli veya nmtui kullanarak eduroam'a bağlanabiliriz.

Eğer bağlanırken herhangi bir hata yaşadıysanız, her zaman iwd journal'a hatayı bulmak için bakabilirsiniz. 
```
journalctl -u iwd --no-pager | tail -50
```
iwd veya eduroam hakkında daha çok okumak istiyorsanız, <u>[wiki](https://wiki.archlinux.org/title/Iwd)</u>'yi ziyaret edebilirsiniz.

