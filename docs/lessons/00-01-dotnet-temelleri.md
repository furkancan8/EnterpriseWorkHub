# Ders 1 — .NET, Solution ve Project Temelleri

## Dersin hedefi

Bu dersin sonunda:

- SDK ile runtime arasındaki farkı açıklayabilmelisin.
- Solution ile project arasındaki ilişkiyi bilmelisin.
- Bir C# dosyasının çalışana kadar geçtiği yolu ana hatlarıyla anlatabilmelisin.
- Projeyi terminalden `restore`, `build`, `run` ve `test` edebilmelisin.
- `global.json`, `.sln`, `.csproj` ve `Program.cs` dosyalarının görevini bilmelisin.

## 1. .NET nedir?

.NET; C# gibi dillerle uygulama geliştirmeyi sağlayan çalışma platformudur.
Tek bir program değildir. Derleyici, standart kütüphaneler, çalışma zamanı ve
geliştirme araçlarından oluşan bir ekosistemdir.

Basitleştirilmiş çalışma akışı şöyledir:

```text
Program.cs içindeki C# kodu
        ↓
C# derleyicisi
        ↓
IL (Intermediate Language) + metadata içeren .dll
        ↓
.NET Runtime / CLR
        ↓
JIT derleyici ile makine kodu
        ↓
İşletim sistemi ve işlemci üzerinde çalışan uygulama
```

C# kodu çoğu durumda doğrudan işlemcinin anlayacağı makine koduna çevrilmez.
Önce ara dil olan IL'e derlenir. Uygulama çalışırken CLR; bellek yönetimi,
exception sistemi, garbage collection ve JIT derleme gibi görevleri yürütür.

## 2. SDK ve runtime farkı

### Runtime

Daha önce derlenmiş bir .NET uygulamasını **çalıştırmak** için gerekir. Sunucuda
yalnızca uygulamayı çalıştıracaksak bazı dağıtım modellerinde runtime yeterlidir.

### SDK

Uygulama **geliştirmek** için gerekir. SDK içinde derleyici ve `dotnet` komutları
gibi geliştirme araçları bulunur. Ayrıca ilgili runtime bileşenlerini de içerir.

Kısa kural:

```text
Sadece çalıştırma → Runtime yeterli olabilir
Kod yazma/derleme/test → SDK gerekir
```

Bu bilgisayara proje için .NET SDK `10.0.401` kuruldu.

Kurulu SDK'ları görmek için:

```powershell
dotnet --list-sdks
```

Seçili SDK'yı görmek için:

```powershell
dotnet --version
```

## 3. Solution ve project nedir?

### Project (`.csproj`)

Derlenebilir bir uygulama veya kütüphanedir. Hedef .NET sürümü, NuGet paketleri,
diğer proje referansları ve derleme ayarları burada tutulur.

Örneğin:

- `EnterpriseWorkHub.Api.csproj`: Çalışan Web API uygulaması
- `EnterpriseWorkHub.Playground.csproj`: C# alıştırma uygulaması
- `EnterpriseWorkHub.Api.Tests.csproj`: Otomatik test projesi

Her project ayrı bir çıktıya dönüşebilir.

### Solution (`.sln`)

Birbiriyle ilişkili project'leri aynı çatı altında gruplar. Solution'ın kendisi
uygulama değildir ve iş kuralı içermez. IDE'nin ve CLI'ın birden fazla projeyi
birlikte açmasını, derlemesini ve yönetmesini kolaylaştırır.

Benzetme:

```text
Solution = Bir apartman
Project  = Apartmandaki bağımsız daireler
Dosyalar = Dairelerin içindeki eşyalar
```

## 4. Oluşturduğumuz klasör yapısı

```text
EnterpriseWorkHub/
├── EnterpriseWorkHub.sln
├── global.json
├── docs/
│   └── lessons/
├── exercises/
│   └── EnterpriseWorkHub.Playground/
├── src/
│   └── EnterpriseWorkHub.Api/
└── tests/
    └── EnterpriseWorkHub.Api.Tests/
```

