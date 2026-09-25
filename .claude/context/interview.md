# Interview: Eagle Eyes — yeni layihə sənədi
Status: active
Started: 2026-09-23

Mənbə: qaralama `layihe-senedi.md` (1699 sətir, 21 bölmə).

## Q00 [answered] Tapşırığın özü
**Asked:** (istifadəçinin ilkin tələbi)
**Answer:** "Layihənin yer aldığı qaralama sənədim var (tamamlanmamış və dəqiqləşdirilməmiş layihə sənədi olduğu üçün qaralama olaraq adlandırılır) təqdim edəcəm. Sən isə bu qaralamadan lazım olan məlumatları ayırıb götürərək uyğun olacaq şəkildə yeni layihə sənədini tərtib et. Cavabını almadığın məlumatları isə mənimlə müsahibə əsnasında topla."
**Recorded:** 2026-09-23

## Q01 [answered] Yeni sənəd kim üçündür və nə üçün işlədiləcək?
**Asked:** Yeni layihə sənədi əsasən kim üçündür və nəyə xidmət etməlidir? (Seçimlər: kodun yazılması üçün / başqa proqramçıya təhvil / hər ikisi / şəxsi istinad)
**Answer:** "Hər ikisi"
**Seçimin təsviri:** "Həm icra üçün dəqiq spesifikasiya, həm də başqasına göstəriləcək səviyyədə oxunaqlı sənəd."
**Recorded:** 2026-09-23
**Mənim oxunuşum:** tələblər nömrələnmiş və yoxlanıla bilən olmalıdır, amma kontekst və "əsas" izahları saxlanılmalıdır ki, kənar oxucu da anlasın.

## Q02 [answered] 10041 kodunda plan buraxılırmı, yoxsa işlək qalırmı?
**Answer:** "B: Plan buraxılır (Tövsiyə)"
**Seçimin təsviri:** "Əks əmr silinir, plan «buraxılmış» olur, təcili bildiriş gedir. 8.5 və 11.1 prinsipləri ilə uyğundur. 9.6 yenidən yazılır."
**Recorded:** 2026-09-23
**Nəticə:** 9.6 yenidən yazılır; 10041 D kateqoriyasında qalır; bu hal 13.2-dəki təcili bildiriş siyahısına əlavə olunur (seçim təsvirində "təcili bildiriş gedir" qəbul edildi).
**Asked:** Qaralamada ziddiyət: 9.6 deyir aktivləşmə rədd edilərsə plan işlək qalır və əks əmr brokerdə qalır; 11.2 D kateqoriyası (10041 daxil) deyir plan buraxılır. Hansı qüvvədədir?
**İlk cavab (aydınlaşdırma istəyi):** "Daha açıqlayıcı ol ki, cavabımda əmin olum. Dediyim kimi sənəd qaralamadır və ziddiyətlərin, boşluqların və toqquşmaların olması mümkün və ehtimaldır." (2026-09-23)
**Qeyd:** sual nümunə ssenari ilə yenidən verildi.

## Q03 [answered] Parametr nüsxəsi anbara yazıla bilmirsə, plan icraya keçsinmi?
**Yenidən verilən sual:** rəsmi sənədlərə əsasən (project.md), seçimlər: 1 davam+növbə+bildiriş / 2 plan buraxılır / 3 davam+lokal ehtiyat
**Answer:** "Həm anbar həm də lokal. Lokal-ın qalacağına zəmanət olmasa belə anbar var. Hər ehtimala qarşı 2 tərəfli nüsxələmə ən böyük qazanc olar. Bir nüans var ki, bu nüsxələr düzgün şəkildə təmizlənməlidir"
**Recorded:** 2026-09-23
**Mənim oxunuşum:** 3-cü seçim. Plan davam edir; nüsxə həm anbara (anbar yoxdursa növbə ilə), həm VPS diskinə yazılır; bərpada hər iki yerə baxılır. İki nüsxə də yoxdursa təcili bildiriş (1-ci seçimdən gəlir; təsdiqlənməlidir). Təmizləmə qaydası → Q18.
**Toqquşduğu yerlər:** 3.4 və 9.8 ("VPS diski yalnız müvəqqəti") — lokal nüsxə yeganə mənbə deyil, ehtiyatdır; 9.8-in 2-ci addımı dəyişir.
**Asked:** Anbar əlçatmaz olanda (11.4 ticarət davam edir) nüsxə davamlı yerə yazılmır; sonra sistem yenidən başlasa Breakeven/Trailing bərpa olunmaz. Plan yenə də icra olunsunmu? (Seçimlər: 1 davam+növbə+bildiriş / 2 plan buraxılır / 3 davam+lokal ehtiyat)
**İlk cavab:** "Bu məsələni rəsmi dokumentasiyalara baxaraq yenidən təqdim et" (2026-09-23)
**Qeyd:** VPS fayllarının və qlobal dəyişənlərin taleyi rəsmi MetaQuotes sənədlərindən yoxlanılıb yenidən təqdim olunacaq.

## Q04 [answered] Yenidən başlama zamanı anbar əlçatmazsa konfiqurasiya haradan gəlir?
**Asked:** Yenidən başlamadan sonra anbar əlçatmazdırsa, konfiqurasiya haradan gəlsin? (1 lokal nüsxə: anbar → lokal → heç biri yoxdursa plan qurulmur + təcili bildiriş / 2 lokal nüsxə yoxdur)
**Answer:** "Lokal nüsxə + təmizləmə qaydası da ətraflı şəkildə təyin edilməlidir"
**Recorded:** 2026-09-23
**Nəticə:** 1-ci seçim. Konfiqurasiyanın lokal nüsxəsinin təmizləmə qaydası ətraflı təyin olunmalıdır → Q19.

