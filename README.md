# Template-STM32F103-V1.0

意法半导体 STM32F103 系列 MCU 的 VSCode + STM32Cube (GCC + CMake) 开发模板

## 📋 项目简介

本项目提供了一个基于 **STM32CubeIDE for VS Code** 插件 构建系统的 STM32F103 开发模板，支持在 macOS、Linux 和 Windows 平台上进行嵌入式开发。

### 主要特性

- ✅ VS Code 集成开发环境支持
- ✅ 使用 STM32CubeIDE for VS Code 插件
- ✅ CMake 构建系统，支持 Ninja 生成器
- ✅ 集成 CMSIS 和 STM32 HAL 驱动库
- ✅ 集成 FreeRTOS 实时操作系统
- ✅ 支持 Debug 和 Release 两种构建模式
- ✅ 支持 J-Link 和 ST-Link 调试
- ✅ 自动生成 HEX 和 BIN 文件

---

## 🛠️ 环境要求

### 必需软件

1. **STM32CubeIDE for VS Code**
   - 通过 VS Code 扩展安装 STM32CubeIDE for VS Code 插件


### 可选软件

- **VS Code** 及扩展：
  - Serial Monitor

---

## 📦 项目结构

```
Template-STM32F103-V1.0/
├── .vscode/              # VS Code 配置文件
│   ├── launch.json       # 调试配置
│   └── settings.json     # VS Code 设置
├── Core/                 # 核心代码
│   ├── Inc/              # 头文件
│   │   ├── gpio.h        # GPIO 配置
│   │   ├── main.h        # 主程序头文件
│   │   ├── stm32f1xx_hal_conf.h  # HAL 库配置
│   │   ├── FreeRTOSConfig.h      # FreeRTOS 配置
│   │   └── stm32f1xx_it.h        # 中断处理函数声明
│   └── Src/              # 源文件
│       ├── gpio.c        # GPIO 初始化代码
│       ├── main.c        # 主程序入口
│       ├── freertos.c    # FreeRTOS 初始化与任务创建
│       ├── stm32f1xx_hal_msp.c   # HAL MSP 初始化
│       ├── stm32f1xx_it.c        # 中断处理函数实现
│       ├── syscalls.c    # 系统调用实现
│       ├── sysmem.c      # 系统内存分配
│       └── system_stm32f1xx.c    # 系统初始化
├── Drivers/              # 驱动库
│   ├── CMSIS/            # ARM CMSIS 核心库
│   │   ├── Device/       # 设备特定文件
│   │   └── Include/      # CMSIS 头文件
│   └── STM32F1xx_HAL_Driver/     # STM32 HAL 驱动
│       ├── Inc/          # HAL 驱动头文件
│       └── Src/          # HAL 驱动源文件
├── Middlewares/          # 中间件
│   └── Third_Party/
│       └── FreeRTOS/     # FreeRTOS 内核和 CMSIS-RTOS2 适配
├── jlink/                # J-Link 调试辅助文件
│   └── STM32F103.svd     # STM32F103 外设寄存器描述文件
├── cmake/                # CMake 配置文件
│   ├── gcc-arm-none-eabi.cmake  # 工具链配置
│   └── starm-clang.cmake # Clang 工具链配置（可选）
├── CMakePresets.json     # CMake 预设配置
└── README.md             # 项目说明
```

## 🧩 STM32CubeMX 插件与自动化代码生成

本工程集成了 STM32CubeMX（通过 `.ioc` 文件和 `cmake/stm32cubemx/` 目录）自动化外设配置与代码生成流程，极大提升 STM32 工程开发效率。

### CubeMX 插件简介

- **STM32CubeMX** 是 ST 官方的图形化外设配置和代码生成工具。你可以通过 CubeMX 配置 MCU 型号、引脚、时钟、外设等，自动生成初始化代码。
- 本工程通过 `.ioc` 文件（如 `Template-STM32F103-V1.0.ioc`）保存硬件配置，**建议用 STM32CubeMX GUI 打开和编辑**。
- CubeMX 生成的代码会自动放入 `Core/`、`Drivers/` 等目录，CMake 构建系统会自动包含这些文件。

