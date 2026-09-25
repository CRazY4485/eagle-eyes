---
status: draft
created: 2026-09-25
derived-from: layihe-senedi.md (qaralama, 1699 sətir)
decisions: .claude/context/interview.md (Q00–Q26)
---

# Eagle Eyes — Layihə Sənədi

Bu sənəd qaralamanın yerinə keçir. Qaralamadakı qaydaların çoxu burada saxlanılıb. Müsahibədə aşkar edilən boşluqlar, ziddiyətlər və yeni qərarlar isə sənədə işlənib.

## Sənəd necə oxunur

- Sənədin quruluşu qatlıdır: əvvəlcə sistemin **nə olduğu**, sonra **necə işlədiyi**, sonda **rəqəmlər və sərhədlər** gəlir.
- Hər qaydanın **identifikatoru** var, məsələn `PLN-07`. Kod, testlər və müzakirələr qaydaya bu nömrə ilə istinad edir. Nömrə heç vaxt dəyişmir. Qayda ləğv olunsa, nömrəsi "ləğv edilib" qeydi ilə yerində qalır.
- Hər qaydanın yanında onun **əsası** yazılıb. Qaydanı dəyişmək lazım olanda, onu niyə qoyduğumuzu bilmək lazımdır. Əsası olmayan qayda **ixtiyari seçimdir** və sənəddə belə işarələnib.
- Sənəd Azərbaycan dilindədir. Ticarət terminləri ingiliscə, MetaTrader-dəki yazılışı ilə verilir (bax 22).
- Rəsmi sənədlərə istinadlar `.claude/context/project.md` faylında yoxlanma tarixi ilə birlikdə toplanıb.

---

# 1. Giriş

## 1.1 Tərif

**Eagle Eyes** öz hesabında işləyən trader üçün avtomatlaşdırılmış ticarət sistemidir. Sistem iqtisadi xəbərlərin açıqlanma anından əvvəl, əvvəlcədən hazırlanmış ticarət göstərişlərini yerinə yetirir. Operatorun ekran qarşısında olması tələb olunmur.

Bu tərifdən dörd fakt çıxır və onlar bütün sənədi idarə edir:

| Fakt | Nəticəsi |
|---|---|
| İstifadəçi öz hesabında işləyən traderdir | Bir sistem = bir hesab. Çoxistifadəçili, çoxhesablı mürəkkəblik yoxdur |
| İcra **xəbər anından əvvəl** baş verir | Sistem gözləyən əmr qoyur, xəbər anında işi broker görür |
| Operatorun iştirakı tələb olunmur | Hər qərarın avtomatik cavabı olmalıdır. "Operatordan soruş" cavab deyil |
| İcra vahidi **plandır** | Plan əvvəlcədən hazırlanır, parametrləri sabitlənir, sonra icra olunur |

## 1.2 Məqsəd

Məqsəd üç qatlıdır və heç biri digərini əvəz etmir:

- **Qazanc.** Sistemin var olma səbəbi budur. Bu, ticarət sistemidir və uğurun ölçüsü nəticədir.
- **Sürət.** Xəbər ticarətində məcburi şərtdir. Bu dizaynda sürət **yerləşdirmə dəqiqliyi** deməkdir: gözləyən əmrlər nəzərdə tutulan anda brokerdə hazır olmalıdır.
- **Avtonomluq.** Sistem 7/24 çalışır və operatordan mümkün qədər az müdaxilə, diqqət və nəzarət tələb edir. Bu, sadəcə rahatlıq deyil. Sistem qərarsız qalanda operatordan soruşsaydı, operator onsuz da ekrana baxmalı olardı və məqsəd pozulardı.

## 1.3 İstifadəçi rolu

| Rol | Sayı | İşi |
|---|---|---|
| Operator | 1 | Sistemi qurur, konfiqurasiyanı təyin edir, vəziyyətə baxır |

Operator texniki işçi deyil, sistemin sahibidir. **Heç bir gündəlik əməliyyat server bilikləri tələb etmir.** Sistem operatorun idarə etməli olduğu server, virtual maşın və ya əməliyyat sistemi saxlamır. Bütün komponentlər idarə olunan xidmətlərdə işləyir (bax 3).

---

# 2. Əsas axın

Bu bölmə bütöv zənciri bir yerdə göstərir. Qalan bölmələr bu zəncirin ayrı-ayrı halqalarını açır.

```
┌─ 1 ── TƏQVİM OXUNUR ─────────────────────────────────────────────┐
│ Üç mənbədən: MetaTrader 5-in daxili təqvimi, Forex Factory-nin   │
│ həftəlik siyahısı və (yalnız demo hesabda) sınaq cədvəli.        │
└───────────────────────────────┬──────────────────────────────────┘
                                ▼
┌─ 2 ── PLAN QURULUR ──────────────────────────────────────────────┐
│ Eyni valyutanın bir-birinə ≤ 60 s yaxın xəbərləri zəncirvari     │
│ qrupa birləşir. Planın anı qrupdakı ən yüksək Impact-li xəbərin  │
│ anıdır. Valyuta simvol xəritələməsindən keçir, hər simvol üçün   │
│ bir plan yaranır və simvol × Impact konfiqurasiyasını alır.      │
└───────────────────────────────┬──────────────────────────────────┘
                                ▼
┌─ 3 ── HAZIRLIQ PƏNCƏRƏSİ ────────────────────────────────────────┐
│ Plan Ticarət EA-ya keçir, parametr nüsxəsi götürülür, yoxlamalar │
│ və hesablamalar aparılır. Yoxlama keçməzsə plan buraxılır.       │
└───────────────────────────────┬──────────────────────────────────┘
                                ▼
┌─ 4 ── İKİ GÖZLƏYƏN ƏMR YERLƏŞDİRİLİR ────────────────────────────┐
│ Ask-dan offset qədər yuxarıda alış, Bid-dən offset qədər aşağıda │
│ satış. SL və TP əmrə qoşulur, onları broker saxlayır.            │
└───────────────────────────────┬──────────────────────────────────┘
                                ▼
┌─ 5 ── XƏBƏR AÇIQLANIR ───────────────────────────────────────────┐
│ Əmrlərdən biri işə düşür. Bu anda sistem heç nə etmir, işi       │
│ broker görür. Sistem dayanmış olsa belə giriş baş verir.         │
└───────────────────────────────┬──────────────────────────────────┘
                                ▼
┌─ 6 ── ƏKS ƏMR SİLİNİR ───────────────────────────────────────────┐
│ Broker hadisəsi gələn kimi (hadisə əsaslı yanaşma), üstəlik      │
│ dövri uzlaşdırma ilə təminatlı şəkildə.                          │
└───────────────────────────────┬──────────────────────────────────┘
                                ▼
┌─ 7 ── ƏMƏLİYYAT İDARƏ OLUNUR ────────────────────────────────────┐
│ SL və TP brokerdədir. Breakeven və Trailing Stop sistemdədir.    │
└───────────────────────────────┬──────────────────────────────────┘
                                ▼
┌─ 8 ── ƏMƏLİYYAT BAĞLANIR ────────────────────────────────────────┐
│ SL, TP, Trailing Stop və ya zərər limiti ilə. Brokerdə plana     │
│ aid heç nə qalmayanda plan son vəziyyətə çatır.                  │
└──────────────────────────────────────────────────────────────────┘
```

**Bu axının ən mühüm xassəsi:** əmrlər brokerə çatdıqdan sonra **giriş sistemin canlı olmasından asılı deyil**. Sistem dayansa, internet kəsilsə, server texniki xidmətə keçsə belə, qiymət səviyyəyə çatanda broker əmri işə salır. SL və TP də yerində qalır.

---

# 3. Sistemin quruluşu

## 3.1 Komponentlər

```
 ☁ MetaTrader VPS (MetaQuotes, 7/24, brokerə ən yaxın server)
 ┌──────────────────────────────────────────────────────────────┐
 │ Qrafik 1 │ TİCARƏT EA                                        │
 │          │ hazırlıq, yoxlamalar, əmrlər, SL/TP/BE/TS,        │
 │          │ zərər limiti, blackout, rejim, uzlaşdırma         │
 │          │ ⛔ WebRequest işlətmir (yeganə istisna: ARX-04)    │
 │                     ▲ │  qlobal dəyişənlər + lokal fayllar   │
 │                     │ ▼                                      │
 │ Qrafik 2 │ RABİTƏ EA                                         │
 │          │ təqvim, planların qurulması, API ilə əlaqə,       │
 │          │ push bildirişləri   ⛔ ticarət etmir               │
 └──────────────┬───────────────────────────────┬───────────────┘
                │ push                          │ HTTPS + token
                ▼                               ▼
      📱 MetaTrader mobil            ┌─ Operatorun domeni ────────────┐
                                     │ api.<domen>   → EA API         │
                                     │ panel.<domen> → Access (MFA)   │
                                     │                 → panel + API  │
                                     │   (Cloudflare Workers)         │
                                     └──────────────┬─────────────────┘
                                                    │ S3 API (SigV4)
                                                    ▼
                                     🗄 Obyekt anbarı: AWS S3 (əsas),
                                        Cloudflare R2 (ehtiyat)
```

| Komponent | Harada | Kim idarə edir |
|---|---|---|
| Ticarət EA, Rabitə EA | MetaTrader VPS | MetaQuotes (terminal), sistem (kod) |
| API qatı və panel | Cloudflare Workers (ödənişli plan) | Cloudflare |
| Panel girişi | Cloudflare Access + müstəqil MFA | Cloudflare |
| Obyekt anbarı | AWS S3, ehtiyat Cloudflare R2 | Provayder |
| Bildiriş kanalı | MetaTrader push | MetaQuotes |

**ARX-01. Sistem iki EA-dan ibarətdir: Ticarət EA və Rabitə EA.**
Əsas: MetaTrader 5-də `WebRequest` sinxrondur: *"breaks the program execution and waits for the response"*. Hər EA isə öz icra sapında işləyir. Şəbəkə gözləməsi hədəf anda əmri gecikdirə bilər. Ona görə şəbəkə ilə bağlı bütün iş Rabitə EA-dadır, Ticarət EA isə heç vaxt bloklanmır. Bölünüş məsuliyyəti də ayırır: biri pul riski daşıyır, digəri daşımır.

**ARX-02. Ticarət EA `WebRequest` işlətmir.**

**ARX-03. Rabitə EA ticarət əmri göndərmir.**

**ARX-04. Ticarət EA yalnız bir halda push göndərir:** Rabitə EA susanda (NSZ-14).
Əsas: Rabitə EA susanda push göndərə biləcək başqa komponent qalmır. SendNotification-ın bloklayıb-bloklamadığı rəsmi sənəddə yazılmayıb, ona görə bu istisna isti yoldan kənarda saxlanılır (NSZ-14).

**ARX-05. Panel və API qatı sistemin nüvəsi deyil.** Onlar çöksə, ticarət davam edir (NSZ-09, NSZ-10).

## 3.2 Şəbəkə istiqaməti: yalnız çıxış

```
Sistem (VPS) ──────► API qatı, Forex Factory      ✅ mümkündür
Sistem (VPS) ◄────── kənardan gələn sorğu          ❌ mümkün deyil
```

MetaTrader-in şəbəkə vasitələri yalnız müştəri rejimində işləyir: qoşulur, göndərir, cavab alır. Dinləyən port yoxdur və qurula da bilməz.

Bunun üç nəticəsi var:

1. **Panel sistemlə birbaşa danışa bilmir.** Aralarında API qatı və anbar məhz buna görə var.
2. **Paneldən verilən dəyişiklik ani deyil.** Rabitə EA konfiqurasiyanı 30 saniyədə bir yoxlayır (PNL-10).
3. **Fövqəladə dayandırma paneldən keçmir.** Onun üçün alət masaüstü və ya mobil MetaTrader-dir (3.6).

**ARX-06. VPS-in icazəli ünvan siyahısında yalnız iki ünvan olur: API qatının domeni (`api.<domen>`) və Forex Factory.**
Əsas: EA panelə heç vaxt müraciət etmir. Panelin ünvanı və hostinqi dəyişəndə VPS-ə heç nə təsir etmir. API ünvanı operatorun öz domenində olduğu üçün API platforması və ya anbar provayderi dəyişsə də EA-nın bildiyi ünvan dəyişmir.

**ARX-07. İlk quraşdırmada sıra vacibdir:** əvvəlcə ünvanlar masaüstü terminalda icazəli siyahıya əlavə edilir, **sonra** sinxronizasiya aparılır.
Əsas: icazəli siyahı VPS-ə yalnız sinxronizasiya ilə köçürülür. Tərs sıra olsa, sistem VPS-də işləyər, amma şəbəkəyə çıxa bilməz.

## 3.3 Niyə MetaTrader VPS

MetaTrader VPS, MetaQuotes-un öz şəbəkəsində işlətdiyi xüsusi virtual terminaldır. Operator ona qoşulmur, onu saxlamır, yeniləmir.

**Üstünlükləri:**
- Hostinq serveri brokerin serverinə ən yaxın olan kimi seçilir. Yerləşdirmə dəqiqliyi üçün ən vacib amil budur.
- Terminalı MetaQuotes işlədir: server, əməliyyat sistemi və avtomatik başlatma məsələləri ortadan qalxır.
- Avtomatik ticarət virtual terminalda həmişə icazəlidir.

**Məhdudiyyətləri** (bunlar dizaynı təyin edir):
- Operatorun virtual terminala fiziki girişi yoxdur.
- Xarici kitabxanalara (DLL) müraciət tamamilə qadağandır. Belə müraciət edən proqram dərhal dayandırılır.
- Miqrasiya ilə yalnız bunlar köçürülür: hesab bağlantısı, Market Watch simvolları, EA işləyən qrafiklər, EA parametrləri və icra faylları, FTP/Email/Notifications parametrləri, WebRequest icazəsi və icazəli ünvan siyahısı.
- Sinxronizasiya yalnız operatorun sorğusu ilə olur və bir istiqamətlidir.
- Texniki xidmət: həftəsonu (şənbə və bazar, **EET vaxtı ilə**) gündə cəmi 4 saata qədər, iş günləri lazım gələrsə gündə cəmi 15 dəqiqəyə qədər.
- Serverlər müstəqil hostinq şirkətlərinə məxsusdur. MetaQuotes rəsmi qaydalarda terminalların fasiləsiz işləməsinə zəmanət vermir: *"makes no guarantees of the Virtual terminals' smooth operation"*. Deməli, **planlaşdırılmamış yenidən başlamalar da mümkündür.**
- EA-nın yazdığı fayllar və terminalın qlobal dəyişənləri miqrasiya siyahısında yoxdur. Onların yenidən başlamadan sonra qalıb-qalmadığı rəsmi sənədlərdə yazılmayıb.

**ARX-08. VPS diski və qlobal dəyişənlər heç vaxt yeganə yaddaş deyil.** Davamlı saxlanmalı olan hər şeyin əsas nüsxəsi anbardadır. Lokal nüsxələr yalnız ehtiyatdır (LOK bölməsi).
Əsas: sistemin mənası nəzarətsiz işləməkdir. Sənədi olmayan davranışın üzərinə açıq mövqe bağlamaq olmaz.

## 3.4 API qatı

API qatı EA və panel ilə anbar arasında dayanan kiçik server funksiyasıdır. Server və ya virtual maşın deyil: Cloudflare Workers-də işləyir və yalnız sorğu gələndə oyanır.

**ARX-09. EA və panel anbarla birbaşa danışmır, yalnız API qatı ilə danışır.**
Əsas:
- **Təhlükəsizlik.** Anbar açarları yalnız API qatında saxlanılır, brauzerə və VPS-ə heç vaxt çıxmır. Panel girişi 2FA ilə qorunur (THL bölməsi).
- **Sadəlik.** EA adi HTTPS sorğusu göndərir, başlığında token olur. MQL5-də SigV4 imzası (əl ilə yazılan HMAC-SHA256, kanonik sorğu) lazım olmur.
- **İki qat yoxlama.** Konfiqurasiya anbara yazılmazdan əvvəl API-də yoxlanılır (API-06).

**ARX-10. API kodu standart veb interfeysi ilə yazılır** (`export default { fetch }` forması, `Request` və `Response`). Platformaya xas olan yalnız giriş faylı və yerləşdirmə ayarlarıdır. Platformanın öz anbar bağlantısı kimi xüsusi funksiyaları işlədilmir.
Əsas: portativlik (PRT bölməsi). Eyni forma Workers-də və digər standart runtime-larda işləyir.

## 3.5 Panel

**ARX-11. Panel quraşdırıla bilən veb tətbiqdir (PWA).** O, API qatı ilə eyni Worker-də statik fayllar kimi yerləşir və eyni ünvandan verilir.
Əsas: eyni ünvan olduğu üçün CORS lazım olmur. Statik fayllar Workers-də pulsuz və limitsizdir. PWA-nın əsası olan service worker yalnız HTTPS-də işləyir, bunu da platforma avtomatik təmin edir.

**ARX-12. Panel vəziyyət saxlamır.** Panel çöksə və ya bağlansa, heç nə dayanmır və heç nə itmir. Sonradan başqa klient (mobil tətbiq və s.) yazmaq sistemi dəyişmək deyil, ikinci klient əlavə etməkdir.

## 3.6 Fövqəladə dayandırma

Operatorun ticarəti dayandırmaq üçün **heç bir kanaldan asılı olmayan** yolu var və bu yol sistemin özündən kənardadır:

- Masaüstü və ya mobil MetaTrader-dən hesaba qoşulub açıq əməliyyatları və gözləyən əmrləri **əl ilə bağlamaq**.
- Masaüstü terminaldan virtual platformanı **dayandırmaq** (Toolbox → VPS → Stop Server). Bu, tətbiqin bağlanması ilə eynidir: EA dayanır, gözləyən əmrlər və brokerdəki SL/TP isə yerində qalır.

Panel bu iş üçün alət **deyil**, çünki paneldən verilən dəyişiklik 30 saniyəyə qədər gecikə bilər.

---

# 4. Əhatə dairəsi

## 4.1 Daxildir