## Q19 [answered] Konfiqurasiyanın lokal nüsxəsi (və digər lokal fayllar) necə yazılır və təmizlənir?
**Asked:** L-1 öz qovluğu, yalnız öz ad nümunəsini silir; L-2 müvəqqəti fayl → FileMove(FILE_REWRITE); L-3 bütövlük nişanı (uzunluq + yoxlama cəmi), uyğunsuzsa fayl yoxdur sayılır və silinir; L-4 başlanğıcda müvəqqəti faylların süpürülməsi. Fayl ömürləri cədvəli (konfiqurasiya nüsxəsi 1 ədəd, silinmir, üzərinə yazılır; parametr nüsxəsi Q-3/Q-4; ötürmə faylları və xəbər keşi üzərinə yazılır; qeyd növbəsi təsdiqdən sonra silinir). K-1 anbar qalib gəlir, lokal anbara geri yazılmır, anbar əlçatandırsa lokala baxılmır; K-2 yalnız yoxlanmış konfiqurasiya lokala yazılır; K-3 lokal istifadə olunanda təcili bildiriş; K-4 yaş həddi yoxdur. Qeyd növbəsinə hədd qoyulmur (təklif).
**Cavab 1:** "Anbar əlçatandırsa lokal nüsxəyə heç baxılmır - lakin anbarda yer alan konfiqurasiyaları lokal'a yazmaq lazımdır" (2026-09-23)
**Mənim oxunuşum:** K-1-ə dəqiqləşdirmə — anbardan oxunan hər (yoxlanmış) konfiqurasiya lokala yazılmalıdır (sinxron saxlanılır). Qalan qaydalar və "hədd yoxdur" hələ açıq təsdiqlənməyib.
**Düzəldilmiş K-1 təklif edildi:** anbar həqiqətdir; hər 30 saniyəlik oxunuşda yoxlanmış konfiqurasiya lokala yazılır — yalnız məzmun fərqlidirsə, lokal yoxdursa və ya zədəlidirsə; anbar əlçatandırsa lokal oxunmur; lokal anbara geri yazılmır.
**Cavab 2:** "Bəli, haması uyğundur" (seçim: düzəldilmiş K-1 + L-1…L-4 + fayl ömürləri + K-2…K-4 + növbəyə hədd yoxdur)
**Recorded:** 2026-09-23

## Q05 [answered] Rabitə EA susarsa, təcili bildirişi Ticarət EA özü göndərirmi?
**Asked:** 11.5 deyir susmuş EA-nı digəri aşkarlayıb təcili bildiriş göndərir; amma push yalnız Rabitə EA-dadır (3.1). Ticarət EA push göndərə bilərmi? (Rəsmi sənədlər: WebRequest sinxrondur; SendNotification-ın bloklaması və həddin əhatəsi yazılmayıb — project.md)
**Answer:** "1: Yalnız susma mesajı (Tövsiyə)"
**Seçimin təsviri:** "Hazırlıq/yerləşdirmə anlarından kənarda, epizod başına bir mesaj. Mərhələ 1-də SendNotification müddəti ölçülür. 3.2 dəqiqləşdirilir."
**Recorded:** 2026-09-24
**Nəticə:** 3.2 → "Ticarət EA WebRequest işlətmir; yeganə istisna susma xəbərdarlığı üçün SendNotification". Bərpa mesajını Rabitə EA göndərir. Mərhələ 1-ə SendNotification müddətinin ölçülməsi əlavə olunur. Yan nəticə: Rabitə EA-nın WebRequest timeout-u susma həddindən (15 s) xeyli qısa olmalıdır (məs. 5 s).

## Q06 [answered] ±1 dəqiqə birləşmə zəncirvari işləyirmi?
**Asked:** ±1 dəqiqə birləşmə qaydası necə oxunsun? (İlk xəbərdən sayılır — tövsiyəm / Zəncirvari)
**Answer:** "Zəncirvari"
**Seçimin təsviri:** "Qonşu xəbərlər arası ≤ 60 saniyədirsə, hamısı bir qrupdur. Qrupun uzunluğu məhdud deyil."
**Recorded:** 2026-09-24
**Nəticə:** mənim tövsiyəmin əksinə seçildi — qəbul. Törəmə məsələlər → Q20.
**Əlavə (istifadəçi, 2026-09-24):** "Zəncirvari - qrup daxilində xəbər anına yox, təsir səviyyəsinə görə ən yüksək olan. Senaridəki `High` üzrə 14:32:00 plan qurulur"
**Mənim oxunuşum:** planın xəbər anı = qrupda təsir səviyyəsi ən yüksək olan xəbərin anı (ən erkən xəbərin yox). Qaralamanın 6.2-dəki "planın xəbər anı — birləşən xəbərlərin ən erkənidir" qaydası ilə toqquşur → toqquşma istifadəçiyə göstəriləcək; bərabər səviyyəli xəbərlərdə hansı anın seçildiyi açıqdır.
**Toqquşma göstərildi, bərabər səviyyə soruşuldu:** "Qrupda ən yüksək təsir səviyyəli bir neçə xəbər varsa, plan hansı anı alır?"
**Answer:** "Ən erkəni (Tövsiyə)"
**Recorded:** 2026-09-24
**Qərar:** planın xəbər anı = qrupdakı ən yüksək təsir səviyyəli xəbərlərin ən erkəninin anı. 6.2 yenidən yazılır (əsası: eyni hadisənin iki mənbəsi adətən eyni səviyyədədir → bərabərlikdə ən erkən 6.2-nin əsasını qoruyur). Silinmə müddəti, blackout, təkrar cəhd son həddi — hamısı planın xəbər anından sayılır. Q20-dən yalnız yenilənmədə birləşmə/bölünmə açıq qalır.

## Q20 [answered] Zəncirvari qrupda hansı an nə üçün sayılır; yenilənmədə qruplar birləşir/bölünərsə nə olur?
**Asked:** silinmə müddəti (ən erkən / ən gec xəbərdən), blackout (yalnız ən erkən / qrupun bütün anları), təkrar cəhd son həddi, təqvim yenilənməsində körpü xəbərin iki planı birləşdirməsi və orta xəbərin çıxarılması ilə bölünmə.
**Anlar hissəsi:** Q06 əlavəsi ilə həll olundu — hamısı planın xəbər anından.
**Birləşmə/bölünmə sualı:** R-1 hər yenilənmədə qruplar təzədən; R-2 açar üst-üstə düşürsə plan yenilənir; R-3 qrupa düşən digər icraya keçməmiş planlar ləğv ("başqa planla birləşdi"); R-4 yeni qrup → yeni plan (bölünmə buradan); R-5 icraya keçmiş/son vəziyyətli planların xəbərləri dondurulur, qruplaşdırmada iştirak etmir; R-6 son vəziyyətli açar təkrar qurulmur.
**Answer:** "Bəli, haması uyğundur"
**Recorded:** 2026-09-24

