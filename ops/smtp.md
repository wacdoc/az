Konfiqurasiya anbarına ops.soft daxil edin, `./ssl.sh` proqramını işə salın və **yuxarı kataloqda** `conf` qovluğu yaradılacaq.

## preambula

SMTP birbaşa bulud satıcılarından xidmətləri ala bilər, məsələn:

* [Amazon SES SMTP](https://docs.aws.amazon.com/ses/latest/dg/send-email-smtp.html)
* [Əli bulud e-poçt push](https://www.alibabacloud.com/help/directmail/latest/three-mail-sending-methods)

Siz həmçinin öz poçt serverinizi yarada bilərsiniz - limitsiz göndərmə, aşağı ümumi xərc.

Aşağıda biz öz poçt serverimizi necə quracağımızı addım-addım nümayiş etdiririk.

## Server seçimi

Öz-özünə yerləşdirilən SMTP serveri 25, 456 və 587 portları açıq olan ictimai IP tələb edir.

Tez-tez istifadə olunan ictimai buludlar bu portları defolt olaraq bloklayıb və iş əmri verməklə onları açmaq mümkün ola bilər, lakin bu, çox əziyyətlidir.

Bu portları açıq olan və əks domen adlarının qurulmasını dəstəkləyən hostdan satın almağı məsləhət görürəm.

Burada [Contabo-nu](https://contabo.com) tövsiyə edirəm.

Contabo, 2003-cü ildə çox rəqabətli qiymətlərlə qurulan Almaniyanın Münhen şəhərində yerləşən hosting provayderidir.

Satınalma valyutası kimi Avro seçsəniz, qiymət daha ucuz olacaq (8 GB yaddaş və 4 CPU-ya malik bir server ildə təxminən 529 yuana başa gəlir və ilkin quraşdırma haqqı bir il ərzində pulsuzdur).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/UoAQkwY.webp)

Sifariş verərkən `prefer AMD` verin və AMD CPU-lu server daha yaxşı performansa sahib olacaq.

Aşağıda öz poçt serverinizi necə quracağınızı nümayiş etdirmək üçün Contabo-nun VPS-ni nümunə götürəcəyəm.

## Ubuntu sistem konfiqurasiyası

Buradakı əməliyyat sistemi Ubuntu 22.04-dür

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/smpIu1F.webp)

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/m7Mwjwr.webp)

ssh-dəki server `Welcome to TinyCore 13!` (aşağıdakı şəkildə göstərildiyi kimi), bu, sistemin hələ quraşdırılmadığını bildirir. Lütfən, ssh-ni ayırın və yenidən daxil olmaq üçün bir neçə dəqiqə gözləyin.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/-qKACz9.webp)

`Welcome to Ubuntu 22.04.1 LTS` göründükdə, başlatma tamamlandı və siz aşağıdakı addımlarla davam edə bilərsiniz.

### [Könüllü] İnkişaf mühitini işə salın

Bu addım isteğe bağlıdır.

