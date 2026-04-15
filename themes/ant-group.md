# 蚂蚁集团主题 `ant-group`（默认）

品牌蓝，来自 Ant Design 设计系统。

## Logo

Logo 显示规则：
- 检测到**子品牌**（支付宝、花呗等）→ 双 Logo：集团 ＋ 子品牌
- **其他所有情况（默认）** → 双 Logo：蚂蚁集团 ＋ 支付宝
- 例外：内容明确为**集团层面**（投资者关系、集团战略、ESG报告等）→ 单 Logo：仅集团

双 Logo 路径：
- 深色页：`./themes/logos/ant-group-white.png` ＋ 分隔线 ＋ `./themes/logos/alipay-white.png`
- 白色页：`./themes/logos/ant-group-color.png` ＋ 分隔线 ＋ `./themes/logos/alipay-color.png`

单 Logo 路径：
- 深色页：`./themes/logos/ant-group-white.png`
- 白色页：`./themes/logos/ant-group-color.png`

其他子品牌（明确提及时）：
- mybank：`./themes/logos/mybank-white.png` / `./themes/logos/mybank-color.png`

## CSS

```css
:root {
    --primary:       #1677FF;
    --primary-dark:  #0950D9;
    --primary-light: #4096FF;
    --primary-pale:  #E6F4FF;
    --primary-dim:   rgba(22, 119, 255, 0.12);
    --cover-bg:      linear-gradient(125deg, #0A3DA8 0%, #1263EA 35%, #2B8FFF 65%, #5AB6FF 100%);
    --section-bg:    linear-gradient(135deg, #0B2F8A 0%, #1554AD 50%, #1677FF 100%);
    --red:           #E8380D;
    --green:         #52C41A;
    --orange:        #FA8C16;
}
.slide-section { background: linear-gradient(135deg, #0B2F8A 0%, #1554AD 50%, #1677FF 100%) !important; }
.slide-qa      { background: linear-gradient(125deg, #0A3DA8 0%, #1263EA 35%, #2B8FFF 65%, #5AB6FF 100%) !important; }
```
