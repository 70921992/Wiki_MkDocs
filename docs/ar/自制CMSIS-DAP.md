# صنع CMSIS-DAP 🚧

CMSIS DAP هو محاكي مفتوح المصدر تم إطلاقه من قبل ARM ، ويدعم جميع أجهزة Cortex-ARM ، ويدعم واجهات JTAG / SWD ، وفي أحدث إصدارات البرامج الثابتة ، يدعم واجهة SWO ذات الخط الواحد ، ويمكن إخراج البيانات المقابلة مباشرةً من البرنامج عبر واجهة SWO إلى نافذة التصحيح ، مما يؤدي إلى تحقيق أغراض مشابهة لتصحيح المنافذ التسلسلية. تتميز DAP بالميزات التالية:

1. مفتوح المصدر بالكامل ، لا يوجد قيود على الإصدار ، لذلك سيكون السعر المقابل مناسبًا جدًا
2. لا يوجد حاجة لتثبيت برامج التشغيل ، يمكن استخدامه عند الاتصال
3. تم دمج منفذ السلسلة في إصدارات DAP الجديدة ، بالإضافة إلى التنزيل والتصحيح ، يمكن استخدامه أيضًا كوحدة تحويل USB إلى سلسلة ، واستخدامها لأغراض متعددة
4. يمكن أن تلبي الأداء احتياجات المستخدم العامة

(غير مكتمل)

مستودع GitHub: [**linyuxuanlin/DashDAP**](https://github.com/linyuxuanlin/DashDAP)

## المراجع والشكر

- [x893/CMSIS-DAP](https://github.com/x893/CMSIS-DAP)
- [موقع ARM الرسمي لتقديم DAP](http://www.keil.com/pack/doc/cmsis/DAP/html/index.html)
- [حنين مهووس الإلكترونيات: محاكي CMSIS DAP](http://www.stmcu.org.cn/module/forum/thread-610968-1-2.html)
- [محاكي CMSIS DAP](https://item.taobao.com/item.htm?spm=a1z10.1-c.w5003-21405148310.36.78726a3dta5ieC&id=550828063764&scene=taobao_shop)
- [konosubakonoakua/Various_MCU_Debugger_DIY](https://github.com/konosubakonoakua/Various_MCU_Debugger_DIY)

---

`2.0 تحرير الإصدار`

![](https://wiki-media-1253965369.cos.ap-guangzhou.myqcloud.com/img/20200613154907.jpg)

معاينة المشروع عبر الإنترنت:

<div class="altium-iframe-viewer">
  <div
    class="altium-ecad-viewer"
    data-project-src="https://github.com/linyuxuanlin/DashDAP/raw/master/Hardware/DashDAP.zip"
  ></div>
</div>

## الخلفية

CMSIS-DAP / DAP-Link مقارنة بـ J-Link / ST-Link لديها المزايا التالية:

- مفتوح المصدر بالكامل ، لا يوجد خطر قانوني
- يدعم المنافذ السلسلية الافتراضية
- لا يوجد حاجة لتثبيت برامج التشغيل
- DAPLink هو CMSIS-DAP ، يدعم السحب والإسقاط للحرق / ترقية البرامج الثابتة

## الجزء العتادي

### وحدة المعالجة المركزية

#### المذبذب

تم اختيار مذبذب غير نشط من Murata بسرعة 8 ميجا هرتز ، والذي يحمل الرقم التسلسلي CSTCE8M00G53-R0 ، والذي يتم تغليفه في 3213 ، ويحتوي على سعة 15 بيكوفاراد. لماذا تم اختيار هذا؟ لأن حجمه صغير نسبيًا ، ويتم دمج مكثفي الاهتزاز فيه ، مما يوفر الكثير من الوقت في التصميم العتادي. يمكن الرجوع إلى الجدول التالي لمعرفة طريقة تسمية نماذج مذبذب Murata:

![](https://wiki-media-1253965369.cos.ap-guangzhou.myqcloud.com/img/20200612143451.jpg)

### الطاقة

### وحدات الوظائف

## الجزء البرمجي

### برامج التشغيل

لا يلزم تثبيت برامج التشغيل يدويًا في Win10 / MacOS / Linux ؛ يجب تثبيت برامج التشغيل يدويًا في Win8 والإصدارات الأقدم.

### السحب والإسقاط (MSC)

يمكن حرق الملفات `.hex` أو `.bin` المترجمة عن طريق سحبها مباشرةً إلى وحدة التخزين الافتراضية لـ DAPLink. إذا حدث خطأ ، سيتم تخزين معلومات الخطأ في `FAIL.txt`.

### المنافذ السلسلية الافتراضية (CDC)

يتمتع ميزة المنافذ السلسلية الافتراضية CDC بميزات المنافذ التسلسلية العادية ، وتسمح بالاتصال ثنائي الاتجاه ، وتسمح بإرسال أوامر مقاطعة لإعادة تعيين اللوحة الهدف.

## المراجع والشكر

- [JLink، STLink، DAPLink، CMSIS DAP استخدام الفروقات](https://blog.csdn.net/zhouml_msn/article/details/105298776)
- [تقنية جديدة · محاكي DAPLink](https://www.jixin.pro/bbs/topic/4187)
- [wuxx / nanoDAP](https://github.com/wuxx/nanoDAP)
- [LGG001 / DAPLink-Brochure](https://github.com/LGG001/DAPLink-Brochure)

> عنوان النص: <https://wiki-power.com/>  
> يتم حماية هذا المقال بموجب اتفاقية [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by/4.0/deed.zh)، يُرجى ذكر المصدر عند إعادة النشر.

> تمت ترجمة هذه المشاركة باستخدام ChatGPT، يرجى [**تزويدنا بتعليقاتكم**](https://github.com/linyuxuanlin/Wiki_MkDocs/issues/new) إذا كانت هناك أي حذف أو إهمال.