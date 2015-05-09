# 封装 ARM mbed 成为 WoT App

将ARM mbed 装置「封装」成web app 只要简单的7 个步骤，而且不用编写代码。本文以温度感测器为例，说明​​「装置到IoT App」的做法。以下步骤使用现成的项目实例与SDK 进行，几乎不涉及实现细节，很适合初次接触WoT 的创客。

## 准备工作

1. 取得ARM mbed 开发板，本文使用的Arch Pro 是由Seeed Studio 设计与生产的ARM mbed IoT ethernet kit。
2. 申请[wotcity.com](http://wotcity.com) 帐号。
3. DIY 温度感测装置

### DIY 温度感测装置

使用Arch Pro 手作一个温度感测装置，需要准备以下零件：

* Arch Pro 开发板
* Base Shield
* Grove Temperature（温度感测器）
* Grove - Universal 4 Pin 20cm Unbuckled Cable（杜邦线）
* mbed WiFi Shield

以下是基本零件：

![温度感测装置零件](https://camo.githubusercontent.com/c5a247cd8ed437bf4299e7a3a9314d5f349e43cb/687474703a2f2f692e696d6775722e636f6d2f6b7861554a4b752e6a7067)

七段显示器仅作为本地测试使用，非必备。要让Arch Pro 上网，可直接连接RJ45 乙太网路，或使用WiFi 上网。要使用WiFi 乙太网路，必须再安装mbed WiFi Shield：

![WiFi Shield](http://www.seeedstudio.com/depot/images/product/113030003%201.jpg)

组装完成图：

![完成图](http://i.imgur.com/AKgq0Qf.jpg)

使用本文范例，请使用杜邦线将温度感测器，连接至Base Shield 上的A0 插槽。使用杜邦线连线温度感测器与Base Shield 时，要注意黑线是接地（GND）。 Pin 脚在电路板上有印刷字体。

Arch Pro 开发板可向 Seeed Studio 购买。

## Step 1：汇入 ARM mbed 专案

至[https://developer.mbed.org/](https://developer.mbed.org/) 申请开发者帐号，进入[Online Compiler](https://developer.mbed.org/compiler/) 后可看到图1.1 的Workspace 环境。

![图1.1：mbed 的workspace](https://raw.githubusercontent.com/mbed-taiwan/mbed-school/master/09-wot-city/1.1_workspace.png)

图 1.1：mbed 的 workspace

依照以下步骤汇入范例：

1. 点选workspace 上方选单的*Import* 选项
2. 进入*Import Wizard* 后，点选*Click here* to import from URL.（如图1.2）
3. 输入实例网址`https://developer.mbed.org/users/jollen/code/Mokoversity_WoT_Wifly_Temperature/`，如图1.3
4. 汇入成功后，可以在*Program Workspace* 看到*Mokoversity_WoT_Wifly_Temperature* 项目
5. 将项目展开后，可以找到*main.cpp* 文件
6. 完成范例汇入

![图1.2：Import Wizard](https://raw.githubusercontent.com/mbed-taiwan/mbed-school/master/09-wot-city/8.2.png)

图 1.2：Import Wizard

![图1.3：实例网址](https://raw.githubusercontent.com/mbed-taiwan/mbed-school/master/09-wot-city/8.3.png)

图 1.3：实例网址

## Step 2：让 Arch Pro 上网

本文范例使用 Arch Pro + mbed WiFi Shield。请开启 *main.cpp*，找到以下代码：

```
WiflyInterface eth(D1, D0, D5, LED4, "<SSID>", "<Password>", WPA);
```

将*<SSID>* 修改为WiFi 热点名称；将*<Password>* 修改为WiFi 热点密码。例如：

```
WiflyInterface eth(D1, D0, D5, LED4, "jollenchen", "12345678", WPA);
```

如果是使用ethernet cable（RJ-45）让Arch Pro 上网，请在Step 1 时改汇入以下实例：

```
https://developer.mbed.org/users/jollen/code/Mokoversity_WoT_Ethernet_Temperature/
```

二个例子只有极小的差异。将WiFi 上网修改为ethernet cable 时，只要找到以下代码：

```
WiflyInterface eth(D1, D0, D5, LED4, "<SSID>", "<Password>", WPA);
```

并修改为：

```
EthernetInterface eth;
```

即可。上述二个范例预设皆使用DHCP 进行网路组态，如果要手动进行组态（Static IP），请参阅代码里的注解说明。

## Step 3：取得 Device Name

至[http://wotcity.com/](http://wotcity.com/) 申请帐号并登入。依照以下步骤取得 Device Name：

1. 点撃[http://wotcity.com/account](http://wotcity.com/account) 页面左边的*Device Manager*
2. 点撃 *Launch New Device* 按扭
3. 系统会创建一个新的装置
4. 从列表中的*Device Name (Physical Object)* 栏位Copy 装置名称，如图1.4

![图1.4：取得Device Name](https://raw.githubusercontent.com/mbed-taiwan/mbed-school/master/09-wot-city/8.4.png)

图 1.4：取得 Device Name

## Step 4：修改 *main.cpp*

延续Step 1，将*main.cpp* 开启后，找到以下代码片断：

```
    /*
     * We use WoT.City Websocket channel service.
     * See: https://www.mokoversity.com/wotcity
     */
    Websocket ws("ws://wot.city/object/[Object Name]/send");
    while( !ws.connect() );
    led2 = 1;
```

修改Websocket 的URI，将*[Object Name]* 替换为Step 2 取得的Device Name。例如：

```
    /*
     * We use WoT.City Websocket channel service.
     * See: https://www.mokoversity.com/wotcity
     */
    Websocket ws("ws://wot.city/object/5547870f4dd3e08d63000007/send");
    while( !ws.connect() );
    led2 = 1;
```

## Step 5：更新 Firmware

延续Step 4，点撃选单*Save* 选项后，再点选*Compile*。编译成功后，浏览器会自动下载firmware 档案（*.bin）。

将Arch Pro 与电脑连接后，会看到*MBED/* 文件夹。请注意，Arch Pro 有二个Mini USB Port，更新firmware 时，请连接至Debug Port。

接着将新的firmware 档案拉到此资料里即可，如图1.5。更新完成后，请拔除并重新连接USB，让Arch Pro 重新关机。

![图1.5：Drag and Drop 更新firmware](https://raw.githubusercontent.com/mbed-taiwan/mbed-school/master/09-wot-city/1.3_drag-drop.png)

图 1.5：Drag and Drop 更新 firmware

## Step 6：开始 Data Push

更新firmware 并启动Arch Pro 后，温度感测器数据就会开始推送（Data Push）到Internet。要知道目前的Data Push 情况，可以使用WoT.City 的监视看功能：

1. 登入[http://wotcity.com/](http://wotcity.com/)
2. 点选[http://wotcity.com/account](http://wotcity.com/account) 页面左边的*Device Manager*
3. 在列表中找到所使用的 *Device Name*
4. *Status* 栏位会切换为*Live*，表示目前装置已连上Internet，如图1.6
5. 点撃*Manage* 栏位中的*Watch* 按纽，即可看到即时的推送数据（JSON 格式的文件）

![图1.6：装置显示为Live](https://raw.githubusercontent.com/mbed-taiwan/mbed-school/master/09-wot-city/8.6.png)

图 1.6：装置显示为 Live

点撃*Manage* 栏位中的*Live App* 按纽，可以开启这个装置的web app。目前WoT.City 的Live App 功能，暂时只支援温度感测装置。未来将可以由使用者自行布署Live App。

## Step 7：制作 Web Frontend

Live App 的功能其实是一个 web frontend。想自行打造Live App 的创客，可使用.CITY Starter Kit。 **tl;dr**: 取得[.CITY Starter Kit](http://wotcity.com/docs/dotcity-starter-kit)。

.CITY Starter Kit 使用*Backbone.View* 与*Backbone.Model* 做为主要的framework。但在*render()* 方面，则是不同的设计，对*Backbone.Model* 也做了小幅度的重构，以支援Dataset-View 的设计模式。 .CITY Starter Kit 的主要特色如下：

* Use *Backbone.Model* to composite streaming data.
* Use virtual dom for view boundary composition.
* Dataset-View framework pattern
* Integrate WoT.City websocket broker service

以上特色皆实现于[AutomationJS](https://github.com/wotcity/automationjs)，并整合在.CITY Starter Kit 里。