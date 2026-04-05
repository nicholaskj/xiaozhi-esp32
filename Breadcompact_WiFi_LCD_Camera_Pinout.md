# Breadcompact WiFi+LCD+Camera 开发板引脚配置说明

## 硬件配置总览

基于ESP32-S3开发板，包含：
- 摄像头模块 (OV3660)
- LCD显示屏 (ST7735 128x160)
- 音频输入输出 (麦克风+喇叭)
- WiFi连接

## 引脚详细配置

### 摄像头引脚 (OV3660)
```
D0:    GPIO11
D1:    GPIO9  
D2:    GPIO8
D3:    GPIO10
D4:    GPIO12
D5:    GPIO18
D6:    GPIO17
D7:    GPIO16
XCLK:  GPIO15
PCLK:  GPIO13
VSYNC: GPIO6
HREF:  GPIO7
SIOC:  GPIO5 (I2C时钟)
SIOD:  GPIO4 (I2C数据)
PWDN:  GPIO-1 (未连接)
RESET: GPIO-1 (未连接)
```

**摄像头规格：**
- 传感器：OV3660
- 分辨率：VGA (640x480)
- 像素格式：RGB565
- 时钟频率：20MHz

### LCD显示屏引脚 (ST7735 128x160)
```
MOSI:      GPIO20 (SPI数据线)
CLK:       GPIO19 (SPI时钟线)
DC:        GPIO47 (数据/命令选择)
RST:       GPIO21 (复位)
CS:        GPIO45 (片选)
Backlight: GPIO38 (背光控制)
```

**显示屏规格：**
- 驱动芯片：ST7735
- 分辨率：128x160
- 色彩深度：16位 (RGB565)
- SPI模式：0
- 镜像设置：X轴镜像、Y轴镜像

### 音频引脚 (Simplex模式)

#### 麦克风输入
```
MIC SCK:  GPIO2  (I2S时钟)
MIC WS:   GPIO1  (I2S字选择)
MIC DIN:  GPIO42 (I2S数据输入)
```

#### 喇叭输出
```
SPK BCLK: GPIO40 (I2S位时钟)
SPK LRCK: GPIO41 (I2S左/右时钟)
SPK DOUT: GPIO39 (I2S数据输出)
```

**音频规格：**
- 输入采样率：16kHz
- 输出采样率：24kHz
- 工作模式：Simplex (独立收发)

### 控制引脚
```
Boot Button:  GPIO0 (启动按钮)
Builtin LED:  GPIO48 (内置LED)
```

### 其他功能引脚
```
LAMP GPIO: GPIO14 (灯控引脚)
UART Console: GPIO44, GPIO43 (串口控制台)
```

## 硬件连接注意事项

### 摄像头连接
- 数据总线：D0-D7 必须按顺序连接
- I2C控制：SIOC/SIOD 用于摄像头配置
- 同步信号：VSYNC/HREF 用于帧同步

### 显示屏连接
- SPI总线：MOSI/CLK 必须连接正确
- 控制信号：DC/RST/CS 用于显示屏控制
- 背光控制：支持PWM调光

### 音频连接
- 麦克风：支持I2S数字麦克风
- 喇叭：支持I2S数字音频输出
- 注意：Simplex模式下收发通道独立

## 开发板技术规格

### 核心配置
- 主控芯片：ESP32-S3
- 主频：240MHz
- PSRAM：8MB
- Flash：BOYA芯片，QIO模式

### 内存分配
- 内部RAM：220KB + 21KB + 32KB
- PSRAM：8MB (用于图像缓存和帧缓冲)
- RTCRAM：7KB

### 网络功能
- WiFi：支持STA+AP双模式
- 配网：通过Web界面配置
- 默认AP：Xiaozhi-EEE5 (192.168.4.1)

## 调试信息

引脚调试功能已实现，在设备启动时会自动输出所有硬件引脚配置信息，便于硬件调试和故障排查。

---
*文档生成时间：2026年4月5日*  
*基于小智AI项目硬件配置*