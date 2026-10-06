## 🖥️ 第一阶段（第 1–6 周）：计算机基础快速唤醒

**核心课程：**

| 课程 | 学校/平台 | 说明 |
|---|---|---|
| **CS50: Introduction to Computer Science** | Harvard / edX | 全网评价最高的 CS 导论，2026 年已支持中文字幕，免费旁听，证书可选付费 |
| **数据结构与算法** | 浙江大学 / 中国大学 MOOC | 陈越老师主讲，中文，有作业和讨论区，证书免费 |
| **Python 快速复习** | 任意平台 / 直接刷 LeetCode | 你有基础，不需要系统课，直接写代码唤醒手感即可 |

**目标：** 能流畅写 Python，理解时间复杂度和基本算法思想。不需要在这阶段花太多时间，2–3 周足够。


## 📡 第二阶段（第 7–18 周）：信号与系统 + 神经科学（最核心缺口）

### 📶 信号与系统（第一优先级）

| 课程 | 学校/平台 | 说明 |
|---|---|---|
| **Signals and Systems** | MIT / MIT OpenCourseWare | Oppenheim 亲授，经典中的经典，完全免费，有完整讲义、作业和考试。这是最“硬”的信号与系统课 |
| **信号与系统** | 清华大学 / 学堂在线 | 中文授课，覆盖傅里叶、拉普拉斯、Z 变换和滤波器设计，明确标注是研究生入学考试的主要先修课。适合作为 MIT 课程的辅助理解 |

**建议：** 先跟清华中文课建立直觉，再用 MIT OCW 刷题和深化。这门课至少要投入 **8–10 周**。

### 🧠 神经科学导论（第二优先级）

| 课程 | 学校/平台 | 说明 |
|---|---|---|
| **Fundamentals of Neuroscience (MicroBachelors)** | Harvard / edX | 三门课系列：神经元的电学特性 → 神经元与网络 → 大脑。有 college credit 选项，是神经科学 MOOC 里最系统的 |
| **Medical Neuroscience** | Duke / Coursera | 研究生级别的人体神经解剖与功能，偏医学方向，适合想往临床/康复 BCI 走的同学 |

**建议：** 选 Harvard 的系列，3 门课约 6–8 周，每周 5–8 小时。Duke 的课作为选补。


## 📊 第三阶段（第 19–30 周）：DSP + 机器学习 + 计算神经科学

信号与系统学完后，这三门课可以并行推进，互相支撑。

### 🎛️ 数字信号处理（DSP）

| 课程 | 学校/平台 | 说明 |
|---|---|---|
| **Digital Signal Processing 1: Basic Concepts and Algorithms** | EPFL / edX | 从离散信号定义开始，覆盖傅里叶分析、滤波器设计、采样、插值、量化。推荐具备微积分和线代基础，会提供 Python notebook 示例 |
| **Digital Signal Processing** | IIT KGP / NPTEL | Prof. T. K. Basu 主讲，工程圈认可度高，便宜，适合作为 EPFL 的补充 |

### 🤖 机器学习

| 课程 | 学校/平台 | 说明 |
|---|---|---|
| **Machine Learning Specialization** | Stanford / Coursera（吴恩达） | 你有算法基础，可以快速过。重点放在监督学习、正则化、神经网络基础 |
| **Deep Learning Specialization** | DeepLearning.AI / Coursera | 重点看 CNN 和 RNN 部分，为后面 EEGNet 打基础 |

### 🧮 计算神经科学（强烈推荐）

| 课程 | 学校/平台 | 说明 |
|---|---|---|
| **Computational Neuroscience** | University of Washington / Coursera | 讲解神经编码、神经解码、尖峰神经元信息表示、PCA、贝叶斯估计。**神经解码模块直接对应 BCI 的核心问题**，对申请和项目都非常有用 |


## 🧪 第四阶段（第 31–42 周）：BCI 项目 + 申请材料

**推荐项目：BCI Competition IV 2a 运动想象分类**

- 数据集：BCI Competition IV 2a（9 被试，22 通道，4 类运动想象，250 Hz）
- 工具链：MOABB + Braindecode + MNE-Python
- 基线：CSP + LDA
- 进阶：EEGNet（有现成的 PyTorch 实现可以直接参考）
- 评估：被试内交叉验证，报告 kappa 和准确率

**产出：** GitHub 仓库 + README + 混淆矩阵 + 拓扑图 + 一段 3 分钟讲解视频