### CubeMX 使用流程

1. **用 STM32CubeMX 打开本项目的 `Template-STM32F103-V1.0.ioc` 文件**。
2. 在 CubeMX 中配置 MCU 型号、引脚、时钟、外设等。
3. 点击“生成代码”（Generate Code），CubeMX 会自动更新 `Core/`、`Drivers/` 相关代码。
4. 回到 VS Code，直接用 CMake/Ninja 构建，无需手动调整工程结构。

> ⚠️ **注意**：CubeMX 生成代码时不会覆盖你在 `User Code` 区域外的自定义代码，但建议所有用户代码都写在 `/* USER CODE BEGIN */ ... /* USER CODE END */` 区域内。

### CubeMX 与 CMake 集成说明

- `cmake/stm32cubemx/CMakeLists.txt` 自动收集 CubeMX 生成的源文件和头文件。
- 工程主 `CMakeLists.txt` 通过 `add_subdirectory(cmake/stm32cubemx)` 集成 CubeMX 代码。
- 你可以随时用 CubeMX 重新生成代码，然后直接用 VS Code + CMake 构建，无需手动维护源文件列表。

## ⏱️ FreeRTOS 支持说明

本工程已集成 `FreeRTOS`，并通过 STM32CubeMX 生成的 `Core/Src/freertos.c`、`Core/Inc/FreeRTOSConfig.h` 以及 `Middlewares/Third_Party/FreeRTOS/` 目录下的内核源代码来构建。

- `Core/Src/freertos.c` 包含 RTOS 初始化函数 `MX_FREERTOS_Init()`。
- `Core/Inc/FreeRTOSConfig.h` 用于配置 FreeRTOS 内核参数，如任务堆栈、时钟节拍、调度选项等。
- `Middlewares/Third_Party/FreeRTOS/` 包含 FreeRTOS 内核实现、CMSIS-RTOS2 适配层和 ARM_CM3 可移植层。
- 默认构建会自动包含 FreeRTOS 内核，无需额外手动添加源文件。

> 如果你希望在用户代码中添加任务、定时器或消息队列，请在 `freertos.c` 的 `USER CODE BEGIN` 区域中进行配置，并确保在 `main()` 中调用 `MX_FREERTOS_Init()` 后启动调度器。

---

## 🚀 快速开始

### 1. 克隆项目

```bash
git clone <repository-url>
cd Template-STM32F103-V1.0
```

### 2. 用 STM32CubeMX 配置外设并生成代码

1. 用 STM32CubeMX 打开 `Template-STM32F103-V1.0.ioc` 文件，配置 MCU 型号、引脚、时钟、外设等。
2. 点击“生成代码”（Generate Code），CubeMX 会自动更新 `Core/`、`Drivers/` 相关代码。
3. 用户代码请写在 `/* USER CODE BEGIN */ ... /* USER CODE END */` 区域，避免被覆盖。

### 3. 配置芯片型号（如需手动调整）

如需手动切换芯片型号，请编辑 `Drivers/CMSIS/Device/ST/STM32F1xx/Include/stm32f103xe.h` 或对应型号头文件，确保：

```c
#define STM32F103xE // 或你的实际型号
```

并确认 `Core/Inc/stm32f1xx_hal_conf.h` 中 HAL 配置：

```c
#define HAL_MODULE_ENABLED
#define HAL_GPIO_MODULE_ENABLED
// 根据需要启用其他模块...
```

### 4. 构建项目

推荐使用 CMake Presets：

```bash
cmake --preset=Debug
cmake --build build/Debug
# 或 Release
cmake --preset=Release
cmake --build build/Release
```