- İqtisadi təqvimin üç mənbədən oxunması və birləşdirilməsi (MT5, Forex Factory, demo hesabda sınaq cədvəli)
- Valyuta ilə simvol arasında operatorun qurduğu xəritələmə
- Xəbər anına, valyutasına və Impact səviyyəsinə bağlı planların qurulması
- Hər simvol × Impact cütü üçün ayrıca ticarət konfiqurasiyası
- Hazırlıq pəncərəsində yoxlamalar və hesablamalar
- İki istiqamətdə gözləyən əmrlərin yerləşdirilməsi və izlənməsi
- Stop Loss, Take Profit, Breakeven, Trailing Stop ilə əməliyyatın idarəsi
- Günlük və həftəlik zərər limitləri
- Blackout aralıqları, ticarət rejimi, quraşdırma kilidi
- **Hedging və netting** uçot sistemli hesablar
- Vəziyyətin, təqvimin, konfiqurasiyanın və dəlil qeydlərinin operatora göstərilməsi
- Push bildirişləri və gündəlik xülasə
- Dəlil qeydlərinin aparılması, həftəlik arxivlənməsi və qorunması
- Demo hesabda canlı sınaq planları

## 4.2 Daxil deyil

Bu siyahı qəsdən qısa və kəskindir. Hər maddə bir mübahisəni əvvəlcədən kəsir.

- **Xəbərin məzmununun təhlili.** Sistem xəbərin rəqəmini (faktiki, gözlənilən, əvvəlki) oxumur və ticarət istiqamətini **özü seçmir**. Hər zaman hər iki istiqamətdə gözləyən əmr qoyur, istiqaməti bazar seçir.
- **Xəbərdən başqa siqnallara görə ticarət.** Texniki analiz, indikator, qiymət naxışı yoxdur. Yeganə tətikləyici iqtisadi təqvim xəbəridir (və demo hesabda sınaq cədvəli).
- **Bir neçə hesabın idarə olunması.** Bir sistem = bir hesab.
- **Backtest və strategiya optimallaşdırması.** Sistem yalnız canlı və gələcək hadisələrlə işləyir və sınanır.
- **Xəbər anında bazar əmri ilə reaksiya.** Bu, tamamilə başqa dizayndır.
- **Yerləşdirmə gecikməsinin kompensasiyası.** Sistem şəbəkə gecikməsini ölçüb əmri qabaqcadan göndərmir. EA brokerə ən yaxın data-mərkəzdə işləyir. Kompensasiya etməyə dəyməyən gecikmə üçün parametr və orta hesablama saxlamaq artıq mürəkkəblikdir. Sapma yalnız ölçülür (EMR-09).
- **Birja (exchange) uçot sistemli hesablar.** Belə hesabda swap zamanı SL/TP sıfırlanır və gözləyən əmrlər Last qiymətinə görə işə düşür. Bu, İDR-01 və EMR-01 prinsiplərini pozur. Sistem belə hesabı aşkarlayır və quraşdırma kilidi qoyur (NSZ-03).
- **Real hesaba keçid.** Layihə demo hesabda tam funksionallıqla bitir. Real hesaba keçid layihə sahibinin öz qərarı və işidir.
- **Paneldən ticarət hərəkəti.** Panel plan ləğv etmir, əməliyyat bağlamır, gözləyən əmr silmir.
- **Öz server infrastrukturu.** Linux VPS və ya başqa öz idarə olunan server yoxdur (1.3).

---

# 5. Domen modeli

## 5.1 Anlayışlar və aralarındakı əlaqə

```
     Xəbər ──────────┐
 (vaxt, valyuta,     │ valyuta
  Impact, mənbə)     │    │
                     │    ▼
                     │ Simvol xəritələməsi
                     │    │ simvol
                     ▼    ▼
               Xəbər qrupu ──► PLAN ◄──── Ticarət konfiqurasiyası
                                │         (simvol × Impact)
                                │               │
                                │               ▼
                                │        Parametr nüsxəsi
                                ▼        (hazırlığa keçəndə)
                        İki gözləyən əmr
                                │
                                ▼
                          Açıq əməliyyat
```

## 5.2 Xəbər

Təqvim mənbəyində göstərilən iqtisadi hadisədir. Sistem üçün vacib sahələri: **adı**, **açıqlanma anı**, **valyutası**, **Impact səviyyəsi**, **mənbəyi** (`MT5`, `FF` və ya `TEST`).

Impact üç dəyərdən birini alır: **High**, **Medium**, **Low**. Aralarındakı sıra belədir: High > Medium > Low.

## 5.3 Simvol xəritələməsi

Valyuta ilə simvol arasında operatorun qurduğu uyğunluqdur. Bir valyutaya bir neçə simvol, bir simvola bir neçə valyuta bağlana bilər, hədd qoyulmur.

- Valyuta üç böyük latın hərfi ilə yazılır.
- Simvol brokerin verdiyi adla, dəyişdirilmədən işlənir.

**PLN-01. Xəritələmə bir dəfə qurulur və bütün gələcək xəbərlərə tətbiq olunur.**
Əsas: hər xəbər üçün ayrıca simvol seçmək həftədə onlarla qərar deməkdir və bu, avtonomluq məqsədini pozur.

**PLN-02. Rabitə EA xəritələnmiş hər simvolu Market Watch-a özü əlavə edir** (`SymbolSelect`). Simvol brokerdə yoxdursa, bu, paneldə göstərilir.
Əsas: Market Watch siyahısı VPS-ə yalnız sinxronizasiya ilə köçürülür. Paneldən yeni simvol əlavə ediləndə sinxronizasiya tələb olunmamalıdır.

## 5.4 Ticarət konfiqurasiyası

Bir **simvol** və bir **Impact** cütü üçün təyin edilən parametrlər toplusudur. Bir cüt üçün yalnız bir konfiqurasiya ola bilər.

| Parametr (paneldə) | Vahid | Qeyd |
|---|---|---|
| **Volume** — üsul | — | Fixed Lot / Risk % / Risk Amount |
| **Volume** — dəyər | Üsula görə | Lot, faiz və ya hesabın valyutası |
| **Lead Time** | saniyə | Xəbərdən əvvəl yerləşdirmə müddəti. 0 olduqda hədəf an = xəbər anı |
| **Expiration** — rejim | on / off | Gözləyən əmrlərin sistem tərəfindən silinməsi |
| **Expiration** — müddət | saniyə | Rejim on olduqda tələb olunur. Xəbər anından sayılır |
| **Offset** | point | Gözləyən əmrin cari qiymətdən məsafəsi |
| **Stop Loss** | point | **Məcburidir** |
| **Take Profit** | point | 0 olduqda qoyulmur |
| **Max Spread** | point | Aşılarsa plan buraxılır |
| **Breakeven** — rejim | on / off | |
| **Breakeven — Trigger** | point | Rejim on olduqda tələb olunur |
| **Breakeven — Lock** | point | Rejim on olduqda tələb olunur |
| **Trailing Stop** — rejim | on / off | |
| **Trailing Stop — Trigger** | point | Rejim on olduqda tələb olunur |
| **Trailing Stop — Distance** | point | Rejim on olduqda tələb olunur |

Aralıqlar 18.2-dədir.

**PLN-03. Konfiqurasiyanın simvolu yalnız xəritələmədəki simvollardan seçilir.**

**PLN-04. Mövcud konfiqurasiyanın nüsxəsini götürüb yalnız simvolunu və ya Impact səviyyəsini dəyişmək mümkündür.**
Əsas: hər konfiqurasiyada on beş parametr var. On simvol və üç səviyyə otuz konfiqurasiya deməkdir və hər birini əl ilə doldurmaq əziyyətlidir.

**PLN-05. Konfiqurasiyanın silinməsi ayrıca təsdiq tələb edir.**
Əsas: silinmənin iki geri dönülməz nəticəsi var: parametrlər itir və planlar ləğv edilir (PLN-06). Ləğv edilmiş plan geri qayıtmır.

**PLN-06. Konfiqurasiya silinəndə yalnız həmin simvolun, cari Impact səviyyəsi silinmiş səviyyə olan və hələ icraya keçməmiş planları ləğv edilir.** Planın səviyyəsi sonradan dəyişsə (TQV-12, R-2), plan yeni səviyyənin konfiqurasiyasını alır.

**PLN-07. Simvol xəritələmədən çıxarılanda onun konfiqurasiyaları silinmir.**
Əsas: xəritələmədən çıxarmaq çox vaxt müvəqqəti addımdır. Simvol geri qaytarılanda doldurulmuş parametrlər yerində olur.

**PLN-08. Konfiqurasiya dəyişikliyinin planlara təsiri:**

| Nə baş verdi | Hələ icraya keçməmiş plan | İcraya keçmiş plan |
|---|---|---|
| Konfiqurasiya **dəyişdirildi** | Yeni parametrlərlə **yenilənir** | **Toxunulmur** |
| Konfiqurasiya **silindi** | **Ləğv edilir** (PLN-06) | **Toxunulmur** |

Əsas: operator səhv parametri düzəldirsə, günün qalan planları düzgün işləməlidir. İcraya keçmiş plan isə öz nüsxəsini artıq götürüb və başladığı qaydalarla bitir (PLN-15).

## 5.5 Plan

Bir simvol və bir xəbər anı üçün qurulmuş, icra vaxtı və parametrləri əvvəlcədən təyin edilmiş ticarət göstərişidir.

### Planı nə tanıdır

| Nə | Açarı |
|---|---|
| **Ticarət konfiqurasiyası** | **simvol × Impact** |
| **Plan** | **simvol × planın xəbər anı** |

**Impact planın açarının hissəsi deyil, onun atributudur.** Ardıcıllıq belədir: xəbərlər qrupa birləşir → qrupun anı və Impact-i təyin olunur → həmin səviyyənin konfiqurasiyası seçilir.

**PLN-09. Plan identifikatoru deterministikdir:** simvol × planın xəbər anından hesablanır. Eyni açar həmişə eyni identifikatoru verir. Plan brokerdə yerləşdirdiyi əmrləri bu identifikatorla işarələyir (magic).
Əsas: sistem yenidən başlayanda brokerdəki əmrləri tanıyır. Son vəziyyətə çatmış açar yenidən qurulmaq istənəndə də tanınır və təkrarlanmır (PLN-12).

### 5.5.1 Planın vəziyyətləri

| Vəziyyət | Nə vaxt alınır | Sahibi |
|---|---|---|
| **Qurulmuş** | Plan təqvimdən yaradıldıqda | Rabitə EA |
| **Hazırlıqda** | Hazırlıq pəncərəsi başladıqda | Ticarət EA |
| **Əmri göndərilmiş** | Gözləyən əmrlər brokerdə yerləşdirildikdə | Ticarət EA |
| **İcra olunmuş** | Plan üzrə açıq əməliyyat yarandıqda | Ticarət EA |
| **Tamamlanmış** ⏹ | İcra olunmuş plan üzrə brokerdə mövqe qalmadıqda | Ticarət EA |
| **Vaxtı bitmiş** ⏹ | Gözləyən əmrlər mövqe yaratmadan silindikdə | Ticarət EA |
| **Buraxılmış** ⏹ | Yoxlama keçmədiyinə və ya nasazlığa görə plan icra olunmadıqda | İkisi də |
| **Ləğv edilmiş** ⏹ | Qurulmuş plan icradan çıxarıldıqda: xəbər təqvimdən çıxdı, konfiqurasiya silindi, başqa planla birləşdi | Rabitə EA |
| **Dayandırılmış** ⏹ | Ticarət rejimi, zərər limiti və ya quraşdırma kilidi səbəbindən | Ticarət EA |

⏹ son vəziyyətdir.

**PLN-10. Son vəziyyət həmişə "brokerdə bu plana aid heç nə qalmayıb" deməkdir.** "Ləğv edilmiş" və "Dayandırılmış" yalnız açıq mövqeyi olmayan plana verilir. Açıq mövqeyi olan plan "İcra olunmuş"da qalır və mövqe bağlananda "Tamamlanmış" olur. Zərər limitində mövqe əvvəlcə bağlanır (RSK-07), sonra plan "Dayandırılmış" olur.
Əsas: son vəziyyətdə parametr nüsxəsi silinir (LOK-08) və simvol azad olur. Mövqe hələ canlıdırsa, onun BE/TS idarəsi nüsxəyə ehtiyac duyur.

**PLN-11. Son vəziyyətə çatan plan simvolu yeni plan üçün azad edir.**

"Dayandırılmış"ın ayrıca vəziyyət olmasının əsası: bu, operatorun öz qoyduğu qaydanın işləməsidir, nasazlıq deyil. "Buraxılmış" altında gizlədilsəydi, operator öz qaydasının normal nəticəsini sistem xətası kimi görərdi. Vəziyyətlərin bu qədər ətraflı olmasının əsası: operator "niyə ticarət olmadı" sualını həmişə cavablı görməlidir.

### 5.5.2 Son vəziyyət geri dönülməzdir

**PLN-12. Son vəziyyətə çatmış plan bir daha qurulmur**, xəbər anı hələ gələcəkdə olsa belə.

Nümunə, qayda olmasaydı:
```
16:29:55  Plan hazırlığa keçir, Max Spread aşılıb → plan BURAXILIR
16:29:58  Təqvim yenidən oxunur, xəbər anı (16:30) hələ gələcəkdədir
          → yeni plan qurulur → yenə hazırlığa keçir → yenə buraxılır …
```
Qayda ilə hər **simvol × xəbər anı** cütü üçün **bir nəticə** olur. Paneldə hər xəbərin bir aydın aqibəti görünür.

### 5.5.3 Dayandırılmış planın davranışı

- Brokerdə qalan gözləyən əmrləri silinir.
- Yeni gözləyən əmr yerləşdirilmir.
- Açıq mövqe varsa, ticarət rejimi və quraşdırma kilidi halında **bağlanmır**, öz nüsxəsindəki qaydalarla idarə olunmağa davam edir (PLN-10). Zərər limiti halında isə bağlanır (RSK-07).

Əsas: artıq açılmış əməliyyat başladığı qaydalarla bitir. Zərər limiti yeganə istisnadır, çünki limitin məqsədi zərəri kəsməkdir.

## 5.6 Parametr nüsxəsi

Plan hazırlığa keçəndə ticarət konfiqurasiyasından alınan parametr dəyərlərinin nüsxəsidir.

**PLN-13. Nüsxəni hazırlığa keçid anında Ticarət EA götürür.**

**PLN-14. Nüsxə iki yerə yazılır:** lokal fayla (Ticarət EA yazır) və anbara (Rabitə EA API vasitəsilə yükləyir). Həyat dövrü LOK-06…LOK-10-dadır.
Əsas: anbar davamlı yaddaşdır, lokal fayl isə anbar əlçatmaz olanda ehtiyatdır. İkisinin də itməsi eyni anda iki nadir hadisənin üst-üstə düşməsini tələb edir.

**PLN-15. İşləyən əməliyyatın qaydaları orta yerdə dəyişmir.** Operator konfiqurasiyanı dəyişsə və ya silsə, icradakı plan nüsxədəki qaydalarla bitir.

## 5.7 Açıq əməliyyat

Brokerdə icra olunmuş və hələ bağlanmamış ticarət əməliyyatıdır.

**PLN-16. Əməliyyat mövqe identifikatoru ilə izlənilir, bilet ilə yox.**
Əsas: broker svop yazarkən mövqeyi yenidən aça bilər və bilet dəyişir. Sistem əməliyyatları gecəyə keçirdiyi üçün bu, real riskdir.

Mövqenin plana aidiyyəti:
- **Hedging** hesabda mövqenin magic-i ilə (PLN-09);
- **Netting** hesabda mövqeni açan sövdələşmənin plan identifikatoru ilə. Simvolda yalnız bir mövqe olur. Bu davranış Mərhələ 1-də yoxlanılır (MRH-01).

---

# 6. Təqvim və planın qurulması

## 6.1 Mənbələr

**TQV-01. Sistem təqvimi üç mənbədən oxuyur:**

| Mənbə | Nədir | Qeyd |
|---|---|---|
| `MT5` | MetaTrader 5-in daxili iqtisadi təqvimi | Terminalın içindədir, şəbəkə tələb etmir. Vaxtlar server vaxtı ilə gəlir. Valyuta, Impact, bayram və vaxtın müəyyənlik dərəcəsi hazır gəlir |
| `FF` | Forex Factory-nin həftəlik JSON siyahısı | MT5-də olmayan xəbərlər üçündür. Tamamlayıcı mənbədir |
| `TEST` | Sınaq cədvəli (konfiqurasiyada) | **Yalnız demo hesabda** (TQV-13) |

Forex Factory haqqında bilinməli olanlar:
- **Yalnız cari həftə əlçatandır.** Yeni həftəyə hansı anda keçdiyi bilinmir və Mərhələ 1-də ölçülür (MRH-01).
- **JSON formatı seçilib**, çünki vaxt **açıq UTC fərqi ilə** gəlir (məs. `2026-09-20T19:00:00-04:00`). Xəbər ticarətində saat qurşağı səhvi ən bahalı səhvdir, ona görə fərziyyəyə yer qoyulmur. Bunun əvəzi odur ki, sabit hadisə identifikatoru yoxdur: xəbərlər vaxt və valyuta üzrə uzlaşdırılır.
- **Valyuta sahəsinin adı `country`-dir**, içində isə valyuta kodu olur (USD, EUR, JPY).
- "Vaxtı təsdiqlənməmiş" xəbəri göstərən sahənin olub-olmadığı bilinmir və Mərhələ 1-də müşahidə olunur (MRH-01).

## 6.2 Xəbərlərin birləşməsi

**TQV-02. Eyni valyutaya aid xəbərlər zəncirvari birləşir:** iki xəbər arasında **60 saniyə və ya az** fərq varsa, onlar bir qrupdadır. Qrupun uzunluğu məhdud deyil. Məsələn, 14:30, 14:31 və 14:32 bir qrupdur.
Əsas: iki müstəqil mənbə eyni hadisəni bir neçə saniyə və ya dəqiqə fərqlə göstərə bilər. Dözüm olmasaydı, eyni hadisə eyni simvolda toqquşan iki plan yaradardı.