## Q07 [answered] Gecə yarısını keçən blackout aralığı necə təyin olunur?
**Asked:** Gecə yarısını keçən blackout aralığı necə təyin olunsun? (1 bir aralıq gecəni keçir / 2 iki aralıq yazılır) + ikinci boşluq: "bitmə anı daxildir" dəqiqə dəqiqliyində nə deməkdir.
**Answer:** "1: Bir aralıq, gecəni keçir (Tövsiyə)"
**Seçimin təsviri:** "Bitmə < başlama olarsa aralıq növbəti günə davam edir. Bitmə dəqiqəsi tam daxildir (hh:mm:59)."
**Recorded:** 2026-09-24
**Nəticə:** 10.2 genişlənir; bazar → bazar ertəsi keçidi də eyni qayda ilə; panel "23:50 → 00:15 (cümə)" kimi göstərir.

## Q08 [answered] Parametrlərin yuxarı hədləri və standart dəyərlər
**Yekun təklif:** yerləşdirmə 0–300 s (əsas: uzun müddət xəbərdən əvvəlki tərpənişin əmri işə salma ehtimalını artırır; hazırlıqla ziddiyət yoxdur — müddətlər ardıcıldır); hazırlıq 1–300 s, standart 10 s; silinmə 1–86400 s, faktiki bitmə ən gec 00:00; point dəyərləri > 0, yuxarı hədd yox; TP ≥ 0; sabit lot > 0; risk faizi 0 < x ≤ 100; risk məbləği, limitlər > 0. S-1 simvol xüsusiyyətləri bölməsi (point, digits, OrderCalcProfit ilə 1 point/1 lot pul dəyəri, valyuta, lot hədləri, min məsafə, dondurma); S-2 vəziyyət obyektinin içində ayrıca bölmə, hər 10 s, əlavə yazma yox; S-3 panel qiymət ifadəsi + pul ifadəsi (sabit lot) / təxmini lot (risk üsulları); S-4 "≈" + hesablama anı; S-5 köhnədirsə boz + "köhnə"; S-6 Rabitə EA xəritələnmiş simvolları SymbolSelect ilə Market Watch-a əlavə edir, brokerdə yoxdursa panel göstərir.
**Cavab 2:** "Bəli, haması uyğundur"
**Recorded:** 2026-09-24
**Asked:** 14-də yalnız "sıfırdan böyük" deyilir. Yuxarı hədlər və hazırlıq pəncərəsinin standart müddəti nədir? Təklif cədvəli: hazırlıq 1–300 s (standart 10 s); yerləşdirmə 0–3600 s; silinmə 1–86400 s, faktiki bitmə ən gec 00:00; point dəyərləri > 0 sabit yuxarı hədd yox; TP ≥ 0; sabit lot > 0; risk faizi 0 < x ≤ 100; risk məbləği və limitlər > 0. Əlavə seçim: paneldə point dəyərinin qiymət və pul ifadəsi.
**Cavab 1:** "Yerləşdirmə anı hazırlıq pəncərəsi aralığından böyük olarsa ziddiyət yaranır. Hazırlıq üçün 1-300, yerləşdirmə üçün 0-300 (sıfır birbaşa xəbər anı olur) qəbul edilə bilər. Paneldə point qiyməti və pul ifadəsinin əks olunması dəyərin qeyd olunması ilə real olacaqsa məntiqlidir. Məsələn; hər simvolun point qiyməti və pul ifadəsi üçün point dəyərləri bazada ayrıca olaraq sinxron saxlanıla bilər." (2026-09-24)
**Mənim oxunuşum:** yerləşdirmə müddəti 0–300 s (0 = xəbər anı); hazırlıq 1–300 s. Pul ifadəsi — yalnız real dəyərlərlə: hər simvolun point qiyməti və point-in pul dəyəri anbarda ayrıca obyekt kimi, sinxron saxlanılır. Qalan cədvəl sətirlərinə etiraz edilməyib — yekun təsdiqdə yoxlanılacaq.

## Q09 [answered] Panelin dili və vaxtların göstərilməsi
**Terminlər siyahısı təklif edildi:** Symbol, Currency, Impact (High/Medium/Low), Volume (Fixed Lot / Risk % / Risk Amount), Lead Time, Expiration, Offset, Stop Loss, Take Profit, Max Spread, Breakeven (Trigger, Lock), Trailing Stop (Trigger, Distance), Daily/Weekly Loss Limit, Blackout, Trading Mode (Active/Paused), Preparation Window. Ekran adları, düymələr, xəbərdarlıqlar, izahlar Azərbaycan dilində.
**Cavab 2:** "Siyahı uyğun, hər yerdə (Tövsiyə)" — terminlər bütün ekranlarda, dəlil qeydlərində və bildirişlərdə eyni ingilis yazılışı ilə.
**Recorded:** 2026-09-24
**Asked:** Panelin dili və vaxtların göstərilməsi necə olsun? (Tövsiyə: AZ + hər şey server vaxtı ilə göstərilir və daxil edilir, yanında yerli vaxt yalnız köməkçi / AZ + yalnız server / AZ + yerli vaxt çevirmə ilə)
**Cavab 1:** "Tövsiyən uyğundur lakin konfiqurasiyada yer alan texniki terminlər ingilis dilində (olduğu kimi) yazılsın" (2026-09-24)
**Mənim oxunuşum:** interfeys Azərbaycan dilində; server vaxtı + yerli köməkçi qəbul. Konfiqurasiyadakı texniki terminlər ingiliscə olduğu kimi — hansı terminlərin siyahısı təsdiqlənməlidir.

## Q22 [answered] Anbarın quruluşu: dəlil qeydlərinin həftəlik arxivi, bucketlar/qovluqlar, saxlama sinifləri, qoruma
**Asked:** Q10 cavabından: arxiv həftədə bir, ayrıca qovluqda bir bucket-da, Glacier Instant Retrieval; "və s." — anbar quruluşunun digər təfərrüatları.
**Təklif (2026-09-25):** 13.4 toqquşması göstərildi (arxiv qadağası → dəyişir; əlavə əsas: ayda ~259k obyekt, LIST yazma tarifi ilə). 5 bucket: config, state (versiyasız), snapshots, evidence-live (Standard, versiyalı, qısa qoruma), evidence-archive (Glacier IR / R2-də Standard-IA, versiyalı, uzun qoruma). "1 bucket" əvəzinə 2 bucket — səbəb: AWS default retention bucket-dakı hər obyektə aiddir, obyekt başlıqlarını R2 dəstəkləmir. A-1 bazar ertəsi 00:00 server vaxtı; A-2 cədvəlləyici API sorğusunu çağırır; A-3 geri oxuma + say + yoxlama cəmi, sonra canlı silinir; A-4 idempotent; A-5 qısa qoruma erkən silməni bloklayır; A-6 panel hər ikisini göstərir.
**Answer 1:** "Bəli uyğundur. Lakin bir məsələ mənə maraqlıdır ki, "Rabitə EA" bütün bu işləri etməkdə məhdudiyyət yaşayacaq yoxsa yox? Bildiyim qədərilə EA-nın da sərhədləri var."
**Answer 2 (qoruma rejimi):** "Governance, müddətsiz (Tövsiyə)"
**Answer 3 (canlı qoruma):** "30 gün"
**Recorded:** 2026-09-25
**Nəticə:** quruluş və A-1…A-6 qəbul; arxiv Governance müddətsiz; evidence-live qoruması 30 gün. Rabitə EA-nın məhdudiyyətləri sualı cavablandırılmalıdır.

