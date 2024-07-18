# تطوير CMSIS-DAP 🚧

CMSIS DAP هو محاكي مفتوح المصدر تم تطويره من قبل شركة ARM ويدعم جميع أجهزة Cortex-ARM ويدعم واجهات JTAG / SWD ، وفي أحدث إصدار للبرامج الثابتة ، يدعم أيضًا واجهة SWO ذات الخط الواحد ، والتي يمكن استخدامها لإخراج البيانات المقابلة مباشرةً إلى نافذة التصحيح في البرنامج ، وتحقيق نفس الغرض من تصحيح البيانات عبر واجهة السلسلة. يتميز DAP بالنقاط التالية:

1. مفتوح بالكامل ولا يوجد قيود على الترخيص ، لذلك سيكون السعر مناسبًا جدًا
2. لا حاجة لتثبيت برامج التشغيل ، يمكن استخدامه مباشرةً
3. تم دمج واجهة السلسلة في إصدارات DAP الجديدة ، بالإضافة إلى التصحيح والتنزيل ، يمكن استخدامه أيضًا كوحدة تحويل USB إلى سلسلة ، لذا يمكن استخدامه لأغراض متعددة
4. تلبية احتياجات المستخدم العامة من حيث الأداء

(غير مكتمل)

مستودع GitHub: [**linyuxuanlin/DashDAP**](https://github.com/linyuxuanlin/DashDAP)

## المراجع والشكر

- [x893/CMSIS-DAP](https://github.com/x893/CMSIS-DAP)
- [موقع ARM الرسمي لمحاكي DAP](http://www.keil.com/pack/doc/cmsis/DAP/html/index.html)
- [حنين المهووس بالإلكترونيات: محاكي CMSIS DAP](http://www.stmcu.org.cn/module/forum/thread-610968-1-2.html)
- [محاكي CMSIS DAP](https://item.taobao.com/item.htm?spm=a1z10.1-c.w5003-21405148310.36.78726a3dta5ieC&id=550828063764&scene=taobao_shop)
- [konosubakonoakua/Various_MCU_Debugger_DIY](https://github.com/konosubakonoakua/Various_MCU_Debugger_DIY)

---

`قيد التحرير الإصدار 2.0`

![](https://media.wiki-power.com/img/20200613154907.jpg)

معاينة المشروع عبر الإنترنت:

<div class="altium-iframe-viewer">
  <div
    class="altium-ecad-viewer"
    data-project-src="https://github.com/linyuxuanlin/DashDAP/raw/master/Hardware/DashDAP.zip"
  ></div>
</div>

## الخلفية

CMSIS-DAP / DAP-Link لديها مزايا مقارنة بـ J-Link / ST-Link:

- مفتوح بالكامل ولا يوجد مخاطر قانونية
- يدعم منفذ السلسلة الافتراضي
- لا حاجة لتثبيت برامج التشغيل
- DAPLink هو CMSIS-DAP ويدعم النسخ عبر السحب والإفلات إلى وحدة التخزين العابرة / ترقية البرامج الثابتة

## الجزء الأجهزة

### وحدة المعالجة المركزية (MCU)

#### المذبذب

تم اختيار مذبذب غير نشط من مورد موريتا بتردد 8 ميجاهرتز ، والذي يحمل الرقم CSTCE8M00G53-R0 ويأتي في حزمة 3213 ويحتاج إلى سعة تخزين 15 بيكوفاراد. لماذا تم اختيار هذا المذبذب؟ لأنه صغير الحجم نسبيًا ويدمج مكثفي الاهتزاز فيه ، مما يوفر الكثير من الجهد في التصميم الأجهزة. يمكن الاطلاع على طريقة تسمية موديلات مذبذب موريتا من خلال الجدول التالي:

![](https://media.wiki-power.com/img/20200612143451.jpg)

### مصدر الطاقة

### وحدات الوظائف

## الجزء البرمجي

### برامج التشغيل

لا يلزم تثبيت برامج التشغيل يدويًا على Win10 / MacOS / Linux ؛ يتطلب Win8 والإصدارات الأقدم من النظام تثبيت برامج التشغيل يدويًا.

### التنزيل عبر السحب والإفلات (MSC)

قم بسحب ملف `.hex` أو `.bin` الذي تم إنشاؤه بواسطة الترجمة مباشرةً إلى وحدة التخزين العابرة لـ DAPLink لإكمال عملية الحرق. إذا حدث خطأ ، سيتم تخزين معلومات الخطأ في ملف `FAIL.txt`.

### منفذ السلسلة الافتراضي (CDC)

تتمتع وظيفة منفذ السلسلة الافتراضي (CDC) بالقدرات العامة لمنفذ السلسلة العادي ، وتسمح بالاتصال ثنائي الاتجاه وإرسال أوامر مقاطعة لإعادة تعيين اللوحة المستهدفة.

## المراجع والشكر

- [اختلاف استخدام JLink و STLink و DAPLink و CMSIS DAP](https://blog.csdn.net/zhouml_msn/article/details/105298776)
- [جيتشين · محاكي DAPLink](https://www.jixin.pro/bbs/topic/4187)
- [wuxx / nanoDAP](https://github.com/wuxx/nanoDAP)
- [LGG001 / كتيب DAPLink](https://github.com/LGG001/DAPLink-Brochure)

> عنوان النص: <https://wiki-power.com/>
> يتم حماية هذا المقال بموجب اتفاقية [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by/4.0/deed.zh)، يُرجى ذكر المصدر عند إعادة النشر.

> تمت ترجمة هذه المشاركة باستخدام ChatGPT، يرجى [**تزويدنا بتعليقاتكم**](https://github.com/linyuxuanlin/Wiki_MkDocs/issues/new) إذا كانت هناك أي حذف أو إهمال.
