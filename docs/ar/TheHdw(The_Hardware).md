# الهاردوير (The Hardware) 🚧

**الهاردوير (The Hardware)** هو كائن للوصول إلى الخصائص والأساليب المتعلقة بأجهزة نظام الاختبار.

## DCVI

```vbscript
TheHdw.DCVI
```

### المنافذ

```vbscript
TheHdw.DCVI.Pins(PinList)
```

---

## TheHdw.PPMU

🚧

## TheHdw.Digital

### تطبيق مستويات وتوقيت

لتحميل بيانات المستوى والتوقيت.

#### الاستخدام

```vbscript
TheHdw.Digital.ApplyLevelsTiming(ConnectAllPins, LoadLevels, LoadTiming, RelayMode, InitPinsHi, InitPinsLo, InitPinsHiZ, PinLevelsSheet, DCCategory, DCSelector, TimeSetSheet, ACCategory, ACSelector, EdgeSetSheet)
```

#### المعلمات

- **ConnectAllPins**: بولياني اختياري، الافتراضي `False`.
  - `True`: قم بتوصيل جميع أرجل الجهاز.
  - `False`: لا تقم بالاتصال.
- **LoadLevels**: بولياني اختياري، الافتراضي `False`.
  - `True`: حمل قيم المستوى.
  - `False`: لا تحمل.
- **LoadTiming**: بولياني اختياري، الافتراضي `False`.
  - `True`: حمل قيم التوقيت.
  - `False`: لا تحمل.
- **RelayMode**: اختياري `tlRelayMode`، الافتراضي `tlUnpowered`. يتحكم في التبديل الساخن للريلاي.
  - `tlPowered`: التبديل الساخن. لا تطفئ الجهاز قبل ضبط المستويات والاتصال.
  - `tlUnpowered`: تجنب التبديل الساخن. اطفئ الجهاز قبل ضبط المستويات والاتصال.
- **InitPinsHi**: نص اختياري. قم بتعيين بداية الأرجل بحالة الدرايفر العالي.
- **InitPinsLo**: نص اختياري. قم بتعيين بداية الأرجل بحالة الدرايفر المنخفض.
- **InitPinsHiZ**: نص اختياري. قم بتعيين بداية الأرجل بحالة الدرايفر مع المقاومة.
- **PinLevelsSheet**: نص اختياري. ورقة مستويات الأرجل.
- **DCCategory**: نص اختياري.
- **DCSelector**: نص اختياري.
- **TimeSetSheet**: نص اختياري.
- **ACCategory**: نص اختياري.
- **ACSelector**: نص اختياري.
- **EdgeSetSheet**: نص اختياري.

> تمت ترجمة هذه المشاركة باستخدام ChatGPT، يرجى [**تزويدنا بتعليقاتكم**](https://github.com/linyuxuanlin/Wiki_MkDocs/issues/new) إذا كانت هناك أي حذف أو إهمال.