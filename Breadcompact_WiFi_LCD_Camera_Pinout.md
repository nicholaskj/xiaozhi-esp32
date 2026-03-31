# Breadcompact WiFi+LCD+Camera 开发板引脚说明

## 开发板概述
- **芯片**: ESP32-S3
- **功能**: WiFi + LCD显示 + 摄像头 + 音频
- **支持屏幕**: ST7735 (128x160), ST7789, ILI9341, GC9A01等多种型号

## 引脚分配总览

### 1. LCD显示引脚 (SPI接口)
| 功能 | GPIO引脚 | 说明 |
|------|----------|------|
| 背光控制 | GPIO38 | 背光PWM控制 |
| MOSI (数据线) | GPIO20 | SPI数据输出 |
| SCLK (时钟线) | GPIO19 | SPI时钟 |
| DC (数据/命令) | GPIO47 | 数据/命令选择 |
| RESET (复位) | GPIO21 | 屏幕复位 |
| CS (片选) | GPIO45 | SPI片选 |

### 2. 摄像头引脚 (DVP接口)
| 功能 | GPIO引脚 | 说明 |
|------|----------|------|
| D0 | GPIO11 | 数据位0 |
| D1 | GPIO9 | 数据位1 |
| D2 | GPIO8 | 数据位2 |
| D3 | GPIO10 | 数据位3 |
| D4 | GPIO12 | 数据位4 |
| D5 | GPIO18 | 数据位5 |
| D6 | GPIO17 | 数据位6 |
| D7 | GPIO16 | 数据位7 |
| XCLK | GPIO15 | 摄像头时钟 |
| PCLK | GPIO13 | 像素时钟 |
| VSYNC | GPIO6 | 垂直同步 |
| HREF | GPIO7 | 水平参考 |
| SIOC | GPIO5 | I2C时钟 |
| SIOD | GPIO4 | I2C数据 |
| PWDN | NC | 电源关闭(未使用) |
| RESET | NC | 复位(未使用) |

### 3. 音频引脚 (I2S接口)
| 功能 | GPIO引脚 | 说明 |
|------|----------|------|
| MIC_WS | GPIO1 | 麦克风字选择 |
| MIC_SCK | GPIO2 | 麦克风时钟 |
| MIC_DIN | GPIO42 | 麦克风数据输入 |
| SPK_DOUT | GPIO39 | 扬声器数据输出 |
| SPK_BCLK | GPIO40 | 扬声器位时钟 |
| SPK_LRCK | GPIO41 | 扬声器左右时钟 |

### 4. 控制引脚
| 功能 | GPIO引脚 | 说明 |
|------|----------|------|
| BOOT按钮 | GPIO0 | 启动/复位按钮 |
| 内置LED | GPIO48 | 状态指示灯 |
| 触摸按钮 | NC | 未使用 |
| 音量+按钮 | NC | 未使用 |
| 音量-按钮 | NC | 未使用 |

### 5. MCP测试引脚
| 功能 | GPIO引脚 | 说明 |
|------|----------|------|
| 灯控制 | GPIO14 | MCP协议测试用灯 |

## 屏幕配置参数

### ST7735 128x160 配置
```c
#ifdef CONFIG_LCD_ST7735_128X160
#define LCD_TYPE_ST7789_SERIAL
#define DISPLAY_WIDTH   128
#define DISPLAY_HEIGHT  160
#define DISPLAY_MIRROR_X true    // X轴镜像
#define DISPLAY_MIRROR_Y true    // Y轴镜像
#define DISPLAY_SWAP_XY false    // 不交换XY
#define DISPLAY_INVERT_COLOR false // 不反转颜色
#define DISPLAY_RGB_ORDER LCD_RGB_ELEMENT_ORDER_RGB // RGB顺序
#define DISPLAY_SPI_MODE 0       // SPI模式0
#endif
```

### 其他支持的屏幕类型
- ST7789 (240x320, 240x240, 170x320等)
- ILI9341 (240x320)
- GC9A01 (240x240)
- ST7796 (320x480)

## 硬件接口说明

### SPI总线配置
- **SPI主机**: SPI3_HOST
- **时钟频率**: 40MHz
- **DMA通道**: 自动选择
- **传输大小**: 屏幕分辨率 × sizeof(uint16_t)

### 摄像头参数
- **时钟频率**: 20MHz
- **接口**: DVP (Digital Video Port)
- **I2C地址**: 通过SIOC/SIOD配置

### 音频配置
- **采样率**: 输入16kHz / 输出24kHz
- **模式**: 单工模式(AUDIO_I2S_METHOD_SIMPLEX)
- **声道**: 单声道

## 使用注意事项

1. **引脚冲突**: 确保同一GPIO不被多个功能复用
2. **电源管理**: 注意LCD背光和摄像头的功耗
3. **时序要求**: SPI时钟频率需根据屏幕型号调整
4. **内存分配**: 摄像头和LCD显示需要较大内存空间

## 开发板特性

- 支持多种LCD屏幕，通过条件编译选择
- 集成摄像头和音频功能
- 支持WiFi网络连接
- 提供MCP协议接口用于设备控制
- 模块化设计，易于扩展

## 编译配置

在menuconfig中选择对应的屏幕类型：
```bash
idf.py menuconfig
# 进入 Component config → LCD → LCD panel → 选择对应型号
```

---
*最后更新: 基于项目配置文件分析*