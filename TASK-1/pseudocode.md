HAYAT AY 
250541055

BAŞLA

  card_inserted ← TRUE
  card_blocked ← FALSE
  pin_verified ← FALSE
  pin_attempts ← 0
  max_pin_attempts ← 3
  account_balance ← 1500
  daily_limit ← 2000
  daily_withdrawn ← 0
  cash_unit ← 20
  atm_cash ← 10000

  EĞER card_blocked = TRUE İSE
      YAZ "Kart bloke. Lütfen bankayla iletişime geçin."
      BİTİR
  SON

  // PIN DOĞRULAMA DÖNGÜSÜ
  TEKRAR
      entered_pin ← KULLANICIDAN "PIN giriniz:" AL
      EĞER entered_pin DOĞRU İSE
          pin_verified ← TRUE
      DEĞİLSE
          pin_attempts ← pin_attempts + 1
          EĞER pin_attempts = max_pin_attempts İSE
              card_blocked ← TRUE
              YAZ "3 yanlış giriş! Kart bloke edildi."
              BİTİR
          DEĞİLSE
              YAZ "Hatalı PIN. Kalan deneme: " + (max_pin_attempts - pin_attempts)
          SON
      SON
  PIN DOĞRULANANA KADAR

  // ANA İŞLEM DÖNGÜSÜ
  TEKRAR
      YAZ "1) Bakiye sorgula"
      YAZ "2) Para çek"
      YAZ "3) Çıkış"
      secim ← KULLANICIDAN AL

      EĞER secim = 1 İSE
          YAZ "Bakiyeniz: " + account_balance + " TL"

      DEĞİLSE EĞER secim = 2 İSE
          requested_amount ← KULLANICIDAN "Çekmek istediğiniz tutar (TL):" AL

          // KOŞUL 1: 20 TL’nin katı olmalı
          EĞER requested_amount MOD cash_unit ≠ 0 İSE
              YAZ "Tutar 20 TL’nin katı olmalıdır."
              DEVAM ET
          SON

          // KOŞUL 2: Yetersiz bakiye
          EĞER requested_amount > account_balance İSE
              YAZ "Yetersiz bakiye."
              DEVAM ET
          SON

          // KOŞUL 3: Günlük limit
          EĞER daily_withdrawn + requested_amount > daily_limit İSE
              YAZ "Günlük limit aşıldı."
              DEVAM ET
          SON

          // KOŞUL 4: ATM nakit kontrolü
          EĞER requested_amount > atm_cash İSE
              YAZ "ATM'de yeterli nakit yok."
              DEVAM ET
          SON

          // TÜM KOŞULLAR TAMAMSA PARA VER
          account_balance ← account_balance - requested_amount
          daily_withdrawn ← daily_withdrawn + requested_amount
          atm_cash ← atm_cash - requested_amount
          YAZ "Lütfen paranızı alın."
          YAZ "Yeni bakiyeniz: " + account_balance + " TL"
          YAZ "Fiş veriliyor..."

          // BAŞKA İŞLEM SOR
          cevap ← KULLANICIDAN "Başka işlem yapmak ister misiniz? (E/H):" AL
          EĞER cevap = "E" İSE
              DEVAM ET
          DEĞİLSE
              YAZ "Kart iade edildi. İyi günler."
              ÇIK
          SON

      DEĞİLSE EĞER secim = 3 İSE
          YAZ "Kart iade edildi. İyi günler."
          ÇIK
      SON

  SÜREKLİ TEKRAR

BİTİR