**TQV-03. Qrupun anı ən yüksək Impact-li xəbərin anıdır. Belə xəbər bir neçədirsə, onların ən erkəninin anı götürülür.**
Nümunə: 14:30 Low, 14:31 Medium, 14:32 High → plan **14:32:00**-a, High konfiqurasiyası ilə qurulur.
Əsas: əmrlər əsas xəbərə yönəlməlidir. Eyni hadisənin iki mənbədəki nüsxəsi adətən eyni səviyyədə olur. Bərabərlikdə ən erkən anın seçilməsi əmrlərin ilk mümkün açıqlamadan əvvəl yerdə olmasını təmin edir.

**TQV-04. Qrupun Impact-i qrupdakı ən yüksək səviyyədir.** Konfiqurasiya ona görə seçilir.

**TQV-05. Birləşmə mənbədən asılı deyil,** `MT5` və `FF` eyni qaydaya tabedir. **İstisna: `TEST` xəbərləri real xəbərlərlə heç vaxt birləşmir** (TQV-14).

**TQV-06. Planın bütün vaxt hesabları planın xəbər anından (TQV-03) aparılır:** hədəf yerləşdirmə anı, silinmə müddəti, blackout yoxlaması, təkrar cəhdlərin son həddi.

## 6.3 Xəbərdən simvola

**TQV-07. Plan xəbərin valyutasına bağlanmış hər simvol üçün ayrıca qurulur.** Məsələn, USD xəbəri üçün xəritələmədə USD-yə EURUSD və XAUUSD bağlanıbsa, iki plan yaranır.

## 6.4 Plan üfüqü

**TQV-08. Planlar cari gün ərzində və növbəti günün ilk 10 dəqiqəsində açıqlanacaq xəbərlər üzrə qurulur** (server vaxtı ilə).
Əsas: günün başında bütün günün planları hazır olur və paneldə görünür. Uzaq xəbərin vaxtı daha çox dəyişir, Forex Factory də onsuz da yalnız cari həftəni verir. 10 dəqiqəlik əlavə = ən böyük Lead Time (300 s) + ən böyük hazırlıq pəncərəsi (300 s). Əlavə olmasaydı, gecə yarısından dərhal sonrakı xəbərlərin hazırlığı əvvəlki günə düşər və plan həmişə buraxılardı.

## 6.5 Kənarlaşdırılan xəbərlər

**TQV-09. Bu xəbərlər üzrə plan qurulmur:**

| Hal | Əsası |
|---|---|
| Bayram | Ticarət hadisəsi deyil |
| Açıqlanma saatı göstərilməyib | Hədəf an hesablana bilməz |
| Açıqlanma vaxtı təsdiqlənməyib | Vaxt dəyişəcək, plan səhv anda işləyər |
| Impact tanınmır | Konfiqurasiya seçilə bilməz |
| Valyutası xəritələnməyib | Ticarət ediləcək simvol yoxdur |
| Valyutası konkret deyil (bütün valyutalara aid) | Hansı simvola aid olduğu bilinmir |

Kənarlaşdırmaların **sayı** qeydə alınır, ayrıca bildiriş göndərilmir. Bunlar gündəlik adi hallardır və bildiriş büdcəsini yeyərdi.

## 6.6 Plan qurulmayan hallar

**TQV-10. Xəbər keçərlidir, amma plan qurulmur, əgər:**
- simvol × Impact üçün konfiqurasiya yoxdursa;
- planın xəbər anı və ya hədəf yerləşdirmə anı blackout aralığına düşürsə;
- hədəf yerləşdirmə anına hazırlıq pəncərəsinin müddətindən az vaxt qalıbsa.

Hər halda səbəb qeydə alınır və paneldə həmin xəbərin yanında göstərilir.

Ticarət rejiminin dayandırılması və quraşdırma kilidi plan qurulmasına mane olmur: qurulmuş planlar hazırlıq anında onları yoxlayır (RSK-10, NSZ-04). Kilidlənmiş zərər limiti isə dövrün sonuna qədər yeni plan qurulmasını dayandırır (RSK-06).
Əsas: rejim və ya kilid açılanda günün qalan xəbərləri üçün planlar hazır olmalıdır.

## 6.7 Təqvimin yenilənməsi

**TQV-11. Təqvim gün ərzində 15 dəqiqədə bir yenidən oxunur,** hər iki xarici mənbə eyni ritmdə.

Yenilənmə ucuzdur: daxili təqvim artımlı oxumanı dəstəkləyir (sonuncu baxışın nişanından bəri nə dəyişdiyi soruşulur), Forex Factory isə şərti sorğunu (dəyişməyibsə məzmun ötürülmür). Daha sıx aralıq fayda vermir.

**TQV-12. Hər yenilənmədə qruplar yenidən qurulur:**

| Qayda | Məzmun |
|---|---|
| **R-1** | Qruplar zəncir qaydası ilə (TQV-02) təzədən hesablanır. Qrupun anı TQV-03 ilə təyin olunur |
| **R-2** | Qrupun anı mövcud, icraya keçməmiş planın açarı ilə eynidirsə, həmin plan yeni səviyyə və konfiqurasiya ilə yenilənir |
| **R-3** | Yeni qrupa düşən, amma açarı qrupun anı ilə üst-üstə düşməyən icraya keçməmiş plan **ləğv edilir** ("başqa planla birləşdi") |
| **R-4** | Heç bir planla üst-üstə düşməyən qrup üçün yeni plan qurulur. Qrupun bölünməsi də bu qayda ilə həll olunur |
| **R-5** | İcraya keçmiş və son vəziyyətli planların xəbərləri **dondurulur** və yenidən qruplaşdırmada iştirak etmir |
| **R-6** | Son vəziyyətli açar üçün yeni plan qurulmur (PLN-12) |

Nümunə, birləşmə: planlar 14:30 (Medium) və 14:32 (High). Yenilənmədə 14:31 Low xəbəri görünür. Zəncir üçünü birləşdirir, qrupun anı 14:32 olur: 14:32 planı yenilənir (R-2), 14:30 planı ləğv edilir (R-3).

Nümunə, bölünmə: qrup 14:30 High, 14:31 Low, 14:32 Medium (plan 14:30-dadır). 14:31 xəbəri mənbədən çıxarılır: 14:30 planı qalır (R-2), 14:32 üçün yeni plan qurulur (R-4).

Xəbərin vaxtı dəyişəndə və ya xəbər çıxarılanda da eyni qaydalar işləyir. İcraya keçmiş plana heç vaxt toxunulmur.

## 6.8 Sınaq cədvəli (`TEST` mənbəyi)

**TQV-13. Sınaq cədvəli (Test Schedule) konfiqurasiyanın bir hissəsidir.** Hər sətirdə simvol, Impact və vaxt qaydası olur: "hər N dəqiqədən bir" və ya "bu gün saat HH:MM". Rabitə EA bu qaydadan `TEST` mənbəli sınaq xəbərləri yaradır. Onlar real xəbərlərin **eyni yolundan** keçir: xəritələmə, konfiqurasiya, hazırlıq, yoxlamalar, əmrlər, idarə.
Əsas: sistemdə əmr anlayışı yoxdur (PNL-02). Sınaq planları göstəriş deyil, qaydadır. Sınanan kod real kodun özüdür.

**TQV-14. `TEST` xəbərləri real xəbərlərlə birləşmir.** Həmin simvolda real planın xəbər anına **Test Min Distance**-dən yaxın olan sınaq xəbəri qurulmur və səbəbi qeydə alınır. Test Min Distance konfiqurasiyada operator parametridir, standart dəyəri 15 dəqiqədir (*standart dəyər ixtiyari seçimdir*).
Əsas: sınaq xəbəri real planı dəyişməməli və sınaq mövqeyi simvolu məşğul edib real planın buraxılmasına səbəb olmamalıdır.

**TQV-15. Sınaq cədvəli yalnız demo hesabda nəzərə alınır.** Hesabın demo, yoxsa real olduğu brokerdən oxunur. Real hesabda sınaq cədvəli tamamilə nəzərə alınmır və bir dəfə **təcili bildiriş** gedir.

**TQV-16. Sınaq planları hər yerdə `TEST` işarəsi ilə göstərilir:** təqvimdə, planlarda, qeydlərdə, bildirişlərdə. Onlar blackout-a, ticarət rejiminə və zərər limitlərinə tabedir.

## 6.9 Mənbə nasazlığı

**TQV-17. Mənbələrdən biri alınmasa, digəri ilə davam edilir.** Hal qeydə alınır və xülasəyə düşür.
Əsas: mənbələr müstəqildir və yarım təqvim heç təqvimdən yaxşıdır.

**TQV-18. Ayrıca təkrar cəhd məntiqi yoxdur.** Növbəti planlı oxuma (15 dəqiqə sonra) təkrar cəhdin özüdür.
Əsas: ayrıca təkrar mexanizmi öz vəziyyətini, sayğacını və dayanma şərtini tələb edir. 15 dəqiqəlik dövr onsuz da keçici nasazlığı udur.

**TQV-19. Hər iki xarici mənbə ardıcıl 4 dəfə (1 saat) alınmırsa və ya günün başlanğıc qurulması alınmırsa, epizod başına bir təcili bildiriş gedir. Bərpa da təcili bildirişlə bildirilir.**
Əsas: təqvimsiz yeni plan qurulmur, ticarət səssizcə dayanar. Tək mənbənin nasazlığı adi haldır, ikisinin birlikdə saatlarla alınmaması isə anormal.

## 6.10 Ticarət serverinin saatının dəyişməsi

**TQV-20. Sistem ticarət serverinin UTC-dən fərqini yadda saxlayır. Fərq dəyişərsə, təqvim yenidən oxunur və hələ icraya keçməmiş planlar yeni vaxtlarla qurulur.** Hal qeydə alınır, ticarət dayandırılmır.
Əsas: planlar server vaxtı ilə ifadə olunur. Serverin saatı dəyişsə, köhnə vaxta bağlı planlar səhv anda və səssizcə ticarət edərdi.

Bilinməli nəticələr: blackout aralıqları divar saatı qaydalarıdır və server saatı sürüşəndə onlar da sürüşür. Günlük zərər dövrü keçid günündə 23 və ya 25 saat olur. CFI demo serveri 2026-09-25-də UTC+3 idi. Qış/yay qaydası Mərhələ 1-də qeyd olunur.

---

# 7. Hazırlıq pəncərəsi və yoxlamalar

## 7.1 Pəncərə

- **Hədəf yerləşdirmə anı** = planın xəbər anı − Lead Time.
- **Hazırlıq pəncərəsi** hədəf andan **Preparation Window** qədər əvvəl başlayır. Bu, **qlobal parametrdir**: bütün planlar üçün eynidir, aralığı 1–300 saniyə, standart dəyəri 10 saniyədir.

Əsas: hazırlıq işi hər plan üçün eynidir və simvoldan asılı deyil. Yoxlamaların hamısı lokaldır və millisaniyələr çəkir, 10 saniyə geniş ehtiyatdır.

Pəncərənin məqsədi: bütün hesablama və yoxlamaları hədəf andan **əvvəl** bitirmək ki, hədəf an çatanda yalnız göndərmə qalsın.

**HZR-01. Hazırlığa keçid sahibliyin atomik keçididir** (EAL-04). Ticarət EA planın vəziyyətini "Qurulmuş"dan "Hazırlıqda"ya yalnız vəziyyət hələ "Qurulmuş"dursa keçirir. Parametr nüsxəsi bu anda götürülür (PLN-13).

## 7.2 Yoxlamalar və sırası

Yoxlamalar ucuzdan bahaya doğru sıralanır, erkən dayanma boş iş görməməyi təmin edir.

**HZR-02.** Yoxlamalar bu sıra ilə aparılır. Hər hansı biri keçməzsə, plan göstərilən vəziyyəti alır və səbəb qeydə alınır:

| # | Yoxlama | Keçməzsə |
|---|---|---|
| 1 | **Simvolun məşğulluğu:** həmin simvolda brokerdə gözləyən əmr və ya açıq mövqe varmı. Həqiqət mənbəyi brokerdir, daxili reyestr yalnız sürətli ilkin süzgəcdir | Buraxılmış |
| 2 | **Blackout:** planın xəbər anı və ya hədəf an blackout aralığındadırmı (RSK-08) | Buraxılmış |
| 3 | **Ticarət rejimi:** Paused-dırmı (RSK-10) | Dayandırılmış |
| 4 | **Zərər limiti:** kilidlidirmi (RSK-06) | Dayandırılmış |
| 5 | **Quraşdırma kilidi:** aktivdirmi (NSZ-04) | Dayandırılmış |
| 6 | **Simvolun ticarət rejimi:** broker hər iki istiqamətdə əməliyyat açmağa icazə verirmi. Yalnız alış, yalnız satış, yalnız bağlama və ya bağlı simvolda plan buraxılır | Buraxılmış |
| 7 | **Spred:** cari spred Max Spread-dən böyükdürmü | Buraxılmış |
| 8 | **Məsafə hədləri:** Offset, SL və TP brokerin minimum məsafəsindən kiçikdirmi | Buraxılmış |
| 9 | **Həcm** hesablanır (HZR-03) | Buraxılmış |
| 10 | **Girov:** bir mövqenin girov tələbini ödəyəcək vəsait varmı | Buraxılmış |

Qeydlər:
- **1-ci yoxlama netting hesabı da qoruyur:** simvolda mövqe varsa, yeni plan buraxılır. Beləcə yeni plan köhnə mövqenin həcmini heç vaxt dəyişmir.
- **7-ci yoxlamanın gücü məhduddur,** sənəd bunu açıq deyir. Təhlükəli genişlənmə xəbər anında, əmr işə düşəndə baş verir və o anda yoxlamaq mümkün deyil. Genişlənən spredin özü əmri işə sala bilər: alış əmri Ask-a baxır və spred genişlənəndə Ask qalxır. Bundan qoruyan şey spred həddi deyil, **kifayət qədər böyük offset**-dir. Spred yoxlaması anormal bazar şəraitini tutur.
- **Dondurma məsafəsi:** qiymət əmrə bu qədər yaxın olanda broker əmrin dəyişdirilməsini və silinməsini bloklayır. O, məhz bir əmr işə düşəndə digərini silmək istədiyiniz anda vurur (İDR-08).
- **10-cu yoxlamada girov bir mövqe üçün hesablanır, iki üçün yox,** çünki normal halda yalnız bir istiqamət işə düşür.

## 7.3 Həcmin hesablanması

**HZR-03.**
- **Fixed Lot:** həcm birbaşa götürülür.
- **Risk %** (hesab vəsaitinin faizi) və **Risk Amount:** həcm SL məsafəsinə görə elə hesablanır ki, SL işə düşəndə itki təyin edilmiş dəyərə bərabər və ya ondan az olsun. Point-in pul dəyəri brokerdən, zərər istiqaməti üçün götürülür.
- Hesablanan həcm brokerin lot addımına **aşağı** yuvarlaqlaşdırılır.

**HZR-04. Brokerin hədlərindən kənar həcm asimmetrik işlənir:**

| Hal | Davranış | Əsası |
|---|---|---|
| Həcm maksimum lotdan böyükdür | Maksimum lot götürülür, hal qeydə alınır | Riski azaldır |
| Həcm minimum lotdan kiçikdir | **Plan buraxılır** | Minimumu götürmək riski artırar |

## 7.4 Pəncərə ərzində vəziyyətin dəyişməsi

**HZR-05. Əmrlər göndərilməzdən dərhal əvvəl simvolun məşğulluğu yenidən yoxlanılır.** Yoxlama ucuzdur: terminal brokerin vəziyyətinin lokal nüsxəsini saxlayır, şəbəkə sorğusu getmir.

## 7.5 Yenidən başlamadan sonra hədəf anı yaxın olan planlar

**HZR-06. Sistem yenidən başlayanda tam hazırlıq pəncərəsi sığmayan plan buraxılır, səbəb qeydə alınır. Qısaldılmış pəncərə qurulmur.**
Əsas: nadir hallarda işləyən kod səhvlərin gizləndiyi yerdir. İtki nadir bir halda bir plandır.

---

# 8. Əmrin yerləşdirilməsi

## 8.1 İki gözləyən əmr

**EMR-01.** Plan üzrə iki gözləyən əmr yerləşdirilir:
- **Alış** — cari **Ask**-dan Offset qədər **yuxarıda**;
- **Satış** — cari **Bid**-dən Offset qədər **aşağıda**.

Ask alıcının ödədiyi, daha yüksək qiymətdir: alış əmri onun üstündədir və onunla işə düşür. Bid satıcının aldığı, daha aşağı qiymətdir: satış əmri onun altındadır və onunla işə düşür. "Alış qiyməti" və "satış qiyməti" ifadələri qəsdən işlədilmir, çünki kimin baxımından deyildiyinə görə tərs oxuna bilir.

**EMR-02. Hər iki əmrin həcmi bərabərdir. Hər ikisinə SL və TP qoşulur,** onları broker saxlayır və icra edir. SL və TP əmrin qiymətindən sayılır.

**EMR-03. Hər iki əmr brokerə eyni anda, cavab gözlənilmədən göndərilir.** Nəticələr ayrıca izlənilir.

**EMR-04. Gözləyən əmrlər hər iki uçot sistemində eyni qaydada yerləşdirilir.**
Əsas: gözləyən əmr mövqe yaratmır. İki istiqamətdə gözləyən əmr üçün hedging lazım deyil.

## 8.2 İcra şərti

**EMR-05. Gözləyən əmrlər "qalığın saxlanılması" icra şərti ilə göndərilir.**
Əsas: gözləyən əmr göndərildiyi anda icra olunmaq üçün deyil. Bazar əmrlərinə aid "tam həcm və ya heç nə" və "qalığı ləğv et" şərtləri burada düzgün deyil.

## 8.3 Gözləyən əmrlərin ömrü

İki qat qoruma tətbiq olunur.