Rahatlıq üçün ubuntu proqramının quraşdırılmasını və sistem konfiqurasiyasını [github.com/wactax/ops.os/tree/main/ubuntu](https://github.com/wactax/ops.os/tree/main/ubuntu) ünvanına qoyuram.

Bir kliklə quraşdırmaq üçün aşağıdakı əmri işə salın.

```
bash <(curl -s https://raw.githubusercontent.com/wactax/ops.os/main/ubuntu/boot.sh)
```

Çinli istifadəçilər, lütfən, əvəzinə aşağıdakı əmrdən istifadə edin və dil, saat qurşağı və s. avtomatik olaraq təyin olunacaq.

```
CN=1 bash <(curl -s https://ghproxy.com/https://raw.githubusercontent.com/wactax/ops.os/main/ubuntu/boot.sh)
```

### Contabo IPV6-nı aktivləşdirir

SMTP də IPV6 ünvanları ilə e-poçt göndərə bilməsi üçün IPV6-nı aktiv edin.

redaktə edin `/etc/sysctl.conf`

Aşağıdakı sətirləri dəyişdirin və ya əlavə edin

```
net.ipv6.conf.all.disable_ipv6 = 0
net.ipv6.conf.default.disable_ipv6 = 0
net.ipv6.conf.lo.disable_ipv6 = 0
```

[Contabo təlimatını izləyin: IPv6 bağlantısının serverinizə əlavə edilməsi](https://contabo.com/blog/adding-ipv6-connectivity-to-your-server/)

`/etc/netplan/01-netcfg.yaml` redaktə edin, aşağıdakı şəkildə göstərildiyi kimi bir neçə sətir əlavə edin (Contabo VPS defolt konfiqurasiya faylında artıq bu xətlər var, sadəcə şərhdən çıxarın).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/5MEi41I.webp)

Sonra dəyişdirilmiş konfiqurasiyanın qüvvəyə minməsi üçün `netplan apply` .

Konfiqurasiya uğurlu olduqdan sonra xarici şəbəkənizin ipv6 ünvanına baxmaq üçün `curl 6.ipw.cn` istifadə edə bilərsiniz.

## Konfiqurasiya depo əməliyyatlarını klonlayın

```
git clone https://github.com/wactax/ops.soft.git
```

## Domen adınız üçün pulsuz SSL sertifikatı yaradın

Poçt göndərmək şifrələmə və imzalamaq üçün SSL sertifikatı tələb edir.

Sertifikatlar yaratmaq üçün [acme.sh-](https://github.com/acmesh-official/acme.sh) dən istifadə edirik.

acme.sh açıq mənbəli avtomatlaşdırılmış sertifikat imzalama vasitəsidir,

Konfiqurasiya anbarına ops.soft daxil edin, `./ssl.sh` proqramını işə salın və **yuxarı kataloqda** `conf` qovluğu yaradılacaq.

[Acme.sh dnsapi](https://github.com/acmesh-official/acme.sh/wiki/dnsapi) saytından DNS provayderinizi tapın, `conf/conf.sh` redaktə edin.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/Qjq1C1i.webp)

Sonra domen adınız üçün `123.com` və `*.123.com` sertifikatları yaratmaq üçün `./ssl.sh 123.com` işə salın.

İlk qaçış avtomatik olaraq [acme.sh-i](https://github.com/acmesh-official/acme.sh) quraşdıracaq və avtomatik yeniləmə üçün planlaşdırılmış tapşırıq əlavə edəcək. Siz `crontab -l` görə bilərsiniz, aşağıdakı kimi belə bir xətt var.

```
52 0 * * * "/mnt/www/.acme.sh"/acme.sh --cron --home "/mnt/www/.acme.sh" > /dev/null
```

Yaradılan sertifikat üçün yol `/mnt/www/.acme.sh/123.com_ecc。`

Sertifikat yenilənməsi `conf/reload/123.com.sh` skriptini çağıracaq, bu skripti redaktə edəcək, əlaqədar proqramların sertifikat keşini yeniləmək üçün `nginx -s reload` kimi əmrlər əlavə edə bilərsiniz.

## Chasquid ilə SMTP server qurun

[chasquid](https://github.com/albertito/chasquid) Go dilində yazılmış açıq mənbəli SMTP serveridir.

Postfix və Sendmail kimi qədim poçt server proqramlarını əvəz edən chasquid daha sadə və istifadəsi asandır və ikinci dərəcəli inkişaf üçün də daha asandır.

Çalışın `./chasquid/init.sh 123.com` bir kliklə avtomatik quraşdırılacaq (123.com-u göndərən domen adı ilə əvəz edin).

## E-poçt İmza DKIM-i konfiqurasiya edin

DKIM məktubların spam kimi qəbul edilməsinin qarşısını almaq üçün e-poçt imzaları göndərmək üçün istifadə olunur.

Komanda müvəffəqiyyətlə işlədikdən sonra sizdən DKIM qeydini təyin etmək istəniləcək (aşağıda göstərildiyi kimi).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/LJWGsmI.webp)

Sadəcə DNS-ə TXT qeydini əlavə edin (aşağıda göstərildiyi kimi).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/0szKWqV.webp)

## Xidmət statusuna və qeydlərə baxın

 `systemctl status chasquid` Xidmət statusuna baxın.

Normal işləmə vəziyyəti aşağıdakı şəkildə göstərildiyi kimidir

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/CAwyY4E.webp)

 `grep chasquid /var/log/syslog` və ya `journalctl -xeu chasquid` xəta qeydinə baxa bilər.

## Ters domen adı konfiqurasiyası

Əks domen adı IP ünvanının müvafiq domen adına həll edilməsinə imkan verməkdir.

Əks domen adının təyin edilməsi e-poçtların spam kimi müəyyən edilməsinin qarşısını ala bilər.

Məktub qəbul edildikdə, qəbul edən server göndərən serverin etibarlı əks domen adına malik olub-olmadığını təsdiqləmək üçün göndərən serverin IP ünvanında əks domen adının təhlilini aparacaq.

Göndərən serverin əks domen adı yoxdursa və ya əks domen adı göndərən serverin IP ünvanı ilə uyğun gəlmirsə, qəbul edən server e-poçtu spam kimi tanıya və ya onu rədd edə bilər.

[https://my.contabo.com/rdns](https://my.contabo.com/rdns) saytına daxil olun və aşağıda göstərildiyi kimi konfiqurasiya edin

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/IIPdBk_.webp)

Əks domen adını təyin etdikdən sonra, domen adının ipv4 və ipv6-nın serverə irəli həllini konfiqurasiya etməyi unutmayın.

## chasquid.conf host adını redaktə edin

`conf/chasquid/chasquid.conf` əks domen adının dəyərinə dəyişdirin.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/6Fw4SQi.webp)

Sonra xidməti yenidən başlatmaq üçün `systemctl restart chasquid` işə salın.

## Git deposuna ehtiyat nüsxəsini çıxarın

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/Fier9uv.webp)