## Q23 [answered] Rabitə EA-nın MQL5 sərhədləri
**Asked:** Rabitə EA üçün R-EA-1 (vəziyyət + qeyd toplusu bir sorğuda, API ayırır), R-EA-2 (taktda ən çoxu bir şəbəkə sorğusu), R-EA-3 (timeout ≤ 5 s, nəbz sorğudan əvvəl və sonra), öz JSON oxuyucusu (MQL5-də JSON yoxdur), Mərhələ 1-də VPS→API sorğu müddətinin ölçülməsi qəbul olunurmu?
**Answer:** "Bəli, uyğundur"
**Recorded:** 2026-09-25

## Q10 [answered] Anbar provayderi hansıdır?
**Asked:** Qaralama provayder adını qəsdən çəkmir; icra üçün konkret seçim lazımdır. Soruşuldu: mövcud hesab / büdcə / ödəniş imkanı.
**Cavab 1:** "Hesab yoxdur, ödənişli provayderə üstünlük veriləcək. Ödənişli olması təhlükəsizlik və üstünlüklər üçün nəzərdə tutulur. Provayderin seçimi isə layihəyə təsir etməməlidir, layihə portativ və esnək olmalıdır. Provayder dəyişikliyi olarsa sistemə düzəliş, təkmilləşdirmə, əlavələr və s. edilməməlidir. Portativlik və esnəklik nə dərəcədə tutarlılıq göstərə bilər? Provayderlərin strukturu, açarları, quruluşu və s. ətraflı araşdırılmalıdır zənnimcə. Bu barədə məsləhətin və təklifin nədir?" (2026-09-24)
**Mənim oxunuşum:** tələb — provayder dəyişikliyi kodda heç bir dəyişiklik tələb etməməlidir. Ödənişli provayder üstündür. Rəsmi sənədlər üzrə provayderlərin açar, icazə, qoruma, CORS, ünvan quruluşu araşdırılıb portativlik modeli və tövsiyə təqdim olunmalıdır.
**Təqdim edildi (2026-09-24):** üç qat (kod portativ / quraşdırma deyil / keçid bir sinxronizasiya); P-1 dörd S3 əməliyyatı + SigV4; P-2 provayderə aid hər şey parametr; P-3 həmişə Content-MD5; P-4 sahə = bucket; P-5 dörd tək səviyyəli açar (17.1 dəyişir); P-6 qeydlər üzərinə yazılmır; P-7 kod bucket/CORS/kilid qurmur; quraşdırma təlimatı + uyğunluq testi; 15.3 "öz domen → sinxronizasiya lazım deyil" səhvdir (açarlar EA parametrindədir); 2-ci meyar "sabit ünvan"a enir. Tövsiyə: R2 əsas, B2 ehtiyat; Wasabi yaramır (90 gün minimum).
**Cavab 2 (portativlik sualına):** "Panel quraşdırmaq üçün provayder axtarışını necə etmək lazımdır? Hansı provayderin uyğun olduğuna necə qərar veriləcək? Layihə sahibi olaraq nələri bilmək lazımdır? Bunları və bu minvalda bilinməli olanları qısaca izah et. Məsələn, axtarış üçün axtarışa `web hosting` yazılır ya necə?"
**Cavab 2 (provayder sualına):** "[No preference]"
**Qeyd:** portativlik modeli hələ təsdiqlənməyib; provayder seçimi açıqdır. İstifadəçi panel hostinqi (Q14) və layihə sahibinin bilməli olduqları haqqında qısa izah istəyir.
**İzah verildi:** statik hostinq, axtarış sözləri, 6 meyar, hesablar/sirlər/2FA, panel hostinqi VPS-ə təsir etmir (Q15-i də həll edir).
**Cavab 3 (portativlik sualına, yenidən):** "Panel və baza üçün infrastruktur/arxitektura baxımından sadə yolu (üsulu) seçmişik yoxsa mürəkkəblik mövcuddur? Əgər daha da sadə üsul ilə panel və baza quruluşu varsa hansılardır və keçid etməyi məsləhət görürsən mi? Təhlükəsizlik baxımıdan xüsusi olaraq ələ al." (2026-09-24)
**Cavab 3 (provayder):** "[No preference]"
**Qeyd:** arxitektura alternativləri (xüsusən təhlükəsizlik) müqayisə olunub tövsiyə verilməlidir → Q21.

