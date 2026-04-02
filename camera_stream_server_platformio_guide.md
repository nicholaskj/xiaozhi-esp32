# Camera Stream Server - PlatformIO 移植指南

## 📁 项目结构

```
camera_stream_server/
├── include/
│   └── camera_stream_server.h
├── camera_stream_server.cc
├── CMakeLists.txt
└── README.md
```

## 📋 PlatformIO 项目配置

### 1. 创建 PlatformIO 项目

```bash
# 创建新项目
pio project init --board esp32-s3-devkitc-1

# 或者手动创建 platformio.ini
```

### 2. platformio.ini 配置

```ini
[env:esp32-s3]
platform = espressif32
board = esp32-s3-devkitc-1
framework = espidf
monitor_speed = 115200

; 依赖库
lib_deps = 
    espressif/esp32-camera@^2.0.4
    espressif/esp-http-server@^1.0.0

; 构建配置
build_flags = 
    -D CONFIG_ESP32_CAMERA_SUPPORT=y
    -D CONFIG_HTTPD_WS_SUPPORT=y

; 分区表（如果需要）
board_build.partitions = no_ota.csv
```

### 3. 目录结构

```
你的PlatformIO项目/
├── lib/
│   └── camera_stream_server/     ← 复制组件到这里
│       ├── include/
│       │   └── camera_stream_server.h
│       └── camera_stream_server.cc
├── src/
│   └── main.cpp                  ← 你的主程序
├── platformio.ini
└── ...
```

## 🔧 移植步骤

### 步骤1：复制组件文件

将 `camera_stream_server` 文件夹复制到 PlatformIO 项目的 `lib/` 目录下。

### 步骤2：修改主程序

```cpp
// src/main.cpp
#include <Arduino.h>
#include "camera_stream_server.h"
#include "esp_camera.h"

// 摄像头配置（根据你的硬件调整）
camera_config_t camera_config = {
    .pin_pwdn = -1,
    .pin_reset = -1,
    .pin_xclk = 15,
    .pin_sccb_sda = 4,
    .pin_sccb_scl = 5,
    .pin_d7 = 16,
    .pin_d6 = 17,
    .pin_d5 = 18,
    .pin_d4 = 12,
    .pin_d3 = 10,
    .pin_d2 = 8,
    .pin_d1 = 9,
    .pin_d0 = 11,
    .pin_vsync = 6,
    .pin_href = 7,
    .pin_pclk = 13,
    .xclk_freq_hz = 20000000,
    .ledc_timer = LEDC_TIMER_0,
    .ledc_channel = LEDC_CHANNEL_0,
    .pixel_format = PIXFORMAT_JPEG,
    .frame_size = FRAMESIZE_VGA,
    .jpeg_quality = 12,
    .fb_count = 1
};

CameraStreamServer* stream_server = nullptr;

void setup() {
    Serial.begin(115200);
    
    // 初始化摄像头
    esp_err_t err = esp_camera_init(&camera_config);
    if (err != ESP_OK) {
        Serial.printf("Camera init failed with error 0x%x\n", err);
        return;
    }
    
    // 初始化流服务器
    stream_server = new CameraStreamServer();
    if (!stream_server->Start()) {
        Serial.println("Failed to start stream server");
    } else {
        Serial.println("Camera stream server started");
    }
}

void loop() {
    // 主循环可以处理其他任务
    delay(1000);
}
```

### 步骤3：适配 Board 类（如果需要）

如果 PlatformIO 项目中没有 `Board` 类，需要修改 `camera_stream_server.cc` 中的相关代码：

```cpp
// 在 camera_stream_server.cc 中修改
// 替换 Board::GetInstance().GetCamera() 调用

// 修改前：
auto camera = Board::GetInstance().GetCamera();

// 修改后（添加全局变量）：
extern bool camera_available = true;  // 在main.cpp中定义

// 然后修改检查逻辑：
if (camera_available) {
    // 摄像头可用
}
```

## ⚙️ 配置选项

### 摄像头参数配置

在 `platformio.ini` 中可以通过构建标志配置：

