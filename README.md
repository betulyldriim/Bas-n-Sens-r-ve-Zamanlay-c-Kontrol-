Bu proje, bir motoru, bir valfi ve bazı sensörleri kontrol etmek için tasarlanmış bir sıralı (sequential) otomasyon uygulamasıdır. Program, temel lojik kapılar (AND, OR), set/reset lojiği ve zamanlayıcılar kullanarak belirli koşullara bağlı olarak çeşitli çıktıları (motor, valf) aktive eder. Proje, endüstriyel makinelerde yaygın olarak kullanılan basit kontrol senaryolarını temsil etmektedir.

Çalışma Mantığı
Proje, üç ana mantıksal ağdan (Network) oluşmaktadır. Her bir ağ, belirli bir kontrol senaryosunu gerçekleştirmek için tasarlanmıştır.

1. Ağ (Motor Kontrolü)
Bu ağ, bir motorun başlatılması ve durdurulması için bir kontrol lojiği içerir.

Başlatma Koşulu: Motor, iki koşulun aynı anda sağlanmasıyla başlatılır.

%I0.0 (Start): Başlatma butonu veya sinyali aktif olduğunda.

%I0.1 (Micro Switch 1): Birinci mikro anahtar aktif olduğunda.

%I0.2 (Micro Switch 2): İkinci mikro anahtar aktif olduğunda.

Motorun Devreye Alınması (Set): Yukarıdaki üç koşulun hepsi doğru olduğunda, SR (Set-Reset) lojik bloğunun S girişi aktif olur ve %Q4.0 (Motor) çıkışı "Set" edilerek motorun çalışmasını sağlar. Bu, motorun, başlangıç koşulları artık aktif olmasa bile çalışmaya devam edeceği anlamına gelir.

Durdurma Koşulu: Motorun durdurulması için bir sensör veya durdurma butonu kullanılır.

%I0.3 (Sensor): Bir sensör aktif olduğunda.

%I0.4 (Dur): Bir durdurma butonu veya sinyali aktif olduğunda.

Motorun Devreden Çıkarılması (Reset): Bu koşullardan herhangi biri doğru olduğunda, SR bloğunun R1 girişi aktif olur ve %Q4.0 (Motor) çıkışı "Reset" edilerek motorun durmasını sağlar.

2. Ağ (Birinci Zamanlayıcı Kontrolü)
Bu ağ, birinci ağdaki motor kontrolüyle ilişkili bir zamanlayıcı işlevi görür.

Zamanlayıcının Tetiklenmesi:

%I0.3 (Sensor): Bir sensör aktif olduğunda.

%Q4.0 (Motor): Motor çalışırken.

Bu iki koşul, bir kenar tetiklemesi (P_TRIG) ile TONR (On-Delay Retentive Timer) bloğunun CLK girişini tetikler.

Zamanlayıcı İşlevi:

TONR bloğu, %I0.3 ve %Q4.0 koşulları doğru olduğu sürece çalışır.

PT (Preset Time) girişi, zamanlayıcının ne kadar süre sayacağını belirler. Bu değer, 20 milisaniye (T#20ms) olarak ayarlanmıştır.

Zamanlayıcı süresini tamamladığında, Q çıkışı aktif olur.

Çıkışlar: Zamanlayıcının Q çıkışı, %Q4.1 (Jack 1) ve %Q4.2 (Jack 2) adlı iki çıkışı aktif eder. Bu çıkışlar, muhtemelen bir hidrolik silindir veya başka bir aktüatörün kontrolü için kullanılmaktadır.

3. Ağ (İkinci Zamanlayıcı Kontrolü ve Valf)
Bu ağ, ikinci bir zamanlayıcı kullanarak bir valfi kontrol eder. Bu ağın lojiği, önceki ağların çıktısına bağımlıdır.

Zamanlayıcının Tetiklenmesi:

%Q4.1 (Jack 1): Birinci ağın çıktıları olan Jack 1 ve Jack 2 aktif olduğunda.

%Q4.2 (Jack 2): Birinci ağın çıktıları olan Jack 1 ve Jack 2 aktif olduğunda.

Zamanlayıcı İşlevi:

TONR bloğu, %Q4.1 ve %Q4.2 koşulları doğru olduğu sürece çalışır.

PT (Preset Time) girişi, zamanlayıcının süresini belirler, bu değer 1 saniye (T#1s) olarak ayarlanmıştır.

Zamanlayıcı süresini tamamladığında, Q çıkışı aktif olur.

Çıkış: Zamanlayıcının Q çıkışı, %Q4.3 (Valve) çıkışını aktif eder. Bu, belirli bir süre geçtikten sonra bir valfin açılmasını veya kapanmasını sağlayabilir.
