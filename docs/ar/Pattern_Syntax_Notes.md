# ملاحظات بناء الأنماط 🚧

> هذه المقالة متاحة باللغة الإنجليزية فقط.

يحتوي ملف نمط رقمي على ثلاثة أجزاء رئيسية:
**بيان الرأس**، **بيان الإعداد**، و **وحدة النمط**. (البيانات الأولية والتعليقات اختيارية).

فيما يلي مثال على ملف نمط يستخدم بشكل أساسي بتنسيق `.atp`:

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
 > tset1 0 X 0 0    .V 	  ; // capture
HALT
 > tset1 0 X 0 0  d000000 ; // end
}
```

## بيان الرأس

**بيان الرأس** يحتوي على البيانات التالية: الأداة الرقمية، خريطة الأسلاك، التحكم في المترجم، استيراد tset أو label. وفيما يلي مثال:

```
digital_inst = HSDMQ;           // بيان الأداة الرقمية
opcode_mode = single;           // بيان الترجمة
import tset tset1, tset1;       // استيراد مجموعات الوقت
import subr xxx;                // استيراد الدوال الفرعية
```

المعلمات المستخدمة بشكل متكرر:

- بيانات الأداة الرقمية
  - **digital_inst**: `hsdm`(HSD1000، UltraPin800)، `hsdmq`(UltraPin1600)، `hsdp`(UltraPin2200) ...
- مواصفات خريطة الأرجل:
  - **pinmap_workbook**: اسم دفتر IG-XL، مثل `"xxx.igxl"`
  - **sheetname**: اسم ورقة خريطة الأرجل، مثل `"pinmap"`
- بيانات التحكم في المترجم
  - **compressed**: `yes` أو `no`
  - **opcode_mode**: `single` أو `dual` أو `quad`(UltraPin1600)، يمكن أن تتضمن كل 1/2/4 متجهات أوبكود.
  - **save_comments**: `yes` أو `no`
  - **version**: مثل `V1.0`
- Tset و Label
  - **Tset**: `import tset tset1، tset2، ...؛`
  - **Label**: `import label label1، label2، ...؛`

## بيان الإعداد

يحتوي **بيان الإعداد** على إعداد الأرجل والأدوات والمسح الضوئي للأرجل.

```
pin_setup = {
    gpio_1    2x;                                           //إعداد الأرجل: gpio_1 تم تعيينها إلى وضع 2X
}
instruments = {
vcc:DCVS 1;                                                 // أداة DCVS
    tdo:DigCap 32:format=twos_complement:auto_trig_enable;  // أداة DigCap
}
scan_pins = {
    tdi، tdo؛                                               // tdi - المسح الضوئي في، tdo - المسح الضوئي خارج
}
```

المعلمات المستخدمة بشكل متكرر:

- أحرف حالة الدبابيس والأكواد الصغيرة
  - **أحرف حالة الدبابيس**: `0` (تنخفض الإشارة)، `1` (ترتفع الإشارة)، `2` (ترتفع الإشارة بجهد عالٍ فقط لـ UP800)، `L` (توقع إشارة منخفضة)، `H` (توقع إشارة مرتفعة)، `M` (توقع إشارة في منتصف النطاق)، `V` (توقع إشارة صالحة)، `X` (قناع)، `W` (نبضة نافذة)، `D` (تشغيل ADS (DigSrc/MTO))، `I` (تشغيل ADS العكسي (DigSrc/MTO))، `E` (توقع ADS (DigSrc/MTO))، `C` (توقع ADS العكسي (DigSrc/MTO))، `-` (تكرار الحالة السابقة).
  - **أكواد الأرقام الصغيرة**: `Trig` (بدء الالتقاط)، `Store` (تخزين عينة البيانات)، `Trig، Store` (مزيج من Trig و Store)، `Store، Inst_Cond_Strobe` (تخزين وبوابة إشارة `condition` المولدة داخليًا للعمل عليها).

## وحدة النمط

تحتوي **وحدة النمط** على قائمة دبابيس ومجموعة من النوافذ. هناك نوعان منها: ذاكرة النوافذ (VM) والذاكرة (SRM):

```
vm_vector [module-name] (pin-list)
{ vectors }

srm_vector [module-name] (pin-list)
{ vectors }
```

يتطلب ملف النمط وجود وحدة نمط واحدة على الأقل. إذا كان هناك أكثر من وحدة، فإن أعمدتها وقوائم دبابيسها يجب أن تكون متطابقة.

المعلمات المستخدمة بشكل متكرر:

- قائمة الدبابيس
  - **عناصر الدبابيس**: `pin-or-group[.modifier][:radix]`، يمكن أن يكون النظام العشري `:S` (رمزي، الافتراضي)، `:B` (ثنائي)، `:D` (عشري)، `:O` (ثماني)، `:H` (ست عشرية)
- التسمية: قيد الإعداد

> تمت ترجمة هذه المشاركة باستخدام ChatGPT، يرجى [**تزويدنا بتعليقاتكم**](https://github.com/linyuxuanlin/Wiki_MkDocs/issues/new) إذا كانت هناك أي حذف أو إهمال.