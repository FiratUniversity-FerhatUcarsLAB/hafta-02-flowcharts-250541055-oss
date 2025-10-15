HAYAT AY
250541055

// Başlangıç / Başlatma
init_system():
    load_config()
    connect_all_sensors()         // hareket, kapı/pencere, kamera, vb.
    connect_notification_services() // SMS, App, Email
    system_active = true
    alarm_state = "OFF"           // OFF / ON
    alarm_level = 0               // 0 yok, 1 düşük, 2 orta, 3 yüksek
    false_alarm_timer = 0

// Ana döngü (Sürekli)
main_loop():
    init_system()
Sürekli:                          // DOT'ta geri ok ile işaretlenecek
    while system_active == true:   // Sonsuz/kalıcı çalışma koşulu
        // 1) Tüm sensörleri oku
        sensor_readings = read_all_sensors()
            // örn: motion = sensor_readings.motion, doors = sensor_readings.doors, cameras = sensor_readings.cameras

        // 2) Threat (tehdit) tespiti
        threat_score = 0
        if motion.detected:
            threat_score += evaluate_motion_intensity(motion)
        if door_open_unexpected(doors):
            threat_score += evaluate_entry_risk(doors)
        if camera.detects_person:
            threat_score += evaluate_camera_confidence(camera)
        // diğer sensör değerlendirmeleri...

        // 3) Yanlış alarm kontrolü (ev sahibi evde mi? izinli durum?)
        if homeowner_is_home():
            // ev sahibi evdeyse, düşük duyarlılık / yanlış alarm koruması uygula
            threat_score = apply_false_alarm_mitigation(threat_score)
            false_alarm_possible = (threat_score < FALSE_ALARM_THRESHOLD)
        else:
            false_alarm_possible = false

        // 4) Alarm seviyesi belirle
        if threat_score >= HIGH_THRESHOLD:
            alarm_level = 3
        else if threat_score >= MEDIUM_THRESHOLD:
            alarm_level = 2
        else if threat_score > 0:
            alarm_level = 1
        else:
            alarm_level = 0

        // 5) Kamera aktivasyonu (detaya göre)
        if alarm_level >= 1 and not camera.is_active():
            camera.activate(record=true, stream=true)

        // 6) Alarmı tetikle / yükselt
        if alarm_level > 0 and not alarm_state == "ON":
            if not false_alarm_possible:
                alarm_state = "ON"
                alarm_trigger_time = now()
                sound_alarm_local()
                log_event("ALARM_TRIGGERED", alarm_level, sensor_readings)
                send_notifications(alarm_level, sensor_readings)
            else:
                // şüpheli ama ev sahibi evde -> önce uyarı / doğrulama isteği
                send_verification_request_to_app(sensor_readings)
                if homeowner_confirms_safe_within(timeout=VERIFICATION_TIMEOUT):
                    log_event("VERIFIED_SAFE_BY_HOMEOWNER")
                    // alarm tetiklenmez
                else:
                    // onay gelmedi -> alarmı yükselt
                    alarm_state = "ON"
                    sound_alarm_local()
                    send_notifications(alarm_level, sensor_readings)

        // 7) Eğer alarm zaten açıksa: devam veya sıfırlama kontrolü
        if alarm_state == "ON":
            if reset_command_received_from_authorized_user():
                alarm_state = "OFF"
                alarm_level = 0
                camera.deactivate_if_not_required()
                stop_local_alarm()
                log_event("ALARM_RESET_BY_USER", now())
                send_notifications_reset_confirm()
            else:
                // alarmı sürdür, periyodik bildirim/güncelleme yolla
                if time_since(alarm_trigger_time) mod NOTIFICATION_INTERVAL == 0:
                    send_notifications(alarm_level, latest_snapshot())

        // 8) Kayıt ve bekleme (debouncing / sensör döngü bekleme)
        persist_logs_periodically()
        sleep(SENSOR_POLL_INTERVAL)   // kısa bekleme, sonra tekrar oku (döngü devam)

    // system_active false olursa sistem kapanır
    shutdown_procedures()