**EMR-06. Birinci qat — sistem.** Expiration rejimi on olduqda, müddət planın xəbər anından sayılır. Həmin müddətdə heç bir əmr işə düşməzsə, sistem əmrləri silir və plan "Vaxtı bitmiş" olur. Faktiki bitmə anı **ən gec günün sonudur** (server vaxtı ilə 00:00). Rejim off olduqda birinci qat işləmir.
Əsas: rejimin ayrıca açar olması sıfır dəyərinin "söndürülmüş", yoxsa "müddət sıfırdır" mənasında ikimənalı oxunmasının qarşısını alır. Gün sonu həddi plan üfüqündən gəlir: əmrin günü aşması plan məntiqində mənasızdır.

**EMR-07. İkinci qat — broker.** Broker əmrə vaxt həddi qoymağı dəstəkləyirsə, bitmə vaxtı **həmişə** qoyulur:

| Birinci qat | Broker səviyyəsində bitmə vaxtı |
|---|---|
| On | Silinmə müddətinin bitdiyi an |
| Off | Həmin günün sonu, server vaxtı ilə 00:00 |

Hər iki halda vaxt **növbəti tam dəqiqəyə yuxarı** yuvarlaqlaşdırılır.
Əsas: sistem dayanarsa birinci qat işləməz, broker isə sistemdən asılı deyil. Bir çox broker yalnız dəqiqə dəqiqliyi qəbul edir. Yuxarı yuvarlaqlaşdırma əmrin vaxtından əvvəl silinməsinin qarşısını alır. Broker vaxt həddini dəstəkləmirsə, plan buraxılmır, sadəcə ikinci qat olmur.

## 8.4 Sapma

**EMR-08. Hədəf an ilə brokerin qeyd etdiyi real yerləşdirmə anı arasındakı fərq ölçülür və dəlil qeydinə yazılır.**

**EMR-09. Sapma heç bir davranışa təsir etmir:** həddi yoxdur, xəbərdarlıq doğurmur, kompensasiya edilmir.
Əsas: broker onsuz da yerləşdirmə anını qaytarır, fərqi hesablamaq pulsuzdur. Onun yeganə dəyəri müşahidədir.

## 8.5 Broker cavabları ilə iş

**EMR-10. Broker hadisələri çatma sırasına görə deyil, aid olduqları əmrə görə işlənir.**
Əsas: platforma hadisələrin çatma sırasına zəmanət vermir.

**EMR-11. Bir istiqamətdəki əmr yerləşdirilə bilməzsə, təkrar qaydası cavabın növünə görədir:**

| Cavabın növü (NSZ-02) | Təkrar |
|---|---|
| **C** — müvəqqəti rədd | Lead Time > 0 olduqda xəbər anına qədər **ən çoxu üç** cəhd. Lead Time = 0 olduqda cəhd yoxdur |
| **B** — nəticəsi naməlum | Əvvəlcə brokerdən soruşulur. Əmr orada yoxdursa, **bir dəfə** təkrar göndərilir |
| **D** — daimi rədd | Təkrar yoxdur |

**EMR-12. Təkrarlar tükəndikdən sonra əmr yenə yerləşdirilə bilmirsə, digər istiqamətdəki əmr də silinir və plan buraxılır.**
Əsas: sistem istiqamət seçmir. Bir tərəf brokerdə qalsa, brokerin təsadüfi rəddi operatorun əvəzinə istiqamət seçmiş olar.

**EMR-13. Cavabı gəlməyən əmr kor-koranə təkrar göndərilmir.** Sistem brokerdən həmin plana aid əmrin olub-olmadığını soruşur. Varsa, əmr yerləşdirilmiş sayılır.
Əsas: "cavab gəlmədi" "əmr getmədi" demək deyil. Kor-koranə təkrar ikiqat əmr yaradır.

---

# 9. Əməliyyatın idarəsi

## 9.1 Kim nəyi icra edir

| Mexanizm | İcra edən | Sistem dayanarsa |
|---|---|---|
| **Stop Loss** | **Broker** | **İşləyir** |
| **Take Profit** | **Broker** | **İşləyir** |
| **Breakeven** | Sistem | Dayanır, SL olduğu yerdə qalır |
| **Trailing Stop** | Sistem | Dayanır, SL olduğu yerdə qalır |

**İDR-01. SL və TP həmişə brokerdədir.** Sistemin nəzarətsiz işləməsi məhz buna söykənir.

## 9.2 Stop Loss və Take Profit

**İDR-02. Stop Loss məcburidir və sıfırdan böyükdür.** Onsuz risk əsaslı həcm hesablanmır və zərər limiti mənasını itirir.

**İDR-03. Take Profit 0 olduqda qoyulmur.** Əməliyyat SL, BE və ya TS ilə bağlanır.

## 9.3 Breakeven

**İDR-04. Mənfəət Breakeven Trigger-ə çatanda SL giriş qiymətindən mənfəət yönündə Lock məsafəsinə çəkilir.** Rejim off olduqda tətbiq olunmur.

## 9.4 Trailing Stop

**İDR-05. SL Distance məsafəsini saxlayaraq qiymətin ardınca mənfəət yönündə hərəkət edir. SL heç vaxt geri qaytarılmır.**

**İDR-06. Breakeven ilə sıra:**

| Breakeven | Trailing nə vaxt işləyir |
|---|---|
| On | Yalnız Breakeven tətbiq olunandan sonra |
| Off | Öz Trigger-i ilə müstəqil |

Əsas: sıra qəti olsaydı, Breakeven-i söndürən operator Trailing-i də bilmədən söndürmüş olardı.

**İDR-07. Yadda saxlanılan "tətiklənmişdi" bayrağı yoxdur.** Trailing hər dəfə iki sual verir: Breakeven tətbiq olunubmu (rejim on-dursa) və qiymət hazırda Trigger səviyyəsindədirmi.
Əsas: "SL yalnız mənfəət yönündə hərəkət edir" qaydası ilə birlikdə bayraq heç vaxt fərqli nəticə vermir. Sadəcə bərpa ediləsi əlavə bir vəziyyət yaradardı.

## 9.5 Əks əmrin silinməsi

Gözləyən əmrlərdən biri işə düşəndə digəri silinir. Bu, üç mexanizmin birləşməsi ilə həll olunur.

**İDR-08. Hadisə əsaslı silmə — sürət üçün.** Broker gözləyən əmrin işə düşməsini ticarət hadisəsi ilə bildirir. Hadisə gələn kimi əks əmr bloklamadan silinir. "Hadisə əsaslı" termini sənəddə yalnız bu mexanizmə aiddir.

**İDR-09. Dövri uzlaşdırma — düzgünlük üçün.** Sistem brokerin faktiki vəziyyətini oxuyub öz reyestri ilə tutuşdurur:

| Vəziyyət | Aralıq |
|---|---|
| Brokerdə sistemin gözləyən əmri və ya mövqeyi var | 1 saniyə |
| Yoxdur | 60 saniyə |

Uzlaşdırmanın dörd işi var: əmrləri və mövqeləri plan identifikatoruna görə reyestrlə tutuşdurmaq; işə düşmüş planın qalmış əks əmrini silmək; planların vəziyyətini brokerin faktiki halına uyğunlaşdırmaq; yenidən başlamadan sonra bərpanı aparmaq (NSZ-08 ayrıca kod yazmır, uzlaşdırmanı çağırır).
Əsas: platformanın hadisə növbəsi məhduddur və hadisə itə bilər. Yalnız hadisəyə güvənən sistem əks əmri brokerdə unuda bilər. Yoxlama lokaldır, şəbəkə sorğusu getmir, obyekt sayı kiçikdir. Platforma taymer hadisələrini yığmır, ona görə sıx takt növbəni şişirtmir.

**İDR-10. Dondurma halında silmə təkrar edilir.** Dondurma keçicidir.

**İDR-11. Əmr işə düşəndən 10 saniyə sonra əks əmr hələ brokerdədirsə, bir təcili bildiriş göndərilir.** Silmə cəhdləri uzlaşdırma ilə davam edir, ticarət dayandırılmır.
Əsas: brokerdə qalan, sistemin idarə edə bilmədiyi əmr real pul riskidir. Dondurma adətən qısadır, 10 saniyədən uzun qalma anormal haldır.

**İDR-12. Hər iki əmr işə düşərsə** (nasazlıq halı):

| | Hedging | Netting |
|---|---|---|
| Həcmlər bərabərdir | İki mövqe ayrıca yaşayır, hər biri öz SL/TP-si və nüsxədəki BE/TS ilə idarə olunur | İkinci sövdələşmə birincini **bağlayır**. Mövqe qalmır, plan "Tamamlanmış" olur |
| Həcmlər fərqlidir (qismən icradan sonra) | İki mövqe ayrıca yaşayır | Mövqe **çevrilir**. Yeni mövqe ikinci əmrin SL/TP-si ilə olur və nüsxədəki BE/TS ilə idarə olunur |
| Broker ikinci mövqeni rədd edir (10046) | Plan bir mövqe ilə davam edir | — |

Hər halda hal qeydə alınır və xülasəyə düşür.
Əsas: netting-in davranışı rəsmi qaydadır: əks sövdələşmə mövqeni azaldır, bağlayır və ya çevirir, "It does not matter, what has caused the opposite deal". Çevrilmədə SL/TP "according to its latest order" qoyulur.

## 9.6 Əmr işə düşdü, amma mövqe yaranmadı

**İDR-13. Broker aktivləşməni rədd edib əmri ləğv edibsə (10041): əks əmr də silinir, plan buraxılır, təcili bildiriş göndərilir.**
Əsas: brokerin rəddi bir istiqaməti aradan götürür. Əks əmr qalsaydı, istiqaməti bazar yox, broker seçmiş olardı (EMR-12). Gözlənilməz halda imkanı itirmək pul qoymaqdan ucuzdur (NSZ-01). Qaralamadakı "plan işlək qalır" qaydası bu səbəbdən ləğv edilib.

## 9.7 Qismən icra

**İDR-14. Əmrin bir hissəsi icra olunarsa: açılmış mövqe azaldılmış həcmlə öz qaydaları ilə idarə olunur, qalıq əmr və əks istiqamətdəki əmr silinir.** Hal qeydə alınır.
Əsas: planın məntiqi "bir istiqamət işə düşdü, iş bitdi"dir. Qalıq əmr eyni plan üzrə ikinci əməliyyat yaradardı.

---

# 10. Risk nəzarəti

## 10.1 Zərər limitləri

**RSK-01. İki müstəqil limit var: Daily Loss Limit və Weekly Loss Limit.** Hər biri ayrıca on/off edilir və məbləği hesabın valyutasında ayrıca təyin olunur.
Əsas: günlərə yayılan pis seriyanı günlük limit tutmur.

**RSK-02. Zərər belə hesablanır:**

| Daxil edilir | Çıxılmır |
|---|---|
| Dövr ərzində bağlanmış **zərərli** əməliyyatların zərəri | Bağlanmış əməliyyatların qazancı |
| Açıq əməliyyatların **mənfi** cari nəticəsi | Açıq əməliyyatların müsbət nəticəsi |
| Dövr ərzində alınmış **bütün** komissiya | Müsbət svop |
| Dövr ərzində yazılmış **mənfi** svop | |

Nümunə: gün ərzində +100 və −150 olan iki əməliyyat varsa, zərər **50 deyil, 150** sayılır.
Əsas: limitin məqsədi "bu gün X-dən çox itirməyim" deyil, **"X-lik pis ticarətdən sonra məni dayandır, şansdan asılı olmayaraq"**-dır. Açıq zərərin daxil edilməsinin əsası: böyük açıq zərər görünməsəydi, sistem ticarətə davam edərdi.

**RSK-03. Dövrlər sabit qaydadır, operator parametri deyil:** günlük dövr hər gün 00:00-da, həftəlik dövr bazar ertəsi 00:00-da sıfırlanır (server vaxtı ilə).

**RSK-04. Zərər limitin məbləğinə çatanda limit aşılmış sayılır** (bərabərlik də aşılmadır).

**RSK-05. Aşılmış limit dövrün sonuna qədər kilidlənir.** Rəqəm sonradan limitin altına düşsə belə kilid açılmır. Operatorun kilidi ləğv etmək imkanı yoxdur.
Əsas: bazarın geri dönməsi artıq edilmiş pis ticarəti yaxşı etmir.

**RSK-06. Kilid dövründə: yeni plan qurulmur, bütün planlar icradan çıxarılır, gözləyən əmrlər silinir.**

**RSK-07. Kilid dövründə açıq mövqelər bağlanır.** Bu, sistemin açıq mövqeni öz qərarı ilə bazar qiyməti ilə bağladığı yeganə haldır. Bağlama sırası:
- **Hedging:** bir simvolda bir neçə mövqe varsa, **ən köhnəsi birinci** bağlanır;
- **Netting:** bərabər həcmli əks sövdələşmə ilə.

Bağlama alınmazsa, mövqe qaldığı müddətcə cəhd təkrarlanır, hər rədd qeydə alınır. Plan mövqe bağlanandan sonra "Dayandırılmış" olur (PLN-10).
Əsas: limitin məqsədi zərəri kəsməkdir. Açıq əməliyyatın mənfi nəticəsi limitə daxildir, bağlanmasa limit məqsədinə çatmazdı. Ən köhnəni birinci bağlamaq FIFO qaydası olan brokerdə də (10045) rədd yaranmamasını təmin edir.

## 10.2 Blackout

**RSK-08. Blackout aralıqları həftənin hər günü üçün ayrıca təyin olunur,** bir gündə bir və ya daha çox aralıq ola bilər. Aralıqlar saat və dəqiqə ilə, server vaxtı ilə yazılır.
- Başlama və bitmə anları aralığa daxildir. **Bitmə dəqiqəsi tam daxildir:** `18:20` 18:20:59-a qədər deməkdir.
- **Bitmə başlamadan kiçikdirsə, aralıq növbəti günə keçir.** Məsələn, cümə axşamı `23:50–00:15` cümə saat 00:15:59-a qədər davam edir. Bazar günündən bazar ertəsinə keçid də eyni qaydadır. Panel bunu "23:50 → 00:15 (cümə)" kimi göstərir.

Əsas: blackout-un ən tipik istifadəsi gecə yarısı rollover-idir. Bitmə dəqiqəsinin tam daxil olması iki aralıq arasında saniyə boşluğu qalmasının qarşısını alır.

**RSK-09. Blackout iki nöqtədə yoxlanılır — plan qurularkən (qurulmur) və hazırlıqda (buraxılır) — və iki an üçün: planın xəbər anı və hədəf yerləşdirmə anı.**
Əsas: əmr blackout-dan kənarda yerləşdirilsə belə, xəbər blackout içində açıqlanırsa, əməliyyat məhz orada açılacaq. Hazırlıqda təkrar yoxlama lazımdır, çünki operator bu arada blackout əlavə etmiş ola bilər.

## 10.3 Ticarət rejimi

**RSK-10. Trading Mode iki dəyərdən birini alır: Active və Paused.** Paused olduqda:
- icraya keçmiş planların (Hazırlıqda, Əmri göndərilmiş) gözləyən əmrləri silinir, bu planlar PLN-10-a uyğun "Dayandırılmış" olur;
- açıq mövqelər öz qaydaları ilə idarə olunmağa davam edir;
- **"Qurulmuş" planlara toxunulmur.** Onların hazırlıq anında 3-cü yoxlama (HZR-02) rejimi yoxlayır: rejim hələ Paused-dırsa, plan "Dayandırılmış" olur, Active-dirsə, adi qaydada icra olunur.

Əsas: dayandırma yalnız **yeni riski** kəsir. Operator ticarəti qısa müddətə dayandırıb geri açanda günün qalan planları itməməlidir. Qaralamada bütün planların birdən son vəziyyətə keçməsi bu itkiyə səbəb olurdu.

**RSK-11. Ticarət rejimi ilə zərər limiti arasındakı fərq:**

| | Yeni plan | Qurulmuş planlar | Gözləyən əmrlər | Açıq mövqelər | Geri açılma |
|---|---|---|---|---|---|
| Trading Mode = Paused | Qurulur | Hazırlıqda yoxlanılır | Silinir | İdarə olunur | Operator istədiyi an |
| Zərər limiti kilidlidir | Qurulmur | Dayandırılır | Silinir | **Bağlanır** | Dövrün sonunda |
| Quraşdırma kilidi (NSZ-04) | Qurulur | Hazırlıqda yoxlanılır | Silinir | İdarə olunur | Operatorun təsdiqi ilə |

---

# 11. Nasazlıq və bərpa

## 11.1 Əsas qayda

**NSZ-01. Sənəddə adı çəkilməyən, gözlənilməz bir hal yaşandıqda plan buraxılır və hal dəlil qeydinə yazılır.**
Əsas: gözlənilməz hal tərifinə görə heç kimin düşünmədiyi haldır. Ora pul qoymaq risklidir, imkanı itirmək isə ucuzdur.

Qeydə alma ilə bildiriş eyni şey deyil. Qeydə alma limitsizdir, bildiriş isə qıt resursdur və ayrıca siyasətə tabedir (15.1).

## 11.2 Broker cavablarının təsnifatı

**NSZ-02. Broker cavabları beş kateqoriyaya bölünür:**

**A — Uğur.** Cəhd uğurlu sayılır.

| Kod | Mənası |
|---|---|
| 10008 | Əmr yerləşdirildi |
| 10009 | Sorğu tamamlandı |
| 10025 | Sorğuda dəyişiklik yoxdur |
| 10036 | Göstərilən mövqe artıq bağlanıb (məqsəd əldə olunub) |

**B — Nəticəsi naməlum.** Təkrar göndərmə təhlükəlidir. Əvvəlcə brokerdən soruşulur (EMR-11, EMR-13).

| Kod | Mənası |
|---|---|
| 10011 | Sorğunun emal xətası |
| 10012 | Sorğu vaxta görə ləğv edildi |
| 10031 | Ticarət serveri ilə bağlantı yoxdur |

**C — Müvəqqəti rədd.** Xəbər anına qədər ən çoxu üç cəhd (EMR-11).

