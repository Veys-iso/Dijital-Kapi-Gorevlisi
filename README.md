# Dijital-Kapi-Gorevlisi
🛡️ Dijital Kapı Görevlisi: Snort 3 ile Akıllı Trafik Analizi (Hafta 11-12)
📋 Proje Geçmişi ve Amacı
Projemizin 11. haftasında, Kali Linux makinemizi bir Router (Geçit) gibi yapılandırarak Windows 10 sanal makinesinin trafiğini kendi üzerimizden geçirmeyi başardık. Geçen hafta tcpdump aracıyla bu trafiği sadece pasif olarak izlemiştik.

Bu haftaki hedefimiz (Hafta 12): Sadece izlemekle yetinmeyip, ağda olup bitenleri analiz eden ve belirli durumlarda bizi uyaran bir Saldırı Tespit Sistemi (IDS) kurmaktır. Bu amaçla siber güvenlik dünyasının en güçlü araçlarından biri olan Snort 3'ü projemize entegre ettik.

1️⃣ Snort Kuralının Yazılması (Alarm Sistemi Kurulumu)
Snort'a neye bakması gerektiğini söylemek için bir "imza" oluşturduk. Amacımız, ağdaki ICMP (Ping) paketlerini tespit edip özel bir alarm üretmekti.

Bash: sudo nano /etc/snort/rules/local.rules

Kural:  alert icmp any any -> any any (msg:"Dikkat: Birisi Ping Atıyor!"; sid:1000001; rev:1;)

Kuralın Detaylı Analizi:

alert: Şüpheli bir durum gördüğünde hem kaydet hem de ekrana uyarı bas.

icmp: Sadece Ping paketlerine odaklan.

any any -> any any: Hangi cihazdan gelirse gelsin ve hangi cihazı hedeflerse hedeflesin tüm trafiği tara.

msg: Alarm tetiklendiğinde ekranda yazacak olan özel mesajımız.

sid:1000001: Bu kuralın sisteme kayıtlı benzersiz kimlik numarasıdır.

rev:1: Kuralın sürüm (revizyon) numarasıdır.

2️⃣ Sistemin Canlı İzleme Modunda Çalıştırılması
Yazdığımız kuralın aktif olması için Snort 3'ü eth1 arayüzü (Windows 10'un bağlı olduğu kapı) üzerinden başlattık.

Bash
sudo snort -R /etc/snort/rules/local.rules -i eth1 --daq afpacket -A alert_fast                                                                              
Komutun Detaylı Analizi:

sudo: Ağ trafiğine kanca atmak için gereken en üst düzey yönetici yetkisi.

-R [yol]: Snort'a karmaşık dosyaları boşverip sadece bizim yazdığımız kural dosyasını okumasını söyler.

-i eth1: Dinlemenin yapılacağı fiziksel ağ kartını belirtir.

--daq afpacket: Paket yakalama motorunu "aktif" hale getirerek canlı trafiği analiz etmemizi sağlar.

-A alert_fast: Tespit edilen paketlerin anında terminal ekranına düşmesini sağlar.

3️⃣ Test ve Doğrulama (Windows 10 -> Kali)
Sistemi test etmek için Windows 10 makinemizden ağ geçidimiz olan 192.168.10.1 IP adresine sürekli bir ping trafiği başlattık.

Windows CMD: ping 192.168.10.1 -t

Sonuç: Windows üzerinden gönderilen her ping paketi, Kali Linux üzerindeki Snort IDS tarafından anında yakalanmış ve terminalde "Dikkat: Birisi Ping Atıyor!" uyarısı oluşturulmuştur.

<img width="1493" height="813" alt="Image" src="https://github.com/user-attachments/assets/fdd5f953-deac-4208-b2b5-a59f415f3e02" />
