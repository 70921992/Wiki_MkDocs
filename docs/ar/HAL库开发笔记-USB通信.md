# ملاحظات تطوير مكتبة HAL - الاتصال بواسطة USB 🚧

يستند هذا المقال إلى مجموعة تطوير RobotCtrl الخاصة بنا ، ويتم تشغيل نواة الميكروكنترولر بواسطة STM32F407ZET6 ، ويتم توصيل دبوس USB_Slave بـ `PA11` و `PA12` ، يرجى الرجوع إلى المخطط الأساسي والمقدمة المفصلة في [**RobotCtrl - STM32 通用开发套件**](https://wiki-power.com/ar/RobotCtrl-STM32%E9%80%9A%E7%94%A8%E5%BC%80%E5%8F%91%E5%A5%97%E4%BB%B6) .

## خطوات بسيطة للاختبار الدائري

### التكوين الداخلي لـ CubeMX

1. تكوين المؤقت الخارجي (HSE).
2. تكوين شجرة الساعة ، وتأكد من أن نهاية شجرة الساعة "48MHz Clocks (MHz)" هي 48 ميجاهرتز.
3. في صفحة `USB_OTG_FS` ، قم بتكوين `Mode` كـ `Device_Only` ، والدبابيس الافتراضية هي `PA11` و `PA12`.
4. في صفحة `USB_DEVICE` ، قم بتكوين `Class For FS IP` كـ `Commmunication Device Class (Virtual Port Com)`.

### التكوين الداخلي للكود

لتنفيذ وظيفة الدائرة الراجعة للبيانات ، ما عليك سوى إضافة سطر واحد في دالة `CDC_Receive_FS` في ملف `usbd_cdc_if.c`:

```c title="usbd_cdc_if.c"
CDC_Transmit_FS(Buf,*Len); // إرجاع نفس البيانات
```

### الاختبار

افتح مدير الأجهزة للتحقق مما إذا كان الجهاز قد تم عرضه ، إذا لم يتم العثور على الجهاز أو كان هناك علامة تعجب صفراء ، يرجى تنزيل برنامج التشغيل من موقع ST [**STM32 Virtual COM Port Driver**](https://www.st.com/content/st_com/en/products/development-tools/software-development-tools/stm32-software-development-tools/stm32-utilities/stsw-stm32102.html) .

إذا لم يتم التعرف على الجهاز بشكل صحيح بعد تثبيت التعريفات ، فيمكنك محاولة زيارة CubeMX - `Project Manager` - `Project` - `Linker Settings` ، وتعديل `Minimum Heap Size` إلى `0x600` أو أعلى.

افتح أداة المنفذ التسلسلي (أي معدل بت يعمل) ، وسوف تلاحظ أنه عند إرسال أي حرف ، سيتم إرجاع نفس الحرف.

## المراجع والشكر

- [استخدام STM32 CubeMX HAL لإنشاء مشروع USBVCP Virtual Serial Port بسرعة](https://blog.csdn.net/yxy244/article/details/102620249)

> عنوان النص: <https://wiki-power.com/>  
> يتم حماية هذا المقال بموجب اتفاقية [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by/4.0/deed.zh)، يُرجى ذكر المصدر عند إعادة النشر.

> تمت ترجمة هذه المشاركة باستخدام ChatGPT، يرجى [**تزويدنا بتعليقاتكم**](https://github.com/linyuxuanlin/Wiki_MkDocs/issues/new) إذا كانت هناك أي حذف أو إهمال.