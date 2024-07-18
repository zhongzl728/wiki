# مذكرات تطوير مكتبة HAL - اتصال CAN 🚧

تستند هذه المقالة إلى مجموعة أدوات تطوير RobotCtrl المصممة ذاتيًا، مع نواة ميكروكنترولر STM32F407ZET6، واستخدام اتصال CAN باستخدام شريحة TJA1050. يُرجى الرجوع إلى [**RobotCtrl - STM32 通用开发套件**](https://wiki-power.com/RobotCtrl-STM32%E9%80%9A%E7%94%A8%E5%BC%80%E5%8F%91%E5%A5%97%E4%BB%B6) للمزيد من التفاصيل والرسومات التوضيحية.

## خطوات بسيطة لاختبار الحلقة المغلقة

### تكوين داخل CubeMX

1. استنادًا إلى عتاد CAN المستخدم، انتقل إلى صفحة `CAN1` أو `CAN2` في الشريط الجانبي الأيسر، وحدد `تنشيط` (Activated). ثم قم بتكوين هذه المعلمات في صفحة المعلمات:
   1. قم بتعيين `Prescaler (for Time Quantum)` إلى `6`، وحدد `Time Quanta in Bit Segment 1` و `Time Quanta in Bit Segment 2` على `3 Times`. هذا الجمع سيعين معدل البت بمقدار 1 ميجابت في الثانية (الأعلى).
   2. ضبط `ReSynchronization Jump Width` على `1 Time`، وهذا هو أقصى قدر يمكن تعديله أثناء إعادة المزامنة.
   3. تعيين `Operating Mode` إلى `Loopback`، وهذا للاختبار داخل الحلقة المغلقة.

2. في علامة التبويب `NVIC Settings`، قم بتمكين `CANx RX0 interrupts`.


### تكوين داخل الكود

قم بإنشاء ملف `can.c` في مشروعك وقم بتكوين المرشحات. في هذا المثال، تم تكوين وضع القائمة وتم تصفية معرف ممتد `0x2233` ومعرف قياسي `0`:

```c title="can.c"/*
 * اسم الوظيفة: CAN_Filter_Config
 * الوصف: تكوين مرشح CAN
 * المدخلات: لا شيء
 * المخرجات: لا شيء
 * استدعاء: استدعاء داخلي
 */
static void CAN_Filter_Config(void) {
    CAN_FilterTypeDef CAN_FilterTypeDef;

    /* تكوين مرشح CAN */
    CAN_FilterTypeDef.FilterBank = 0;                        // مجموعة المرشح 0
    CAN_FilterTypeDef.FilterMode = CAN_FILTERMODE_IDLIST;     // تعمل في وضع القائمة
    CAN_FilterTypeDef.FilterScale = CAN_FILTERSCALE_32BIT;    // عرض المرشح 32 بت فردي.
    /* تمكين المرشح، حيث يتم مقارنة محتوى العلامات التجارية، ويتم تجاهل ما لم تكن الهوية الممتدة هي كما يلي، ستتم إضافتها إلى FIFO0. */

    CAN_FilterTypeDef.FilterIdHigh = ((((uint32_t) 0x2233 << 3) | CAN_ID_EXT
            | CAN_RTR_DATA) & 0xFFFF0000) >> 16;        // أعلى بت من الهوية المراد تصفيةها
    CAN_FilterTypeDef.FilterIdLow = (((uint32_t) 0x2233 << 3) | CAN_ID_EXT
            | CAN_RTR_DATA) & 0xFFFF;                   // أدنى بت من الهوية المراد تصفيةها
    CAN_FilterTypeDef.FilterMaskIdHigh = 0;        // أعلى بت للهوية الثانية
    CAN_FilterTypeDef.FilterMaskIdLow = 0;         // أدنى بت للهوية الثانية
    CAN_FilterTypeDef.FilterFIFOAssignment = CAN_FILTER_FIFO0;    // المرشح مرتبط ب FIFO0
    CAN_FilterTypeDef.FilterActivation = ENABLE;            // تمكين المرشح
    HAL_CAN_ConfigFilter(&hcan1, &CAN_FilterTypeDef);
}
```

### الاختبار


```markdown
افتح مدير الأجهزة للتحقق مما إذا كانت الجهاز قد ظهر بالفعل. إذا لم تجد الجهاز أو إذا ظهر رمز تعجب أصفر، يرجى زيارة موقع ST الرسمي لتنزيل برنامج التشغيل [STM32 Virtual COM Port Driver](https://www.st.com/content/st_com/en/products/development-tools/software-development-tools/stm32-software-development-tools/stm32-utilities/stsw-stm32102.html).

إذا كنت قد قمت بتثبيت برنامج التشغيل ولا تزال تواجه مشكلة في التعرف على الجهاز، يمكنك محاولة ضبط حجم الذاكرة الدنيا (Minimum Heap Size) على قيمة 0x600 أو قيمة أعلى في CubeMX - Project Manager - Project - Linker Settings.

افتح أداة الاتصال المتسلسل (Serial) بأي معدل بت (Baud Rate) وقم بإرسال أي حرف. ستتلقى نفس الحرف كرد من الجهاز.

## مراجع وشكر

- [STM32CubeMX ومكتبة HAL - تعلم الاختبار الدوري البسيط لبروتوكول CAN](https://blog.csdn.net/weixin_45209978/article/details/119850600)

> عنوان النص: <https://wiki-power.com/>
> يتم حماية هذا المقال بموجب اتفاقية [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by/4.0/deed.zh)، يُرجى ذكر المصدر عند إعادة النشر.
```

Note: I have translated the content into Arabic while maintaining the original markdown format.

> تمت ترجمة هذه المشاركة باستخدام ChatGPT، يرجى [**تزويدنا بتعليقاتكم**](https://github.com/linyuxuanlin/Wiki_MkDocs/issues/new) إذا كانت هناك أي حذف أو إهمال.