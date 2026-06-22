# - 兩輪自平衡車 -
 基於STM32L452RET6微控制器開發，實現了自平衡、速度控制功能的兩輪自平衡車


# - 系統架構 -
  ### 硬體架構
  <img width="1707" height="952" alt="硬體架構圖" src="./System Architecture/硬體架構圖.png" />
  
  ### 控制架構
  <img width="1707" height="952" alt="流程圖" src="./System Architecture/流程圖.png" />
  
# - 硬體清單與電路 -
 ### 硬體清單
 | 元件名稱 | 型號/規格 | 數量 | 主要用途說明 |
| :--- | :--- | :--- | :--- |
| **微控制器 (MCU)** | STM32L452RET6 (Cortex-M4) | 1 | 負責姿態數據解析、PID 計算、馬達控制 |
| **慣性測量單元 (IMU)**| MPU6050 (三軸加速度+三軸陀螺儀) | 1 | 偵測小車當前的傾角（Pitch）與角速度，用於直立平衡 |
| **紅外線感測器** | TCRT5000 | 2 | 用於黑線循跡  |
| **馬達驅動模組** | TB6612FNG | 1 | 雙路直流馬達驅動晶片 |
| **麵包板與電源** | MB102 雙路電源供應模組 + 18650 電池盒 | 1 | 提供 5V/3.3V 電源分配 |

 ### 電路圖


# - 引腳配置 -
| 引腳編號 | CubeMX 標籤 / 功能 | 目標模組與腳位 | 功能描述 |
| :--- | :--- | :--- | :--- |
| **PA0** | `GPIO_Output` | TB6612 AIN1 | 左輪方向控制 |
| **PA1** | `GPIO_Output` | TB6612 AIN2 | 左輪方向控制 |
| **PA5** | `GPIO_Output` | TB6612 STBY | 驅動模組致能 (Standby) |
| **PA6** | `TIM3_CH1` | TB6612 Bureau PWMA | 左輪速度控制 (PWM) |
| **PA9** | `I2C1_SCL` | MPU-6050 SCL | 姿態感測器時鐘線 |
| **PA10**| `I2C1_SDA` | MPU-6050 SDA | 姿態感測器數據線 |
| **PA15**| `TIM2_CH1` | TB6612 PWMB | 右輪速度控制 (PWM) |
| **PB0** | `GPIO_Output` | TB6612 BIN1 | 右輪方向控制 |
| **PB1** | `GPIO_Output` | TB6612 BIN2 | 右輪方向控制 |
| **PC2** | `TRACK_LEFT` (GPIO_Input) | 左 TCRT OUT | 左紅外線循跡輸入 |
| **PC3** | `TRACK_RIGHT` (GPIO_Input)| 右 TCRT OUT | 右紅外線循跡輸入 |


```mermaid
graph TD
    %% 能源分配
    Power[5. 能源分配 MB102 + 18650] -->|5V 電源| IMU
    Power -->|5V 電源| Track
    Power -->|3.3V/5V 電源| MCU[1. 控制大腦 STM32L452RETx]
    Power -->|7.4V-8.4V & 5V| Drive

    %% 訊號流向
    IMU[2. 姿態感知 MPU-6050] -->|姿態數據 I2C| MCU
    Track[4. 環境偵測 TCRT5000 x2] -->|數位訊號 GPIO| MCU
    MCU -->|PWM & 邏輯訊號| Drive[3. 動力驅動 TB6612FNG]
