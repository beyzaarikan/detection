# Obstacle Detection
Bu repository, Teknofest Robotaxi yarışması için tasarlanmış bir otonom araç için OpenCV ve NumPy kütüphanelerini kullanarak, belirli renk aralıklarına sahip nesneleri tespit eden ve bu nesnelerin özelliklerini analiz eden kodları içermektedir.

![Otonom Araç](https://github.com/beyzaarikan/detection/blob/main/githaba.jpg)


Renk Tabanlı Maskeleme: Kırmızı renk tonlarına sahip nesneleri tespit eder.

Kontur Tespiti: Maske üzerinde konturları bularak nesnelerin sınırlarını belirler.

Dikdörtgen Tespiti: Nesnelerin köşelerini ve boyutlarını analiz eder.

Görselleştirme: Tespit edilen nesnelerin merkezlerine ve köşelerine işaretler ekler.

## Kullanım
ObstacleDetection sınıfını kullanarak bir nesne oluşturun.
 
process_image metodunu kullanarak görüntüyü işleyin. Görüntüyü bir dosya adı olarak veya doğrudan bir numpy dizisi olarak geçebilirsiniz.

İşlenen görüntüde tespit edilen engellerin merkezleri ve boyutları hakkında bilgi alabilirsiniz.

## Kod Örneği
```python
from obstacle_detection import ObstacleDetection

processor = ObstacleDetection(min_area=8000) # ObstacleDetection sınıfından bir nesne oluşturun

final_mask = processor.process_image("den.png", print=True) # Görüntüyü işleyin
```
## Fonksiyonlar

### `calculate_hypotenuse(x1, y1, x2, y2)`
İki nokta arasındaki hipotenüsü (mesafeyi) hesaplar.

**Parametreler:**
- `x1`, `y1`: Birinci noktanın koordinatları.
- `x2`, `y2`: İkinci noktanın koordinatları.

**return:**
- distance (hipotenüs).

### `process_image(img, print=False)`
Görüntüyü işler ve tespit edilen nesneleri belirler. Görüntüyü dosya adı olarak veya `numpy` dizisi olarak alır.

**Parametreler:**
- `img`: Görüntü dosya adı veya `numpy` dizisi.
- `print` (isteğe bağlı): Görselleştirme pencerelerini açıp açmamak için bir bayrak. Varsayılan değer `False`'dır.

**return:**
- Sonuç maskesi (`final_mask`).

### `detect_objects(contours, img)`
Konturları analiz eder ve tespit edilen nesnelerin özelliklerini belirler.

Kontur Çizgileri ile Çalışma:

`epsilon` hesaplanır; bu, konturun yaklaşma algoritmasının hassasiyetini belirler. epsilon, konturun çevresinin (perimeter) %4'ü olarak ayarlanır.
`cv2.approxPolyDP` fonksiyonu, konturu daha az köşe ile yaklaşık bir çokgen haline getirir. Bu, şekli daha basitleştirmeye yardımcı olur.

Köşe Koordinatlarını Alma:

`approx` dizisinden ilk iki köşenin koordinatları alınır.
Eğer approx dizisinin uzunluğu 4 değilse, bu, nesnenin dört kenarlı olmadığını gösterir; bu yüzden o kontur işlenmez.

Köşe Mesafelerini Hesaplama:

`calculate_hypotenuse` fonksiyonu, her iki köşe arasındaki mesafeyi hesaplar. Bu mesafeler, nesnenin kenar uzunluklarını belirlemek için kullanılır.
distances listesi bu mesafeleri toplar ve çapraz uzunluklarla kıyaslama yapılır.

Şekil Analizi:

Eğer şeklin kenar uzunlukları arasındaki oran 0.75'ten küçükse, bu, şeklin kare veya dikdörtgen gibi belirgin bir şekil olduğunu gösterir ve köşe noktaları yeşil bir daire ile işaretlenir.

Kontur Alanı ve Merkez Hesaplama:

`cv2.contourArea` fonksiyonu ile konturun alanı hesaplanır. Eğer alan MIN_AREA değerinden büyükse, kontur büyük bir nesne olarak kabul edilir.
`cv2.moments ` fonksiyonu, konturun kütle merkezini hesaplar. Bu merkez, nesnenin ağırlık merkezi olarak kullanılır.

Köşe Noktaları ve Engellerin Saklanması:

`np.amax` ve  `np.amin ` fonksiyonları, nesnenin en alt sol ve en üst sağ köşelerini belirler.
`self.barriers` listesine, tespit edilen nesnenin ağırlık merkezi ve köşe noktaları eklenir.

**Parametreler:**
- `contours`: Bulunan konturlar.
- `img`: İşlenen görüntü.