Məsələn, mən conf qovluğunu öz github prosesimə aşağıdakı kimi yedəkləyirəm

Əvvəlcə şəxsi anbar yaradın

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/ZSCT1t1.webp)

conf kataloqunu daxil edin və anbara təqdim edin

```
git init
git add .
git commit -m "init"
git branch -M main
git remote add origin git@github.com:wac-tax-key/conf.git
git push -u origin main
```

## Göndərən əlavə edin

qaçmaq

```
chasquid-util user-add i@wac.tax
```

Göndərən əlavə edə bilərsiniz

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/khHjLof.webp)

### Parolun düzgün qurulduğunu yoxlayın

```
chasquid-util authenticate i@wac.tax --password=xxxxxxx
```

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/e92JHXq.webp)

İstifadəçi əlavə edildikdən sonra `chasquid/domains/wac.tax/users` yenilənəcək, onu anbara təqdim etməyi unutmayın.

## DNS SPF qeydini əlavə edir

SPF (Sender Policy Framework) e-poçt saxtakarlığının qarşısını almaq üçün istifadə edilən e-poçt yoxlama texnologiyasıdır.

Göndərənin IP ünvanının iddia etdiyi domen adının DNS qeydlərinə uyğunluğunu yoxlayaraq poçt göndərən şəxsin kimliyini yoxlayır və fırıldaqçıların saxta e-poçtlar göndərməsinin qarşısını alır.

SPF qeydlərinin əlavə edilməsi e-poçtların spam kimi müəyyən edilməsinin mümkün qədər qarşısını ala bilər.

Domen adı serveriniz SPF növünü dəstəkləmirsə, sadəcə TXT tipli qeyd əlavə edin.

Məsələn, `wac.tax` -ın SPF-si aşağıdakı kimidir

`v=spf1 a mx include:_spf.wac.tax include:_spf.google.com ~all`

`_spf.wac.tax` üçün SPF

`v=spf1 a:smtp.wac.tax ~all`

Nəzərə alın ki, mən bura `include:_spf.google.com` , çünki mən `i@wac.tax` ünvanını daha sonra Google poçt qutusunda göndərmə ünvanı kimi konfiqurasiya edəcəyəm.

## DNS konfiqurasiyası DMARC

DMARC (Domain-based Message Authentication, Reporting & Conformance) qısaltmasıdır.

O, SPF sıçrayışlarını ələ keçirmək üçün istifadə olunur (bəlkə də konfiqurasiya xətaları nəticəsində yaranır və ya başqası spam göndərmək üçün özünü sizin kimi göstərir).

TXT qeydini əlavə edin `_dmarc` ,

Məzmun aşağıdakı kimidir

```
v=DMARC1; p=quarantine; fo=1; ruf=mailto:ruf@wac.tax; rua=mailto:rua@wac.tax
```

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/k44P7O3.webp)

Hər bir parametrin mənası aşağıdakı kimidir

### p (Siyasət)

SPF (Göndərən Siyasət Çərçivəsi) və ya DKIM (DomainKeys Identified Mail) yoxlamasından keçməyən e-poçtların necə idarə olunacağını göstərir. p parametri üç dəyərdən birinə təyin edilə bilər:

* heç biri: Heç bir tədbir görülmür, yalnız yoxlama nəticəsi e-poçt hesabat mexanizmi vasitəsilə göndərənə qaytarılır.
* Karantin: Doğrulamadan keçməmiş məktubu spam qovluğuna qoyun, lakin məktubu birbaşa rədd etməyəcək.
* rədd edin: Doğrulama uğursuz olan e-poçtları birbaşa rədd edin.

### fo (Uğursuzluq Seçimləri)

Hesabat mexanizmi tərəfindən qaytarılan məlumatların miqdarını müəyyən edir. Aşağıdakı dəyərlərdən birinə təyin edilə bilər:

* 0: Bütün mesajlar üçün doğrulama nəticələrini bildirin
* 1: Yalnız yoxlanılmayan mesajları bildirin
* d: Yalnız domen adının yoxlanılması uğursuzluqlarını bildirin
* s: yalnız SPF doğrulama uğursuzluqlarını bildirin
* l: Yalnız DKIM doğrulama uğursuzluqlarını bildirin

