# TheHdw (العتاد) 🚧

> هذه المقالة متاحة باللغة الإنجليزية فقط.

**TheHdw** هو كائن للوصول إلى الخصائص والأساليب المتعلقة بعتاد نظام الاختبار.

## DCVI

```vbscript
TheHdw.DCVI
```

### Pins

```vbscript
TheHdw.DCVI.Pins(PinList)
```

---

## TheHdw.PPMU

🚧

## TheHdw.Digital

### ApplyLevelsTiming

لتحميل بيانات المستوى والتوقيت.

#### الاستخدام

```vbscript
TheHdw.Digital.ApplyLevelsTiming(ConnectAllPins, LoadLevels, LoadTiming, RelayMode, InitPinsHi, InitPinsLo, InitPinsHiZ, PinLevelsSheet, DCCategory, DCSelector, TimeSetSheet, ACCategory, ACSelector, EdgeSetSheet)
```

#### المعلمات

- **ConnectAllPins**: اختياري بوليان، الافتراضي هو `False`.
  - `True`: توصيل جميع دبابيس الجهاز.
  - `False`: عدم التوصيل.
- **LoadLevels**: اختياري بوليان، الافتراضي هو `False`.
  - `True`: تحميل قيم المستوى.
  - `False`: عدم التحميل.
- **LoadTiming**: اختياري بوليان، الافتراضي هو `False`.
  - `True`: تحميل قيم التوقيت.
  - `False`: عدم التحميل.
- **RelayMode**: اختياري `tlRelayMode`، الافتراضي هو `tlUnpowered`. يتحكم في التحويل الساخن للريليهات.
  - `tlPowered`: التحويل الساخن. عدم إيقاف تشغيل الجهاز قبل تعيين المستويات والتوصيل.
  - `tlUnpowered`: تجنب التحويل الساخن. إيقاف تشغيل الجهاز قبل تعيين المستويات والتوصيل.
- **InitPinsHi**: اختياري سلسلة. تعيين دبابيس البدء بحالة المشغل العالية.
- **InitPinsLo**: اختياري سلسلة. تعيين دبابيس البدء بحالة المشغل المنخفضة.
- **InitPinsHiZ**: اختياري سلسلة. تعيين دبابيس البدء بحالة مشغل المقاومة.
- **PinLevelsSheet**: اختياري سلسلة. ورقة مستويات دبوس.
- **DCCategory**: اختياري سلسلة.
- **DCSelector**: اختياري سلسلة.
- **TimeSetSheet**: اختياري سلسلة.
- **ACCategory**: اختياري سلسلة.
- **ACSelector**: اختياري سلسلة.
- **EdgeSetSheet**: اختياري سلسلة.

> تمت ترجمة هذه المشاركة باستخدام ChatGPT، يرجى [**تزويدنا بتعليقاتكم**](https://github.com/linyuxuanlin/Wiki_MkDocs/issues/new) إذا كانت هناك أي حذف أو إهمال.