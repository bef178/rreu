# FPV Drone Tips

桨叶 - 机型
- 31mm - 12(65)
  - HummingBird V4 1S
- 40mm - 16(75)
  - Carbonfly 75 2S
  - Firefly16 1S
  - Flylens 75 V1.3 2S
- 45mm - 18(80)
  - Firefly18 1S
  - Meteor75 Pro 1S
- 51mm - 20
  - Flylens 85 V1.3 2S
- 63mm - 25
  - CarbonFly25 V3 4S
  - Bee25 4S
  - Mario Mini25 3S

飞行模式
- Angle/Horizon/Acro 对应 自稳/半自稳/手动
- Airmode 使油门归零时仍尝试保持飞机姿态，也使得飞行器落地时弹跳
  - 可舍弃 Horizon，令 Acro+Airmode 替代之

人眼
- 人眼正常视力角分辨约1'，极限约0.5'
- 精细辨识：中央凹(fovea)视角范围约 1-1.5°，提供最高视觉分辨率，负责颜色识别和日间精细视力
- 清晰辨识：视角范围约 5–10°，识别形状、文字
- 有效辨识：视角范围约 30°，识别大致信息如颜色、形状、运动等
- 超过 30° 为周边视野(余光)
- 双眼瞬时视野范围：水平 FOV ≈ 120°，垂直 ≈ 60°

视网膜屏
- 屏幕距离为 L 毫米时，如果要求人眼分辨不出单个像素，则最小像素密度为
  - $1px / (sin(1/60/180*\pi) * Lmm)$
    - $(3437.74681927/L)ppm$
    - $(87318.76920936/L)ppi$
  - 显示器离眼约 60cm，此值为 $145.53ppi$
  - 手机屏离眼约 30cm，此值为 $291.06ppi$
  - fpv眼罩离眼约 150mm，此值为 $582.13ppi$
    - 4.3" 5:3 屏幕实际水平视角为 34.68°，需求分辨率为 2147x1288
    - 如果需求实际水平视角 155°，则屏宽 $1353.21mm$。这太夸张了
  - 一般认为成年人的明视距离是25cm，此值为 $349.28ppi$
  - 如果屏幕为 1920x1080 5.5"，合 400.53ppi，距离不应小于 218.0mm

朗视特008D pro
  - 4.3" 800x480 5:3 95x54mm 93.66x56.19mm 约 216.97ppi
  - 32G
  - 3.7v 2000mAH
  - MicroUSB
  - 155x144x113mm

模拟视频制式
- 模拟摄像头(如RunCam Nano2)通常输出模拟 CVBS 信号，有PAL / NTSC 两种制式
  - 有效分辨率 PAL 为 720x576 像素，NTSC 为 720x480 像素
- NTSC 的 525 线 → 去掉场逆程 45 线 → 有效 480 线
  - ITU-R BT.601 规定采样率 13.5 MHz → 每行 858 样点（含消隐）→ 有效 720 样点
  - 最终得到数字帧：720x480 像素，4:3 幅型，隔行 60 Hz（29.97 fps）
- PAL 的 625 线 → 去掉场逆程 49 线 → 有效 576 线
  - 水平方向受「6 MHz 视频带宽」限制
  - ITU-R BT.601 规定采样率 13.5 MHz → 每行 720 样点（含 16 像素消隐） → 有效 720 样点
  - 最终数字帧 720x576i，4:3 幅型，隔行，50 Hz

OSD芯片
- MAX7456/AT7456E等OSD芯片将屏幕划成30x16网格，
  - 单字符点阵为12x18，任何大于此分辨率的细节都会被抽稀
    - 颜色2bit，00=透明 01=白色 02=黑色 11=保留(透明)
    - https://github.com/betaflight/betaflight-configurator/tree/master/resources/osd/2
    - https://www.reddit.com/r/TinyWhoop/comments/1gdtzyt/adding_custom_glyphs_to_betaflights_osd/?tl=zh-hans
    - https://github.com/Knifa/MAX7456-Font-Tools
    - https://github.com/bri3d/mcm2img
    - https://github.com/ascended/MAX7456FontEditor

ISM 频段
  - 模拟：A-B-E-F 区总宽 200 MHz（5645-5945 MHz），单信道常用 20 MHz 间隔。
  - 数字：DJI/Walksnail 用 5.725-5.850 GHz（125 MHz 宽）。
  - 全球 ISM：5725–5875 MHz → 150 MHz
  - 中国大陆：5725–5850 MHz → 125 MHz
