# 🎈 THE BALLOON GAMBIT · 气球模拟风险任务

> 一个用于实验心理学课堂演示的经典风险决策实验，单文件部署，开箱即用。

---

## 📖 关于本实验

**气球模拟风险任务（Balloon Analogue Risk Task, BART）** 由 Lejuez 等人于 2002 年提出，是测量个体真实风险倾向的经典行为范式。被试通过反复决策"再充一次气"还是"立即收取收益"，研究者由此可量化个体在不确定性下的风险偏好。该范式与吸毒、酗酒、危险驾驶、青少年问题行为等多种现实风险行为均有显著相关，是临床与发展心理学研究中应用最广泛的风险决策任务之一。

本程序以 **编辑设计 / 包豪斯（Editorial Bauhaus）** 风格构建——奶油底色、衬线大字、三原色（红/黄/蓝）几何视觉，旨在打破传统心理学实验程序的"实验室冷感"，提升被试参与度。

---

## 🎮 实验设计

### 流程

```
欢迎页（输入被试编号）
    ↓
任务说明页（含三种气球类型预览）
    ↓
Block 1（15 个气球）
    ↓
中场休息页
    ↓
Block 2（15 个气球）
    ↓
结果汇总页（含数据导出）
```

### 气球类型规则

| 气球 | 颜色 | 最大充气次数 | 平均爆炸临界 | 单次充气收益 | 类型特征 |
| --- | --- | --- | --- | --- | --- |
| **🔴 红色** | `#D8483A` | 8 | ~4.5 | +¥1 | 高风险 · 易爆炸 · 收益低 |
| **🟡 黄色** | `#F2B829` | 32 | ~16.5 | +¥1 | 中等风险 · 风险与收益均衡 |
| **🔵 蓝色** | `#2C5FA8` | 128 | ~64.5 | +¥1 | 低风险 · 不易爆炸 · 高收益 |

> 每个气球的具体爆炸临界点 *k* 在 [1, max] 中均匀随机抽取。当本轮充气次数达到 *k*，气球立即爆炸，本轮收益归零。被试事先不知晓上述参数。

**实验参数：**

* 起始账户余额：¥0（仅通过充气收取获得）
* 总试次：30 次（分两个 Block，每 Block 15 次，每色 5 个）
* 每个 Block 内气球顺序独立随机洗牌（Fisher-Yates 算法）
* 每次充气积累 ¥1 潜在收益，"收取"将潜在收益转入总余额

---

## ✨ 功能特性

* **零依赖**：单个 `index.html` 文件，仅引用 Google Fonts，无需安装任何依赖
* **被试编号**：欢迎页支持输入被试 ID，自动写入导出数据
* **气球动画**：SVG 气球随充气次数平滑膨胀（`scale ∝ √pumps`），并伴有轻微摆动
* **🔊 音效合成**：使用 Web Audio API 实时合成（无需音频文件）——
  * **充气声**：短促"噗呲"气流，音高随充气次数递增以增强紧张感
  * **爆炸声**：高频咔嚓 + 噪声爆裂 + 低频轰鸣的三层叠加
  * **静音开关**：右上角可一键切换，或按 `M` 键快速切换
* **爆炸特效**：14 片彩色碎片向四周飞散 + 同色冲击波 + 屏幕震动 + 大写"Pop!"字
* **收取动画**：金额浮字向上飘出 + 余额数字更新
* **实时 HUD**：顶部信息栏 5 格——被试编号、试次进度（含进度条）、Block、总余额、上轮结果
* **侧边监控**：左侧实时显示当前充气数与潜在收益，右侧显示当前类型与近 6 次结果记录
* **两阶段设计**：Block 1 / Block 2 之间有中场页，可对比前后期策略变化
* **结果汇总**：实验结束后自动生成总览面板、分色统计卡片与两幅条形图
* **CSV 导出**：一键导出完整逐试次数据，包含每次充气的精细时间戳，可直接用 Excel / R / SPSS / Python 分析
* **键盘快捷键**：`Space` / `P` 充气，`Enter` / `C` 收取，`M` 静音切换
* **响应式布局**：桌面 / 笔记本 / 平板均可正常使用

---

## 📊 数据格式

点击结果页的 **"Export Data · 导出 CSV"** 按钮，将下载一个 UTF-8（含 BOM）的 CSV 文件，文件名格式为：

```
BART_<participant_id>_<datetime>.csv
```

CSV 包含 **三大区块**：

### 1️⃣ 主表（每行对应一次试次）

| 字段 | 说明 |
| --- | --- |
| `participant_id` | 被试编号 |
| `trial` | 试次序号（1–30） |
| `block` | 所属阶段（1 或 2） |
| `balloon_color` | 气球颜色（red / yellow / blue） |
| `breakpoint` | 该气球真实爆炸临界点（仅供研究者分析） |
| `pumps` | 本次充气总次数 |
| `exploded` | 是否爆炸（1 = 爆炸 / 0 = 主动收取） |
| `earned` | 本次实际入账收益（爆炸时为 0） |
| `balance_after` | 本次后总余额 |
| `trial_duration_ms` | 本试次耗时（毫秒） |
| `rt_ms_since_start` | 距实验开始的总毫秒数 |

