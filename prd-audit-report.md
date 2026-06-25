# PRD 审计报告 — 2026-06-26 03:20

## 一、PRD（revision 193）与线上页面的关键不匹配

| # | 不匹配项 | PRD现状 | 线上实际 | 影响 |
|---|----------|---------|----------|------|
| 1 | **ADR-005：M08暂缓** | ⏸️ 暂缓（ADR-005："暂不实现"） | ✅ 已实现（20人团队卡片+点击详情+虚位以待） | **严重**：智能体会误判M08不可编辑 |
| 2 | **ENGINE模块（四驾马车）** | 不存在 | ✅ 已实现（2×2卡片+AIGC闭环链，data-module=M05） | **严重**：智能体不知道这个模块的存在和边界 |
| 3 | **模块映射表** | "11个模块，7已实现，2暂缓（M05/M08）" | 实际12个模块（ENGINE新增），M08已实现，M05仍暂缓但M05被ENGINE复用slot | **高**：映射表与实际完全不一致 |
| 4 | **SYS.ONLINE状态栏** | PRD定义了完整样式（背景、padding、字号等） | `display:none`（彻底隐藏） | **中**：智能体可能重新实现已隐藏的元素 |
| 5 | **推广链接名称** | "5元Token" | "⚡ 模型服务" | **中**：智能体改回旧名 |
| 6 | **CSS动画体系** | PRD只提到scrollReveal | 实际有6个动画（scan-beam/statPulse/valueBreath/dividerPulse/vacantPulse/scanBeam） | **中**：智能体不知道这些动画的存在和命名空间 |
| 7 | **文本对齐** | PRD未指定 | vision/engine/loop-section全部左对齐（不是居中） | **低**：智能体可能改回居中 |
| 8 | **HTML模块顺序** | PRD骨架：NAV→HERO→TEAM | 实际：NAV→HERO→VISION→ENGINE→TEAM→PRODUCTS→KB→COMMUNITY→RECRUIT→FOOTER | **高**：智能体不知道模块间插入顺序 |
| 9 | **section-num隐藏** | PRD未提及 | `.section-num{display:none}` | **低**：智能体可能重新显示 |
| 10 | **头像黑边处理** | PRD未提及 | 原图边缘像素处理+CSS 4px border+inset shadow | **低**：智能体替换头像时可能用原始未处理版本 |
| 11 | **虚位以待卡片** | PRD未提及 | team-card-vacant+dashed边框+vacantPulse动画 | **中**：智能体可能删除 |

## 二、需要更新的PRD内容清单

### 必须更新（P0级）

1. **撤销ADR-005** → 改为"已启用"，更新callout内容
2. **新增ADR-006** → ENGINE模块定义（四驾马车+AIGC闭环）
3. **更新模块映射表** → 12模块，M08✅，ENGINE新增

### 建议更新（P1级）

4. **新增3.12 ENGINE模块规范** → 2×2卡片+闭环链+左对齐+CSS命名空间
5. **更新HTML骨架** → 反映真实模块顺序
6. **新增3.13守卫规范** → 防止其他智能体破坏现有结构

### 可选更新（P2级）

7. 更新CSS动画映射表（加入6个新增动画）
8. 更新推广链接名称（"5元Token"→"⚡ 模型服务"）
9. 记录SYS.ONLINE隐藏决策
10. 记录头像黑边处理方案

## 三、守卫规范建议（防破坏策略）

其他智能体运维时，必须遵守以下规则：

### 3.1 不可删除的结构标记

```html
<!-- ========== MODULE: X START/END ========== -->
```
这些标记是模块边界锚点，删除任何一个都会导致模块定位混乱。

### 3.2 CSS命名空间铁律

所有CSS选择器必须以 `.module-X` 开头：
- `.module-nav .status-bar{display:none}` ← 不可改为可见
- `.module-hero .scan-beam` ← 不可删除动画
- `.module-vision .vision-block{text-align:left}` ← 不可改回居中
- `.module-engine .loop-chain` ← 新模块，不可改对齐方式

### 3.3 已隐藏的元素清单（不可恢复）

| 元素 | CSS规则 | 隐藏原因 |
|------|---------|----------|
| SYS.ONLINE状态栏 | `.module-nav .status-bar{display:none}` | 用户要求不显示 |
| section-num标签 | `.section-num{display:none}` | 内部标记不应暴露 |
| vision-label | `.module-vision .vision-label{display:none}` | 同上 |
| recruit-tag | 同理 | 同上 |
| ft-label | `.module-footer .ft-label{display:none}` | 同上 |

### 3.4 已处理的素材（不可替换为原始版本）

- `assets/avatars/chenyufeng.webp` — 已做圆形遮罩+边缘暗色替换
- `assets/avatars/jkl.webp` — 同上
- `assets/avatars/liyuqiao.webp` — 同上

替换这些头像时，必须用同样方法处理边缘像素，否则黑边会重现。

### 3.5 模块间divider是结构元素

```html
<div class="divider"></div>
```
每个模块之间有divider，这是页面节奏控制元素，不可删除。divider自带脉冲动画(`dividerPulse`)。

### 3.6 推广链接命名

导航栏和页脚的推广链接固定显示为"⚡ 模型服务"，不可改回"5元Token"或任何其他名称。
