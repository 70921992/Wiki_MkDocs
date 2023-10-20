Here is the translated text in Arabic while maintaining the original markdown format:

# ملاحظات بنية النمط 🚧

ملف النمط الرقمي يحتوي أساسًا على 3 أجزاء:
**بيان الرأس**, **بيان الإعداد** و **وحدة النمط**. (البيانات القبلية **والتعليقات** اختيارية).

فيما يلي مثال على ملف نمط يُستخدم بشكل شائع في تنسيق `.atp`:

```atp
// example.atp
digital_inst = HSDMQ;
opcode_mode = single;
pinmap_workbook = "..\xx.igxl";
import tset tset1 ;
instruments = {
        (TIC_DATABUS):DigCap 32:format=twos_complement:auto_trig_enable;
}
vm_vector cpr_test($tset TIC_CLK, TIC_ACK, TIC_REQ_A, TIC_DATABUS)
{
cpr_test:
 > tset1 0 X 0 0 .d000000 ;
repeat 100
 > tset1 0 X 1 1 .rFFFFFF ;
 > tset1 0 X 0 0    .X    ;
 ((TIC_DATABUS):DigCap = Store)
 > tset1 0 X 0 0    .V 	  ; // الالتقاط
توقف
 > tset1 0 X 0 0  d000000 ; // نهاية
}
```

## بيان الرأس

**بيان الرأس** يحتوي على هذه البيانات: أداة رقمية، خريطة الأوصاف، التحكم في المترجم، استيراد تعيين الوقت أو التسمية. فيما يلي مثال:

```
digital_inst = HSDMQ;           // بيان أداة رقمية
opcode_mode = single;           // بيان تجميع
import tset tset1, tset1;       // استيراد مجموعات الزمن
import subr xxx;                // استيراد الإجراءات الفرعية
```

المعلمات المستخدمة بشكل متكرر:

---

Please note that Arabic text is written from right to left, and this translation maintains the original Markdown format while translating the content into Arabic.

Here is the provided text translated into Arabic while maintaining the original markdown format:

- تصريحات الأداة الرقمية
  - **digital_inst**: `hsdm`(HSD1000, UltraPin800), `hsdmq`(UltraPin1600), `hsdp`(UltraPin2200) ...
- مواصفات الخريطة دباسة:
  - **pinmap_workbook**: اسم دفتر IG-XL، مثل `"xxx.igxl"`
  - **sheetname**: اسم ورقة دباسة الأوصال، مثل `"pinmap"`
- تصريحات التحكم في المترجم
  - **compressed**: `نعم` أو `لا`
  - **opcode_mode**: `واحد` أو `زوجي` أو `رباعي`(UltraPin1600)، يمكن أن تحتوي كل 1/2/4 متجهات على مبرر.
  - **save_comments**: `نعم` أو `لا`
  - **version**: مثل `V1.0`
- Tset وLabel
  - **Tset**: `استيراد tset tset1، tset2، ... ;`
  - **Label**: `استيراد label label1، label2، ... ;`

## تعيين التصريح

**تعيين التصريح** يحتوي على إعداد الأوصال، الأدوات، وأوصال المسح.

```
pin_setup = {
    gpio_1    2x;                                           // إعداد الدبوس: تعيين gpio_1 إلى وضع 2X
}
instruments = {
vcc:DCVS 1;                                                 // أداة DCVS
    tdo:DigCap 32:format=twos_complement:auto_trig_enable;  // أداة DigCap
}
scan_pins = {
    tdi, tdo;                                               // tdi - الأوصال للمسح الداخلي، tdo - الأوصال للمسح الخارجي
}
```

المعلمات المستخدمة بشكل متكرر:


Here is the translated text in Arabic, maintaining the original markdown format:

- الأحرف والأكواد الصغيرة لحالة المساجم
  - **الأحرف لحالة المساجم**: `0` (تشغيل منخفض), `1` (تشغيل مرتفع), `2` (تشغيل جهد عالي فقط لـ UP800), `L` (توقع منخفض), `H` (توقع مرتفع), `M` (توقع منتصف النطاق), `V` (توقع صالح), `X` (قناع), `W` (اضرب نافذة), `D` (تشغيل ADS (DigSrc/MTO)), `I` (تشغيل ADS عكسي (DigSrc/MTO)), `E` (توقع ADS (DigSrc/MTO)), `C` (توقع ADS عكسي (DigSrc/MTO)), `-` (كرر الحالة السابقة).
  - **الأكواد الصغيرة للقابس الرقمي**: `Trig` (ابدأ الالتقاط), `Store` (قم بتخزين عينة من البيانات), `Trig, Store` (مزيج من الابدأ والتخزين), `Store, Inst_Cond_Strobe` (قم بتخزين وبوابة الإشارة `condition` المُولدة داخليًا للعمل عليها).

## وحدة النمط

تحتوي **وحدة النمط** على قائمة للأوصاف ومجموعة من النوافذ. هناك نوعان منها: ذاكرة النوافذ (VM) والذاكرة (SRM):

```
vm_vector [اسم-الوحدة] (قائمة-الأوصاف)
{ نوافذ }

srm_vector [اسم-الوحدة] (قائمة-الأوصاف)
{ نوافذ }
```

يجب أن تحتوي ملفات النمط على وحدة نمط واحدة على الأقل. إذا كان هناك أكثر من وحدة واحدة، فإن عموديهما وقوائم الأوصاف يجب أن تكون متطابقة.

المعلمات المستخدمة بشكل متكرر:

- قائمة-الأوصاف
  - **عناصر الأوصاف**: `الوصف-أو-المجموعة[.المعدل][:النظام]`، النظام يمكن أن يكون `:S` (رمزي، الافتراضي)، `:B` (ثنائي)، `:D` (عشري)، `:O` (ثماني)، `:H` (ست عشري)
- العلامة: لاحقًا

> تمت ترجمة هذه المشاركة باستخدام ChatGPT، يرجى [**تزويدنا بتعليقاتكم**](https://github.com/linyuxuanlin/Wiki_MkDocs/issues/new) إذا كانت هناك أي حذف أو إهمال.