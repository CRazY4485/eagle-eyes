# Eagle Eyes — mühit faktları

Rəsmi mənbələrdən yoxlanmış faktlar (yoxlama tarixi: 2026-09-23).

## MetaTrader VPS
- Miqrasiya ilə köçürülənlər: hesab bağlantısı, Market Watch simvolları, EA işləyən qrafiklər, onların parametrləri, icra faylları (EX5 və kitabxanalar), FTP/Email/Signals/Notifications parametrləri, WebRequest icazəsi və ünvan siyahısı. `MQL5\Files` faylları və terminalın qlobal dəyişənləri siyahıda **yoxdur**. — https://www.mql5.com/en/vps/rules (V. Migration)
- Texniki xidmət: həftəsonu (şənbə və bazar, EET vaxtı ilə) gündə cəmi 4 saata qədər, iş günləri lazım gələrsə gündə cəmi 15 dəqiqəyə qədər. — https://www.mql5.com/en/vps/rules (II. Hosters)
- Serverlər müstəqil hostinq şirkətlərinə məxsusdur. Rəsmi mətndə belə yazılıb: "makes no guarantees of the Virtual terminals' smooth operation" və "Possible malfunctions of a Hoster's hardware may lead to unexpected circumstances". Deməli, planlaşdırılmamış yenidən başlamalar da mümkündür. — https://www.mql5.com/en/vps/rules (I, VII)
- Sinxronizasiya bir istiqamətlidir (yerli terminaldan VPS-ə) və yalnız istifadəçinin sorğusu ilə aparılır. — https://www.metatrader5.com/en/terminal/help/virtual_hosting/virtual_hosting_migration
- Hostinq ləğv edilərsə köçürülmüş bütün məlumat bərpa imkanı olmadan silinir. — https://www.metatrader5.com/en/terminal/help/virtual_hosting/virtual_hosting_terminal

## MQL5
- Qlobal dəyişənlər son müraciətdən 4 həftə sonra silinir. `GlobalVariablesFlush` onları məcburi diskə yazır. — https://www.mql5.com/en/docs/globals
- `MqlTradeRequest.comment` üçün sənəddə yalnız "Order comment" yazılıb. Brokerin şərhi saxlayacağına zəmanət verilmir. — https://www.mql5.com/en/docs/constants/structures/mqltraderequest

- `FileMove(src, common_flag, dst, FILE_REWRITE)` mövcud faylın üzərinə köçürməyə imkan verir. Sənəd əməliyyatın atomik olduğunu **demir**. Fayllar yalnız sandbox (`MQL5\Files`) içində ola bilər. — https://www.mql5.com/en/docs/files/filemove

- `WebRequest` sinxrondur: "breaks the program execution and waits for the response". Yalnız EA və skriptlərdən çağırıla bilər, indikatordan yox. Strategy Tester-də işləmir. — https://www.mql5.com/en/docs/network/webrequest
- `SendNotification` "Network Functions" bölməsindədir. Mesaj ən çoxu 255 simvoldur; "no more than 2 calls per second and not more than 10 calls per minute"; hədd pozularsa funksiya söndürülə bilər. Strategy Tester-də işləmir. Sənəd funksiyanın bloklayıb-bloklamadığını **demir**, həddin proqrama, yoxsa terminala aid olduğunu da **demir**. Xəta kodları: 4515–4518. — https://www.mql5.com/en/docs/network/sendnotification

- Simvol xüsusiyyətləri: `SYMBOL_POINT` ("Symbol point value"), `SYMBOL_TRADE_TICK_SIZE` ("Minimal price change"), `SYMBOL_TRADE_TICK_VALUE_PROFIT` / `_LOSS` ("Calculated tick price" — mənfəətli və zərərli mövqe üçün ayrı-ayrı hesablanır). — https://www.mql5.com/en/docs/constants/environment_state/marketinfoconstants
- `OrderCalcProfit(action, symbol, volume, price_open, price_close, profit)` nəticəni hesabın valyutasında, "in the current market conditions" hesablayır; "can differ in different market environments". — https://www.mql5.com/en/docs/trading/ordercalcprofit