如遇 CMake 缓存错误，建议先删除旧的构建目录：

```bash
rm -rf build/Debug
cmake --preset=Debug
```

构建成功后，会在 `build/<BuildType>/` 目录下生成：
- `${CMAKE_PROJECT_NAME}.elf` 对应的 ELF 文件
- `${CMAKE_PROJECT_NAME}.hex` 对应的烧录镜像
- `${CMAKE_PROJECT_NAME}.bin` 对应的裸二进制文件
- `${CMAKE_PROJECT_NAME}.map` 对应的链接映射文件

### 5. 配置与启动调试

1. 确保 J-Link 驱动已正确安装，目标板连接到 J-Link，使用 SWD 调试。
2. 检查 `.vscode/launch.json` 配置项（如 deviceName/serverInterface/gdb/svdPath），其中 `svdPath` 建议指向 `jlink/STM32F103.svd`。
3. `jlink/STM32F103.svd` 用于在调试器的寄存器/外设视图中显示 STM32F103 的寄存器定义；缺失时通常不影响下载和单步，但会影响外设寄存器查看体验。
4. 本仓库内已包含可直接使用的文件：`jlink/STM32F103.svd`。
5. 如需重新获取或替换，可从本机 STM32Cube 安装目录复制：`~/Library/Application Support/stm32cube/packs/STMicroelectronics/stm32f1xx_dfp/1.2.0/SVD/STM32F103.svd`
6. 若后续 STM32Cube 升级导致版本目录变化，可在 `~/Library/Application Support/stm32cube/packs/STMicroelectronics/stm32f1xx_dfp/` 下查找最新版本目录中的 `SVD/STM32F103.svd`。
7. 常用快捷键
- `F5` - 启动调试
- `Shift+F5` - 停止调试
- `F9` - 切换断点
- `F10` - 单步跳过
- `F11` - 单步进入

---

### 常见问题

**问题 1**: 调试时连接失败或超时
**解决**: 确认 J-Link 固件和 SWD 连接是否正常，若必要可降低调试接口频率。

**问题 2**: 构建时找不到头文件或源文件
**解决**: 确认 CMakeLists.txt 中正确包含了相关目录和文件，特别是 CubeMX 生成的代码。

---

## 📝 开发指南

### 添加新源文件

在 `CMakeLists.txt` 中添加你的源文件：

```cmake
target_sources(${CMAKE_PROJECT_NAME} PRIVATE
    Hardware/Src/my_driver.c    # 添加你的文件
)
```

### 添加头文件路径

```cmake
target_include_directories(${CMAKE_PROJECT_NAME} PRIVATE
    ${CMAKE_SOURCE_DIR}/Your/New/Path    # 添加你的路径
)
```

### CubeMX 自动集成说明

如果你通过 CubeMX 生成了新外设代码，`cmake/stm32cubemx/CMakeLists.txt` 会自动收集所有 CubeMX 生成的源文件和头文件，无需手动维护。
如需添加自定义模块，建议放在 `Hardware/` 或其他自定义目录，并在主 `CMakeLists.txt` 里补充。

### 添加宏定义

```cmake
target_compile_definitions(${CMAKE_PROJECT_NAME} PRIVATE
    USE_MY_FEATURE=1
    DEBUG_MODE=1
)
```

---

## 📚 参考资源

- [STM32F103 数据手册](https://www.st.com/content/st_com/en/products/microcontrollers-microprocessors/stm32-32-bit-arm-cortex-mcus/stm32-mainstream-mcus/stm32f1-series/stm32f103.html)
- [STMicroelectronics 官网](https://www.st.com/)


---

## 📄 许可证

本项目中的驱动程序库遵循意法半导体官方许可协议。详见各源码文件开头的版权声明。

---

## 🤝 贡献

欢迎提交 Issue 和 Pull Request！

---

## 📮 联系方式

如有问题，请通过 GitHub Issues 反馈。
