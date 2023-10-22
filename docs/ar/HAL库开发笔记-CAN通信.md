# مذكرات تطوير مكتبة HAL - اتصال CAN 🚧

هذا المقال مستند إلى مجموعة تطوير RobotCtrl المصممة ذاتياً، ولوحة الدارة المصغرة للنواة تحتوي على STM32F407ZET6، واستخدام اتصال CAN مع رقاقة TJA1050. للحصول على مخططات الدارة وشرح مفصل، يرجى الاطلاع على [**RobotCtrl - STM32 通用开发套件**](to_be_replace[3]).

## خطوات بسيطة لاختبار الحلقة المغلقة

### التكوين الداخلي باستخدام CubeMX

1. افتح صفحة `CAN1` أو `CAN2` من الجانب الأيسر وقم بتحديد خيار `Activated`. ثم، في صفحة المعلمات، قم بتكوين هذه المعلمات:
   1. اضبط `Prescaler (for Time Quantum)` على `6`، واضبط `Time Quanta in Bit Segment 1` و `Time Quanta in Bit Segment 2` على `3 Times`. هذا الجمع يقوم بضبط معدل البت على 1Mbps (الأعلى).
   2. ضبط `ReSynchronization Jump Width` على `1 Time`، وهذا هو الحد الأقصى الذي يمكن تعديله عند إعادة المزامنة.
   3. قم بضبط `Operating Mode` على `Loopback`، وهو يستخدم في اختبار الحلقة المغلقة.
2. في علامة تبويب `NVIC Settings`، قم بتمكين `CANx RX0 interrupts`.

### التكوين في الشيفرة

قم بإنشاء ملف `can.c` في مشروعك وقم بتكوين مرشحات الاتصال. في هذا المثال، تم تكوين وضع القائمة، وتم تصفية الهوية الممتدة `0x2233` والهوية القياسية `0`:

```c title="can.c"/*
 * اسم الدالة: CAN_Filter_Config
 * الوصف: تكوين مرشحات CAN
 * الإدخال: لا شيء
 * الإخراج: لا شيء
 * الاستدعاء: استدعاء داخلي
 */
static void CAN_Filter_Config(void) {
	CAN_FilterTypeDef CAN_FilterTypeDef;

	/* تهيئة مرشحات CAN */
	CAN_FilterTypeDef.FilterBank = 0;						// مجموعة المرشح 0
	CAN_FilterTypeDef.FilterMode = CAN_FILTERMODE_IDLIST;	// تعمل في وضع القائمة
	CAN_FilterTypeDef.FilterScale = CAN_FILTERSCALE_32BIT;	// عرض المرشح هو 32 بت فردي.
	/* تمكين المرشح، حيث يتم المقارنة وفقًا لمحتوى العلامات، وإذا لم يتم العثور على هذه العلامة، يتم التخلص منها، وإذا تم العثور على هذه العلامة، سيتم تخزينها في FIFO0. */

	CAN_FilterTypeDef.FilterIdHigh = ((((uint32_t) 0x2233 << 3) | CAN_ID_EXT
			| CAN_RTR_DATA) & 0xFFFF0000) >> 16;		// أعلى جزء للهوية التي يجب تصفيتها
	CAN_FilterTypeDef.FilterIdLow = (((uint32_t) 0x2233 << 3) | CAN_ID_EXT
			| CAN_RTR_DATA) & 0xFFFF; // الجزء السفلي للهوية المطلوب تصفيتها
	CAN_FilterTypeDef.FilterMaskIdHigh = 0;		// الجزء العلوي للهوية الثانية
	CAN_FilterTypeDef.FilterMaskIdLow = 0;			// الجزء السفلي للهوية الثانية
	CAN_FilterTypeDef.FilterFIFOAssignment = CAN_FILTER_FIFO0;	// تعيين المرشح إلى FIFO0
	CAN_FilterTypeDef.FilterActivation = ENABLE;			// تمكين المرشح
	HAL_CAN_ConfigFilter(&hcan1, &CAN_FilterTypeDef);
}
```

### الاختبار

افتح مدير الأجهزة للتحقق مما إذا كان الجهاز قد ظهر بالفعل. إذا لم تجد الجهاز أو إذا كان هناك علامة تعجب صفراء، يُرجى زيارة موقع ST وتنزيل مشغل [STM32 Virtual COM Port Driver](https://www.st.com/content/st_com/en/products/development-tools/software-development-tools/stm32-software-development-tools/stm32-utilities/stsw-stm32102.html).

إذا لم يتم التعرف على الجهاز بشكل صحيح حتى بعد تثبيت المشغل، يمكنك محاولة ضبط "Minimum Heap Size" في CubeMX - `Project Manager` - `Project` - `Linker Settings` إلى `0x600` أو قيمة أعلى.

بعد ذلك، قم بفتح أداة الشريط (بأي معدل نقل) وقم بإرسال أي حرف، وسوف تتلقى نفس الحرف كرد.

## مراجع وشكر

- [STM32CubeMX ومكتبة HAL - تعلم الاختبار البسيط للـ CAN](https://blog.csdn.net/weixin_45209978/article/details/119850600)

> عنوان النص: <https://wiki-power.com/>
> يتم حماية هذا المقال بموجب اتفاقية [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by/4.0/deed.zh)، يُرجى ذكر المصدر عند إعادة النشر.

> تمت ترجمة هذه المشاركة باستخدام ChatGPT، يرجى [**تزويدنا بتعليقاتكم**](https://github.com/linyuxuanlin/Wiki_MkDocs/issues/new) إذا كانت هناك أي حذف أو إهمال.