### rua & ruf

* rua (Məcmu hesabatlar üçün Hesabat URI): Ümumi hesabatların qəbulu üçün e-poçt ünvanı
* ruf (Məhkəmə hesabatları üçün hesabat URI): ətraflı hesabatları almaq üçün e-poçt ünvanı

## E-poçtları Google Mail-ə yönləndirmək üçün MX qeydləri əlavə edin

Universal ünvanları dəstəkləyən pulsuz korporativ poçt qutusu tapa bilmədiyim üçün (Catch-All, bu domen adına göndərilən istənilən e-poçtu, prefikslərə məhdudiyyət olmadan qəbul edə bilər), bütün e-poçtları Gmail poçt qutuma yönləndirmək üçün chasquid-dən istifadə etdim.

**Öz pullu biznes poçt qutunuz varsa, lütfən, MX-də dəyişiklik etməyin və bu addımı atlayın.**

`conf/chasquid/domains/wac.tax/aliases` redaktə edin, yönləndirmə poçt qutusunu təyin edin

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/OBDl2gw.webp)

`*` bütün e-poçtları göstərir, `i` yuxarıda yaradılmış göndərən istifadəçinin e-poçt ünvanı prefiksidir. Poçtu yönləndirmək üçün hər bir istifadəçi bir xətt əlavə etməlidir.

Sonra MX qeydini əlavə edin (aşağıdakı şəkildəki birinci sətirdə göstərildiyi kimi burada birbaşa əks domen adının ünvanını göstərirəm).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/7__KrU8.webp)

Konfiqurasiya tamamlandıqdan sonra siz Gmail-də e-poçt qəbul edə bilməyəcəyinizi öyrənmək üçün `i@wac.tax` və `any123@wac.tax` ünvanlarına e-poçt göndərmək üçün digər e-poçt ünvanlarından istifadə edə bilərsiniz.

Əgər yoxsa, chasquid jurnalını yoxlayın ( `grep chasquid /var/log/syslog` ).

## Google Mail ilə i@wac.tax ünvanına e-poçt göndərin

Google Mail məktubu aldıqdan sonra mən təbii olaraq i.wac.tax@gmail.com əvəzinə `i@wac.tax` ilə cavab yazmağa ümid etdim.

[https://mail.google.com/mail/u/1/#settings/accounts](https://mail.google.com/mail/u/1/#settings/accounts) saytına daxil olun və "Başqa e-poçt ünvanı əlavə edin" üzərinə klikləyin.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/PAvyE3C.webp)

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/_OgLsPT.webp)

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/XIUf6Dc.webp)

Sonra, göndərilən e-poçta gələn doğrulama kodunu daxil edin.

Nəhayət, o, defolt göndərici ünvanı kimi təyin edilə bilər (eyni ünvanla cavab vermək seçimi ilə birlikdə).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/a95dO60.webp)

Bu yolla biz SMTP poçt serverinin yaradılmasını tamamladıq və eyni zamanda e-poçt göndərmək və qəbul etmək üçün Google Mail-dən istifadə etdik.

## Konfiqurasiyanın uğurlu olub olmadığını yoxlamaq üçün test e-poçtu göndərin

`ops/chasquid` daxil edin

`direnv allow` (direnv əvvəlki bir düyməli işə salma prosesində quraşdırılıb və qabığa çəngəl əlavə edilib)

sonra qaç

```
user=i@wac.tax pass=xxxx to=iuser.link@gmail.com ./sendmail.coffee
```

Parametrlərin mənası aşağıdakı kimidir

* istifadəçi: SMTP istifadəçi adı
* keçid: SMTP parolu
* üçün: alıcı

Test e-poçtu göndərə bilərsiniz.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/ae1iWyM.webp)

Konfiqurasiyaların uğurlu olub-olmadığını yoxlamaq üçün test e-poçtlarını almaq üçün Gmail-dən istifadə etmək tövsiyə olunur.

### TLS standart şifrələmə

Aşağıdakı şəkildə göstərildiyi kimi, bu kiçik kilid var, yəni SSL sertifikatı uğurla aktivləşdirilib.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/SrdbAwh.webp)

Sonra "Orijinal E-poçtu göstər" düyməsini basın.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/qQQsdxg.webp)

### DKIM

Aşağıdakı şəkildə göstərildiyi kimi, Gmail orijinal poçt səhifəsində DKIM göstərilir, bu da DKIM konfiqurasiyasının uğurlu olduğunu bildirir.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/an6aXK6.webp)

Orijinal e-poçtun başlığında Qəbul edilənləri yoxlayın və siz görə bilərsiniz ki, göndərən ünvan IPV6-dır, yəni IPV6 da uğurla konfiqurasiya olunub.