| Kod | Mənası |
|---|---|
| 10004 | Yeni qiymət təklifi |
| 10020 | Qiymətlər dəyişdi |
| 10021 | Kotirovka yoxdur |
| 10023 | Əmrin vəziyyəti dəyişdi |
| 10024 | Sorğular həddindən çox tez-tezdir |
| 10028 | Sorğu emal üçün kilidlənib |
| 10029 | Əmr və ya mövqe dondurulub |

**D — Daimi rədd.** Plan buraxılır, təkrar edilmir.

| Kod | Mənası |
|---|---|
| 10006 | Sorğu rədd edildi |
| 10007 | Sorğu trader tərəfindən ləğv edildi |
| 10013 | Sorğu düzgün deyil |
| 10014 | Həcm düzgün deyil |
| 10015 | Qiymət düzgün deyil |
| 10016 | Stop səviyyələri düzgün deyil |
| 10017 | Ticarət qadağandır |
| 10018 | Bazar bağlıdır |
| 10019 | Vəsait çatmır |
| 10022 | Bitmə tarixi düzgün deyil |
| 10030 | İcra şərti düzgün deyil |
| 10033 | Gözləyən əmrlərin sayı limitə çatıb |
| 10034 | Simvol üzrə həcm limitə çatıb |
| 10035 | Əmrin növü düzgün deyil və ya qadağandır |
| 10038 | Bağlama həcmi mövqe həcmini aşır |
| 10039 | Mövqe üçün artıq bağlama əmri var |
| 10040 | Açıq mövqelərin sayı limitə çatıb |
| 10041 | Aktivləşmə rədd edildi, əmr ləğv olundu (İDR-13: əlavə olaraq təcili bildiriş) |
| 10042 | Simvolda yalnız alışa icazə var |
| 10043 | Simvolda yalnız satışa icazə var |
| 10044 | Simvolda yalnız bağlamaya icazə var |

10040 haqqında: hedging hesablarında gözləyən əmrlər açıq mövqelərlə **birlikdə sayılır**. Çox simvol xəritələnəndə bu hədd gözlənilmədən vura bilər.

**E — Quraşdırma pozuntusu.** Quraşdırma kilidi qoyulur (NSZ-04).

| Kod | Mənası |
|---|---|
| 10026 | Avtomatik ticarət server tərəfindən qadağandır |
| 10027 | Avtomatik ticarət terminal tərəfindən qadağandır |
| 10032 | Əməliyyat yalnız real hesablar üçün icazəlidir |

Əsas: bu rəddlər "bu plan alınmadı" demək deyil, **"sistem bu hesabda ümumiyyətlə işləyə bilməz"** deməkdir. Onları daimi rədd kimi işləmək sistemin hər xəbərdə plan buraxa-buraxa səssiz işləməsi olardı.

**Nasazlıq halı kodları.** Bunlar yalnız hər iki əmr işə düşəndə, bir simvolda iki mövqe olanda görünə bilər. Sistemin işləmə qabiliyyətini göstərmirlər:

| Kod | Mənası | Davranış |
|---|---|---|
| 10046 | Eyni simvol üzrə əks mövqelər qadağandır | Plan bir mövqe ilə davam edir (İDR-12) |
| 10045 | Bağlama yalnız FIFO ilə | Ən köhnə mövqe birinci bağlanır (RSK-07), düzgün sıra ilə təkrarlanır |

**Qismən icra (10010)** ayrıca işlənir (İDR-14). **Siyahıda olmayan kod** üçün NSZ-01 tətbiq olunur.

## 11.3 Quraşdırma kilidi

**NSZ-03. Quraşdırma kilidi E kateqoriyasında və birja uçot sistemli hesab aşkarlananda qoyulur.**

**NSZ-04. Kilidin təsiri Trading Mode = Paused ilə eynidir** (RSK-10): icraya keçmiş planların gözləyən əmrləri silinir, qurulmuş planlar hazırlıqda yoxlanılır (HZR-02, 5-ci yoxlama), açıq mövqelər öz qaydaları ilə idarə olunur. Kilid qoyulanda **təcili bildiriş** gedir.
Əsas: pozuntu pul riskini yox, sistemin işləmə qabiliyyətini göstərir. Mövqeni bağlamaq üçün əsas yoxdur.

**NSZ-05. Kilid yadda qalır:** səbəbi, broker kodu və vaxtı vəziyyətdə və lokal olaraq saxlanılır. Sistem yenidən başlasa da qüvvədədir.

**NSZ-06. Kilid yalnız operatorun təsdiqi ilə açılır:** operator problemi həll edir, sonra paneldə Trading Mode-u **Paused, sonra Active** edir. Kilid anından sonra edilmiş belə keçid təsdiq sayılır. Keçidin vaxtını API qatı qeyd edir (API-06). Yeni düymə və ya əmr yaranmır.
Əsas: pozuntu adətən quraşdırma problemidir və özü düzəlmir. Avtomatik açılış hər gün eyni rəddi və eyni həyəcanı təkrarlayardı.

**NSZ-07. Kilid aktiv olduqda panelin Vəziyyət ekranında səbəb, kod, vaxt və açılış qaydası qırmızı ilə göstərilir.**

## 11.4 Ticarət serveri ilə bağlantının kəsilməsi

**NSZ-08. Kəsilmə müddətində sistem heç bir əmr göndərmir** (nə yerləşdirmə, nə silmə, nə SL hərəkəti). Bərpadan sonra **əvvəlcə** brokerin faktiki vəziyyəti oxunur, sonra işə davam edilir. Kəsilmə müddətində hədəf anı ötmüş planlar buraxılır. Kəsilmə və bərpa təcili bildirişlə bildirilir.
Əsas: kəsilmə zamanı yaranan əmrləri növbəyə yığıb sonra göndərmək təhlükəlidir, çünki xəbər anı keçmiş ola bilər.

## 11.5 API qatı və ya anbar əlçatmaz olduqda

**NSZ-09. Ticarət davam edir.** Sistem sonuncu yoxlanmış konfiqurasiya ilə işləyir (LOK-11). Qeydlər, vəziyyət və parametr nüsxələri lokal növbəyə yığılır və əlaqə bərpa olunanda göndərilir. Epizod başına bir təcili bildiriş gedir ("sistemin bir hissəsi cavabsızdır"), bərpa xülasəyə düşür.
Əsas: hər iki xarici təqvim mənbəyi API-dən kənardır, konfiqurasiya isə yaddaşda və lokal nüsxədədir. API ticarətin isti yolunda deyil.

**NSZ-10. Panel əlçatmaz olduqda sistemə heç bir təsiri olmur.**

## 11.6 Sistem yenidən başladıqda bərpa

**NSZ-11. Ticarət EA yenidən başlayanda:**
1. Brokerdən öz gözləyən əmrlərini və mövqelərini plan identifikatoruna görə tanıyır. **Broker həqiqət mənbəyidir.**
2. Qlobal dəyişənlər itibsə (ARX-08), planların vəziyyəti brokerin faktiki halından və Rabitə EA-nın plan siyahısından bərpa olunur. Plan identifikatoru deterministik olduğu üçün (PLN-09) eyni plan eyni nömrəni alır.
3. Açıq mövqelərin parametr nüsxələri LOK-07-yə görə bərpa olunur. Nüsxə gələnə qədər mövqe yalnız brokerdəki SL/TP ilə qorunur.
4. Breakeven-in artıq tətbiq olunub-olunmadığı SL-in giriş qiymətinə nisbətindən bərpa olunur.
5. Tam hazırlıq pəncərəsi sığmayan planlar buraxılır (HZR-06).

**NSZ-12. Rabitə EA yenidən başlayanda** konfiqurasiyanı LOK-11-ə görə götürür, təqvimi yenidən oxuyur və planları deterministik identifikatorlarla yenidən qurur. Son vəziyyətli açarlar təkrarlanmır (PLN-12).

## 11.7 Sistemin öz-özünü izləməsi

**NSZ-13. Hər EA 5 saniyədə bir nəbz qoyur** (qlobal dəyişəndə). **Ardıcıl üç nəbz buraxılarsa** (15 saniyə), digər EA onu susmuş sayır.
Əsas: tək bir gecikmə saxta həyəcan verməməlidir. Nəbz bir rəqəmdir, şəbəkəyə çıxmır, xərci yoxdur.

**NSZ-14. Ticarət EA susarsa, Rabitə EA təcili bildiriş göndərir.** Rabitə EA susarsa, Ticarət EA **bir** təcili bildiriş göndərir (ARX-04). Bu, yalnız heç bir plan hazırlıq pəncərəsində və ya yerləşdirmə anında olmayanda baş verir. Susan EA qayıdanda bərpa mesajını Rabitə EA göndərir.
Əsas: SendNotification-ın müddəti rəsmi sənəddə yazılmayıb. Bu şərtlərlə o, heç vaxt əmrləri gecikdirmir. Müddət Mərhələ 1-də ölçülür.

**NSZ-15. İki EA birdən susarsa, proaktiv aşkarlama yoxdur.** Operator bunu iki yolla bilir: paneldə vəziyyətin vaxt möhrü köhnəlir və gündəlik xülasə gəlmir. Bu, şüurlu qərardır: xarici izləyici xidmət yeni asılılıq olardı. **Sistem tamamilə dayanıbsa, operator bunu ən geci növbəti gündəlik xülasənin gəlmədiyi anda biləcək.**

---

# 12. İki EA arasında əlaqə və lokal fayllar

## 12.1 Əlaqə vasitələri

İki EA eyni terminalın içindədir, aralarındakı əlaqə lokaldır və şəbəkədən keçmir.

| Vasitə | Nə üçün | Xassəsi |
|---|---|---|
| **Qlobal dəyişən** | Nəbzlər, plan siyahısının növbə nömrəsi, hər planın vəziyyət kodu, quraşdırma kilidinin bayrağı | Ani, parse tələb etmir. `SetOnCondition` atomikdir. Yalnız rəqəm saxlayır, ad ≤ 63 simvol, son istifadədən 4 həftə sonra silinir |
| **Lokal fayl** | Plan siyahısı, konfiqurasiya nüsxəsi, parametr nüsxələri, qeyd növbəsi | Strukturlu məlumat, mətn |

**EAL-01. İsti yolda nə fayl, nə şəbəkə işlədilir.** İsti yol bunlardır: hədəf anda əmrlərin göndərilməsi, əks əmrin silinməsi, BE/TS hərəkətləri. Burada Ticarət EA yalnız öz yaddaşı və broker ilə işləyir. Lazım olan hər şey əvvəlcədən yaddaşa yüklənir. İsti yolda yaranan qeydlər yaddaşda toplanır və isti yoldan çıxandan sonra fayla yazılır.
Əsas: fayl əməliyyatı və şəbəkə gözləməsi yerləşdirmə dəqiqliyini poza bilər. İsti yolda qeydlərin yaddaşda olması o deməkdir ki, həmin bir neçə saniyədə sistem çöksə, yalnız "niyə" qeydləri itir. "Nə oldu" brokerin tarixçəsində qalır.

**EAL-02. Qlobal dəyişənlərin adları `EE.` prefiksi ilə başlayır.**

**EAL-03. Fayllar yalnız isti yoldan kənarda oxunur və yazılır.**

**EAL-04. Planın sahibliyi atomik keçir:**
- "Qurulmuş" vəziyyətdə planın sahibi **Rabitə EA**-dır: qurur, yeniləyir, ləğv edir.
- "Hazırlıqda" və sonrakı vəziyyətlərdə sahibi **Ticarət EA**-dır.
- Keçidlər yalnız `GlobalVariableSetOnCondition` ilə aparılır: Ticarət EA "Qurulmuş → Hazırlıqda", Rabitə EA "Qurulmuş → Ləğv edilmiş". **Birinci gələn qalib gəlir,** digərinin cəhdi rədd edilir.
Əsas: plan eyni anda həm ləğv edilə, həm icraya keçə bilməz. Rəsmi sənədə görə bu funksiya "atomic access … for providing of a mutex at interaction of several Expert Advisors" verir.

**EAL-05. Plan siyahısının ötürülməsi:** Rabitə EA yeni siyahını fayla yazır (LOK-02), sonra qlobal dəyişəndəki növbə nömrəsini artırır. Ticarət EA öz taktında nömrənin dəyişdiyini görüb faylı oxuyur. Köhnə nömrəli siyahı yenisini əvəz edə bilmir. EA-lar arasında qrafik hadisəsi işlədilmir.
Əsas: siyahılar dəqiqələrlə əvvəl hazırlanır, bir taktlıq gecikmə heç nəyə təsir etmir. Hadisə ilə xəbərdarlıq əlavə kod və terminoloji qarışıqlıq olardı.

**EAL-06. Qlobal dəyişənlər təmizlənir:** plan son vəziyyətə çatanda onun dəyişənləri silinir. Hər EA işə düşəndə öz köhnə `EE.` dəyişənlərini süpürür.

**EAL-07. Hər EA bir baza taktla işləyir.** Bütün dövri işlər (hazırlıq, uzlaşdırma, nəbz, növbə nömrəsinin yoxlanması) bu taktdan törəyir.
Əsas: rəsmi qaydaya görə "For each program no more than one timer can be run". Növbədə və ya emalda Timer hadisəsi varsa, yenisi əlavə olunmur, ona görə sıx takt növbə şişirtmir.

## 12.2 Rabitə EA-nın şəbəkə qaydaları

**EAL-08. Vəziyyət və qeyd toplusu API-yə bir sorğu ilə göndərilir.** API onları ayırır (API-07).

**EAL-09. Rabitə EA hər taktda ən çoxu bir şəbəkə sorğusu göndərir.** Forex Factory sorğusu, konfiqurasiya oxunuşu və vəziyyət yazılışı ayrı taktlara düşür.

**EAL-10. Hər şəbəkə sorğusunun gözləmə həddi ≤ 5 saniyədir. Nəbz hər sorğudan əvvəl və sonra yenilənir.**
Əsas: `WebRequest` sinxrondur və gözləmə zamanı nəbz dayanır. Bu qaydalarla ən pis halda bir nəbz buraxılır, susma həddi olan üç yox (NSZ-13).

**EAL-11. JSON emalı sistemin öz kodu ilə aparılır.** API qatı EA-ya sadə və sabit formatlı JSON göndərir.
Əsas: MQL5-in rəsmi funksiyalar siyahısında JSON funksiyası yoxdur. Sabit format oxuyucunu sadə və sınanan saxlayır.

## 12.3 Lokal fayllar