- `SymbolSelect(name, true)` simvolu proqramdan Market Watch-a əlavə edir. Simvolun qrafiki açıqdırsa və ya açıq mövqeyi varsa, Market Watch-dan çıxarıla bilmir. — https://www.mql5.com/en/docs/marketinformation/symbolselect

- `GlobalVariableSet(name, double)`: ad 63 simvoldan uzun ola bilməz, dəyər yalnız rəqəmdir, son istifadədən 4 həftə sonra silinir. — https://www.mql5.com/en/docs/globals/globalvariableset
- `GlobalVariableSetOnCondition(name, value, check_value)`: "atomic access … can be used for providing of a mutex at interaction of several Expert Advisors". Dəyişən yoxdursa 4501 xətası verir. — https://www.mql5.com/en/docs/globals/globalvariablesetoncondition
- `EventChartCustom(chart_id, id, long, double, string)`: başqa qrafikin hadisə növbəsinə xüsusi hadisə göndərir. `sparam` 63 simvoldan uzun olsa kəsilir. Alan EA onu `OnChartEvent`-də emal edir. — https://www.mql5.com/en/docs/eventfunctions/eventchartcustom
- Komanda sətrindən kompilyasiya: `metaeditor64.exe /compile:"<fayl və ya qovluq>"`. `/include` ilə xarici include qovluğu verilir. — https://www.metatrader5.com/en/metaeditor/help/beginning/integration_ide
- Avtomatik başlatma: `terminal64.exe /config:<ini>`. `[StartUp]` bölməsində `Script=`, `ScriptParameters=`, `ShutdownTerminal=1` (skript bitəndə terminal bağlanır) olur. `[Tester]` bölməsi Strategy Tester sınağını avtomatik başladır. — https://www.metatrader5.com/en/terminal/help/start_advanced/start
- `EventSetTimer`: "For each program no more than one timer can be run". Növbədə və ya emalda Timer hadisəsi varsa, yeni Timer hadisəsi növbəyə əlavə olunmur. — https://www.mql5.com/en/docs/eventfunctions/eventsettimer
- MQL5 funksiyalarının tam siyahısında JSON funksiyası **yoxdur** (yoxlama: 2026-09-25). JSON emalı üçün öz kodumuz lazımdır. — https://www.mql5.com/en/docs/function_indices
- `ENUM_CRYPT_METHOD` üsulları: CRYPT_HASH_SHA256, CRYPT_HASH_MD5, CRYPT_BASE64 və başqaları. Hazır HMAC yoxdur. — https://www.mql5.com/en/docs/constants/namedconstants/otherconstants