```ini
build_flags = 
    -D CAMERA_RESOLUTION=FRAMESIZE_VGA
    -D CAMERA_QUALITY=12
    -D STREAM_PORT=80
```

### 网络配置

```cpp
// WiFi 连接
#include <WiFi.h>

const char* ssid = "你的WiFi名称";
const char* password = "你的WiFi密码";

void connectToWiFi() {
    WiFi.begin(ssid, password);
    while (WiFi.status() != WL_CONNECTED) {
        delay(1000);
        Serial.println("Connecting to WiFi...");
    }
    Serial.println("Connected to WiFi");
    Serial.print("IP Address: ");
    Serial.println(WiFi.localIP());
}
```

## 🚀 快速开始示例

### 完整的 PlatformIO 主程序

```cpp
#include <Arduino.h>
#include <WiFi.h>
#include "camera_stream_server.h"
#include "esp_camera.h"

// WiFi 配置
const char* ssid = "你的WiFi";
const char* password = "你的密码";

// 摄像头配置（ESP32-S3 Breadcompact 开发板）
camera_config_t camera_config = {
    .pin_pwdn = -1,
    .pin_reset = -1,
    .pin_xclk = 15,
    .pin_sccb_sda = 4,
    .pin_sccb_scl = 5,
    .pin_d7 = 16,
    .pin_d6 = 17,
    .pin_d5 = 18,
    .pin_d4 = 12,
    .pin_d3 = 10,
    .pin_d2 = 8,
    .pin_d1 = 9,
    .pin_d0 = 11,
    .pin_vsync = 6,
    .pin_href = 7,
    .pin_pclk = 13,
    .xclk_freq_hz = 20000000,
    .ledc_timer = LEDC_TIMER_0,
    .ledc_channel = LEDC_CHANNEL_0,
    .pixel_format = PIXFORMAT_JPEG,
    .frame_size = FRAMESIZE_VGA,
    .jpeg_quality = 12,
    .fb_count = 1
};

CameraStreamServer* stream_server = nullptr;

void setup() {
    Serial.begin(115200);
    
    // 连接WiFi
    WiFi.begin(ssid, password);
    while (WiFi.status() != WL_CONNECTED) {
        delay(1000);
        Serial.println("Connecting to WiFi...");
    }
    Serial.print("IP Address: ");
    Serial.println(WiFi.localIP());
    
    // 初始化摄像头
    esp_err_t err = esp_camera_init(&camera_config);
    if (err != ESP_OK) {
        Serial.printf("Camera init failed with error 0x%x\n", err);
        return;
    }
    
    // 启动流服务器
    stream_server = new CameraStreamServer();
    if (stream_server->Start()) {
        Serial.println("Camera stream server started successfully");
    }
}

void loop() {
    delay(1000);
}
```

## 🔍 调试和故障排除

### 常见问题

1. **摄像头初始化失败**
   - 检查引脚配置是否正确
   - 确认摄像头模块电源正常
   - 检查摄像头模块是否兼容

2. **HTTP服务器启动失败**
   - 检查端口是否被占用
   - 确认WiFi连接正常
   - 检查内存分配

3. **视频流不显示**
   - 检查浏览器控制台错误信息
   - 确认IP地址正确
   - 检查防火墙设置

### 调试输出

启用串口调试信息：

```cpp
// 在 setup() 中添加
Serial.setDebugOutput(true);
```

## 📊 性能优化

### 降低分辨率提高帧率

```cpp
camera_config.frame_size = FRAMESIZE_QVGA;  // 320x240
camera_config.jpeg_quality = 8;            // 降低质量
```

### 调整缓冲区大小

```cpp
camera_config.fb_count = 2;  // 双缓冲区
```

## 📚 参考资料

- [ESP32-Camera 文档](https://github.com/espressif/esp32-camera)
- [ESP-HTTP-Server 文档](https://docs.espressif.com/projects/esp-idf/en/latest/esp32/api-reference/protocols/esp_http_server.html)
- [PlatformIO 文档](https://docs.platformio.org/)

---

**现在你的摄像头流服务器组件已经准备好移植到 PlatformIO 项目了！** 🎉