# أساسيات بنية VBT

## كائنات البيانات

### TheHdw و TheExec

هناك مقابض عالمية اثنتان في واجهة VBT لتشغيل عتاد الاختبار:

- **TheHdw (العتاد)**: يدعم الوصول والتحكم في الأجهزة ويتضمن وظائف عامة أكثر للعتاد، مثل الإنذارات.
- **TheExec (التنفيذي)**: للتحكم في وظائف برنامج الاختبار العامة، مثل تنفيذ الاختبار، ومعالجة نتائج الاختبار، وتسجيل البيانات.

فيما يلي أمثلة على استخدامهم:

```vbscript
' تعيين النطاق الحالي للمنفذ p0
TheHdw.DCVI.Pins("p0").CurrentRange = 0.002
```

```vbscript
' الحصول على مسار ملف الإخراج STDF الحالي
CurrStdfFile = TheExec.Datalog.Setup.STDFOutputFile
```

### كائنات البيانات الأخرى

تم تضمين مقابض عالمية أخرى في واجهة VBT، مثل **PinListData**, **DSPWave**, **RtaDataObj (كائن بيانات تعديل الوقت التشغيلي)** وما إلى ذلك. سنستمر في استكشافها في المقالات المستقبلية.

## الوصول بواسطة الأداة أو بواسطة المنفذ

يدعم بنية VBT وصول عتاد الاختبار **بواسطة الأداة** أو **بواسطة المنفذ**، وهما مكافئان في النتيجة. فيما يلي أمثلة على استخدامهم:

```vbscript
' الوصول بواسطة الأداة، يطبق أداة واحدة على أمنافذ مختلفة
With TheHdw.instrument
    .Pins("Vcc").CurrentLimit = 0.75
    .Pins("Vee").ForceValue = 3.2
End With
```

```vbscript
' الوصول بواسطة المنفذ، يحدد قائمة المنافذ ثم يستخدم أدوات مختلفة
With TheHdw.Pins("Vcc,Vdd,Vee")
    .instrument1.Disconnect
    .instrument2.CurrentLimit = 0.75
End With
```

## هيكل كود VBT

يجب تسمية ملف كود VBT باسم `VBT_xxx`، ويجب أن يكون الاسم فريدًا.

يُتوقع أن يكون **قيمة العودة** لوظيفة VBT تساوي 0 افتراضيًا، أو قد تتسبب في نتائج غير متوقعة.

للمعلمات المتعلقة بـ **التوقيت** و **المستويات**، يُمكنك إضافتها في محرر النماذج الفردية أو ورقة الاختبار الفوري، وليس من الضروري تضمينها في وظيفة VBT. يمكنك التحكم في تمكينها في وظيفة VBT عبر الاستخدام التالي:

```vbscript
TheHdw.Digital.ApplyLevelsTiming
```

أما بالنسبة لـ **حدود الاختبار**، فيمكنك استخدام الشيفرة التالية:

```vbscript
TheExec.Flow.TestLimit
```

لمقارنة قيمة النتيجة مع الحدود السفلى والعليا، وإرسال نتيجة الاختبار (`TL_SUCCESS`/`TL_ERROR`) ومعلومات أخرى إلى دفتر البيانات.

لرؤية **الهيكل الأساسي** لوظيفة اختبار VBT بوضوح أكبر، إليك مثال:

```vbscript
Public Function VBTLeakTest(Pins As PinList, ForceVoltage As Double, PrePattern As PatternSet) As Long
    On Error GoTo errHandler

    Dim measure_results As New PinListData

    ' قم بإعداد التوقيت والمستويات لنمط الاستبهام
    TheHdw.Digital.ApplyLevelsTiming ConnectAllPins:=True, loadLevels:=True, loadTiming:=True, relaymode:=tlPowered

    ' قم بتشغيل نمط الاستبهام واختباره للنجاح/الفشل
    TheHdw.Patterns(PrePattern).test pfAlways, 0

    ' قم بتطبيق الجهد وقم بقياس التيار
    With TheHdw.DCVI.Pins(Pins)
        .Mode = tlDCVIModeVoltage
            ... ' الشيفرة الإضافية
        measure_results = .Meter.Read
    End With

    ' اختبر باستخدام الحدود في السير واكتب دفتر البيانات
    Call TheExec.Flow.TestLimit(resultval:=measure_results, unit:=unitAmp, forceval:=ForceVoltage, forceunit:=unitVolt, ForceResults:=tlForceFlow)

    ' أعد تعيين المتغير
    measure_results = Nothing

    Exit Function
errHandler:
    If AbortTest Then Exit Function Else Resume Next
End Function
```

## متعدد المواقع

🚧

## عمليات PinList

🚧

## نصائح في VBA


تجنب حفظ الكود في VBA، لأن ذلك سيخلق روابط داخلية صعبة في مصنف العمل. بدلاً من ذلك، احفظه في واجهة DataTool.
إذا واجهت خطأ "الإجراء كبير جدًا"، فقد تكون ضد القيود التي تفرضها إكسل بحد أقصى 64 كيلوبايت لكل ملف VBA. ولكن في الواقع، من الممكن أنك نسيت تغيير النسخة من 32 بت إلى 64 بت في نظام ويندوز.

> تمت ترجمة هذه المشاركة باستخدام ChatGPT، يرجى [**تزويدنا بتعليقاتكم**](https://github.com/linyuxuanlin/Wiki_MkDocs/issues/new) إذا كانت هناك أي حذف أو إهمال.