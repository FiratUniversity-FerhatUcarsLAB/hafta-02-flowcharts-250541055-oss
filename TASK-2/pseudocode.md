HAYAT AY
250541055

BAŞLA

// Kullanıcı girişi
EĞER kullanıcı_girişi_yapılmış_mı() = HAYIR İSE
    GİRİŞ_YAP()
SON

// Ürün kategorilerinde gezinme
TEKRARLA
    kategori = KATEGORİ_SEÇ()
    ürün_listesi = KATEGORİ_ÜRÜNLERİNİ_GÖSTER(kategori)
    ürün = ÜRÜN_SEÇ(ürün_listesi)
    
    // Stok kontrolü
    EĞER STOK_KONTROL(ürün) = VAR İSE
        SEPETE_EKLE(ürün)
        YAZ("Ürün sepete eklendi.")
    DEĞİLSE
        YAZ("Stokta yok, başka ürün seçiniz.")
    SON

    devam = KULLANICI_DEVAM_EDİYOR_MU()
TAAKİ devam = HAYIR OLANA KADAR

// Sepet görüntüleme ve düzenleme
TEKRARLA
    SEPETİ_GÖSTER()
    işlem = SEPET_DÜZENLEME_İŞLEMİ_SEÇ()
    
    EĞER işlem = "ÜRÜN SİL" İSE
        ÜRÜN_SİL_SEPETTEN()
    EĞER işlem = "ADET DEĞİŞTİR" İSE
        ADET_GÜNCELLE()
    EĞER işlem = "DEVAM ET" İSE
        ÇIKIŞ_DÖNGÜ
    SON
TAAKİ işlem = "DEVAM ET"

// İndirim kodu
EĞER KULLANICI_İNDİRİM_KODU_GİRMEK_İSTİYOR_MU() = EVET İSE
    kod = İNDİRİM_KODU_AL()
    EĞER KOD_GEÇERLİ_Mİ(kod) = EVET İSE
        İNDİRİM_UYGULA(kod)
        YAZ("İndirim uygulandı.")
    DEĞİLSE
        YAZ("Geçersiz indirim kodu.")
    SON
SON

// Minimum tutar kontrolü
EĞER SEPET_TOPLAMI() < 50 İSE
    YAZ("Sipariş verebilmek için minimum 50 TL gereklidir.")
    GİT SEPETİ_GÖSTER()
SON

// Kargo ücreti hesaplama
EĞER SEPET_TOPLAMI() >= 200 İSE
    KARGO_UCRETI = 0
    YAZ("Kargo ücretsiz!")
DEĞİLSE
    KARGO_UCRETI = 29.99
    YAZ("Kargo ücreti eklendi: 29.99 TL")
SON

// Ödeme yöntemi seçimi
YAZ("Ödeme yöntemi seçiniz: 1-Kredi Kartı 2-Banka Kartı 3-Kapıda Ödeme 4-Havale")
ödeme_yöntemi = ÖDEME_YÖNTEMİ_SEÇ()

EĞER ödeme_yöntemi = GEÇERLİ İSE
    ODEME_AL(ödeme_yöntemi)
DEĞİLSE
    YAZ("Geçersiz ödeme yöntemi.")
    GİT ÖDEME_YÖNTEMİ_SEÇ()
SON

// Sipariş onayı
SİPARİŞ_OLUŞTUR()
YAZ("Sipariş başarıyla oluşturuldu. Teşekkür ederiz!")

BİTİR