- `src`: Ürünün çalışan kaynak kodu
- `tests`: Otomatik test projeleri
- `exercises`: Ana ürünü bozmadan yapacağımız küçük alıştırmalar
- `docs`: Dersler ve teknik kararlar

Bu ayrım büyüyen projede dosyaların amaçlarını anlaşılır tutar.

## 5. Önemli dosyalar

### `global.json`

Makinede birden fazla SDK kuruluysa projenin hangi SDK ailesini kullanacağını
belirler. Böylece ekipte bir kişinin farklı SDK ile beklenmedik sonuç üretme
olasılığı azalır.

### `.sln`

Solution'a bağlı projelerin listesini ve solution düzeyindeki yapılandırmayı
tutar.

### `.csproj`

XML biçimindedir. Örneğin `<TargetFramework>net10.0</TargetFramework>`, projenin
.NET 10'u hedeflediğini söyler.

### `Program.cs`

Uygulamanın başlangıç noktasıdır. Modern C#'ta `Main` metodu açıkça yazılmadan
"top-level statements" kullanılabilir. Console projesindeki şu satır geçerli bir
uygulamadır:

```csharp
Console.WriteLine("Hello, World!");
```

Derleyici bu kod için gerekli giriş metodunu bizim adımıza üretir.

## 6. Temel CLI komutları

Komutları `EnterpriseWorkHub` klasöründeyken çalıştır:

```powershell
dotnet restore
dotnet build
dotnet test
```

- `restore`: `.csproj` dosyalarında belirtilen NuGet bağımlılıklarını çözer.
- `build`: Kaynak kodu derler. Gerekiyorsa önce restore da yapar.
- `test`: Test projelerini derler ve testleri çalıştırır.

Console alıştırma projesini çalıştırmak için:

```powershell
dotnet run --project exercises/EnterpriseWorkHub.Playground
```

API'yi çalıştırmak için:

```powershell
dotnet run --project src/EnterpriseWorkHub.Api
```

API çalışırken terminal meşgul görünür; çünkü sunucu istek beklemektedir.
`Ctrl+C` ile güvenli biçimde durdurabilirsin.

## 7. Neden üç proje oluşturduk?

`Playground`, yeni C# konularını hızlı ve risksiz denemek içindir. `Api`, gerçek
üründür. `Api.Tests` ise uygulamanın beklenen davranışını otomatik olarak kontrol
eder. Test kodunu ürün koduna karıştırmamak, bağımlılıkları ve sorumlulukları
ayırmamızı sağlar.

Test projesinden API projesine bir `ProjectReference` eklendi. Bağımlılık yönü:

```text
EnterpriseWorkHub.Api.Tests ───→ EnterpriseWorkHub.Api
```

Yani test projesi API'yi tanır; API projesi test projesini tanımaz.

## 8. Senin uygulaman

1. Terminalde proje klasörüne git.
2. `dotnet --version` çalıştır ve sonucu kontrol et.
3. `dotnet build` çalıştır.
4. Playground projesini `dotnet run --project exercises/EnterpriseWorkHub.Playground`
   komutuyla çalıştır.
5. `exercises/EnterpriseWorkHub.Playground/Program.cs` içindeki mesajı aşağıdaki
   gibi değiştir:

```csharp
Console.WriteLine("EnterpriseWorkHub öğrenme yolculuğu başladı!");
```

6. Tekrar çalıştır ve değişen çıktıyı gözlemle.
7. `dotnet test` çalıştır.

Buradaki amaç zor kod yazmak değil; edit → build → run → test döngüsünü bilinçli
şekilde görmek.

## 9. Kontrol soruları

1. Bir sunucuda yalnızca derlenmiş uygulamayı çalıştıracaksak SDK her zaman şart mı?
2. `.sln` dosyası ile `.csproj` dosyasının temel farkı nedir?
3. `dotnet build` ile `dotnet run` arasındaki fark nedir?
4. Test projesi neden API projesine referans verir; tersi neden istenmez?
5. `global.json` ekip çalışmasında hangi problemi azaltır?

Bu sorulara kendi cümlelerinle cevap vermen, sonraki derse geçmek için bu dersteki
en değerli alıştırmadır.

