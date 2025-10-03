# bendgrid-uk — Bölge Tabanlı Erken Uyarı ve Risk Skoru (Düşünce Dosyası)

Bu readme bir “nasıl kurulur” rehberi değil; **ne yapıyoruz, neden yapıyoruz, nereye kadar gideceğiz ve nerede duracağız** sorularının net cevabıdır. Kod ve kurulum daha sonra eklenecek. Burada vizyon, prensipler, etik çerçeve, değer kanıtı (PoV) ve ürünleşme kurgusu var. Hayal değil; ölçülebilir, denetlenebilir, durdurulabilir bir sistem.

---

## 1) Amaç
- Şehir ölçeğinde **bölge tabanlı** (500 m – 1 km) **kısa vadeli risk skorları** üretmek.
- Soru net: **Nerede ve ne zaman yoğunlaşma/anomali riski artıyor?**
- Hedef, karar vericilere (emniyet, belediye, etkinlik/güvenlik, lojistik) **erken uyarı sinyali** sağlamak. Kararı insanlar verir; sistem yön gösterir.

---

## 2) Ne Değil?
- Kişi veya grup hedefleyen bir sistem değil.
- “Suçu kesin olarak tahmin eden” kristal küre hiç değil.
- Demografik, etnik, dini, politik veya hassas kişisel özellikleri **kullanmayan** bir yaklaşım.
- PR gösterisi değil; **baseline’ı yenerse kalır, yenemezse kapanır.**

---

## 3) Vizyon (ve Sınırlar)
- **Vizyon:** Şeffaf, açıklanabilir, etik sınırları net bir **bölge risk radarı**.
- **Sınırlar:** Verinin doğası gereği belirsizlik var. Mutlak doğruluk iddiası yok. Model yalnızca **trend/anomali** sinyali üretir.
- **İlke:** “Ölçülebilir fayda üret, aksi halde projeyi sonlandır.”

---

## 4) Değer Önermesi
- Operasyon planlamasında **top-K bölge** seçimi yapılırken, naif yaklaşıma (dün neredeyse bugün de orası) karşı **ölçülebilir bir iyileştirme** sunmak.
- Etkinlik dönemleri, hava koşulları, POI (mekân) yoğunluğu gibi sinyalleri birleştirerek **erken uyarı penceresini** birkaç saat/gün öne çekmek.

---

## 5) Veri Felsefesi
- **Birincil veri:** UK Police sokak düzeyi (anonimleştirilmiş) olay istatistikleri (aylık).
- **Bağlamsal veriler:** Hava (saatlik), OSM/Overpass (POI/altyapı).
- **İsteğe bağlı sinyal:** Haber/olay akışı (tema/sentiment) — yalnızca gerçekten katkı sağlıyorsa.
- **Gerçekçilik:** UK Police verisi “gerçek zamanlı” değildir; bu nedenle model **proxy sinyallerle** (hava, POI, haber) **nowcasting** kabiliyeti kazanır.
- **Şeffaflık:** UI’da veri kaynakları ve tazelik bilgisi gösterilir.

---

## 6) Tasarım İlkeleri
- **Basitten başla:** Naif baseline → istatistiksel yöntem → klasik ML → gerekiyorsa derin öğrenme.
- **Açıklanabilirlik şart:** Her skorla birlikte “özellik katkı dökümü” (ör. son 24s yoğunluk, ivme, yağış bayrağı, POI yoğunluğu).
- **Grid öncelikli:** Hücre tabanlı yapı, geokod hatalarını yutar, etik sınırları korur.
- **Operasyonel düşün:** Sinyal gecikmesi, güncelleme pencereleri ve veri gecikmeleri UI’da görünür olmalı.
- **Kapı kontrolü:** Metriği geçemezse durdur.

---

## 7) AI Yaklaşımı (Aşamalı)
- **Aşama 1 (PoV):**  
  - İstatistiksel yoğunlaşma ve ivme sinyalleri (3 saat / 24 saat / 7 gün).  
  - Klasik ML (lojistik regresyon → gradient boosting).  
  - Anomali algılama (gerekirse).  
- **Aşama 2 (yeterli veri/sinyal varsa):**  
  - Zaman serisi modelleri (TFT gibi).  
  - Spatio-temporal graf yaklaşımları (komşuluk etkisi belirginsa).  
- **Koşul:** Aşama 2’ye geçmek için Aşama 1’e kıyasla **istatistiksel anlamlı uplift** şarttır.

---

## 8) Etik ve Hukuk
- **Kişi yok:** Hücre (grid) bazlı skorlar.  
- **Hassas veri yok:** Demografi/ırk/din/politik görüş yok.  
- **Açıklanabilirlik:** Her risk skorunda “neden”e dair kısa ve net bir özet.  
- **Yanlılık farkındalığı:** Sinyallerin temsil yanlılığı yaratmaması için ablation testleri.  
- **Kullanım sözleşmesi:** Üretilecek çıktılar “yardımcı sinyal”dir; nihai kararı insana ait olduğuna dair açık uyarı.

---