## Q21 [answered] Panel və anbar arxitekturası: birbaşa S3 (qaralama) yoxsa daha sadə/təhlükəsiz alternativ?
**Asked:** A birbaşa anbar (qaralama; SigV4 MQL5-də, panelin yazma açarı brauzerdə) / A + giriş qapısı / B nazik API qatı (EA token, panel 2FA girişi, anbar açarları yalnız API-də, API standart interfeyslə portativ, anbar standart S3 ilə)
**Answer:** "B: Nazik API qatı (Tövsiyə)"
**Recorded:** 2026-09-24
**B quruluşu təklif edildi (2026-09-24):** Workers (API + panel eyni ünvanda, CORS yox), Access + müstəqil MFA, EA üçün ayrıca ünvan + token, anbar R2 S3 API ilə, B2 ehtiyat; risklər: bir provayderdə cəmləşmə, EA tokeninin dəyişməsi sinxronizasiya tələb edir.
**Cavab:** "Hosting-in istənilən linux sistemli VPS-də edilməsi üçün arxitektura qurulmasını dəyərləndir. Məsləhət və tövsiyələrini təqdim et"
**Linux VPS qiymətləndirməsi təqdim edildi (2026-09-24):** 1 idarə olunan əsas + Linux VPS hazır çıxış yolu (tövsiyə) / 2 Linux VPS əsas, məlumat anbarda / 3 hər şey VPS-də.
**Cavab:** "[No preference]" və sual (2026-09-25): "S3 API istifadə edəcək olarsaq əgər mümkün olan ən yüksək protativlik və esnəklik əldə olunacaq olaraq başa düşdüm, doğrudur?"
**Qeyd:** hostinq seçimi hələ açıqdır.
**S3 izahı verildi (2026-09-25):** dörd portativlik qatı (S3 API məlumat, standart fetch API kodu, öz domen, standart giriş); S3-ün sərhədləri (alt çoxluq, quraşdırma, məlumatın köçürülməsi).
**Hostinq sualına cavab:** "Linux VPS lazım deyil. Bu zaman VPS-in infrastrukturu, təhlükəsizlik və s.trader-in iş əhatəsinə girir və trader bunu edə bilməz." (2026-09-25)
**Mənim oxunuşum:** idarə olunan hostinq (Workers + Access + anbar); Linux VPS yerləşdirməsi yoxdur; 1.3 və 3.5 prinsipləri qalır. VPS çıxış konteynerinin hazırlanıb-hazırlanmaması və anbar provayderi (R2/B2) təsdiqlənməlidir.
**Yekun quruluş sualı:** "Workers (API + panel) + Access (MFA) + anbar (S3 API), Linux VPS konteyneri hazırlanmır, API kodu standart fetch formatında yazılır?" → **Answer:** "Bəli" (2026-09-25)
**Anbar provayderi sualı (Q10-a aid):** → **Answer:** "AWS əsas R2 və B2 ehtiyat, mümkündür?" (2026-09-25) — sual kimi, cavab gözləyir.
**İzah verildi:** mümkündür; Object Lock versiyalama tələb edir (yalnız qeydlər bucket-ında); IAM ən mürəkkəb quraşdırmadır; tariflər dəqiq çıxarılmadı; bir ehtiyat tövsiyəsi (B2).
**Answer (Q10):** "AWS əsas, R2 ehtiyat. Lakin digər məsələləri dəqiqləşdirmək lazımdır. Məsələn, dəlil qeydləri həftədə 1 dəfə arxivlənir və hamısını ayrıca qovluqda 1 bucket içərisində saxlamaq, bu arxivlərə baxış üçün `S3 Glacier İnstant Retrieval` depolama sinifi seçimi və s." (2026-09-25)
**Nəticə:** Q10 answered — AWS əsas, R2 ehtiyat. Yeni tələb: dəlil qeydlərinin həftəlik arxivi → Q22. Qaralamanın 13.4 "heç vaxt … arxivləşdirmir" qaydası ilə toqquşur.
**Qeyd:** quruluş hələ təsdiqlənməyib; öz Linux VPS-də hostinq variantı qiymətləndirilməlidir. Qaralamanın 1.3 ("heç bir gündəlik əməliyyat server bilikləri tələb etmir") və 3.5 ("server deyil, virtual maşın deyil") prinsipləri ilə toqquşma göstərilməlidir.
**Ripple:** qaralamanın 3.1, 3.3, 3.5, 3.6, 12.3, 16.1, 17, 18 yenidən yazılır. P-1…P-7 API qatının içinə keçir. Əvvəlki qərarlarda "anbar" → "API vasitəsilə anbar" (Q03/Q18 nüsxələr, Q04/Q19 konfiqurasiya, Q08 S-2 simvol xüsusiyyətləri, Q-6 EA siyahılaması → API sorğusu). EA parametrləri: API ünvanı + token. Q10 ikiyə bölünür: API platforması + anbar provayderi; panel girişi (2FA) üsulu seçilməlidir.

## Q11 [answered] Broker və demo hesab hazırdırmı?
**Asked:** Hansı broker, demo hesab hedcinq rejimindədirmi, OTP tələb etmirmi?
**Cavab 1:** "Broker seçilib `https://cfi.trade/az/az`, Demo hesab Metatrader 5 terminal-da açıqdır. `Hedcing` rejimi naməlumdur (aydınlaşdır)." (2026-09-25)
**Qeyd:** hedcinq rejimi, əks mövqelərə icazə, OTP və server vaxtı aydınlaşdırılmalıdır.
**Yoxlama (2026-09-25, lokal MT5 jurnalı `%APPDATA%\MetaQuotes\Terminal\D0E8…\logs`):** "authorized on CFI2-Demo … trading has been enabled, demo account - hedging mode" (17, 21 və 25 sentyabr). Terminal işə düşəndə avtomatik daxil olur, OTP istənmir. Əks mövqelərə icazə (10046) və server vaxtı jurnaldan bilinmir → Mərhələ 1-də sınaq.
**Sual:** "Əks mövqe sınağı və server vaxtının ölçülməsi Mərhələ 1-ə əlavə olunsunmu?" → **Answer:** "Özüm indi yoxlayaram" (2026-09-25)
**Qeyd:** istifadəçinin öz yoxlamasının nəticəsi gözlənilir (əks mövqe; server vaxtı da).
**Nəticə (istifadəçi, 2026-09-25):** "Hər iki əməliyyat açıldı və sonrasında tək-tək qapatdım. Market Watch terminalda - 16:23 / Bakıda - 17:23"
**Mənim oxunuşum:** əks mövqelərə icazə var ✅. Bakı UTC+4 → UTC 13:23 → server vaxtı hazırda UTC+3. Qış vaxtında dəyişə bilər (məs. UTC+2), 6.9 keşikçisi bunu idarə edir; Mərhələ 1-də qış/yay qaydası yoxlanılacaq.
**Status:** Q11 answered — CFI demo (CFI2-Demo), MT5, hedging, əks mövqelər icazəlidir, OTP yoxdur, server UTC+3 (sentyabr).

## Q12 [answered] İnkişaf mühiti: MT5 və MetaTrader VPS
**Asked:** Bu maşında MT5/MetaEditor quraşdırılıbmı, VPS abunəliyi varmı?
**Fakt:** MT5 build 6182, MetaEditor64 və metatester64 `C:\Program Files\MetaTrader 5`-də quraşdırılıb (2026-09-25 yoxlandı).
**Sual:** "MetaTrader VPS abunəliyi var mı?" → **Answer:** "Hələ yoxdur" (2026-09-25)
**Nəticə:** Mərhələ 1 üçün 24 saatlıq pulsuz sınaq dövrü kifayətdir. Abunəlik Mərhələ 2-dən əvvəl alınır.

