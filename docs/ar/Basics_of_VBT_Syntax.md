```markdown
# أساسيات بنية VBT

## كائنات البيانات

### TheHdw و TheExec

هناك مقابض عالمية اثنتان في واجهة VBT، لتشغيل أجهزة الاختبار:

- **TheHdw (العتاد)**: الدعم للوصول والتحكم في الأجهزة وتضمين وظائف عامة أخرى للعتاد، مثل الإنذارات.
- **TheExec (التنفيذي)**: للتحكم في وظائف برنامج الاختبار الشاملة، مثل تنفيذ الاختبار ومعالجة نتائج الاختبار وتسجيل البيانات.

أدناه أمثلة على استخدامهم:

```vbscript
' تعيين النطاق الحالي للمنفذ p0
TheHdw.DCVI.Pins("p0").CurrentRange = 0.002
```

```vbscript
' الحصول على مسار ملف STDF الإخراج الحالي
CurrStdfFile = TheExec.Datalog.Setup.STDFOutputFile
```

### كائنات البيانات الأخرى

تشمل واجهة VBT مزيدًا من المقابض العالمية، مثل **PinListData**، **DSPWave**، **RtaDataObj (كائن بيانات تعديل الوقت التنفيذي)** وما إلى ذلك. سنستمر في استكشافها في المقالات المستقبلية.

## الوصول بواسطة الأداة أو الدبوس

بنية VBT تدعم وصول العتاد للفاحص بواسطة **الأداة** أو **الدبوس**، وهما متكافئان في النتيجة. فيما يلي أمثلة على استخدامهم:

```vbscript
' الوصول بواسطة الأداة، ينطبق أداة واحدة على دبابيس مختلفة
With TheHdw.instrument
    .Pins("Vcc").CurrentLimit = 0.75
    .Pins("Vee").ForceValue = 3.2
End With
```

```vbscript
' الوصول بواسطة الدبوس، يحدد قائمة الدبابيس ثم يستخدم أدوات مختلفة
With TheHdw.Pins("Vcc,Vdd,Vee")
    .instrument1.Disconnect
    .instrument2.CurrentLimit = 0.75
End With
```

## بنية رمز VBT

يجب أن يكون اسم ملف رمز VBT مسمى `VBT_xxx`، ويجب أن يكون الاسم فريدًا.

يُتوقع أن يكون **قيمة العودة** لوظيفة VBT تساوي 0 بشكل افتراضي، وإلا قد تؤدي إلى نتائج غير متوقعة.
```

```markdown
بالنسبة للمعلمات حول **التوقيت** و **المستويات**, يمكنك إضافتها في محرر الإنسان أو ورقة الاختبار الفوري، ليس من الضروري تضمينها في وظيفة VBT. ويمكنك التحكم في تمكينها أو تعطيلها في وظيفة VBT باستخدام الاستخدام التالي:

```vbscript
TheHdw.Digital.ApplyLevelsTiming
```

بالنسبة لـ **حدود الاختبار**, يمكنك استخدام الشيفرة التالية:

```vbscript
TheExec.Flow.TestLimit
```

لمقارنة قيمة النتيجة مع حدود منخفضة/مرتفعة، وإرسال نتيجة الاختبار (`TL_SUCCESS`/`TL_ERROR`) ومعلومات أخرى إلى السجل البيانات.

لرؤية **البنية الأساسية** لوظيفة اختبار VBT بشكل أوضح، إليك مثال:

```vbscript
Public Function VBTLeakTest(Pins As PinList, ForceVoltage As Double, PrePattern As PatternSet) As Long
    On Error GoTo errHandler

    Dim measure_results As New PinListData

    ' إعداد التوقيت والمستويات لنمط الاستباق
    TheHdw.Digital.ApplyLevelsTiming ConnectAllPins:=True, loadLevels:=True, loadTiming:=True, relaymode:=tlPowered

    ' تشغيل نمط الاستباق واختبار النجاح/الفشل
    TheHdw.Patterns(PrePattern).test pfAlways, 0

    ' فرض جهد، قياس التيار
    With TheHdw.DCVI.Pins(Pins)
        .Mode = tlDCVIModeVoltage
            ... ' الشيفرة الإضافية
        measure_results = .Meter.Read
    End With

    ' الاختبار باستخدام الحدود في التدفق وكتابة السجل البيانات
    Call TheExec.Flow.TestLimit(resultval:=measure_results, unit:=unitAmp, forceval:=ForceVoltage, forceunit:=unitVolt, ForceResults:=tlForceFlow)

    ' إعادة تعيين المتغير
    measure_results = Nothing

    Exit Function
errHandler:
    If AbortTest Then Exit Function Else Resume Next
End Function
```

## مواقع متعددة

🚧

## عمليات قائمة الأقطاب

🚧

## نصائح في VBA
```

Here is the text translated into Arabic while maintaining the original markdown format:

- تجنب حفظ الكود في VBA، لأن ذلك سينشئ روابط داخلية صعبة في الدفتر. بدلاً من ذلك، حفظه في واجهة DataTool.
- إذا واجهت خطأ "الإجراء كبير جدًا"، قد تكون ضد قيود Excel البالغة 64K لكل ملف VB. ولكن في الواقع، قد تكون قد نسيت تبديل الإصدار من 32 بت إلى 64 بت لنظام Windows.

> تمت ترجمة هذه المشاركة باستخدام ChatGPT، يرجى [**تزويدنا بتعليقاتكم**](https://github.com/linyuxuanlin/Wiki_MkDocs/issues/new) إذا كانت هناك أي حذف أو إهمال.