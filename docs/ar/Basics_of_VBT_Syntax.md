# أساسيات بناء جمل VBT

> هذه المقالة متاحة باللغة الإنجليزية فقط.

## كائنات البيانات

### TheHdw و TheExec

هناك مقابضان عالميتان في واجهة VBT لتشغيل عتاد الاختبار:

- **TheHdw (العتاد)**: يدعم الوصول والتحكم في الأدوات، ويتضمن المزيد من الوظائف العامة للعتاد، مثل التنبيهات.
- **TheExec (التنفيذي)**: للتحكم في وظائف برنامج الاختبار العامة، مثل تنفيذ الاختبار، ومعالجة نتائج الاختبار، وتسجيل دفتر البيانات.

فيما يلي أمثلة على استخدامها:

```vbscript
' تعيين نطاق دبوس p0 الحالي
TheHdw.DCVI.Pins("p0").CurrentRange = 0.002
```

```vbscript
' الحصول على مسار ملف STDF الإخراج الحالي
CurrStdfFile = TheExec.Datalog.Setup.STDFOutputFile
```

### كائنات البيانات الأخرى

تتضمن واجهة VBT مقابض عالمية أخرى، مثل **PinListData**، **DSPWave**، **RtaDataObj (كائن بيانات التعديل الزمني للتشغيل)** وغيرها. سنستكشفها في مقالات مستقبلية.

## الوصول بواسطة الأداة أو الدبوس

تدعم بناء جمل VBT الوصول إلى عتاد الاختبار **بواسطة الأداة** أو **بواسطة الدبوس**، وهما متكافئان في النتيجة. فيما يلي أمثلة على استخدامها:

```vbscript
' الوصول بواسطة الأداة، يطبق أداة واحدة على دبابيس مختلفة
With TheHdw.instrument
    .Pins("Vcc").CurrentLimit = 0.75
    .Pins("Vee").ForceValue = 3.2
End With
```

```vbscript
' الوصول بواسطة الدبوس، يحدد قائمة دبابيس ثم يستخدم أدوات مختلفة
With TheHdw.Pins("Vcc,Vdd,Vee")
    .instrument1.Disconnect
    .instrument2.CurrentLimit = 0.75
End With
```

## بنية كود VBT

يجب أن يكون اسم ملف VBT بتسمية `VBT_xxx`، ويجب أن يكون الاسم فريدًا.

يتوقع أن يكون **قيمة الإرجاع** لدالة VBT هي 0 بشكل افتراضي، أو قد تتسبب في نتائج غير متوقعة.

بالنسبة للمعلمات المتعلقة بـ **التوقيت** و **المستويات** ، يمكنك إضافتها في محرر النموذج أو ورقة الاختبار الفوري ، ولا يلزم تضمينها في وظيفة VBT. ويمكنك التحكم في تمكينها في وظيفة VBT باستخدام الاستخدام التالي:

```vbscript
TheHdw.Digital.ApplyLevelsTiming
```

بالنسبة لـ **حدود الاختبار** ، يمكنك استخدام الكود التالي:

```vbscript
TheExec.Flow.TestLimit
```

لمقارنة قيمة النتيجة مع الحدود المنخفضة / العالية ، وإرسال نتيجة الاختبار (`TL_SUCCESS` / `TL_ERROR`) وغيرها من المعلومات إلى دفتر السجلات.

لرؤية **الهيكل الأساسي** لوظيفة اختبار VBT بوضوح ، هناك عينة:

```vbscript
Public Function VBTLeakTest(Pins As PinList, ForceVoltage As Double, PrePattern As PatternSet) As Long
    On Error GoTo errHandler

    Dim measure_results As New PinListData

    ' Set up timing and levels for Preconditioning Pattern
    TheHdw.Digital.ApplyLevelsTiming ConnectAllPins:=True, loadLevels:=True, loadTiming:=True, relaymode:=tlPowered

    ' Run Preconditioning Pattern and test for Pass/Fail
    TheHdw.Patterns(PrePattern).test pfAlways, 0

    ' Force V, Measure I
    With TheHdw.DCVI.Pins(Pins)
        .Mode = tlDCVIModeVoltage
            ... ' Addition code
        measure_results = .Meter.Read
    End With

    ' Test using limits in flow and write datalog
    Call TheExec.Flow.TestLimit(resultval:=measure_results, unit:=unitAmp, forceval:=ForceVoltage, forceunit:=unitVolt, ForceResults:=tlForceFlow)

    ' Reset the variable
    measure_results = Nothing

    Exit Function
errHandler:
    If AbortTest Then Exit Function Else Resume Next
End Function
```

## موقع متعدد

🚧

## عمليات PinList

🚧

## نصائح في VBA

- تجنب حفظ الشفرة في VBA ، لأن هذا سيخلق روابط داخلية صعبة في دفتر العمل. بدلاً من ذلك ، يجب حفظها في واجهة DataTool.
- إذا واجهت خطأ "الإجراء كبير جدًا" ، فقد تكون ضد قيود Excel البالغة 64 كيلو بايت لكل ملف vb. ولكن في الواقع ، من الممكن أنك نسيت تحويل الإصدار من 32 بت إلى 64 بت من نظام Windows.

> تمت ترجمة هذه المشاركة باستخدام ChatGPT، يرجى [**تزويدنا بتعليقاتكم**](https://github.com/linyuxuanlin/Wiki_MkDocs/issues/new) إذا كانت هناك أي حذف أو إهمال.