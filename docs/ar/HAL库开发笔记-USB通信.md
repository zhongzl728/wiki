# ملاحظات تطوير مكتبة HAL - الاتصال بواسطة USB 🚧

هذا المقال يعتمد على مجموعة تطوير RobotCtrl الخاصة بنا، مع وحدة معالجة الذكريات المدمجة STM32F407ZET6 ومنافذ USB_Slave على الدبابة PA11 و PA12. للحصول على المخطط الأساسي ومزيد من التفاصيل، يُرجى زيارة [**RobotCtrl - STM32 通用开发套件**](https://wiki-power.com/ar/RobotCtrl-STM32%E9%80%9A%E7%94%A8%E5%BC%80%E5%8F%91%E5%A5%97%E4%BB%B6).

## خطوات بسيطة لاختبار الدورة

### التكوين داخل CubeMX

1. قم بتكوين المؤقت الخارجي عالي السرعة (HSE).
2. قم بتكوين شجرة الساعة للتأكد من أن نهاية شجرة الساعة تحتوي على "48MHz Clocks (MHz)".
3. في صفحة "USB_OTG_FS"، قم بتعيين "Mode" إلى "Device_Only"، حيث يكون منافذ الدبابة الافتراضية هي PA11 و PA12.
4. في صفحة "USB_DEVICE"، قم بتعيين "Class For FS IP" إلى "Commmunication Device Class (Virtual Port Com)".

### التكوين داخل الشيفرة

لتنفيذ وظيفة دورة البيانات، ما عليك سوى إضافة سطر واحد داخل دالة "CDC_Receive_FS" في ملف "usbd_cdc_if.c":

```c title="usbd_cdc_if.c"
CDC_Transmit_FS(Buf,*Len); // إرسال البيانات نفسها
```

### الاختبار

قم بفتح مدير الأجهزة للتحقق من ظهور الجهاز. إذا لم تظهر الجهاز أو إذا كان هناك علامة تعجب صفراء، يُرجى تنزيل برنامج التشغيل من موقع ST [**STM32 Virtual COM Port Driver**](https://www.st.com/content/st_com/en/products/development-tools/software-development-tools/stm32-software-development-tools/stm32-utilities/stsw-stm32102.html).

إذا لم يتم التعرف على الجهاز بشكل صحيح حتى بعد تثبيت البرنامج التشغيل، يُمكنك محاولة زيادة "Minimum Heap Size" إلى "0x600" أو أكثر داخل CubeMX - "Project Manager" - "Project" - "Linker Settings".

باستخدام أداة البورت السلسلي (أي سرعة اتصال)، يُمكنك إرسال أي حروف، وستتلقى نفس الحروف كرد على ذلك.

## المراجع والشكر

- [استخدام STM32 CubeMX HAL لإنشاء مشروع USBVCP لمنفذ السلسلة الافتراضي](https://blog.csdn.net/yxy244/article/details/102620249)

> عنوان النص: <https://wiki-power.com/>  
> يتم حماية هذا المقال بموجب اتفاقية [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by/4.0/deed.zh)، يُرجى ذكر المصدر عند إعادة النشر.

> تمت ترجمة هذه المشاركة باستخدام ChatGPT، يرجى [**تزويدنا بتعليقاتكم**](https://github.com/linyuxuanlin/Wiki_MkDocs/issues/new) إذا كانت هناك أي حذف أو إهمال.