## Anbar provayderləri (yoxlama: 2026-09-24)
### Cloudflare R2
- S3 endpoint: `https://<ACCOUNT_ID>.r2.cloudflarestorage.com`. Region `auto`; boş dəyər və `us-east-1` da `auto` sayılır. PutObject Content-MD5-i dəstəkləyir. S3 Object Lock başlıqları **dəstəklənmir**. ListObjectsV2, DeleteObject, PutBucketCors mövcuddur. — https://developers.cloudflare.com/r2/api/s3/api/
- "Bucket locks" R2-nin öz xüsusiyyətidir: bucket və ya prefiks üzrə müddətli və ya müddətsiz qoruma "prevent the deletion and overwriting". Dashboard, Wrangler və Cloudflare API ilə qurulur, S3 API ilə yox. — https://developers.cloudflare.com/r2/buckets/bucket-locks/
- API tokenləri: Object Read & Write və ya Object Read "in specific buckets". Dashboard-da hər token üçün bir icazə növü seçilir. Prefiks üzrə məhdudlaşdırma sənəddə göstərilməyib. "You must purchase R2 before you can generate an API token." — https://developers.cloudflare.com/r2/api/tokens/
- Qiymət: Class A $4.50/milyon, Class B $0.36/milyon, pulsuz həcm ayda 1M A + 10M B + 10 GB. ListObjects Class A-dır, DeleteObject pulsuzdur, egress pulsuzdur. — https://developers.cloudflare.com/r2/pricing/
### Backblaze B2
- Application keys: bir bucket, bir neçə bucket və ya hamısı üçün. Giriş növü Read and Write / Read Only / Write Only. `namePrefix` ilə prefiks məhdudiyyəti mümkündür. — https://www.backblaze.com/docs/cloud-storage-application-keys
- Bütün tranzaksiyalar (A, B, C) pulsuzdur. Saxlama ilk 10 GB-dan sonra $0.00695/GB-ay. S3 API-də PutObjectLockConfiguration və PutObjectRetention var. — https://www.backblaze.com/cloud-storage/transaction-pricing
- **Yoxlanmayıb:** B2 bucketlarının standart olaraq köhnə versiyaları saxlayıb-saxlamadığı. Buna görə lifecycle ayarı Mərhələ 2-də yoxlanılmalıdır.
### AWS S3
- Object Lock olan bucketda: "The Content-MD5 or x-amz-sdk-checksum-algorithm header is required for any request to upload an object with a retention period configured using Object Lock." — https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-lock-managing.html
- Object Lock "works only in buckets that have S3 Versioning enabled". Silməni və üzərinə yazmanı bloklayır (WORM). Retention və ya legal hold ilə işləyir. — https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-lock.html
- Qiymət (https://aws.amazon.com/s3/pricing/): sorğular hər 1 000 üçün hesablanır; "LIST requests … are charged at the same rate as S3 Standard PUT"; "DELETE and CANCEL requests are free". Yeni müştərilərə 200 dollara qədər Free Tier krediti verilir. Konkret tariflər səhifədən çıxarıla bilmədi, AWS Pricing Calculator ilə hesablanmalıdır.
- S3 Glacier Instant Retrieval (`GLACIER_IR`): millisaniyəlik, "real-time access". Standard-IA ilə müqayisədə saxlama ucuzdur, "higher data access costs". "minimum object size of 128 KB", "minimum storage duration period of 90 days". — https://docs.aws.amazon.com/AmazonS3/latest/userguide/glacier-storage-classes.html
- R2-də yalnız STANDARD və STANDARD_IA saxlama sinifləri var (bax: R2 uyğunluq cədvəli).
- Versiyalı bucketda "a simple DELETE does not actually remove the specified object. Instead, Amazon S3 inserts a delete marker". Qalıcı silmə üçün `DELETE Object versionId` və ya lifecycle-ın `NoncurrentVersionExpiration` qaydası lazımdır: "Amazon S3 permanently removes these object versions". — https://docs.aws.amazon.com/AmazonS3/latest/userguide/DeletingObjectVersions.html
- R2-nin S3 uyğunluq səhifəsində versiya (Versioning) əməliyyatları çəkilmir (2026-09-25). R2-də versiyanın olub-olmaması uyğunluq testində (T-4) yoxlanılacaq. Bucket lock sənədində versiya tələbi göstərilməyib.
- Presigned URL: "presigned URLs are bearer tokens that grant access to those who possess them". Müddət bitənə qədər dəfələrlə işlədilə bilir. IAM istifadəçi açarı ilə SigV4 imzalı URL ən çoxu 7 gün etibarlıdır. Mövcud obyektin üzərinə yazır. — https://docs.aws.amazon.com/AmazonS3/latest/userguide/using-presigned-url.html
### Wasabi
- Minimum saxlama müddəti 90 gündür (Pay as You Go). Obyekt ondan tez silinsə, qalan günlər üçün "Timed Deleted Storage" haqqı tutulur. — https://wasabi.com/pricing/faq

## API platforması və giriş (yoxlama: 2026-09-24)
- Cloudflare Workers runtime V8 üzərindədir. Standart veb API-ləri (fetch, URL, Encoding, AbortController və s.) mövcuddur. `eval` və `new Function` qadağandır. — https://developers.cloudflare.com/workers/runtime-apis/web-standards/
- Workers Paid: hesab üçün minimum $5/ay. Ayda 10 milyon sorğu daxildir, sonra hər milyon $0.30. Statik fayllara sorğular "free and unlimited"-dir. Worker-dən edilən alt sorğulara haqq alınmır. — https://developers.cloudflare.com/workers/platform/pricing/
- Cloudflare Access sorğuya `Cf-Access-Jwt-Assertion` başlığı əlavə edir. Token RS256 JWT-dir, açarlar `https://<team>.cloudflareaccess.com/cdn-cgi/access/certs` ünvanında JWK formatındadır və 6 həftədən bir dəyişir. Tövsiyə olunur ki, `kid` uyğunlaşdırılsın və `iss` ilə `aud` yoxlanılsın. — https://developers.cloudflare.com/cloudflare-one/access-controls/applications/http-apps/authorization-cookie/validating-json/
- Access "Independent MFA": TOTP, WebAuthn təhlükəsizlik açarı və biometrika (Touch ID, Face ID, Windows Hello). — https://developers.cloudflare.com/cloudflare-one/access-controls/access-settings/independent-mfa/
- Zero Trust: Free Plan "teams under 50 users", Pay-as-you-go $7/istifadəçi/ay. — https://www.cloudflare.com/plans/zero-trust-services/

- Deno: `Deno.serve(handler)`. `deno serve` ilə işə salınan modul `Deno.ServeDefaultExport` interfeysinə uyğun olmalıdır, yəni `fetch` export edir (Workers-in `export default { fetch }` quruluşu ilə eyni formadır). — https://docs.deno.com/api/deno/http-server/
- Caddy: "Automatic HTTPS provisions TLS certificates for all your sites and keeps them renewed". Sertifikatlar Let's Encrypt və ya ZeroSSL-dən alınır, HTTP avtomatik HTTPS-ə yönləndirilir. — https://caddyserver.com/docs/automatic-https

## Brauzer
- Service worker (PWA-nın əsası) "only available in secure contexts", yəni səhifə HTTPS ilə verilməlidir (localhost istisnadır). — https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API

## Mövqe uçotu sistemləri (https://www.metatrader5.com/en/terminal/help/trading/general_concept, 2026-09-25)
- İki sistem var: Netting və Hedging, "set by the broker". Netting-də simvol üzrə yalnız bir ümumi mövqe olur. Əks istiqamətli sövdələşmə mövqeni azaldır, bağlayır (həcm bərabərdirsə) və ya çevirir (böyükdürsə). "It does not matter, what has caused the opposite deal — an executed market order or a triggered pending order."
- Netting-də SL/TP-nin miras qaydası: həcm artanda və ya mövqe çevriləndə SL/TP "according to its latest order" qoyulur. Orderdə sıfır varsa, mövqenin SL/TP-si silinir. Qismən bağlamada dəyişmir. Tam bağlamada silinir.
- Hedging-də mövqe çevrilə bilmir. Qismən bağlamada SL/TP dəyişmir.
- OTC bazarlarında (Forex, Futures) swap zamanı, "including swap through re-opening", SL/TP dəyişmir. Birja bazarında isə swap zamanı SL/TP sıfırlanır.
- Exchange Stocks/Futures simvollarında gözləyən əmrlər birja qaydası ilə, adətən Last qiymətinə görə işə düşür. Alış yenə Ask, satış Bid ilə icra olunur.

## Bu maşın və broker (yoxlama: 2026-09-25)
- İnkişaf maşını: Windows Server 2025, AWS-də, 2 vCPU, 3 GB RAM, GMT+0. Kaynak: MT5 jurnalı.
- MT5 quraşdırılıb: `C:\Program Files\MetaTrader 5` (terminal64.exe, MetaEditor64.exe, metatester64.exe), build 6182. Məlumat qovluğu: `%APPDATA%\MetaQuotes\Terminal\D0E8209F77C8CF37AD8BF550E51FF075`.
- Broker: CFI (https://cfi.trade/az/az). Demo server: CFI2-Demo. Jurnalda: "demo account - hedging mode". Giriş avtomatikdir (OTP əlaməti yoxdur).
- Əks mövqelərə icazə var: operator 2026-09-25-də EURUSD üzrə 0.01 alış və 0.01 satış açıb, ikisi də açılıb.
- Server vaxtı 2026-09-25-də UTC+3 idi (terminal 16:23, Bakı UTC+4 ilə 17:23). Qış vaxtında dəyişə bilər.
- Bilinmir (Mərhələ 1-də yoxlanılacaq): serverin qış/yay vaxtı qaydası, SendNotification müddəti, VPS→API sorğu müddəti.

## Qeyd
- Playwright MCP layihə qovluğunda `.playwright-mcp/` yaradır. Git qurulanda bu qovluq `.gitignore`-a əlavə edilməlidir.