### 2️⃣ 汇总表（按颜色聚合）

`color`, `n_trials`, `n_popped`, `n_collected`, **`adj_avg_pumps`**, `total_pumps`, `total_earned`

> **adj_avg_pumps**（Adjusted Average Pumps）：未爆炸气球的平均充气次数，是 BART 的**核心风险指标**，被广泛用于个体风险倾向的量化比较。

### 3️⃣ 充气精细记录

每一次"充气"按键的时间戳记录（`trial`, `pump_no`, `t_ms_since_start`），可用于反应时分析、决策动力学建模、序列依赖性研究等。

---

## 📁 文件结构

```
.
├── README.md
└── index.html    # 全部代码：HTML + CSS + JavaScript，约 2,200 行，含详细中文注释
```

---

## 🚀 快速开始

### 方式 1：本地直接打开

下载 `index.html`，双击即可在浏览器中运行。

### 方式 2：本地服务器（推荐）

```bash
# 任意一种方式
python3 -m http.server 8000
# 或
npx serve
```

然后访问 `http://localhost:8000`。

### 方式 3：免费在线部署

直接上传到 [Vercel](https://vercel.com) / [Netlify](https://netlify.com) / GitHub Pages 即可。

---

## 🛠️ 自定义与二次开发

如果您具有基础的 HTML 与 JavaScript 知识，可轻松修改实验参数。用任意文本编辑器打开 `index.html`：

### 修改实验参数

定位到 `<script>` 块开头的 `CONFIG` 对象：

```javascript
const CONFIG = {
  TOTAL_BALLOONS:     30,    // 总试次数
  BALLOONS_PER_BLOCK: 15,    // 每个 Block 的试次数
  PER_BLOCK_PER_COLOR: 5,    // 每个 Block 每种颜色的气球数
  POINTS_PER_PUMP:    1,     // 每次充气的潜在收益
  INFLATE_GROWTH:     0.085, // 气球膨胀速率
  INFLATE_MAX_SCALE:  2.4,   // 气球最大缩放
  OUTCOME_DELAY_MS:   1500,  // 结果展示时长
};
```

### 修改气球类型

定位到紧随其后的 `BALLOON_TYPES` 对象，可调整每种气球的爆炸概率上限：

```javascript
const BALLOON_TYPES = {
  red:    { max: 8,   color: '#D8483A', name: '红色气球', cn: '红' },
  yellow: { max: 32,  color: '#F2B829', name: '黄色气球', cn: '黄' },
  blue:   { max: 128, color: '#2C5FA8', name: '蓝色气球', cn: '蓝' },
};
```

### 修改主题颜色

定位到 `<style>` 块开头的 `:root` CSS 变量区域，全局替换主题色：

```css
:root {
  --bg:      #EDE6D6;   /* 奶油背景 */
  --ink:     #1A1814;   /* 主文字 */
  --red:     #D8483A;   /* 红色气球 */
  --yellow:  #F2B829;   /* 黄色气球 */
  --blue:    #2C5FA8;   /* 蓝色气球 */
  --green:   #4A8B5C;   /* 收取成功 */
  ...
}
```

### 单 Block 模式

如果您不需要中场休息，可将 `BALLOONS_PER_BLOCK` 设为与 `TOTAL_BALLOONS` 相等。或在 `nextTrial()` 函数中删除 Block break 触发条件。

---

## 🖥️ 浏览器兼容性

支持所有现代浏览器（Chrome / Firefox / Safari / Edge）。不支持 IE。

推荐分辨率：≥ 1280×720。在 1024px 以下宽度会自动切换到单栏布局。

---

## 📚 参考文献

Lejuez, C. W., Read, J. P., Kahler, C. W., Richards, J. B., Ramsey, S. E., Stuart, G. L., Strong, D. R., & Brown, R. A. (2002). Evaluation of a behavioral measure of risk taking: The Balloon Analogue Risk Task (BART). *Journal of Experimental Psychology: Applied, 8*(2), 75–84. <https://doi.org/10.1037/1076-898X.8.2.75>

Lejuez, C. W., Aklin, W. M., Zvolensky, M. J., & Pedulla, C. M. (2003). Evaluation of the Balloon Analogue Risk Task (BART) as a predictor of adolescent real-world risk-taking behaviours. *Journal of Adolescence, 26*(4), 475–479. <https://doi.org/10.1016/S0140-1971(03)00036-8>

Pleskac, T. J., Wallsten, T. S., Wang, P., & Lejuez, C. W. (2008). Development of an automatic response mode to improve the clinical utility of sequential risk-taking tasks. *Experimental and Clinical Psychopharmacology, 16*(6), 555–564. <https://doi.org/10.1037/a0014245>

---

## 🤝 实验联动

本项目的设计与文档结构参照了 [Iowa-Gambling-Task](https://github.com/conglinxiumu-ops/Iowa-Gambling-Task) 仓库，两者可作为实验心理学课堂中**风险决策范式**的姊妹工具一同使用。

---

## 📄 许可证

本项目采用 **MIT License** 开源，您可以自由地使用、修改和分发，无论是用于教学演示还是学术研究。

---

<div align="center">

**🎈 THE BALLOON GAMBIT**

*Made for the curious minds of Experimental Psychology.*

</div>
