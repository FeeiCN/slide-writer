# 蚂蚁集团主题 `ant-group`（默认）

品牌蓝，来自 Ant Design 设计系统。

## Logo

**默认使用蚂蚁集团 + 支付宝双 Logo**（无论是否明确提到支付宝）：

- 深色页：`logo-antgroup-white.png` ＋ 分隔线 ＋ `logo-alipay-white.png`
- 白色页：`logo-antgroup-blue.png` ＋ 分隔线 ＋ `logo-alipay-blue.png`

仅在用户明确说明"只用集团 logo"或场景明确不涉及支付宝时，才退回单 logo。

其他子品牌 logo（仅在用户明确提及时使用）：
- mybank：`./logos/logo-mybank-white.png` / `./logos/logo-mybank-color.png`

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
