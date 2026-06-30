# Kredi Risk Skoru Regresyon Analizi Projesi

Bu proje, bir finans kuruluşunun sentetik kredi veri seti (`focused_synthetic_loan_data.csv`) üzerinde müşterilerin risk skorlarını (`RiskScore`) tahmin etmek amacıyla geliştirilmiştir. Proje kapsamında ham veri analizi, çoklu doğrusal bağlantı (multicollinearity) yönetimi, veri standardizasyonu ve çeşitli doğrusal/düzenlileştirilmiş (regularized) regresyon modellerinin performans karşılaştırmaları gerçekleştirilmiştir.

---

## 📋 Proje Metodolojisi ve İş Akışı

1. **Veri Setinin Keşfi (EDA):** 2000 gözlem ve 36 özellikten oluşan veri setinin şeması, eksik değer durumu ve betimsel istatistikleri incelenmiştir.
2. **Özellik Seçimi ve Boyut Azaltma:** Doğrusal modellerin varsayımlarını ihlal eden kategorik değişkenler ve yüksek derecede korelasyona sahip (multicollinearity) sayısal değişkenler elenmiştir.
3. **Veri Standardizasyonu:** Değişkenlerin farklı ölçeklerde olmasının model katsayıları üzerindeki saptırıcı etkisini önlemek amacıyla `StandardScaler` uygulanmıştır.
4. **Modelleme ve Çapraz Doğrulama (Cross-Validation):** Veri seti %75 Eğitim ve %25 Test (`test_size=0.25`) olacak şekilde kararlı bir yapıda bölünmüştür. 7 farklı regresyon mimarisi (yalın ve hiperparametre optimizasyonlu) eğitilerek test seti üzerinde tahmin başarısı ölçülmüştür.

---

## 🛠️ Veri Ön İşleme ve İstatistiksel Filtreleme

### 1. Doğrudan Elenen Değişkenler
Modelin doğrusal regresyon tabanına uygunluğu ve analiz kolaylığı açısından zaman serisi ve yüksek kategorili metinsel değişkenler ilk aşamada veri setinden düşürülmüştür:
* `ApplicationDate`, `EmploymentStatus`, `EducationLevel`, `MaritalStatus`, `HomeOwnershipStatus`, `LoanPurpose`

### 2. Çoklu Doğrusal Bağlantı (Multicollinearity) Çözümü
Özelliklerin kendi aralarındaki korelasyon yapısı incelenmiş ve varyans şişmelerini engellemek adına dinamik bir korelasyon filtresi yazılmıştır. Eğitim setinde Pearson korelasyon katsayısı mutlak değerce **0.80'den büyük** olan ve modelin kararlılığını bozan şu 4 değişken başarıyla elenmiştir:
* `Experience`
* `InterestRate`
* `MonthlyIncome`
* `NetWorth`

---

## 📊 Model Performans Sonuçları

Modeller, `test_size=0.25` (500 test gözlemi) ve `random_state=42` parametreleri ile ayrılan bağımsız test seti üzerinde sınanmıştır. Elde edilen hata ve açıklayıcılık metrikleri şu şekildedir:

| Model | Mean Absolute Error (MAE) | Mean Squared Error (MSE) | $R^2$ Score (Belirlilik Katsayısı) |
| :--- | :---: | :---: | :---: |
| **LassoCV (cv=5)** | **1.8260** | **5.3768** | **0.9049** |
| **ElasticNetCV (cv=5)** | 1.8292 | 5.4046 | 0.9044 |
| **RidgeCV (cv=5)** | 1.8296 | 5.4456 | 0.9037 |
| **Ridge Regression** | 1.8298 | 5.4509 | 0.9036 |
| **Linear Regression** | 1.8298 | 5.4517 | 0.9036 |
| **Lasso ($\alpha=1$)** | 2.6841 | 11.7805 | 0.7917 |
| **ElasticNet ($\alpha=1$)** | 2.8186 | 12.8273 | 0.7732 |

---

## 🔍 İstatistiksel Değerlendirme ve Çıkarımlar

* **Yüksek Açıklayıcılık Gücü ($R^2$):** Test setinin %25 oranına genişletilmesiyle birlikte, modellerin genel başarı grafiği kararlı bir zemine oturmuş ve en başarılı model olan `LassoCV` ile **%90.49** açıklayıcılık oranına ulaşılmıştır. Bu yüksek oran, eldeki finansal ve demografik değişkenlerin `RiskScore` yapısını neredeyse kusursuz modellediğini gösterir.
* **Varyans-Bias Dengesi ve CV Başarısı:** Yalın `Lasso` (%79.17) ve `ElasticNet` (%77.32) modelleri, varsayılan $\alpha=1.0$ ceza parametresinin ağırlığı sebebiyle hafif bir underfitting yaşasa da, çapraz doğrulama (5-fold CV) uygulandığında en optimum hiperparametreleri otomatik seçerek başarıyı **%90.4** bandının üzerine taşımıştır.
* **Multicollinearity Sonrası Kararlılık:** Model katsayılarının doğrusal regresyon, Ridge ve RidgeCV modellerinde neredeyse tamamen aynı sonuçları vermesi, ön işlemde uygulanan korelasyon filtresinin çoklu doğrusal bağlantı problemini tamamen çözdüğünün ve matris tekilliğini ortadan kaldırdığının en net kanıtıdır.

---

## 🏗️ Proje Yapısı

```text
├── focused_synthetic_loan_data.csv   # Kredi risk ham veri seti
├── odev.ipynb                        # Veri analizi ve modelleme Jupyter Notebook kodu
└── README.md                         # Proje raporu ve sonuçları (Bu dosya)
