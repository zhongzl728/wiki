# TheHdw (الأجهزة) 🚧

**TheHdw** هو كائن للوصول إلى الخصائص والأساليب المتعلقة بأجهزة نظام الاختبار.

## DCVI

```vbscript
TheHdw.DCVI
```

### المنافذ (البنون)

```vbscript
TheHdw.DCVI.Pins(PinList)
```

---

## TheHdw.PPMU

🚧

## TheHdw.Digital

### تطبيق مستويات وتوقيت

لتحميل البيانات المتعلقة بالمستوى والتوقيت.

#### الاستخدام

```vbscript
TheHdw.Digital.ApplyLevelsTiming(ConnectAllPins, LoadLevels, LoadTiming, RelayMode, InitPinsHi, InitPinsLo, InitPinsHiZ, PinLevelsSheet, DCCategory, DCSelector, TimeSetSheet, ACCategory, ACSelector, EdgeSetSheet)
```

#### المعلمات

- **ConnectAllPins**: اختياري، القيمة الافتراضية هي `False`.
  - `True`: قم بتوصيل جميع منافذ الجهاز.
  - `False`: لا تقم بالتوصيل.
- **LoadLevels**: اختياري، القيمة الافتراضية هي `False`.
  - `True`: قم بتحميل قيم المستوى.
  - `False`: لا تقم بالتحميل.
- **LoadTiming**: اختياري، القيمة الافتراضية هي `False`.
  - `True`: قم بتحميل قيم التوقيت.
  - `False`: لا تقم بالتحميل.
- **RelayMode**: اختياري، `tlRelayMode` القيمة الافتراضية هي `tlUnpowered`. يتحكم في التبديل الساخن للريليهات.
  - `tlPowered`: التبديل الساخن. لا تطفئ الجهاز قبل تعيين المستويات والتوصيل.
  - `tlUnpowered`: تجنب التبديل الساخن. قم بإطفاء الجهاز قبل تعيين المستويات والتوصيل.
- **InitPinsHi**: اختياري، سلسلة نصية. قم بتعيين المنافذ ببداية حالة المشغل العالية.
- **InitPinsLo**: اختياري، سلسلة نصية. قم بتعيين المنافذ ببداية حالة المشغل المنخفضة.
- **InitPinsHiZ**: اختياري، سلسلة نصية. قم بتعيين المنافذ ببداية حالة المشغل عالية المقاومة.
- **PinLevelsSheet**: اختياري، سلسلة نصية. ورقة مستويات المنفذ.
- **DCCategory**: اختياري، سلسلة نصية.
- **DCSelector**: اختياري، سلسلة نصية.
- **TimeSetSheet**: اختياري، سلسلة نصية.
- **ACCategory**: اختياري، سلسلة نصية.
- **ACSelector**: اختياري، سلسلة نصية.
- **EdgeSetSheet**: اختياري، سلسلة نصية.

> تمت ترجمة هذه المشاركة باستخدام ChatGPT، يرجى [**تزويدنا بتعليقاتكم**](https://github.com/linyuxuanlin/Wiki_MkDocs/issues/new) إذا كانت هناك أي حذف أو إهمال.