## Q13 [answered] Sınaq strategiyası
**Təklif (2026-09-25):** T-1 MQL5 məntiq testləri (skript, avtomatik); T-2 Ticarət EA Strategy Tester-də hazır planlarla; T-3 API testləri lokal; T-4 uyğunluq testi real provayderlərdə; T-5 Mərhələ 1 mühit sınağı; T-6 demo qəbul; əvvəl test, sonra kod.
**Cavab 1:** "sistem backtest üçün uyğun dizaynda deyil sadəcə gələcək və ya canlı hadisələr ilə test ediləbilər" (2026-09-25)
**Mənim oxunuşum:** T-2 (Strategy Tester) qəbul edilmir — qaralamanın 4.2 "Backtest … Sistem yalnız canlı işləyir" prinsipi ilə uyğun. Alternativ: demo hesabda canlı sınaq planları (T-2′).
**Təklif 2:** T-2 çıxarılır; T-2′ demo hesabda canlı sınaq planları (adi planın eyni yolu, kiçik offset, nadir halların məqsədli yaradılması); sınaq planı yalnız demo hesabda qəbul olunur, real hesabda rədd + təcili bildiriş. T-1, T-3…T-6 qalır; əvvəl test, sonra kod.
**Cavab 2:** "Bəli, T-2′ ilə (Tövsiyə)"
**Recorded:** 2026-09-25
**Asked:** MQL5 məntiqi necə sınaqdan keçiriləcək (skript əsaslı unit testlər, strategy tester, yalnız demo)?

## Q14 [answered] Panel harada yerləşdirilir?
**Həll:** Q21 yekun quruluşu ("Bəli", 2026-09-25): panel Workers-də API ilə eyni ünvanda, statik fayllar kimi yerləşir, Access + MFA arxasındadır, CORS lazım deyil.
**Asked:** Panelin HTML faylı hansı hostinqdə olacaq (anbarın statik səhifəsi, ayrıca hostinq)?

## Q15 [answered] Panelin ünvanı EA-nın icazə siyahısına aiddirmi?
**Həll (fakt, 2026-09-24):** EA panelə müraciət etmir. İcazəli siyahıda yalnız EA-nın müraciət etdiyi ünvanlar olur: API qatının domeni (Q21) və Forex Factory. Panelin hostinqinin dəyişməsi VPS-ə təsir etmir. İzahda istifadəçiyə deyildi, etiraz olmadı. Yekun xülasədə təsdiqlənəcək.
**Asked:** 15.3 panelin ünvanı dəyişsə sinxronizasiya lazımdır deyir, amma EA panelə müraciət etmir. (Mənim düzəliş təklifim: yalnız anbar və xəbər mənbəyinin ünvanları.)

## Q16 [answered] Həftəsonu gündəlik xülasə göndərilsinmi?
**Asked:** Gündəlik xülasə həftəsonu da göndərilsinmi? (1 hər gün, həftəsonu qısa "Bazar bağlı idi. Sistem işləyir." / 2 həftəsonu yox, 48 saat siqnalsız)
**Answer:** "1: Hər gün (Tövsiyə)"
**Recorded:** 2026-09-25

## Q18 [answered] İki tərəfli nüsxələr necə təmizlənir?
**Asked:** Q03-dən: nüsxələrin yazılması, bərpası və təmizlənməsi üçün Q-1…Q-6 qaydaları uyğundurmu?
Q-1 yazma iki yerə (anbar növbə ilə); Q-2 bərpa: anbar → lokal → ikisi yoxdursa təcili bildiriş, yalnız SL/TP; Q-3 plan son vəziyyətdə ikisi də silinir, anbar silməsi növbə ilə; Q-4 yetim süpürmə: yenidən başlamada və hər gün 00:00, yalnız brokerdə həmin plan ID ilə əmr/mövqe yoxdursa; Q-5 ticarət serveri ilə bağlantı yoxdursa süpürmə işləmir, meyar broker vəziyyətidir, silmə uğursuzluğu ticarətə təsir etmir; Q-6 EA nüsxələr sahəsində siyahılaya bilir (16.1, 17.1 dəyişir).
**Answer:** "Bəli, haması uyğundur"
**Recorded:** 2026-09-23

## Q17 [answered] Planın vəziyyətinin sahibi hansı EA-dır?
**Təklif (2026-09-25):** S-1 hazırlığa qədər Rabitə EA (plan siyahısı faylı, növbə nömrəsi); S-2 hazırlıqdan sonra Ticarət EA (öz vəziyyət faylı); S-3 toqquşmada icra qalib; S-4 deterministik plan ID; S-5 nüsxənin lokal faylını Ticarət EA yazır (Q19 cədvəlinə düzəliş).
**Cavab 1:** "EA-lar arasında əlaqə fayl üsulu olarsa əgər fayl oxuma/yazma gecikmə yaradacaq. Təklifim: `global variables`" (2026-09-25)
**Qeyd:** sahiblik qaydalarına etiraz yoxdur; ötürmə vasitəsi (fayl vs qlobal dəyişən) qiymətləndirilməlidir.
**Təklif 2 (2026-09-25):** Ə-1 isti yolda fayl/şəbəkə yox; Ə-2 növbə nömrəsi qlobal dəyişəndə + qrafik hadisəsi (EventChartCustom) siqnal, taymer yoxlaması; Ə-3 vəziyyət kodu qlobal dəyişəndə, keçidlər GlobalVariableSetOnCondition ilə atomik (S-3-ü əvəz edir); Ə-4 qlobal dəyişənlərin təmizlənməsi (EE. prefiksi). Sual rədd edildi (dismiss).
**Cavab 2:** "Əlavə nüans: Hadisə əsaslı yanaşma əks istiqamətin silinməsi üçün nəzərdə tutulub. Qarışıqlıq olmaması üçün bildirirəm." (2026-09-25)
**Mənim oxunuşum:** "hadisə əsaslı" termini yalnız 9.5-ə (əks əmrin silinməsi, broker ticarət hadisəsi) aiddir; EA-lar arası siqnal üçün qrafik hadisəsi bu terminlə qarışdırılmamalıdır. Qrafik hadisəsinin saxlanıb-saxlanmaması soruşulmalıdır.
**Təklif 3:** Ə-2 yenilənmiş — qrafik hadisəsi yoxdur, Ticarət EA növbə nömrəsini öz taktında yoxlayır; Ə-1, Ə-3, Ə-4, S-1, S-2, S-4, S-5 dəyişməz.
**Cavab 3:** "Hadisə əsaslı saxlanıldığı təqdirdə müsbət fayda əldə edilirsə əgər isimləndirməni aydınlaşdıraraq istifadə edə bilərsən." (2026-09-25)
**Qərar (istifadəçinin şərtinə görə mənim qiymətləndirməm):** müsbət fayda yoxdur — siyahılar dəqiqələrlə əvvəl hazırlanır; ən təcili dəyişiklik (ticarət rejiminin dayandırılması) onsuz da API-dən 30 s-ə qədər gecikmə ilə gəlir, qrafik hadisəsinin qənaət etdiyi ≤1 takt bunun yanında əhəmiyyətsizdir. Qrafik hadisəsi işlədilmir. Qalan qaydalara etiraz edilməyib → Q17 answered (yekun xülasədə təsdiqlənəcək).
**Asked:** Planı Rabitə EA qurur, vəziyyətini Ticarət EA dəyişir. Həqiqət mənbəyi kimdir? (Dizayn qərarı — təklifimi dizayn mərhələsində verəcəm.)