## 9) Başarı Kriterleri (PoV Kapısı)
- **Metrik:** Precision@K (operasyon kapasitesiyle uyumlu), PR-AUC, hit-rate uplift.
- **Eşik:** En az üç farklı zaman diliminde **naif baseline’a kıyasla ≥ %20 relatif uplift**.  
- **İstatistiksel güven:** Bootstrap güven aralıkları çakışmamalı; rastgeleliğe dayanmasın.
- **Saha anlamı:** Top-N seçilen bölgeler insan gözüyle makul (etkinlik, POI, hava ile açıklanabilir).

**Karar:**  
- Eşiği geçerse kalibre edip ürünleştir.  
- Geçemezse, açık raporla sonuçları yayımla ve projeyi durdur.

---

## 10) Ürünleşme Kurgusu
- **Harita UI:** Isı katmanı, hücre panelinde kısa açıklama ve özellik katkıları.  
- **Sürümleme:** Model sürümü ve veri penceresi UI’da görünür.  
- **Uyarılar:** Top-K hücreler için e-posta/webhook (oransal artış, ivme notu ile).  
- **Kullanıcı profilleri:** Operasyon planlayıcı, analist, yönetici — farklı detay seviyeleri.

---

## 11) Operasyon ve Yönetişim
- **Sürüm kontrolü:** Model ve veri güncellemeleri versiyonlanır.  
- **Günlük/durum izleme:** ETL gecikmeleri, skor dağılımları, veri boşlukları düzenli raporlanır.  
- **Model drift:** Belirli eşiklerin altında performans → “kırmızı bayrak” ve otomatik uyarı.  
- **Denetim izi:** Önemli çıktılar ve karar destek ekranları, daha sonra incelenebilir şekilde saklanır.

---

## 12) Riskler ve Karşı Önlemler
- **Raporlama yanlılığı:** Haber çıkmayan olaylar görünmeyebilir → “mutlak sayı” değil, **trend/ivme** odaklı yorum.  
- **Coğrafi gürültü:** Konum doğruluğu değişken → hücre boyutu ayarlanır, komşu hücre etkisi değerlendirilir.  
- **Seyrek veri:** Sıfıra yakın sayımlar → uygun eşikleme ve pencereler; sınıf dengesizliği teknikleri.  
- **Yanlış pozitif/negatif:** Operasyon planlamasında “yardımcı sinyal” olduğu vurgulanır; tek başına karar verdirmez.

---

## 13) Kullanım Senaryoları (Somut)
- **Etkinlik güvenliği:** Derbi, konser, festival dönemlerinde çevre ızgarasında beklenen yoğunluk artış pencereleri.  
- **Belediye planlaması:** Belirli bölgelerde geçici aydınlatma/ekip artışı, riskli saat aralıklarının belirlenmesi.  
- **Özel güvenlik/logistik:** Vardiya planlarının dinamik ayarı, güzergâh optimizasyonu.

---

## 14) Raporlama ve Şeffaflık
- **Haftalık rapor:** Top-K bölgeler, önceki haftaya göre değişim, anomali listesi.  
- **Ablation notları:** Hava, POI, haber sinyallerinin açık/kapalı olduğu denemelerin karşılaştırması.  
- **Kısıtlar:** Verinin güncelliği, kapsama farkları ve modelin varsayımları raporda net yazılır.

---

## 15) Yol Haritası (Kurulum Detayı İçermeden)
- **Sprint 1:** Grid yaklaşımı, veri akışları, temel özellik seti, basit skor ve açıklanabilir panel.  
- **Sprint 2:** Klasik ML ile PoV; metrikler ve ablation; go/no-go kararı.  
- **Sprint 3:** Başarı varsa sinyal setini genişlet (haber/tema), UI rafine et, bildirim mekanizmasını ekle.  
- **Sprint 4:** Ürünleşme sertleştirmeleri (izleme, sürümleme, yetkilendirme) ve kurumsal entegrasyon opsiyonları.

---

## 16) Ekip Rolleri (Minimal)
- **Veri/ML:** Özellik tasarımı, modelleme, metrik ve ablation.  
- **Geo/ETL:** Mekânsal işleme, veri güvenilirliği, pencereler ve gecikme yönetimi.  
- **Ürün/Deneyim:** UI/UX, açıklanabilirlik metinleri, raporlama ve kullanıcı geri bildirimleri.  
- **Etik/Denetim:** Yanlılık kontrolleri, kullanım ilkeleri, şeffaflık standartları.

---

## 17) Katkı Prensipleri
- “Önce ölçeklenebilir doğruluk” yerine **“önce doğruluk, sonra ölçek”**.  
- Her yeni özellik, **ablation** ve **değer katkısı** ile gelir.  
- Ekip içi görüş ayrılıkları metrik ve deneyle çözülür; his veya varsayımla değil.  
- Sonuçlar kötü ise de yayımlanır; negatif sonuç da bir sonuçtur.

---

## 18) Lisans ve Uyum
- Kod açık kaynak (MIT) hedeflenir; veri setlerinin kendi lisans koşulları saklıdır.  
- Kaynaklara atıf, rate-limit ve kullanım koşullarına uyum esastır.

---

## 19) Kapanış
Bu proje gösterişli cümleler için değil; **sahada karşılığı olan** sinyal için var. Ölçmediğini yönetemezsin. Ölçtüğünü de dürüstçe raporlamazsan, yalnızca vakit harcarsın.  
FinchGrid UK, bu yüzden “basitten başlayıp” metrikle kendini kanıtlayan; kanıtlayamazsa da **kendini kapatmayı bilen** bir projedir.
