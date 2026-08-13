# Bitcoin OTC İşaretli Güven Ağında Kenar Ağırlığı Tahmini

Bu proje, Bitcoin OTC işaretli güven ağındaki (signed trust network) mevcut ve yönlü bir kenarın (directed edge) normalize edilmiş güven puanını tahmin eder. Çalışma, rastgele değerlendirme (random evaluation) ile zamansal değerlendirmeyi (temporal evaluation) aynı veri ve model sözleşmesi altında karşılaştırır.

Proje, bağlantı tahmini (link prediction), kripto para fiyat tahmini veya yatırım tavsiyesi sunmaz.

## Veri kümesi

[SNAP Bitcoin OTC veri kümesi](https://snap.stanford.edu/data/soc-sign-bitcoinotc.html), kullanıcıların birbirleri için verdiği zaman damgalı güven puanlarını içerir. Depodaki `soc-sign-bitcoinotc.csv` dosyasında:

- 35.592 yönlü değerlendirme (directed rating)
- 5.881 düğüm (node)
- 32.029 pozitif ve 3.563 negatif kenar (edge)

bulunur. Her satır `SOURCE,TARGET,RATING,TIME` biçimindedir. Hedef puan, `rating / 10` işlemiyle `[-1, 1]` aralığına normalize edilir.

## Yöntem

Not defteri (notebook) uçtan uca veri doğrulama (data validation), grafik analizi (graph analysis), özellik çıkarımı (feature extraction), model eğitimi (model training) ve değerlendirme (evaluation) adımlarını içerir.

- Adillik ve iyilik algoritması (Fairness and Goodness Algorithm, FGA) ile düğüm temsilleri
- Derece (degree), geçmiş puan, karşılıklılık (reciprocity) ve iki adımlı yol (two-hop path) bilgilerini kapsayan 28 özellik
- %70 eğitim (training), %10 doğrulama (validation) ve %20 test ayrımı (test split)
- Ridge regresyonu (Ridge regression) ve histogram tabanlı gradyan artırma (Histogram-based Gradient Boosting, HGB) modelleri
- Kök ortalama kare hata (Root Mean Squared Error, RMSE), ortalama mutlak hata (Mean Absolute Error, MAE) ve dengeli doğruluk (balanced accuracy) ölçümleri

Rastgele protokol (random protocol), sabit `403` tohumu ve beş katlı çapraz uydurma (five-fold cross-fitting) kullanır. Zamansal protokol (temporal protocol), gelecekteki bilgilerin geçmişe sızmasını engellemek için kronolojik ve genişleyen bir geçmiş uygular.

## Kurulum

Proje Python `3.13` için yapılandırılmıştır.

```bash
python3.13 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

## Çalıştırma

JupyterLab'i (JupyterLab) proje kökünde başlatın:

```bash
jupyter lab
```

Ardından `projekod_221404032_Hüseyin_Arda_Tuz.ipynb` dosyasını açıp tüm hücreleri sırayla çalıştırın (Run All).

Varsayılan `pretest` kipi, eğitim ve doğrulama adımlarını çalıştırır; kilitli test etiketlerini açmaz. Doğrulama yeniden çalıştırması (verification rerun) için JupyterLab'i şu ortam değişkeniyle (environment variable) başlatın:

```bash
BITCOIN_OTC_NOTEBOOK_MODE=verification_rerun jupyter lab
```

Tam çalıştırma; önbellekleri (cache), ara tabloları, modelleri, metrikleri (metrics), şekilleri ve denetim kayıtlarını (audit manifests) `artifacts/` altında oluşturur.

## Depo yapısı

```text
.
├── projekod_221404032_Hüseyin_Arda_Tuz.ipynb
├── soc-sign-bitcoinotc.csv
├── requirements.txt
├── pyproject.toml
├── README.md
└── LICENSE
```

## Tekrarlanabilirlik

- Kanonik veri dosyasının SHA-256 sağlama toplamı (checksum) not defterinde sabittir.
- Rastgelelik tohumu (random seed) `403` olarak sabitlenmiştir.
- Model ayarları ve 28 özelliğin sırası not defterinde değişmez bir sözleşme olarak tanımlanır.
- Test hedefleri, yalnız doğrulama yeniden çalıştırması (verification rerun) kipinde okunur.

## Lisans

Bu proje [MIT Lisansı](LICENSE) ile lisanslanmıştır.
