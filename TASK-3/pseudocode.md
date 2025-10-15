HAYAT AY
250541055

1) Modül 1 — Randevu Alma (Pseudocode)
Fonksiyon RANDEVU_AL():
    // Hasta kimlik doğrulama
    yaz("TC Kimlik No giriniz:")
    tc = oku()
    eğer NOT KIMLIK_DOGRULA(tc) ise:
        yaz("Kimlik doğrulama başarısız. Tekrar deneyin veya destek ile iletişime geçin.")
        döndür HATA

    döngü:
        // Poliklinik seçimi
        poliklinikler = GET_POLIKLINIK_LISTESI()
        yaz("Polikliniklerden birini seçin:")
        yaz_liste(poliklinikler)
        poliklinik = oku_seçim()

        // Doktor listesi
        doktorlar = GET_DOKTOR_LISTESI(poliklinik)
        yaz("Doktor seçin:")
        yaz_liste(doktorlar)
        doktor = oku_seçim()

        // Uygun saatleri göster
        uygun_saatler = GET_UYGUN_SAATLER(doktor)
        eğer uygun_saatler boş ise:
            yaz("Seçilen doktor için uygun saat yok. Başka doktor/poliklinik seçin.")
            devam // döngü devam eder
        yaz("Uygun saatler:")
        yaz_liste(uygun_saatler)
        saat = oku_seçim()

        // Onaylama
        yaz("Seçiminiz: Poliklinik:", poliklinik, " Doktor:", doktor, " Saat:", saat)
        yaz("Onaylıyor musunuz? (E/H)")
        onay = oku()
        eğer onay == "E" veya onay == "e" ise:
            randevu_id = KAYDET_RANDEVU(tc, poliklinik, doktor, saat)
            // SMS gönderimi (asenkron olarak kuyruğa atılabilir; burada doğrudan çağrı)
            sms_metni = "Randevunuz onaylandı. ID:" + randevu_id + " " + poliklinik + " " + doktor + " " + saat
            GONDER_SMS(tc, sms_metni)
            yaz("Randevunuz onaylandı. SMS gönderildi.")
            döndür BAŞARILI
        aksi halde:
            yaz("Randevu iptal edildi. Yeni seçim yapabilirsiniz.")
            yaz("Başka işlem yapmak ister misiniz? (E/H)")
            cevap = oku()
            eğer cevap != "E" ve cevap != "e" ise:
                döndür İPTAL
            // aksi halde döngü başa döner

2) Modül 2 — Tahlil Sonuçları (Pseudocode)
Fonksiyon TAHLIL_SONUCLU_GOR():
    // Hasta kimlik doğrulama
    yaz("TC Kimlik No giriniz:")
    tc = oku()
    eğer NOT KIMLIK_DOGRULA(tc) ise:
        yaz("Kimlik doğrulama başarısız. Tekrar deneyin veya destek ile iletişime geçin.")
        döndür HATA

    // Tahlil var mı kontrolü
    tahlil_listesi = GET_TAHLIL_LISTESI(tc)
    eğer tahlil_listesi boş ise:
        yaz("Size ait tahlil kaydı bulunamadı.")
        döndür YOK

    yaz("Bulunan tahliller:")
    yaz_liste(tahlil_listesi) // örn: [Hemogram #123, Biyokimya #456]

    seçim = oku_seçim() // kullanıcı hangi tahlili görmek istediğini seçer
    seçilen_tahlil = tahlil_listesi[seçim]

    // Sonuç hazır mı kontrolü
    eğer NOT TAHLIL_HAZIR_MI(seçilen_tahlil) ise:
        yaz("Seçilen tahlil sonucu henüz hazır değil. Lütfen daha sonra tekrar kontrol edin.")
        döndür BEKLEME
    aksi halde:
        sonuç = GET_TAHLIL_SONUCU(seçilen_tahlil)
        yaz("Tahlil Sonuçları:")
        yaz(sonuç.format_text())
        yaz("PDF indirmek ister misiniz? (E/H)")
        cevap = oku()
        eğer cevap == "E" veya cevap == "e" ise:
            pdf_dosya = OLUSTUR_PDF(sonuç)
            yaz("PDF oluşturuldu:", pdf_dosya.yolu)
            // (UI/CLI: dosya yolunu göster veya indirme linki üret)
            döndür PDF_INDIRildi
        döndür GORUNTULENDI

3) Ana Menü ile Birleştirme (Pseudocode)
Fonksiyon ANA_MENÜ():
    döngü:
        yaz("=== HASTANE SİSTEMİ ANA MENÜ ===")
        yaz("1 - Randevu Al")
        yaz("2 - Tahlil Sonucu Gör")
        yaz("3 - Çıkış")
        seçim = oku()

        eğer seçim == "1" ise:
            sonuç = RANDEVU_AL()
            eğer sonuç == HATA ise:
                yaz("Randevu modülünde hata oluştu.")
        değilse eğer seçim == "2" ise:
            sonuç = TAHLIL_SONUCLU_GOR()
            eğer sonuç == HATA ise:
                yaz("Tahlil modülünde hata oluştu.")
        değilse eğer seçim == "3" ise:
            yaz("Sistemden çıkılıyor. İyi günler.")
            kır // döngüyü kır
        değilse:
            yaz("Geçersiz seçim. Tekrar deneyin.")

        yaz("Başka işlem yapmak ister misiniz? (E/H)")
        devam = oku()
        eğer devam != "E" ve devam != "e" ise:
            yaz("Oturum kapatılıyor.")
            kır
