---
author: 022000434 张江杰
date: 2024年6月17日
---

# 劳动实践日报Day1

> 学号: 022000434
> 
> 姓名: 张江杰
> 
> 日期: 2024年6月17日


## 环境配置

由于我经常使用Linux进行学习和开发，因此在本次项目中，我并未采用课程推荐的VMWare镜像方案。我参考了镜像环境的配置，在Ubuntu 22.04物理机上自行搭建了HarmonyOS的编译环境。

在配置过程中，我访问了课件中提及的HPM官方网站，以了解环境配置的具体参数。得知HPM依赖于Node.js后，我根据官方文档的指导，下载并安装了hpm@1.6.0版本。

经过一系列的调整，包括修改Node版本(14.15.1)、安装合适的hpm版本(hpm@1.0.2)以及补充缺失的Python库，我最终成功编译并运行了程序，得到了烧录所需的文件。

为了总结并分享这次环境配置的经验，我编写了一个简洁的环境配置脚本。不过，该脚本目前保存在另一台计算机上，我将在后续的实验报告中提供该脚本的详细信息。

## 代码实现

hello.c

```c
    #include <stdio.h>
    #include <ohos_init.h>

    void led(void)
    {
        printf("Hello, BearPi!\r\n");
    }
    APP_FEATURE_INIT(led);
```


lec.c

```c
    #include <stdio.h>
    #include <ohos_init.h>
    #include "wifiiot_gpio.h"
    #include "wifiiot_gpio_ex.h"
    #include <unistd.h>

    void led(void)
    {
        int cnt = 0;
        while (1)
        {
            GpioSetOutputVal(WIFI_IOT_IO_NAME_GPIO_2, WIFI_IOT_GPIO_VALUE1);
            printf("%d led on\n", cnt);
            usleep(1000000);
            GpioSetOutputVal(WIFI_IOT_IO_NAME_GPIO_2, WIFI_IOT_GPIO_VALUE0);
            printf("%d led off\n", cnt++);
            usleep(1000000);
        }
    }
    APP_FEATURE_INIT(led);
```