## Q24 [answered] Sənədin forması və yazılma vaxtı
**Asked:** (1) Q15 və Q17 oxunuşum düzgündürmü? (2) Yeni layihə sənədi harada və hansı formada olsun?
**Answer (1):** "Sənədi yazmağa tələsmə, ilk öncə bütün boşluqların, ziddiyətlərin və qarışıqlıqların həll edildiyini təyid etməliyik."
**Answer (2):** "Layihə qovluğunda Markdown (Tövsiyə)"
**Recorded:** 2026-09-25
**Nəticə:** sənəd `docs/superpowers/specs/` altında Markdown, git ilə. Yazmazdan əvvəl qaralama + qərarlar sistemli yoxlanılır; Q15/Q17 təsdiqi hələ açıqdır (Q25-də).

## Q27 [answered] Sənəddə açıq təsdiq tələb edən dörd nəticə (review-dan sonra)
**Asked (2026-09-25):** 1 TQV-10 Paused-da planlar qurulur; 2 NSZ-09 API əlçatmazlığı təcili bildiriş; 3 ANB-07 API-nin bir minimum icazəli anbar açarı; 4 17.2 provayder meyarları saxlanılır.
**Answer:** "Layihə sənədini ümumiyyətlə oxumamışam, review tələbimin məqsədi düzgün yazılmış layihə sənədini oxumaq idi. 1. Bəli 2. Bəli 3. Dəlil qeydləri üçün yaradılmış anbar açarı 1 dənədir? Anbarda sadəcə dəlil qeydlərinin saxlamağımız qərarını aldığımızı xatırlamıram. Bucket-ları ayırdığımızı bilirəm. Bu məsələni tam anlamadım. 4. Bəli"
**Recorded:** 2026-09-25
**Nəticə:** 1, 2, 4 qəbul. 3 aydınlaşdırılmalıdır (ANB-07 bütün 5 bucket-a aid bir açardır, yalnız qeydlərə deyil).
**İzah verildi:** 5 bucket, açar yalnız API-də, bucket üzrə icazələr, EA/panel icazələri API qaydalarındadır; iki açarın faydası yoxdur.
**Cavab 2:** "5 bucket və 1 açar var, açarın hər bucket üçün icazəsi fərqlidir olaraq başa düşdüm, doğrudur? Operatorun (trader-in) paneldən etdiyi dəyişikliklər üçün həmin açar istifadə ediləcək?" (2026-09-25)
**İzah 2:** bəli; açarı operator yox, API işlədir: Access girişi → API yoxlayır → API açarla yalnız config-ə yazır → audit qeydi. EA da açarı görmür.
**Cavab 3:** "Bəli, qəbul" — ANB-07 izahla yenilənir. (2026-09-25)