**LOK-01. Bütün lokal fayllar `MQL5\Files\EagleEyes\` qovluğundadır.** Sistem yalnız **öz ad nümunəsinə uyğun** faylları silir.

**LOK-02. Yazma iki addımla aparılır:** əvvəl müvəqqəti fayla, sonra `FileMove(..., FILE_REWRITE)` ilə əsas adın üzərinə.

**LOK-03. Hər faylda bütövlük nişanı var** (məzmunun uzunluğu və yoxlama cəmi). Oxunanda uyğun gəlmirsə, fayl **heç olmamış sayılır** və silinir.
Əsas: rəsmi sənəd `FileMove`-un atomik olduğunu demir. LOK-02 yarımçıq yazmanın qarşısını çox vaxt alır, həqiqi qoruma isə LOK-03-dür.

**LOK-04. Hər EA işə düşəndə öz qovluğunda qalmış müvəqqəti faylları silir.**

**LOK-05. Faylların ömrü:**

| Fayl | Kim yazır | Sayı | Yenilənmə və silinmə |
|---|---|---|---|
| Konfiqurasiya nüsxəsi | Rabitə EA | 1 | LOK-11. Sistem heç vaxt silmir |
| Parametr nüsxəsi | Ticarət EA | Canlı plan başına 1 | LOK-06…LOK-10 |
| Plan siyahısı | Rabitə EA | 1 | Hər dəyişiklikdə üzərinə yazılır |
| Konfiqurasiya ötürməsi (Ticarət EA üçün) | Rabitə EA | 1 | Hər dəyişiklikdə üzərinə yazılır |
| Forex Factory keşi + etiket | Rabitə EA | 1 | Yeni nüsxə gələndə üzərinə yazılır |
| Ticarət EA-nın qeyd növbəsi | Ticarət EA | Toplu başına 1 | Rabitə EA götürəndən sonra silinir |
| API növbəsi (qeyd, vəziyyət, nüsxə, silmə tələbi) | Rabitə EA | Element başına 1 | API təsdiqləyəndən sonra silinir. **Hədd yoxdur** |

Növbəyə hədd qoyulmamasının əsası: sıx xəbər günündə qeydlər yüz kilobaytlarla ölçülür, VPS diski isə 16 GB-dır. Həddin kodu praktikada heç vaxt işləməzdi.

### Parametr nüsxəsinin həyat dövrü

**LOK-06. Yazma.** Hazırlığa keçid anında Ticarət EA nüsxəni lokal fayla yazır. Rabitə EA onu API-yə yükləyir. API əlçatmazdırsa, yükləmə növbəyə düşür.

**LOK-07. Bərpa.** Sistem yenidən başlayanda nüsxə əvvəlcə API-dən, sonra lokal fayldan götürülür. İkisi eyni andan gəldiyi üçün eynidir. **İkisi də yoxdursa,** mövqe yalnız brokerdəki SL/TP ilə qalır və **təcili bildiriş** gedir ("Mövqe X: Breakeven və Trailing aktiv deyil").

**LOK-08. Adi silmə.** Plan son vəziyyətə çatanda lokal fayl dərhal silinir, API-dəki nüsxə üçün silmə tələbi göndərilir. API əlçatmazdırsa, tələb növbəyə düşür.

**LOK-09. Yetim nüsxələrin süpürülməsi.** Sistem yenidən başlayanda və hər gün 00:00-da hər iki yerdəki nüsxələr siyahılanır. Nüsxə **yalnız brokerdə həmin plan identifikatoru ilə nə gözləyən əmr, nə açıq mövqe olmadıqda** silinir. Lokal nüsxələri Ticarət EA, API-dəkiləri Rabitə EA süpürür.
Əsas: silmədən əvvəl sistem çöksə, yaddaşdakı növbə itir və nüsxə əbədi qalar. Meyar tarix deyil, brokerin faktiki vəziyyətidir, ona görə gecəyə keçən mövqenin nüsxəsinə toxunulmur.

**LOK-10. Təhlükəsizlik.** Ticarət serveri ilə bağlantı yoxdursa, süpürmə işləmir. Silmə uğursuzluğu ticarətə təsir etmir, qeydə alınır və növbəti süpürmədə təkrarlanır.
Əsas: bağlantı yoxdursa, terminalın broker vəziyyəti köhnə və ya boş ola bilər. Onda canlı mövqenin nüsxəsi səhvən silinərdi.

### Konfiqurasiya nüsxəsi

**LOK-11. Konfiqurasiya:**
- **K-1.** API həqiqət mənbəyidir. Rabitə EA konfiqurasiyanı hər 30 saniyədə oxuyur və yoxlayır. Məzmun lokal nüsxədən fərqlidirsə, lokal nüsxə yoxdursa və ya zədəlidirsə, lokal nüsxəyə yazır. API əlçatandırsa, lokal nüsxə **oxunmur**. Lokal nüsxə API-yə heç vaxt geri yazılmır.
- **K-2.** Yalnız yoxlamadan keçmiş konfiqurasiya lokal nüsxəni əvəz edir. Pis nüsxə yaxşı nüsxəni silmir.
- **K-3.** Sistem yenidən başlayanda API əlçatmazdırsa, lokal nüsxə işlədilir və **təcili bildiriş** gedir ("konfiqurasiya lokal nüsxədən, saxlanma vaxtı: …"). İkisi də yoxdursa, plan qurulmur və **təcili bildiriş** gedir ("konfiqurasiya yoxdur, ticarət aparılmır"). API qayıdanda sistem özü davam edir.
- **K-4.** Lokal nüsxəyə yaş həddi qoyulmur. Konfiqurasiya həftələrlə dəyişməyə bilər, bu köhnəlmə deyil.

Lokal nüsxə köhnə ola bilər. Məsələn, operator Trading Mode-u 07:59-da Paused edib, Rabitə EA isə onu hələ oxumayıb. Bu, API əlçatmaz olanda yaddaşdakı konfiqurasiya ilə eyni riskdir. Ondan qoruyan qayda 3.6-dadır: fövqəladə dayandırma MetaTrader-dən edilir.

---

# 13. API qatı və anbar

## 13.1 API qatının iki səthi

**API-01. API qatının iki girişi var:**

| Ünvan | Kim | Giriş |
|---|---|---|
| `api.<domen>` | EA | Hər sorğuda token (API-04) |
| `panel.<domen>` | Panel (brauzer) | Cloudflare Access + MFA, API imzalı tokeni yoxlayır (API-05) |

Arxivləmə işi ayrıca daxili sorğudur və platformanın cədvəlləyicisi tərəfindən öz tokeni ilə çağırılır (API-09).

**API-02. EA sorğuları:**

| Sorğu | Məqsəd |
|---|---|
| Konfiqurasiyanı oxu | 30 saniyədə bir. Dəyişməyibsə məzmun ötürülmür |
| Vəziyyəti və qeyd toplusunu yaz | 10 saniyədə bir, bir sorğu ilə |
| Parametr nüsxəsini yaz / oxu / sil | LOK-06…LOK-09 |
| Parametr nüsxələrini siyahıla | LOK-09 |

**API-03. Panel sorğuları:**

| Sorğu | Məqsəd |
|---|---|
| Vəziyyəti oxu | Vəziyyət və Təqvim ekranları |
| Konfiqurasiyanı oxu / yaz | Konfiqurasiya ekranı |
| Qeydləri tarix aralığı üzrə oxu | Canlı və arxiv qeydləri bir cavabda (ANB-08) |

API-də panel üçün **heç bir ticarət hərəkəti** və heç bir silmə sorğusu yoxdur.

**API-04. EA tokeni** EA parametrlərində saxlanılır və yalnız EA sorğularına icazə verir. API-də dərhal ləğv edilə bilir.

**API-05. Panel girişi:** Access hər sorğuya imzalı token qoyur (`Cf-Access-Jwt-Assertion`). API tokeni standart üsulla yoxlayır: RS256 imzası, açıq açarlar siyahısında `kid` uyğunlaşdırılması, `iss` və `aud`. Başlığın adı, açarlar ünvanı, `iss` və `aud` **parametrdir**, kod deyil.
Əsas: portativlik (PRT-01). Açarlar 6 həftədən bir dəyişir, ona görə sabit kodlanmır, ünvandan oxunur.

**API-06. Konfiqurasiya yazılmazdan əvvəl API onu yoxlayır:** 18.2-dəki aralıqlar, məcburi sahələr, simvol × Impact unikallığı. Yoxlamadan keçməyən konfiqurasiya yazılmır və cavabda gözlənilən aralıq göstərilir. API Trading Mode-un **son dəyişmə vaxtını** özü qeyd edir. Operator bu vaxtı dəyişə bilməz (NSZ-06). Hər konfiqurasiya dəyişikliyi kimin tərəfindən (Access kimliyi) və nə vaxt edildiyi ilə dəlil qeydinə yazılır.
Əsas: EA-da da yoxlama var (K-2), bu isə ikinci qatdır. Dəyişikliyin izi təhlükəsizlik baxımından vacibdir (THL-06).

**API-07. Vəziyyət və qeyd toplusu bir sorğu ilə gəlir.** API vəziyyəti üzərinə yazır, qeyd toplusunu isə yeni, unikal adlı obyekt kimi yazır (toplu boşdursa, yazmır).

**API-08. API-nin anbara əlaqəsi yalnız dörd S3 əməliyyatı ilədir:** GetObject, PutObject, DeleteObject, ListObjectsV2. İmza SigV4-dür (hazır kitabxana ilə). Hər yazmada `Content-MD5` göndərilir (PRT-02).

## 13.2 Arxivləmə

**API-09. Dəlil qeydləri həftəlik arxivlənir:**
- **A-1.** Arxivləmə hər həftə, **bazar ertəsi 00:00-dan sonra**, ticarət serveri vaxtı ilə aparılır. Server vaxtının UTC-dən fərqini EA vəziyyətdə yazır. API sonuncu bilinən fərqi işlədir.
- **A-2.** Arxivləmə API-də adi bir sorğudur və platformanın cədvəlləyicisi onu çağırır. Platforma dəyişsə, yalnız cədvəl yenidən qurulur.
- **A-3.** Keçən həftənin canlı topluları bir sıxılmış arxiv faylına yığılır. Arxiv yazılır, **geri oxunur**, qeyd sayı və yoxlama cəmi canlı toplularla tutuşdurulur.
- **A-4.** Həmin həftənin arxivi artıq varsa, iş təkrarlanmır. Uğursuz həftə növbəti çağırışda tamamlanır.
- **A-5.** Canlı toplu **iki şərt birlikdə ödənəndə** silinir: onun həftəsinin arxivi yoxlanıb **və** canlı qoruma müddəti (30 gün) keçib. API bunu hər həftə yoxlayır. Canlı bucket-da təxminən son 5 həftənin topluları olur.
- **A-6.** Uyğunsuzluq varsa, heç nə silinmir və hal qeydə alınır.

Əsas: toplular 10 saniyədə bir yazılır, yəni ayda 259 000-ə qədər obyekt yaranır. Siyahılama AWS-də yazma tarifi ilə ödənilir. Arxiv hər həftəni bir obyektə yığır. Canlı qoruma kodda səhv olsa belə 30 gün ərzində heç bir qeydin itməməsini fiziki olaraq təmin edir.

## 13.3 Anbarın quruluşu

**ANB-01. Anbar beş bucket-dan ibarətdir:**

| Bucket | Məzmun | Saxlama sinfi | Versiya | Qoruma | Yazılma |
|---|---|---|---|---|---|
| `config` | Konfiqurasiya | Standard | Yox | Yox | Redaktədə üzərinə yazılır |
| `state` | Vəziyyət (simvol xüsusiyyətləri daxil) | Standard | **Yox** | Yox | 10 saniyədə bir üzərinə yazılır |
| `snapshots` | Parametr nüsxələri | Standard | Yox | Yox | Yazılır, silinir |
| `evidence-live` | 10 saniyəlik qeyd topluları | Standard | Bəli | **30 gün** | Hər toplu yeni obyektdir |
| `evidence-archive` | Həftəlik arxivlər | **Glacier Instant Retrieval** (R2-də Standard-IA) | Bəli | **Governance, müddətsiz** | Həftədə bir yeni obyekt |

**ANB-02. Hər sahə ayrıca bucket-dır, prefiks deyil.**
Əsas: R2 tokenləri yalnız bucket üzrə daralır. AWS-də qoruma müddəti bucket-dakı "every object version placed in the bucket" üçündür, obyekt üzrə fərqli müddət isə R2-nin dəstəkləmədiyi başlıqlarla verilir. Canlı və arxiv qeydlərinin fərqli qorumaya ehtiyacı var, ona görə ayrı bucketlardadır.

**ANB-03. Versiya və qoruma yalnız iki qeyd bucket-ında açılır.**
Əsas: AWS Object Lock yalnız versiyalı bucketda işləyir. `state` bucket-ında versiya açılsaydı, ayda iki yüz mindən çox versiya yığılardı. Qoruma üzərinə yazmanı da bloklayır, `state`-ə və `config`-ə tətbiq olunsaydı sistem ilk taktda dayanardı.

**ANB-04. Arxivin qoruması Governance rejimindədir, müddətsiz.** Adi açarlar silə bilmir, xüsusi icazəli admin isə səhvi düzəldə bilər.
Əsas: qeydlər brokerə qarşı hüquqi dəlil deyil (ANB-10), ona görə Compliance-in geri dönülməzliyinə ehtiyac yoxdur. Rəsmi sənədə görə Compliance-də obyekti müddətdən əvvəl silməyin yeganə yolu AWS hesabını silməkdir.

**ANB-05. Saxlama sinfi provayder üzrə parametrdir:** AWS-də `GLACIER_IR`, R2-də `STANDARD_IA`.
Əsas: Glacier IR millisaniyəlik, real vaxtda oxunur, paneldən baxmaq mümkündür. Minimum obyekt ölçüsü 128 KB, minimum saxlama 90 gündür, oxuma Standard-dan bahadır. Arxiv heç vaxt silinmir və nadir oxunur, ona görə təsiri əhəmiyyətsizdir. R2-də bu sinif yoxdur.

**ANB-06. Qeyd obyektləri heç vaxt üzərinə yazılmır:** hər toplu və hər arxiv unikal adla yazılır.
- Canlı toplu: `İİİİ/AA/GG/<UTC vaxt>-<növbə>.json`
- Arxiv: `İİİİ-Hhh.jsonl.gz` (server vaxtı ilə ISO həftə nömrəsi) və yanında yoxlama cəmi

**ANB-07. API qatının anbar açarı minimum icazəlidir:**

| Bucket | İcazə |
|---|---|
| `config`, `state`, `snapshots` | Oxu, yaz, siyahıla. `snapshots` üçün həm də sil |
| `evidence-live` | Oxu, yaz, siyahıla, sil (qoruma müddəti bitənə qədər silmə fiziki olaraq bloklanır) |
| `evidence-archive` | Oxu, yaz, siyahıla. **Silmə yoxdur** |

Açarın bucket yaratmaq, qoruma və ya versiya dəyişmək icazəsi yoxdur.
Əsas: API sındırılsa belə, açarla arxiv silinə və qoruma söndürülə bilməz. EA və panelin icazə fərqləri API-nin öz marşrutlarında tətbiq olunur (API-02, API-03).

**ANB-08. Panel üçün qeydlər bir cavabda verilir:** cari və keçən həftə canlı bucket-dan, köhnə həftələr arxivdən. İstifadəçi fərqi görmür.

**ANB-09. Anbar ticarətin isti yolunda deyil.** Anbar və ya API çöksə, ticarət davam edir (NSZ-09).

**ANB-10. Qeydlər üçün ayrıca dəyişdirilməzlik zənciri qurulmur.**
Əsas: broker ilə mübahisədə sistemin öz qeydi arqument deyil. Həmin dəlil artıq iki müstəqil yerdədir: brokerin hesab tarixçəsində və MetaQuotes-un VPS jurnallarında. Təsadüfi itkiyə və dəyişməyə qarşı anbarın qoruması kifayətdir.

---

# 14. Operator interfeysi (panel)

## 14.1 Panelin səlahiyyəti

**PNL-01. Panel yalnız konfiqurasiyanı redaktə edir və vəziyyəti göstərir.** Plan ləğvi, mövqenin bağlanması, gözləyən əmrin silinməsi paneldə yoxdur. Bunlar üçün MetaTrader işlədilir.

**PNL-02. Sistemdə "əmr" anlayışı yoxdur.** Panelin yazdığı yeganə şey konfiqurasiyadır: göstəriş, tapşırıq və ya növbə yazmır. Sınaq cədvəli də konfiqurasiyadır, göstəriş deyil (TQV-13). Quraşdırma kilidinin təsdiqi Trading Mode keçidi ilə edilir (NSZ-06).
Əsas: əmr növbəsi, "əmr icra olundumu" təsdiqi və "sistem cavabsızdırsa göstəriş gözləyir" mexanizmləri qurulmur.

## 14.2 Ekranlar

**PNL-03. Panelin dörd ekranı var:**

**Vəziyyət.**
- Balans və hesab vəsaiti.
- Günlük və həftəlik zərərin cari dəyəri və limitə nisbəti.
- Açıq mövqelər və cari nəticələri.
- Günün son vəziyyətə çatmamış bütün planları və vəziyyətləri.
- Quraşdırma kilidi (varsa, NSZ-07).
- Hər iki EA-nın və təqvim mənbələrinin sağlamlığı.
- **Sonuncu yenilənmə vaxtı.**
- Bağlanmış əməliyyatlar **onları yaradan plan və xəbərlə** birlikdə göstərilir, nəticəyə komissiya və svop daxildir. Əsas: hesab tarixçəsinə telefondakı MetaTrader-dən baxmaq olur. Panelin əlavə etdiyi yeganə şey "hansı xəbər hansı nəticəni verdi" bağlantısıdır.

**Təqvim.** Yaxın xəbərlər: vaxtı, valyutası, Impact-i, mənbəyi (`MT5`, `FF`, `TEST`), qrupu və həmin xəbər üzrə plan qurulub-qurulmadığı. Qurulmayıbsa, **səbəbi**.

**Konfiqurasiya.** Simvol xəritələməsi, ticarət konfiqurasiyaları, zərər limitləri, blackout aralıqları, Trading Mode, Preparation Window, Test Schedule və Test Min Distance.

**Dəlil qeydləri.** Tarix aralığı və növ üzrə süzgəclənən qeydlər. Canlı və arxiv qeydləri birlikdə göstərilir (ANB-08).

**PNL-04. Bütün vəziyyət dəyərləri eyni anda, bir dəfəyə yazılır və tək bir vaxt möhrü ilə göstərilir.** Vaxt möhrü normal işləyən sistemdə 10 saniyədən köhnə olmur. Köhnəldikdə panel bunu fərqləndirir. Sistemin dayandığını göstərən yeganə vizual siqnal budur.

## 14.3 Pul ifadəsi

**PNL-05. Rabitə EA xəritələnmiş hər simvolun xüsusiyyətlərini vəziyyətin ayrıca bölməsinə yazır:** point, rəqəm sayı, 1 point-in 1 lot üçün pul dəyəri (zərər istiqamətində, `OrderCalcProfit` ilə), hesabın valyutası, lot hədləri (minimum, maksimum, addım), minimum məsafə, dondurma məsafəsi. Bölmə vəziyyət ilə birlikdə hər 10 saniyədə yenilənir.
Əsas: çarpaz cütlərdə point-in pul dəyəri qiymətlə dəyişir. Real olması üçün müntəzəm yenilənməlidir. Vəziyyətin içində olduğu üçün əlavə yazma yaranmır.

**PNL-06. Operator point dəyərini yazanda panel yanında bunları göstərir:**
- **qiymət ifadəsini:** point × point ölçüsü, məsələn "2000 point = 0.02000";
- **pul ifadəsini:** Fixed Lot üsulunda lot × point × point-in dəyəri. Risk üsullarında pul dəyəri onsuz da operatorun yazdığı məbləğdir, ona görə panel **təxmini lotu** göstərir.

Dəyər həmişə `≈` işarəsi və hesablama anı ilə göstərilir. Vəziyyət köhnədirsə, boz rəngdə "köhnə" işarəsi ilə göstərilir.
Əsas: Fixed Lot-da SL-də yazı səhvi itkini birbaşa artırır və heç bir yoxlama onu tutmur. Operator səhvi yazdığı anda görür.

## 14.4 Dil və vaxt

**PNL-07. Panelin interfeysi Azərbaycan dilindədir.** Ekran adları, düymələr, xəbərdarlıqlar və izahlar Azərbaycan dilindədir. **Ticarət terminləri ingiliscə** (22-dəki siyahı) yazılır, eyni yazılış panelin bütün ekranlarında, dəlil qeydlərində və bildirişlərdə işlədilir.
Əsas: paneldəki söz telefondakı MetaTrader-dəki sözlə eyni olur. Bir termin hər yerdə eyni yazılır.

**PNL-08. Bütün vaxtlar server vaxtı ilə göstərilir və qəbul edilir.** Göstərilən hər vaxtın yanında operatorun yerli vaxtı köməkçi kimi yazılır. Blackout sahələrinin yanında "Server vaxtı: HH:MM (sizdə HH:MM)" göstərilir. Daxil edilən hər dəyər yalnız server vaxtıdır, çevrilmə yoxdur.
Əsas: sistemin tək saatı var. Yerli vaxtla daxiletmə server ilə operator arasındakı fərq il ərzində dəyişəndə (yay vaxtı) blackout aralıqlarını səssizcə sürüşdürərdi.

**PNL-09. Operatorun ekranlarda seçdiyi süzgəclər brauzerin öz yaddaşında qalır.** Onlar API-yə getmir.

## 14.5 Konfiqurasiyanın EA-ya çatması

**PNL-10. Konfiqurasiya paneldən API-yə yazılır, Rabitə EA onu 30 saniyədə bir oxuyur.** Dəyişiklik ən çoxu bir yoxlama intervalı sonra qüvvəyə minir.

**PNL-11. EA-ların öz parametrlərində yalnız iki şey qalır: API ünvanı və EA tokeni.**
Əsas: VPS-ə sinxronizasiya əl ilədir. Konfiqurasiya EA parametrlərində olsaydı, hər dəyişiklik masaüstü terminalı açıb sinxronlaşdırmağı tələb edərdi. Masaüstü terminal yalnız kod yeniləndikdə və token dəyişəndə lazım olur.

**PNL-12. Qəbul olunmayan dəyər saxlanılmır və operatora gözlənilən aralıq göstərilir** (API-06).

---

# 15. Bildirişlər və dəlil qeydləri

## 15.1 Bildiriş kanalı

**BLD-01. Bildirişlər MetaTrader-in push mexanizmi ilə operatorun telefonundakı MetaTrader tətbiqinə göndərilir.** Məhdudiyyətləri: mesaj ən çoxu **255 simvol**, **saniyədə 2, dəqiqədə 10** mesaj. Hədd pozularsa, funksiya söndürülə bilər.
Əsas: kanal yeni asılılıq əlavə etmir. MetaQuotes onsuz da sistemin mütləq asılılığıdır.

## 15.2 Üç təbəqə

**BLD-02. Təcili — dərhal göndərilir.** Pul və ya sistemin sağlamlığı ilə bağlı nadir hadisələr:

| Hadisə | Qayda |
|---|---|
| Zərər limiti aşıldı | RSK-05 |
| Quraşdırma kilidi qoyuldu (E kateqoriyası, birja hesabı) | NSZ-04 |
| EA-lardan biri susdu (və bərpa oldu) | NSZ-14 |
| Ticarət serveri ilə bağlantı kəsildi və ya bərpa oldu | NSZ-08 |
| API qatı əlçatmazdır (epizod başına bir dəfə) | NSZ-09 |
| Aktivləşmə rədd edildi (10041) | İDR-13 |
| Əks əmr 10 saniyədən çox brokerdə qaldı | İDR-11 |
| Hər iki təqvim mənbəyi alınmır və ya bərpa olundu | TQV-19 |
| Nüsxəsiz bərpa: BE/TS aktiv deyil | LOK-07 |
| Konfiqurasiya lokal nüsxədən götürüldü və ya ümumiyyətlə yoxdur | LOK-11 |
| Real hesabda sınaq cədvəli aşkarlandı | TQV-15 |

**BLD-03. Xülasə — dövri, birləşmiş mesaj.** Tez-tez baş verən, sayıla bilən hadisələr: açılan və bağlanan əməliyyatların sayı, buraxılan planların sayı, təqvim oxunuşunun nəticəsi, tək mənbə nasazlığı, nasazlıq halları (İDR-12).
Əsas: bir xəbər anında bir neçə simvolda eyni anda əməliyyat açıla bilər. Hər biri ayrıca mesaj olsaydı, dəqiqəlik büdcə saniyələrdə dolardı.

**BLD-04. Yalnız qeyd — bildiriş göndərilmir.** Qalan hər şey: plan quruldu, hər broker cavabı, sapma, konfiqurasiya dəyişiklikləri.

**BLD-05. Büdcə heç vaxt aşılmır.** Hadisə büdcəni aşacaqsa, növbəti xülasəyə keçir.

**BLD-06. Hər bildiriş hadisənin anını daşıyır** (qısa vaxt möhrü 14 simvol tutur).
Əsas: bildiriş şəbəkə və ya büdcə səbəbindən gecikə bilər.

**BLD-07. Göndərilə bilməyən təcili bildiriş növbədə saxlanılır və əlaqə bərpa olunanda göndərilir. Xülasə saxlanılmır.**
Əsas: hamısı saxlanılsaydı, əlaqə bərpa olunanda büdcə aşılar və funksiya söndürülə bilərdi.

**BLD-08. Bildiriş göndərilə bilməsə də ticarət davam edir.** Hal qeydə alınır.

## 15.3 Gündəlik xülasə

**BLD-09. Hər gün saat 00:00-da, server vaxtı ilə, bitmiş günə aid bir bildiriş göndərilir, həftəsonu da daxil olmaqla:**
- idxal olunan xəbərlərin sayı;
- qurulan, icra olunan və buraxılan planların sayı;
- günün nəticəsi (komissiya və svop daxil).

Həftəsonu xülasəsi qısadır: "Bazar bağlı idi. Sistem işləyir."
Əsas: 00:00 günlük zərər dövrünün sıfırlanma anıdır. Xülasənin **gəlməməsi** sistemin dayandığını göstərən siqnaldır. Bu siqnal texniki xidmətin aparıldığı həftəsonu xüsusilə vacibdir.

## 15.4 Dəlil qeydləri

**QYD-01. Dəlil qeydləri "nə oldu"nu yox, "niyə oldu"nu saxlayır:** hansı yoxlama keçmədi, hansı plan niyə buraxıldı, hansı xəbər niyə plan yaratmadı, broker nə cavab verdi, hansı hal gözlənilməz idi. "Nə oldu" onsuz da brokerin hesab tarixçəsindədir.

**QYD-02. Hər qeyd iki vaxt daşıyır: server vaxtı və UTC.**
Əsas: server vaxtı planlar, blackout və zərər dövrləri ilə tutuşdurmaq üçündür. UTC isə heç vaxt sürüşmür və serverin saatı dəyişəndə qeydləri düzgün sıralamağa imkan verir.

**QYD-03. Sistemin yazdığı mətn Azərbaycan dilindədir, terminlər ingiliscədir. Broker və platformadan gələn mətn tərcüməsiz yazılır.**
> Nümunə: `Plan buraxıldı: broker rədd etdi — Invalid stops in the request (10016)`

**QYD-04. Qeydlər vəziyyətlə eyni 10 saniyəlik taktda toplu yazılır** (EAL-08, API-07). Paneldə qeyd ən çox 10 saniyə gec görünür. Təcili hallar onsuz da push ilə dərhal gedir.

**QYD-05. Saxlanma:** canlı qeydlər ən az 30 gün qorunur və həftəlik arxivlənir (API-09). Arxiv müddətsiz saxlanılır və Governance qoruması altındadır (ANB-04). Operator istəsə, köhnə arxivləri xüsusi icazə ilə özü təmizləyə bilər.

---

# 16. Təhlükəsizlik

**THL-01. EA tokeni** yalnız EA parametrlərində saxlanılır və yalnız EA sorğularına icazə verir. Sızarsa, API-də dərhal ləğv edilir. Yeni token masaüstü sinxronizasiya ilə VPS-ə çatdırılır, EA bu müddətdə lokal nüsxələrlə işləyir.

**THL-02. Panel yalnız Cloudflare Access arxasında açılır.** Giriş siyahısında yalnız operatorun kimliyi olur. Müstəqil MFA məcburidir: TOTP, təhlükəsizlik açarı və ya biometrika.
Əsas: cihaz oğurlansa belə sessiya bitir, yeni giriş parol və ikinci addım tələb edir.

**THL-03. Anbar açarları yalnız API qatının sirlər anbarındadır.** Brauzerə, VPS-ə, koda və git-ə heç vaxt düşmür.

**THL-04. Bütün hesablarda iki addımlı giriş (2FA) yandırılır:** Cloudflare, AWS, MQL5.community, broker kabineti, domen qeydiyyatçısı, git deposu.
Əsas: Cloudflare hesabı sistemin iki komponentini (API, panel) saxlayır. Onun qorunması ən vacib tək addımdır.

**THL-05. Sirlər heç vaxt koda, git-ə, qeydlərə və ya mesajlara yazılmır.**

**THL-06. Hər konfiqurasiya dəyişikliyi kimin tərəfindən və nə vaxt edildiyi ilə qeydə alınır** (API-06).

**THL-07. Riskin bölünməsi:** API və panel Cloudflare-da, məlumat AWS-dədir. Bir hesabın sındırılması hər şeyi aparmır. Hər iki halda ticarət API-dən asılı deyil (NSZ-09), fövqəladə dayandırma isə MetaTrader-dədir (3.6).

**THL-08. Panelin ünvanını tapan şəxs Access-dən keçmədən heç nə görmür və heç nə yaza bilmir.**

---

# 17. Portativlik və asılılıqlar

## 17.1 Asılılıqların sinifləri

| Asılılıq | Sinif | Şərh |
|---|---|---|
| MetaQuotes (MT5, VPS, push) | **Mütləq** | Onsuz sistem yoxdur |
| Broker | **Mütləq** | Konkret brokerə bağlı deyil |
| API platforması (Cloudflare Workers) | Dəyişdirilə bilən | Standart veb interfeysi |
| Panel girişi (Cloudflare Access) | Dəyişdirilə bilən | Standart JWT yoxlaması, parametrlər |
| Anbar provayderi (AWS S3, ehtiyat R2) | Dəyişdirilə bilən | S3 API |
| Domen | Operatorun mülkü | Bütün ünvanların sabitliyi ona söykənir |

**PRT-01. Portativliyin dörd qatı var və hər biri kodda dəyişiklik tələb etməyən keçid təmin edir:**

| Qat | Standart | Provayder dəyişəndə |
|---|---|---|
| Məlumat | S3 API | Yalnız API parametrləri dəyişir |
| API kodu | Standart `fetch` interfeysi | Kod eyni qalır, yerləşdirmə yeri dəyişir |
| Ünvanlar | Öz domen | DNS yeni yerə yönəldilir. EA və panel dəyişikliyi bilmir, VPS sinxronizasiyası lazım olmur |
| Giriş | EA tokeni, standart JWT | Yoxlama parametrləri dəyişir |

**PRT-02. Anbarla əlaqə S3 API-nin hamının dəstəklədiyi alt çoxluğu ilə məhdudlaşır:** dörd əməliyyat (API-08), SigV4 imzası, həmişə `Content-MD5`.
Əsas: "S3-uyumlu" "tam eyni" demək deyil. R2 S3-ün Object Lock başlıqlarını dəstəkləmir, AWS isə qorunan bucketda `Content-MD5` tələb edir. Alt çoxluq hər ikisində işləyir.

**PRT-03. Provayderə aid hər şey parametrdir:** endpoint, region, ünvanlama üsulu, bucket adları, saxlama sinifləri, açarlar, Access-in başlığı və ünvanları.

**PRT-04. Kod bucket yaratmır, qoruma, versiya və ya lifecycle qurmur.** Bunlar quraşdırmadır.

**PRT-05. Hər provayder üçün yazılı quraşdırma təlimatı olur** (AWS və R2): bucketlar, açar və icazələr, qoruma, versiya, Access tətbiqi, cədvəlləyici, məlumatın köçürülməsi addımı.

**PRT-06. Uyğunluq testi.** Provayder qəbul edilməzdən əvvəl real provayderdə işə salınır və bunları yoxlayır:
- dörd əməliyyat işləyir;
- qadağan olunmalı olan hər şey həqiqətən qadağandır (məsələn, arxivdən silmə);
- canlı və arxiv bucketlarında qoruma silməni və üzərinə yazmanı bloklayır;
- `state` və `config` bucketlarında üzərinə yazma **işləyir** və versiya yaranmır;
- saxlama sinfi düzgündür.

Test keçməyincə provayder qəbul olunmur. Test hər iki provayderdə (AWS və R2) keçməlidir.

**PRT-07. Heç bir üçüncü tərəf xidməti sistemin nüvəsinə (EA-lara) buraxılmır.** EA-lar yalnız öz API-ni, Forex Factory-ni və MetaQuotes-un funksiyalarını bilir.

**PRT-08. Provayder dəyişikliyi kodu dəyişmir,** amma quraşdırma (PRT-05) və mövcud məlumatın köçürülməsi tələb edir. Keçid təlimatda ayrıca addım kimi yazılır.

## 17.2 Yeni provayder seçilərkən meyarlar

Cari seçim: API və panel Cloudflare Workers-də, giriş Cloudflare Access-də, anbar AWS S3-də, ehtiyat Cloudflare R2-dədir. Gələcəkdə provayder dəyişsə, yeni namizəd bu meyarlarla yoxlanılır. Məcburi meyarları uyğunluq testi (PRT-06) təsdiqləyir.

**Anbar provayderi — məcburi:**
1. S3 API, SigV4 imzası və `Content-MD5` dəstəyi (PRT-02).
2. Bucket üzrə icazə verilə bilən açar (ANB-02, ANB-07).
3. Bucket üzrə açılan versiya və silməyə/üzərinə yazmaya qarşı qoruma, həm müddətli (30 gün), həm də müddətsiz (ANB-01, ANB-04). Qoruma **yalnız seçilmiş bucketlara** tətbiq oluna bilməlidir.
4. Obyektin tez silinməsinə və ya üzərinə yazılmasına görə minimum saxlama haqqı olmamalıdır, heç olmasa Standard sinifdə. Sistem vəziyyəti 10 saniyədə bir üzərinə yazır, nüsxələri isə silir. Wasabi bu səbəbdən yaramır (90 günlük minimum).
5. Ödəniş operatorun ölkəsindən mümkün olmalıdır.

**API platforması — məcburi:**
1. Standart `fetch` interfeysli runtime (ARX-10).
2. Öz domen və avtomatik HTTPS.
3. Sirlər anbarı (THL-03).
4. Cədvəlləyici (API-09, A-2).
5. Panel üçün MFA-lı giriş qapısı, sorğuya standart imzalı JWT qoyan (API-05).

**Güclü tövsiyə:** sorğu sayına görə qiymət (yazma sayı həlledicidir: ayda ~260 000 vəziyyət yazması və bir o qədərə qədər qeyd toplusu), giriş jurnalları, pulsuz egress.

**Vacib olmayanlar:** xam sürət, bant genişliyi, saxlama həcmi, hesablama gücü. Məlumat kiçikdir və API ticarətin isti yolunda deyil.

---

# 18. Ölçülə bilən tələblər

## 18.1 Sabitlər

| Tələb | Dəyər | Əsası |
|---|---|---|
| Bir simvolda eyni anda aktiv plan | 1 | İkinci plan riski ikiqat edir və əmrlər bir-birinə mane olur |
| Bir planda gözləyən əmr | 2 | İki istiqamət, sistem istiqamət seçmir |
| Konfiqurasiyanın açarı | simvol × Impact | 5.4 |
| Planın açarı | simvol × planın xəbər anı | 5.5 |
| Bir valyutaya bağlanan simvol / bir simvola bağlanan valyuta | Hədd yoxdur | Operatorun seçimidir |
| Birləşmə dözümü | Qonşu xəbərlər arası ≤ 60 s, zəncirvari | TQV-02 |
| Plan üfüqü | Cari gün + növbəti günün ilk 10 dəqiqəsi | TQV-08 |
| Təqvimin yenilənməsi | 15 dəqiqə | TQV-11 |
| Hər iki mənbə nasazlığında təcili bildiriş | Ardıcıl 4 uğursuz oxunuş (1 saat) | TQV-19 |
| Müvəqqəti rəddə təkrar | Ən çoxu 3, xəbər anına qədər. Lead Time = 0 olduqda 0 | EMR-11 |
| Nəticəsi naməlum əmrin təkrarı | Soruşduqdan sonra 1 | EMR-11 |
| Broker bitmə vaxtının yuvarlaqlaşdırılması | Növbəti tam dəqiqəyə, yuxarı | EMR-07 |
| Gözləyən əmrin ən gec bitməsi | Günün sonu, 00:00 | EMR-06 |
| Uzlaşdırma aralığı | Brokerdə iş varsa 1 s, yoxdursa 60 s | İDR-09 |
| Silinməyən əks əmr üçün təcili bildiriş | Əmr işə düşəndən 10 s sonra | İDR-11 |
| Nəbz aralığı / susma həddi | 5 s / 3 buraxılmış nəbz (15 s) | NSZ-13 |
| Konfiqurasiyanın oxunması | 30 s | PNL-10 |
| Vəziyyət + qeyd toplusunun yazılması | 10 s, bir sorğu | EAL-08 |
| WebRequest gözləmə həddi | ≤ 5 s | EAL-10 |
| Rabitə EA-nın bir taktda şəbəkə sorğusu | Ən çoxu 1 | EAL-09 |
| Gündəlik xülasə | 00:00, hər gün | BLD-09 |
| Günlük / həftəlik zərər dövrü | 00:00 / bazar ertəsi 00:00 | RSK-03 |
| Bildiriş büdcəsi, uzunluq | 2/s, 10/dəq, 255 simvol | Platformanın həddi |
| Arxivləmə | Hər həftə, bazar ertəsi 00:00-dan sonra | API-09 |
| Canlı qeydlərin qoruması | 30 gün | ANB-01 |
| Arxivin qoruması | Governance, müddətsiz | ANB-04 |
| Lokal növbənin həddi | Yoxdur | LOK-05 |
| Qlobal dəyişən adı | `EE.` prefiksi, ≤ 63 simvol | EAL-02 |
| Anbar əməliyyatları | 4 (GetObject, PutObject, DeleteObject, ListObjectsV2) | API-08 |
| Anbar imzası | SigV4 + həmişə Content-MD5 | PRT-02 |

## 18.2 Operator parametrləri və aralıqları

**Aralıqdan kənar dəyər qəbul edilmir, saxlanılmır və operatora gözlənilən aralıq göstərilir.**

| Parametr | Aralıq | Standart |
|---|---|---|
| Preparation Window (qlobal) | 1–300 s | 10 s |
| Lead Time | 0–300 s (0 = xəbər anı) | yoxdur |
| Expiration müddəti (rejim on) | 1–86 400 s. Faktiki bitmə ən gec 00:00 | yoxdur |
| Offset, Stop Loss, Max Spread, BE Trigger/Lock, TS Trigger/Distance | > 0, sabit yuxarı hədd yoxdur | yoxdur |
| Take Profit | ≥ 0 | yoxdur |
| Fixed Lot | > 0. Broker hədləri hazırlıqda yoxlanılır | yoxdur |
| Risk % | 0 < x ≤ 100 | yoxdur |
| Risk Amount, Daily / Weekly Loss Limit | > 0 | yoxdur |
| Test Min Distance | > 0 dəqiqə | 15 dəqiqə (*ixtiyari*) |

Qeydlər:
- Lead Time-ın yuxarı həddi 300 s-dir, çünki əmrlər xəbərdən nə qədər tez qoyulsa, adi qiymət tərpənişinin əmri **xəbər çıxmadan** işə salma ehtimalı o qədər yüksəkdir. Lead Time və Preparation Window bir-birinə mane olmur, ardıcıl gəlir.
- Point dəyərlərinə sabit yuxarı hədd qoyulmur, çünki point simvola görə dəyişir: EURUSD-də 0.00001, XAUUSD-də 0.01. Yazı səhvinə qarşı qoruma pul ifadəsidir (PNL-06).
- Ticarət konfiqurasiyası üçün standart dəyər verilmir: təyin edilməyincə konfiqurasiya saxlanılmır.
- Lot addımı, minimum və maksimum lot, point, girov, minimum məsafə və dondurma məsafəsi brokerdən oxunur, sənəddə sabit yazılmır.

---

# 19. Quraşdırma şərtləri

## 19.1 Hesab

**QRŞ-01. Hesabın uçot sistemi hedging və ya netting ola bilər.** Sistem uçot sistemini brokerdən oxuyur. Davranış yalnız rəsmi qaydaların fərqli olduğu yerlərdə fərqlənir (İDR-12, RSK-07).
Əsas: iki istiqamətdə gözləyən əmr üçün hedging lazım deyil. Hedging-in və netting-in fərqi yalnız hər iki əmrin işə düşdüyü nasazlıq halında və bağlamada görünür.

**QRŞ-02. Birja (exchange) uçot sistemli hesab dəstəklənmir.** Sistem onu aşkarlayıb quraşdırma kilidi qoyur (NSZ-03).

**QRŞ-03. Hesab birdəfəlik parol (OTP) tələb etməməlidir.** Rəsmi qaydaya görə OTP-li hesablar VPS-də işləmir.

**QRŞ-04. Hesabda bu sistemdən başqa heç bir ticarət aparılmır**, nə əl ilə, nə başqa avtomatlaşdırılmış vasitə ilə.
Əsas: zərər limiti bütün hesab üzrə hesablanır, brokerdə tapılan hər mövqe sistemindir, girov hesabı kənar əməliyyatlarla çətinləşmir. Rəsmi VPS qaydaları da kənar ticarətin "unpredictable trading results" verə biləcəyini deyir.

Cari hesab (2026-09-25): CFI, `CFI2-Demo` serveri, MetaTrader 5, **hedging**, əks mövqelərə icazə var (operator yoxlayıb), OTP yoxdur, server vaxtı UTC+3.

## 19.2 Platforma

- Sistem **MetaTrader 5** ilə işləyir, **MetaTrader VPS**-də.
- Sistem konkret brokerə bağlı deyil.
- Alət növünə məhdudiyyət qoyulmur: brokerin verdiyi hər simvol xəritələnə bilər.
- DLL işlədilmir.

## 19.3 Şəbəkə

- ARX-06 və ARX-07.
- API domeni operatorun öz domenindədir.

---

# 20. Sınaq və təhvil mərhələləri

## 20.1 Sınaq qatları

**SNQ-01. Hər məntiq hissəsi üçün əvvəl test yazılır, sonra kod.** Pul riski daşıyan hesablamalar (həcm, zərər limiti, vəziyyət maşını, birləşmə) kod yazılmazdan əvvəl nümunələrlə təsbit olunur. Sənəddəki hər nümunə testə çevrilir.

**SNQ-02. Sistem backtest ilə sınanmır.** Sınaq yalnız canlı və gələcək hadisələrlə aparılır.

| Qat | Nə sınanır | Necə |
|---|---|---|
| **T-1** MQL5 məntiq testləri | Birləşmə və R-1…R-6, blackout, zərər hesabı, həcm, broker cavablarının kateqoriyaları, vəziyyət maşını, sahiblik keçidləri, JSON oxuyucu, bütövlük nişanı | Test skriptləri. `metaeditor64.exe /compile` ilə kompilyasiya olunur, terminal konfiqurasiya faylı ilə işə salır, nəticəni fayla yazır və `ShutdownTerminal=1` ilə bağlanır. Avtomatikdir |
| **T-2′** Demo hesabda canlı sınaq planları | Əmrlərin mexanikası: yerləşdirmə, əks əmrin silinməsi, BE/TS, zərər limiti ilə bağlama, qismən icra, hər iki əmrin işə düşməsi | Test Schedule (TQV-13), canlı bazar, kiçik offset. Hedging **və netting** demo hesablarda |
| **T-3** API testləri | Bütün sorğular, giriş yoxlaması, konfiqurasiyanın yoxlanması, arxivləmə | Lokal runtime-da, anbar imitasiyası ilə. Avtomatikdir |
| **T-4** Uyğunluq testi | PRT-06 | AWS və R2-də |
| **T-5** Mühit sınağı | MRH-01 | VPS-də |
| **T-6** Real xəbərlərlə qəbul | MRH-03 | Canlı demo, hedging və netting |

Strategy Tester canlı brokerin hər davranışını təkrarlamır (dondurma, müvəqqəti rəddlər, cavab gecikmələri) və təqvimin canlı axınını vermir. Bu hallar T-2′ və T-6-da yoxlanılır.

## 20.2 Mərhələlər

**MRH-01. Mərhələ 1 — Mühitin yoxlanması.** Kiçik sınaq proqramı VPS-ə yerləşdirilir (24 saatlıq pulsuz sınaq kifayətdir) və bunlar yoxlanılıb qeydə alınır:
- MT5-in daxili iqtisadi təqvimi VPS-də əlçatandırmı;
- Forex Factory siyahısı VPS-in ünvanından çəkilə bilirmi, **yeni həftəyə hansı anda keçir**, "vaxtı təsdiqlənməmiş" xəbəri göstərən sahə varmı;
- `SendNotification`-ın müddəti;
- VPS-dən API-yə sorğunun müddəti;
- serverin UTC fərqi və qış/yay qaydası;
- **netting demo hesabda** mövqenin plana aidiyyəti (PLN-16).

Qəbul meyarı: bütün nöqtələr üzrə real ölçü alınır və jurnalda görünür. Bu mərhələ birincidir, çünki təqvimlə bağlı cavab "xeyr" olarsa, təqvim dizaynı dəyişir. Nəticələrə görə 6.1 və 6.5 dəqiqləşdirilir.

**MRH-02. Mərhələ 2 — Təqvim, planlar, API, anbar və panel. Broker əmri göndərilmir.** Qəbul meyarları:
- hər iki mənbədən xəbərlər oxunur və zəncir qaydası ilə düzgün birləşir;
- planlar paneldə simvolu, anı, Impact-i və parametrləri ilə görünür;
- plan qurulmayan hər xəbərin yanında səbəbi görünür;
- blackout (gecəni keçən daxil), ticarət rejimi və xəritələnməmiş valyuta qaydaları işləyir;
- konfiqurasiya paneldən dəyişdirilir və 30 saniyə ərzində sistemə çatır;
- gündəlik xülasə gəlir;
- panel yalnız Access + MFA ilə açılır;
- uyğunluq testi (T-4) AWS və R2-də keçir;
- həftəlik arxivləmə işləyir və yoxlamadan keçir;
- lokal nüsxələr və bərpa ssenariləri işləyir.

**MRH-03. Mərhələ 3 — Demo hesabda tam icra.** Hər şey: əmrlər, idarə, zərər limitləri, nasazlıq halları, bərpa. Hedging və netting demo hesablarında. Qəbul meyarları:
- gözləyən əmrlər hədəf anda yerləşdirilir, sapma qeydə alınır;
- biri işə düşəndə digəri silinir, dondurma halında təkrar işləyir;
- SL, TP, BE və TS düzgün tətbiq olunur;
- zərər limiti aşılanda əmrlər silinir və mövqelər bağlanır;
- sistem yenidən başlayanda mövqelər tanınır və nüsxələr bərpa olunur;
- broker cavabları kateqoriyalar üzrə düzgün işlənir;
- bildiriş büdcəsi heç vaxt aşılmır.

Bu mərhələnin qaydası: real hesabda ediləcək hər şey demo hesabda edilir. **Demo-da yoxlanmamış davranış qalmamalıdır.**

**Layihənin sərhədi:** layihə demo hesabda tam funksionallıqla bitir. Real hesaba keçid layihə sahibinin öz qərarı və işidir.

Mərhələ 2-dən əvvəl MetaTrader VPS abunəliyi alınır.

---

# 21. Risklər və açıq məsələlər

## 21.1 Mərhələ 1-də ölçüləcək açıq məsələlər

| Məsələ | Təsiri | Harada |
|---|---|---|
| MT5 təqviminin VPS-də əlçatanlığı | Olmasa, təqvim dizaynı dəyişir | MRH-01 |
| Forex Factory-nin VPS-dən əlçatanlığı, həftə keçidi, təsdiqlənməmiş vaxt sahəsi | 6.1 və 6.5 dəqiqləşir | MRH-01 |
| SendNotification-ın və API sorğusunun müddəti | NSZ-14 və EAL-10-un ehtiyatı yoxlanılır | MRH-01 |
| Serverin qış/yay qaydası | TQV-20 | MRH-01 |
| Netting-də mövqenin aidiyyəti | PLN-16 | MRH-01 |

## 21.2 Risklər

| Risk | Təsiri | Azaldılması |
|---|---|---|
| Broker OTP tələb edir | VPS işləmir | Quraşdırmadan əvvəl yoxlanılır (QRŞ-03) |
| Mövqe sayı limiti vurur (10040) | Yeni əmr yerləşmir | Çox simvol xəritələməkdən çəkinmək. Hal qeydə alınır |
| VPS-in texniki xidməti və ya planlaşdırılmamış yenidən başlama xəbər anına düşür | BE və TS dayanır | SL/TP brokerdə qalır, bərpa NSZ-11 ilə |
| Forex Factory formatını dəyişir və ya əlçatmaz olur | Həmin mənbə oxunmur | Digər mənbə ilə davam. İkisi də gedərsə təcili bildiriş (TQV-19) |
| API qatı və ya anbar çökür | Panel və vəziyyət yenilənmir | Ticarət davam edir, lokal nüsxələr, növbələr (NSZ-09) |
| Sistem tamamilə dayanır və bunu heç kim görmür | Ticarət dayanır | Xülasənin gəlməməsi və köhnəlmiş vaxt möhrü (NSZ-15) |
| Hadisə itir və əks əmr brokerdə qalır | İdarəsiz mövqe riski | Uzlaşdırma 1 saniyədə bir, 10 s sonra təcili bildiriş (İDR-09, İDR-11) |
| Hər iki əmr işə düşür | İkiqat mövqe (hedging) və ya bağlanma/çevrilmə (netting) | İDR-12. Kifayət qədər böyük offset (HZR-02 qeydi) |
| Cloudflare hesabı sındırılır | API və panel təhlükəyə düşür | 2FA (THL-04), məlumat ayrı provayderdə (THL-07), ticarət API-dən asılı deyil |
| EA tokeni sızır | Kənar şəxs EA adından yaza bilər | API-də dərhal ləğv (THL-01) |
| Lokal konfiqurasiya nüsxəsi köhnədir | Yenidən başlamadan sonra köhnə qayda işləyir | Təcili bildiriş (LOK-11 K-3). Fövqəladə dayandırma MetaTrader-dədir |

---

# 22. Terminlər lüğəti

## 22.1 Paneldə ingiliscə yazılan terminlər

Symbol, Currency, Impact (High / Medium / Low), Volume (Fixed Lot / Risk % / Risk Amount), Lead Time, Expiration, Offset, Stop Loss, Take Profit, Max Spread, Breakeven (Trigger, Lock), Trailing Stop (Trigger, Distance), Daily Loss Limit, Weekly Loss Limit, Blackout, Trading Mode (Active / Paused), Preparation Window, Test Schedule, Test Min Distance.

## 22.2 Anlayışlar

| Termin | Mənası |
|---|---|
| **Sistem** | Ticarət EA, Rabitə EA, API qatı, anbar və panelin bütövü |
| **Ticarət EA** | VPS-də işləyən, yoxlamaları aparan, əmrləri yerləşdirən və əməliyyatları idarə edən komponent. WebRequest işlətmir |
| **Rabitə EA** | VPS-də işləyən, təqvimi oxuyan, planları quran, API ilə danışan və bildiriş göndərən komponent. Ticarət etmir |
| **API qatı** | EA və panel ilə anbar arasındakı server funksiyası |
| **Virtual platforma (VPS)** | MetaQuotes-un şəbəkəsində işləyən, operatorun fiziki girişi olmayan MT5 terminalı |
| **Obyekt anbarı** | Konfiqurasiya, vəziyyət, nüsxələr və qeydlərin saxlandığı xidmət (S3 API) |
| **Panel** | Konfiqurasiyanı redaktə edən və vəziyyəti göstərən veb tətbiq. Sistemin nüvəsi deyil |
| **Ticarət serveri vaxtı** | Brokerin serverinin vaxtı. Sistemdəki bütün vaxtlar bununla ifadə olunur |
| **Təqvim mənbəyi** | `MT5`, `FF` və ya `TEST` |
| **Xəbər qrupu** | Zəncirvari birləşmiş xəbərlər (TQV-02) |
| **Planın xəbər anı** | Qrupdakı ən yüksək Impact-li xəbərlərin ən erkəninin anı (TQV-03) |
| **Plan** | Bir simvol və bir xəbər anı üçün qurulmuş ticarət göstərişi |
| **Plan identifikatoru** | Plan açarından deterministik hesablanan nömrə (PLN-09) |
| **Aktiv plan** | Hazırlıq pəncərəsinə girmiş və son vəziyyətə çatmamış plan |
| **Parametr nüsxəsi** | Hazırlığa keçid anında konfiqurasiyadan götürülən parametrlər |
| **Hazırlıq pəncərəsi** | Hədəf andan əvvəl yoxlama və hesablama aralığı |
| **Hədəf yerləşdirmə anı** | Planın xəbər anı − Lead Time |
| **İsti yol** | Hədəf anda əmrlərin göndərilməsi, əks əmrin silinməsi, BE/TS hərəkətləri |
| **Hadisə əsaslı silmə** | Broker ticarət hadisəsi gələn kimi əks əmrin silinməsi (İDR-08) |
| **Uzlaşdırma** | Brokerin faktiki vəziyyətinin sistemin reyestri ilə dövri tutuşdurulması |
| **Nasazlıq halı** | Hər iki gözləyən əmrin işə düşməsi |
| **Sapma** | Hədəf an ilə brokerin qeyd etdiyi real yerləşdirmə anı arasındakı fərq |
| **Hedging / Netting** | Hesabın mövqe uçotu sistemi. Hedging-də bir simvolda bir neçə mövqe, netting-də bir ümumi mövqe olur |
| **Quraşdırma kilidi** | Sistemin bu hesabda işləyə bilmədiyi aşkarlananda qoyduğu dayanma (NSZ-03) |
| **Ask / Bid** | Alıcının ödədiyi daha yüksək qiymət / satıcının aldığı daha aşağı qiymət |
| **Spred** | Ask ilə Bid arasındakı fərq |
| **Minimum məsafə** | Brokerin tələb etdiyi, əmrin və stop səviyyələrinin cari qiymətdən ən az uzaqlığı |
| **Dondurma məsafəsi** | Qiymət əmrə bu qədər yaxın olanda brokerin dəyişdirməni və silməni bloklaması |
| **Girov** | Mövqe açmaq üçün brokerin tələb etdiyi vəsait |
| **Balans / Hesab vəsaiti** | Açıq nəticə nəzərə alınmadan vəsait / balans + açıq nəticə |
| **Komissiya / Svop** | Brokerin tutduğu haqq / gecəyə keçirməyə görə yazılan məbləğ |
| **Point / Lot** | Brokerin simvol üçün qiymət addımı / standart həcm vahidi |
| **Zərər limiti** | Dövr ərzində zərər ona çatanda ticarəti dayandıran məbləğ |
| **Dəlil qeydi** | Sistemin niyə belə davrandığını izah edən qeyd |
| **Arxiv** | Həftəlik sıxılmış, qorunan qeyd faylı |
| **Gündəlik xülasə** | Bitmiş günün sayları və nəticəsi olan bildiriş |

---

# Əlavə A. Qərarların mənbəyi

Bu sənəd qaralamadan fərqlənən hər qərarı müsahibə jurnalına (`.claude/context/interview.md`) bağlayır.

| Qaydalar | Jurnal |
|---|---|
| ARX-09…ARX-12, 3.4, 3.5, API-01…API-05, THL | Q21, Q14 |
| ARX-06, 3.2 | Q15 |
| ARX-04, NSZ-14 | Q05 |
| ANB bölməsi, API-09, QYD-05 | Q10, Q22 |
| EAL-08…EAL-11 | Q23 |
| EAL-01…EAL-07, PLN-09, PLN-13, PLN-14 | Q17 |
| LOK bölməsi | Q03, Q04, Q18, Q19 |
| TQV-02…TQV-06, TQV-12 | Q06, Q20 |
| TQV-08 | Q25 (B5) |
| TQV-13…TQV-16 | Q13, Q25 (B4, B11) |
| TQV-19 | Q25 (B6) |
| RSK-08 | Q07 |
| RSK-10, RSK-11 | Q25 (B2) |
| PLN-10, 5.5.3 | Q25 (B1) |
| PLN-06 | Q25 (B9) |
| İDR-11, EAL-01 | Q25 (B7, B8) |
| İDR-12, RSK-07, QRŞ-01, QRŞ-02, NSZ-02 (nasazlıq halı kodları) | Q25 (B3), Q26 |
| NSZ-03…NSZ-07 | Q25 (B3) |
| İDR-13 | Q02 |
| 18.2, PNL-05, PNL-06, PLN-02 | Q08 |
| PNL-07, PNL-08, 22.1 | Q09 |
| BLD-09 | Q16 |
| SNQ, MRH | Q13, Q11, Q12 |
| PRT bölməsi | Q10, Q21 |

