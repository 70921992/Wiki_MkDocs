# ملاحظات تطوير مكتبة HAL - الاتصال بـ CAN 🚧

يستند هذا المقال إلى مجموعة تطوير RobotCtrl الخاصة بنا، ويستخدم نواة الميكروكنترولر STM32F407ZET6، ويستخدم رقاقة TJA1050 للاتصال بـ CAN. يرجى الرجوع إلى المخطط الأساسي والشرح المفصل في [**RobotCtrl - STM32 通用开发套件**](https://wiki-power.com/ar/RobotCtrl-STM32%E9%80%9A%E7%94%A8%E5%BC%80%E5%8F%91%E5%A5%97%E4%BB%B6) لمزيد من المعلومات.

## خطوات بسيطة لاختبار الحلقة الراجعة

### التكوين الداخلي في CubeMX

1. استنادًا إلى الأجهزة المستخدمة لـ CAN ، انقر فوق صفحة `CAN1` أو `CAN2` في الشريط الجانبي الأيسر ، وحدد `Activated` ، وفي صفحة المعلمات ، قم بتكوين هذه المعلمات:
   1. قم بتعيين `Prescaler (for Time Quantum)` إلى `6` ، وقم بتعيين `Time Quanta in Bit Segment 1` و `Time Quanta in Bit Segment 2` إلى `3 Times` ، وهذا التركيب يعيد معدل البت إلى 1 ميجابت في الثانية (الأعلى).
   2. قم بتكوين `ReSynchronization Jump Width` إلى `1 Time` ، وهذا هو الحد الأقصى الذي يمكن تعديله عند إعادة المزامنة.
   3. قم بتكوين `Operating Mode` إلى `Loopback` ، وهو ما يستخدم للاختبار الدائري.
2. في علامة التبويب `NVIC Settings` ، قم بتمكين `CANx RX0 interrupts`.

### التكوين الداخلي في الكود

أنشئ `can.c` في المشروع ، وقم بتكوين المصفيات ، وهنا تم تكوين وضع القائمة ، وتم تصفية معرف التوسعة `0x2233` ومعرف القياسي `0`:

```c title="can.c"/*
 * 函数名：CAN_Filter_Config
 * 描述  ：CAN的过滤器 配置
 * 输入  ：无
 * 输出  : 无
 * 调用  ：内部调用
 */
static void CAN_Filter_Config(void) {
	CAN_FilterTypeDef CAN_FilterTypeDef;

	/*CAN筛选器初始化*/
	CAN_FilterTypeDef.FilterBank = 0;						//筛选器组0
	CAN_FilterTypeDef.FilterMode = CAN_FILTERMODE_IDLIST;	//工作在列表模式
	CAN_FilterTypeDef.FilterScale = CAN_FILTERSCALE_32BIT;	//筛选器位宽为单个32位。
	/* 使能筛选器，按照标志的内容进行比对筛选，扩展ID不是如下的就抛弃掉，是的话，会存入FIFO0。 */

	CAN_FilterTypeDef.FilterIdHigh = ((((uint32_t) 0x2233 << 3) | CAN_ID_EXT
			| CAN_RTR_DATA) & 0xFFFF0000) >> 16;		//要筛选的ID高位
	CAN_FilterTypeDef.FilterIdLow = (((uint32_t) 0x2233 << 3) | CAN_ID_EXT
			| CAN_RTR_DATA) & 0xFFFF; //要筛选的ID低位
	CAN_FilterTypeDef.FilterMaskIdHigh = 0;		//第二个ID的高位
	CAN_FilterTypeDef.FilterMaskIdLow = 0;			//第二个ID的低位
	CAN_FilterTypeDef.FilterFIFOAssignment = CAN_FILTER_FIFO0;	//筛选器被关联到FIFO0
	CAN_FilterTypeDef.FilterActivation = ENABLE;			//使能筛选器
	HAL_CAN_ConfigFilter(&hcan1, &CAN_FilterTypeDef);
}
```

### الاختبار



فتح مدير الأجهزة للتحقق مما إذا كان الجهاز قد ظهر أم لا. إذا لم يتم العثور على الجهاز أو إذا كان هناك علامة تعجب صفراء، يرجى تنزيل برنامج التشغيل [STM32 Virtual COM Port Driver](https://www.st.com/content/st_com/en/products/development-tools/software-development-tools/stm32-software-development-tools/stm32-utilities/stsw-stm32102.html) من موقع ST الرسمي.

إذا لم يتم التعرف على الجهاز بشكل صحيح بعد تثبيت برنامج التشغيل، يمكنك محاولة ضبط "Minimum Heap Size" إلى "0x600" أو أعلى في CubeMX - Project Manager - Project - Linker Settings.

بعد فتح أداة المنفذ التسلسلي (أي معدل بتات)، يمكنك إرسال أي حرف وستعود نفس الحرف.

المراجع والشكر:

- [STM32CubeMX ومكتبة HAL - اختبار دورة CAN البسيطة](https://blog.csdn.net/weixin_45209978/article/details/119850600)

> تمت ترجمة هذه المشاركة باستخدام ChatGPT، يرجى [**تزويدنا بتعليقاتكم**](https://github.com/linyuxuanlin/Wiki_MkDocs/issues/new) إذا كانت هناك أي حذف أو إهمال.