## Q25 [answered] Sistemli yoxlama: qalan boşluqlar, ziddiyətlər, qarışıqlıqlar
**Tapıntılar (2026-09-25):**
- B1: son vəziyyətli plan + açıq mövqe (5.5, 5.5.3, 10.3) və Q-3 nüsxənin silinməsi ziddiyəti
- B2: ticarət rejimi dayandırılıb geri açılanda gələcək planların həmişəlik itməsi (10.3)
- B3: E kateqoriyası "ticarət dayanır" — necə geri açılır? (11.2)
- B4: T-2′ sınaq planları vs 12.1 "sistemdə əmr anlayışı yoxdur"
- B5: günün ilk dəqiqələrindəki xəbərlər plan üfüqü səbəbindən buraxılır (6.4 + Q08)
- B6: hər iki təqvim mənbəyi alınmırsa bildiriş təbəqəsi (6.8)
- B7: əks əmrin silinməsi alınmazsa təcili bildiriş həddi tanımsızdır (9.5)
- B8: Ə-1 (isti yolda fayl yox) vs Ticarət EA-nın isti yolda yaranan qeydləri
- B9: konfiqurasiyanın silinməsi hansı planları ləğv edir (5.4)
- B10: Forex Factory həftə keçidi və "təsdiqlənməmiş vaxt" sahəsi bilinmir (6.1, 6.5)
- C: Q15 və Q17 təsdiqi
- A: qərarlardan gələn mətn dəyişiklikləri (ripple) siyahısı
**B1 cavabı:** D-1 — "Ləğv edilmiş" və "Dayandırılmış" yalnız açıq mövqesi olmayan plana verilir; dayandırmada mövqesi olan plan "İcra olunmuş"da qalır → "Tamamlanmış"; zərər limitində mövqe bağlandıqdan sonra "Dayandırılmış"; 5.5.3-dəki ləğv ölü cümləsi çıxarılır. **Answer:** "Bəli, D-1 uyğundur" (2026-09-25)
**B2 cavabı:** D-2 — ticarət rejiminin dayandırılması yalnız icraya keçmiş planlara təsir edir (gözləyən əmrlər silinir, "Dayandırılmış" D-1-ə uyğun); "qurulmuş" planlara toxunulmur, hazırlıq anında 7.2-nin 3-cü yoxlaması qərar verir; zərər limitinə (10.1.3) aid deyil. **Answer:** "Bəli, D-2 uyğundur" (2026-09-25)
**B3 təklifi:** D-3 quraşdırma kilidi (D-2 kimi təsir, yadda qalır, Paused → Active ilə açılır, panel göstərir).
**B3 cavabı 1:** "Məsələni düzgün şəkildə aydınlaşdırmaq lazımdır. Sistemin əsası; hər iki istiqamət üçün mövqe tutmaqdır lakin məqsəd tək istiqamət üzrə hərəkətə keçməkdir. Bu zaman isə hedcing-ə onsuz da ehtiyac yoxdur. Çünki pending orderlərin hər iki istiqamətdə yerləşdirilməsi üçün hedcing lazım deyil. Mövzu əslində Hedcing hesabda əks istiqamətdə silinməli olan mövqenin, işlər tərs gedərsə və hər iki istiqamətdə əmr açılarsa əgər nə baş verəcəyini əvvəlcədən aydınlaşdırmaq məqsədilə sənədə daxil edilib." (2026-09-25)
**Mənim oxunuşum:** hedcinq yalnız nasazlıq halı üçündür (hər iki əmr işə düşəndə). Nəticə: 10046 (və 10045) "sistem işləyə bilməz" demək deyil → E kateqoriyasından çıxarılmalı; 15.1-dəki "əks mövqelərə icazə olmalıdır, yoxsa sistem işləmir" şərti və 20.2-dəki risk sətri səhvdir; D-3 kilidi yalnız həqiqi E kodlarına aid olur.
**Yenilənmiş D-3 təklif edildi:** D-3.0 E yalnız 10026/10027/10032; D-3.0a 10046 nasazlıq halı kodu (bir mövqe ilə davam, qeyd + xülasə); D-3.0b bir simvolda bir neçə mövqe bağlananda ən köhnəsi birinci (10045); D-3.1…D-3.4 kilid; 15.1 hedcinq tələbi düzəldilmiş əsasla qalır, "əks mövqelərə icazə" şərti çıxır; 20.2 sətri çıxır.
**B3 cavabı 2:** "Bəli uyğundur. Yalnız layihəni genişləndirmək lazımdır ki, sistem brokerdən asılı olmadığı kimi hesab növündən də asılı olmadan çalışmalıdır" (2026-09-25)
**B4 cavabı:** D-4 — Test Schedule konfiqurasiyada (qayda, göstəriş deyil); `TEST` üçüncü təqvim mənbəyi, real yoldan keçir; hər yerdə TEST işarəsi; real hesabda nəzərə alınmır + bir dəfə təcili bildiriş; blackout/rejim/limitlərə tabe. **Answer:** "Bəli, D-4 uyğundur" (2026-09-25)
**B5 cavabı:** "Bəli, D-5" — plan üfüqü = cari gün + növbəti günün ilk 10 dəqiqəsi. (2026-09-25)
**B6 cavabı:** "Bərpa bildiriş ilə" — D-6: hər iki mənbə ardıcıl 4 dəfə (1 saat) və ya günün başlanğıc qurulmasında alınmırsa epizod başına bir təcili bildiriş; **bərpa da bildirişlə** (xülasə yox). (2026-09-25)
**B7/B8 cavabı:** "Bəli, ikisi də" — D-7: əmr işə düşəndən 10 s sonra əks əmr brokerdədirsə bir təcili bildiriş, cəhdlər davam edir; D-8: isti yolda qeydlər yaddaşda, sonra fayla. (2026-09-25)
**B9 cavabı:** "Bəli, D-9" — konfiqurasiya silinəndə yalnız həmin simvolun cari Impact səviyyəsi silinmiş səviyyə olan, icraya keçməmiş planları ləğv olur. (2026-09-25)
**B10:** sual deyil — FF həftə keçidi və təsdiqlənməmiş vaxt sahəsi Mərhələ 1-də (T-5) müşahidə olunur.
**B11 (yeni tapıntı):** TEST xəbərlərinin real xəbərlərlə birləşməsi və simvolu məşğul etməsi. D-4.6: TEST heç vaxt birləşmir; real planın xəbər anına müəyyən məsafədən yaxın TEST xəbəri qurulmur.
**B11 cavabı:** "Bəli, amma müddət konfiqurasiyada" — məsafə Test Schedule-da operator parametridir, standart 15 dəqiqə. (2026-09-25)
**C (Q15, Q17 təsdiqi):** "Bəli, ikisi də düzgündür" (2026-09-25)
**A (ripple siyahısı):** göstərildi; nəticə kimi A-3 dəqiqləşdi: canlı toplular arxiv yoxlanıb **və** 30 gün keçəndən sonra silinir.
**Yekun sual:** "Bütün boşluqların, ziddiyətlərin və qarışıqlıqların həll olunduğunu təsdiqləyirsinizmi?" → **Answer:** "Təsdiqləyirəm, yaz" (2026-09-25)
**Status:** Q25 answered.
**Nəticə:** D-3 qəbul. **Yeni tələb (Q26):** sistem hesab növündən asılı olmamalıdır — "hedcinq tələbi qalır" hissəsini əvəz edir (netting də dəstəklənməlidir).

## Q26 [answered] Hesab növündən asılı olmama (hedging + netting, bəlkə exchange)
**Asked:** Q25/B3-dən: sistemin netting hesabda davranışı (hər iki əmr işə düşəndə, SL/TP, mövqe identifikasiyası), exchange rejiminin əhatəsi.
**Təklif:** N-1 uçot sistemi brokerdən oxunur; N-2 hər iki əmr işə düşəndə: hedging iki mövqe ayrıca, netting bərabər həcmdə bağlanır (plan Tamamlanmış), fərqli həcmdə çevrilir (ikinci əmrin SL/TP-si, BE/TS nüsxədən); N-3 simvol məşğulluğu netting-i qoruyur; N-4 SL/TP, BE/TS eyni; N-5 bağlama: hedging D-3.0b sırası, netting əks sövdələşmə; N-6 netting-də aidiyyət açan sövdələşmənin plan ID-si ilə, Mərhələ 1-də yoxlanılır; N-7 10045/10046 yalnız hedging; N-8 T-2′ və T-6 netting demo hesabda da. Exchange: əhatədən kənar, aşkarlanır → D-3 kilidi.
**Answer 1:** "Bəli, uyğundur"
**Answer 2 (exchange):** "Əhatədən kənar, aşkarlanır (Tövsiyə)"
**Recorded:** 2026-09-25
