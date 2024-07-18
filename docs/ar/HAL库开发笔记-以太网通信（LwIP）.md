# مذكرات تطوير مكتبة HAL - الاتصال بشبكة الإيثرنت (LwIP) 🚧

فيما يلي دليل استنادي إلى [**لوحة تحكم النواة الرئيسية STM32F407 المعكوسة**](https://item.taobao.com/item.htm?spm=a230r.1.14.16.57314534365ZlN&id=569068950037&ns=1&abbucket=4#detail) و[**وحدة الفيزياء DP83848 لشبكة الإيثرنت**](https://item.taobao.com/item.htm?spm=a230r.1.14.1.38df5bd3YTS6rE&id=12873819988&ns=1&abbucket=4#detail).

## المكونات الهاردويرية

واجهة DP83848 تستخدم تقنية RMII، وتدعم DP83848 سرعات خطية تصل إلى 10 ميجابت/ثانية و 100 ميجابت/ثانية، وهناك عرض تردد بدون قوة يبلغ 50 ميجاهرتز على اللوحة.

| النواة الرئيسية STM32 | وحدة DP83848 |
| --------------------- | ------------ |
| ETH_REF_CLK           | PA1          |
| ETH_MDIO              | PA2          |
| ETH_MDC               | PC1          |
| ETH_CRS_DV            | PA7          |
| ETH_RXD0              | PC4          |
| ETH_RXD1              | PC5          |
| ETH_TX_EN             | PB11         |
| ETH_TXD0              | PB12         |
| ETH_TXD1              | PB13         |

## البرمجيات

### تكوين داخلي باستخدام CubeMX

- RCC: اختيار الكرستال الخارجي HSE
- SYS
  - DEBUG: SW
- GPIO
  - PA15: `USER_BTN`، إدخال، مع مقاومة سحب عالية
  - PC13: `LED_GREEN`، إخراج نشط إيجابي، مستوى عالي
  - PC14: `LED_BLUE`، إخراج نشط إيجابي، مستوى عالي
  - PC15: `LED_RED`، إخراج نشط إيجابي، مستوى عالي
- ETH
  - Mode: RMII
  - Advanced Parameters
    - PHY: عنوان DP83848_PHY_ADDRESS
- LWIP
  - خيارات رئيسية
    - تحديد خيار "Show Advanced Parameters"
    - تأكد من أن LWIP_NETIF_LINK_CALLBACK مفعل (عادة مفعل افتراضيًا)
    - xLWIP_LOOPIF_MULTICAST: تمكين
    - xLWIP_MULTICAST_TX_OPTIONS: تمكين
    - xLWIP_NETIF_STATUS_CALLBACK: تمكين
    - xLWIP_NETIF_EXT_STATUS_CALLBACK: تمكين
    - xLWIP_SO_RCVBUF: تمكين
  - إعدادات عامة
    - xLWIP_IGMP: تمكين

تكوين شجرة الساعة: استنادًا إلى تردد الكرستال على اللوحة (8 ميجاهرتز في هذا اللوحة).

![](https://media.wiki-power.com/img/20220702145310.png)

### إضافة رمز الوظيفة

```c title="main.c"
/* USER CODE BEGIN PV */
extern struct netif gnetif;
/* USER CODE END PV */
```

```c
void ethernetif_notify_conn_changed(struct netif *netif) {
	/* ملحوظة: يمكن تنفيذ هذه الوظيفة في ملف المستخدم عند الحاجة
	 إلى الاستدعاء. */
	if (netif_is_link_up(netif)) {
		HAL_GPIO_WritePin(LED_GREEN_GPIO_Port, LED_GREEN_Pin, GPIO_PIN_RESET);
		HAL_GPIO_WritePin(LED_RED_GPIO_Port, LED_RED_Pin, GPIO_PIN_SET);
	} else {
		HAL_GPIO_WritePin(LED_GREEN_GPIO_Port, LED_GREEN_Pin, GPIO_PIN_SET);
		HAL_GPIO_WritePin(LED_RED_GPIO_Port, LED_RED_Pin, GPIO_PIN_RESET);
	}
}

ethernetif_notify_conn_changed(&gnetif);

MX_LWIP_Process();
```

```c title="lwip.c"
ethernetif_set_link(&gnetif);
if (netif_is_link_up(&gnetif) && !netif_is_up(&gnetif)) {
	netif_set_up(&gnetif);
	dhcp_start(&gnetif);
}
```

## تصحيح

- استعرض عناوين IP المتصلة بهذا الكمبيوتر: `arp -a`
- تحديد عنوان IP لوحدة STM32 عبر إدراج وفصل الكبل.
- `ping [عنوان IP] (-t)`
- إذا تم إزالة الكبل وإعادة توصيله بسرعة، قد يحدث "فشل في النقل، خطأ شائع"، انتظر لاستعادة الاتصال تلقائياً.

## المراجع والشكر

- [تهيئة Ethernet باستخدام STM32 HAL](https://blog.naver.com/eziya76/221852430347)

> عنوان النص: <https://wiki-power.com/>
> يتم حماية هذا المقال بموجب اتفاقية [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by/4.0/deed.zh)، يُرجى ذكر المصدر عند إعادة النشر.

---

```
يعتمد هذا المقال على مجموعة تطوير RobotCtrl المخصصة ذاتية الإنتاج، والتي تستخدم وحدة المعالجة المركزية STM32F407ZET6 كنواة ورقاقة PHY Ethernet كـ LAN8720A. للحصول على المخطط الأساسي ومزيد من التفاصيل، يرجى زيارة [**RobotCtrl - STM32 通用开发套件**](https://wiki-power.com/RobotCtrl-STM32%E9%80%9A%E7%94%A8%E5%BC%80%E5%8F%91%E5%A5%97%E4%BB%B6) (باللغة الصينية).

بروتوكول LwIP هو بروتوكول IP خفيف الوزن يمكن تشغيله بغض النظر عن وجود دعم لنظام التشغيل. يتمحور تنفيذ LwIP حول الحفاظ على وظائف بروتوكول TCP الرئيسية مع تقليل استهلاك الذاكرة العشوائية. يحتاج LwIP إلى حوالي عشرات الكيلوبايتات من الذاكرة العشوائية وحوالي 40 كيلوبايت من الذاكرة القراءة فقط ليعمل، مما يجعله مناسبًا للاستخدام في أنظمة المدمجة على الطرف السفلي.

يقدم LwIP ثلاث واجهات برمجة مختلفة هي RAW/Callback API وNETCONN API وSOCKET API. يزداد سهولة الاستخدام من اليسار إلى اليمين، في حين يتناقص كفاءة التنفيذ بالاتجاه نفسه. يمكنك تحقيق التوازن بين المزايا والعيوب واختيار واجهة برمجة تناسب احتياجاتك. في هذا المقال، تم استخدام واجهة Raw API واستدعاء الدوال التالية:
```

| API Function   | Description                                               |
| -------------- | --------------------------------------------------------- |
| udp_new        | إنشاء PCB UDP جديد                                        |
| udp_remove     | إزالة PCB UDP وتحرير الموارد ذات الصلة                    |
| udp_bind       | ربط PCB UDP بعنوان IP المحلي والمنفذ                      |
| udp_connect    | إنشاء PCB UDP بعنوان IP ومنفذ بعيدين                      |
| udp_disconnect | إزالة عنوان IP ومنفذ بعيدين من PCB UDP                    |
| udp_send       | إرسال بيانات UDP                                          |
| udp_recv       | تسجيل وظيفة انتظار استقبال البيانات عند استلام حزمة جديدة |

## تكوين داخل CubeMX

1. في صفحة `RCC`، قم باختيار المذبذب الخارجي لـ HSE.
2. في صفحة `ETH`، قم بتكوين وضع PHY كـ `RMII`، وقم بتكوين البارامترات التالية:
   1. في علامة تبويب `Parameter Setting`، قم بتكوين `PHY Address` كـ `0` (بناءً على تكوين دبوس PHYAD0).
   2. في علامة تبويب `Advanced Parameter`، استنادًا إلى دليل رقاقة LAN8720A، قم بتكوين `PHY special control/status register Offset` كـ `31`؛ وقم بتكوين `PHY Speed mask` كـ `0x0004`؛ وقم بتكوين `PHY Duplex mask` كـ `0x0010`.
3. في صفحة `LWIP`، قم بتمكينها وقم بتكوين البارامترات التالية:
   1. في علامة تبويب `General Settings`، قم بتكوين `LWIP_DHCP (وحدة DHCP)` كـ `Disabled` (استخدام عنوان IP ثابت)؛ وقم بتكوين `IP_ADDRESS` كـ `192.168.001.100`؛ وقم بتكوين `NETMASK_ADDRESS` كـ `255.255.255.000`؛ وقم بتكوين `GATEWAY_ADDRESS` كـ `192.168.001.001`؛ وقم بتمكين `LWIP_UDP (وحدة UDP)` و `LWIP_TCP (وحدة TCP)`.

## مراجع وشكر

- [LwIP TCP/IP stack demonstration for STM32F4x7 microcontrollers (AN3966)](https://www.st.com/en/embedded-software/stsw-stm32070.html)
- [Developing applications on STM32Cube with LwIP TCP/IP stack (UM1713)](https://www.st.com/resource/en/user_manual/um1713-developing-applications-on-stm32cube-with-lwip-tcpip-stack-stmicroelectronics.pdf)
- [54zorb/stm32-lwip](https://github.com/54zorb/stm32-lwip)

> عنوان النص: <https://wiki-power.com/>
> يتم حماية هذا المقال بموجب اتفاقية [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by/4.0/deed.zh)، يُرجى ذكر المصدر عند إعادة النشر.

> تمت ترجمة هذه المشاركة باستخدام ChatGPT، يرجى [**تزويدنا بتعليقاتكم**](https://github.com/linyuxuanlin/Wiki_MkDocs/issues/new) إذا كانت هناك أي حذف أو إهمال.
