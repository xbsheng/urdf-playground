# 黑马 GenkiPi 模拟器（urdf-playground）

在浏览器里加载 URDF 机器人模型做 3D 可视化，用键盘或鼠标控制各关节，并可通过 Web Serial 直连真实舵机臂。

- 在线体验：<https://urdf-playground.vercel.app/>
- 源码仓库：<https://github.com/xbsheng/urdf-playground>

## 功能

- 加载 `public/URDF/genkiarm.urdf` 及配套 STL 网格，three.js 实时渲染（阴影、反射地面、轨道相机）
- 键盘控制 6 个关节，鼠标点击按键帽或 `+` / `-` 同样可以操作，按住持续转动
- 速度滑块统一调节每次转动的步长
- 通过 Web Serial 连接真实机械臂（Feetech SCS 舵机，ID 1–6），仿真与实机同步动作
- 首次加载资源较多（约 55 MB），页面会显示下载进度
- 虚拟关节带限位检查，超出范围会提示且不执行

## 快速开始

```bash
pnpm install
pnpm dev        # 开发调试
pnpm build      # 产出 dist/
pnpm preview    # 预览构建结果
```

## 操作说明

键盘按键与关节的对应关系：

| 关节 | 按键 |
| --- | --- |
| 腰部旋转 | `1` / `Q` |
| 大臂控制 | `2` / `W` |
| 小臂控制 | `3` / `E` |
| 腕部控制 | `4` / `R` |
| 腕部旋转 | `5` / `T` |
| 爪子控制 | `6` / `Y` |

同一行的两个键方向相反，按住即持续转动，松开停止。控制面板里的绿色 `+`、红色 `-` 和按键帽都可以直接用鼠标按住，效果与按键盘完全一致；顶部的速度滑块（0.1–1）决定单次转动的角度。

## 连接真实机械臂

控制面板的「连接真实机械臂」按钮使用 Web Serial API 与舵机总线通信，采用相对位移方式：连接时先读取各舵机当前位置，之后每次按键只下发相对变化量，因此实机起始姿态不影响操作。

连接要求：

- Chrome / Edge 等支持 Web Serial API 的浏览器
- USB 转串口适配器已接到舵机总线
- 舵机 ID 为 1–6，依次对应 6 个关节
- 波特率固定 1,000,000，协议固定 SCS(1)

步骤：

1. 用支持的浏览器打开应用
2. 点击「连接真实机械臂」，在弹窗中选择正确的串口
3. 用键盘或鼠标控制，虚拟机械臂与实机同步动作

> 连接前请确认实机姿态与仿真一致，避免动作幅度过大造成损坏。

## 目录结构

```
index.html                      页面结构、样式与加载进度蒙层
index.js                        场景初始化、URDF/STL 加载与渲染循环
robotControls.js                键盘/鼠标控制、关节限位、Web Serial 舵机通信
robotConfig.js                  机型配置（预留，当前未接入）
public/URDF/                    模型与网格（genkiarm.urdf、meshes/*.stl）
feetech/                        Feetech SCS 舵机 SDK 参考实现与调试页面
```

## 技术栈

three.js、urdf-loader、Vite；实机通信基于 Web Serial API 与 Feetech SCS 协议。
