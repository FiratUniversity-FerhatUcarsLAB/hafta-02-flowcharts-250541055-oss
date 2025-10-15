HAYAT AY
250541055

Başla
    Girdi: öğrenci_no, şifre
    Eğer giriş_başarılıysa:
        DersListesiniGörüntüle()

        ToplamKredi = 0
        SeçilenDersler = []

        Tekrar:
            Kullanıcıdan işlem al (1: Ders ekle, 2: Ders çıkar, 3: Kayıt Onayla)
            
            Eğer işlem == 1 ise:
                DersSeç()
                Eğer kontenjan_müsait_mi(ders):
                    Eğer on_kosul_saglanmis_mi(ders):
                        Eğer zaman_cakismasi_yok_mu(ders, SeçilenDersler):
                            Eğer ToplamKredi + ders.kredi ≤ 35:
                                SeçilenDersler’e ekle
                                ToplamKredi += ders.kredi
                            Değilse:
                                Yazdır "Kredi limiti aşıldı!"
                        Değilse:
                            Yazdır "Zaman çakışması!"
                    Değilse:
                        Yazdır "Ön koşul dersi alınmamış!"
                Değilse:
                    Yazdır "Kontenjan dolu!"
            
            Eğer işlem == 2 ise:
                DersSil(SeçilenDersler)
                ToplamKredi -= silinen_ders.kredi

        Kayıt özeti göster (SeçilenDersler, ToplamKredi)

        Eğer GPA < 2.5 ise:
            Danışman onayı iste
            Eğer onay_verildi:
                Kayıt onayla
            Değilse:
                Yazdır "Kayıt reddedildi"
        Aksi halde:
            Kayıt onayla
    Aksi halde:
        Yazdır "Giriş başarısız!"
Bitir
