# Theme Index — Slide-Writer

Phase 0 主题识别用。确定公司后，Phase 4 只需读取 `themes/[id].md`。

---

## 主题识别规则

**自动识别**：从用户请求、署名、部门名称中提取关键词。先匹配子品牌表，再匹配集团表。未识别时使用默认（蚂蚁集团+支付宝蓝）。

识别逻辑：
1. 在「子品牌归属表」中查找关键词 → 得到「所属集团」和「子品牌 ID」
2. 在「集团主题表」中查找集团 → 得到对应主题文件 `themes/[id].md`
3. Logo 展示 = **集团 logo ＋ 分隔线 ＋ 子品牌 logo**（子品牌有 logo 文件时）

### 多品牌冲突处理

1. 用户明确指定主题名或公司名 → 直接采用。
2. 标题、署名、部门里出现的品牌 → 优先级高于正文提及的竞品。
3. 比较型内容（X vs Y）→ 使用演讲者所属公司主题；无法判断则退回默认蚂蚁蓝，并说明"含多品牌，已使用中性默认主题"。
4. 正文只是引用合作方/竞品 → 不切换主题。
5. 集团 + 子品牌同时出现 → 主题跟随集团，logo 按子品牌规则。

---

## 集团主题表

| 关键词（集团） | 主题 ID | 主题文件 |
|---|---|---|
| 蚂蚁集团、Ant Group、蚂蚁 | ant-group | themes/ant-group.md |
| 阿里巴巴、Alibaba、阿里 | alibaba | themes/alibaba.md |
| 腾讯、Tencent | tencent | themes/tencent.md |
| 字节跳动、ByteDance、字节 | bytedance | themes/bytedance.md |
| 美团、Meituan | meituan | themes/meituan.md |
| 京东、JD | jd | themes/jd.md |
| 百度、Baidu | baidu | themes/baidu.md |
| 华为、Huawei | huawei | themes/huawei.md |
| 小米、Xiaomi | xiaomi | themes/xiaomi.md |
| 网易、NetEase | netease | themes/netease.md |
| 滴滴、DiDi | didi | themes/didi.md |
| 微软、Microsoft | microsoft | themes/microsoft.md |
| 谷歌、Google | google | themes/google.md |
| 苹果、Apple | apple | themes/apple.md |

---

## 子品牌归属表

### 蚂蚁集团旗下
| 关键词 | 集团 ID | 子品牌 ID |
|---|---|---|
| 支付宝、Alipay | ant-group | alipay |
| 花呗、Huabei | ant-group | huabei |
| 借呗、Jiebei | ant-group | jiebei |
| 蚂蚁森林 | ant-group | ant-forest |
| 芝麻信用 | ant-group | sesame-credit |
| 网商银行、MYbank | ant-group | mybank |
| 蚂蚁公益基金会 | ant-group | ant-foundation |
| 余额宝 | ant-group | yuebao |

### 阿里巴巴旗下
| 关键词 | 集团 ID | 子品牌 ID |
|---|---|---|
| 淘宝、Taobao | alibaba | taobao |
| 天猫、Tmall | alibaba | tmall |
| 钉钉、DingTalk | alibaba | dingtalk |
| 饿了么、Eleme | alibaba | eleme |
| 优酷、Youku | alibaba | youku |
| 高德、Amap | alibaba | amap |
| 盒马、Hema | alibaba | hema |
| 阿里云、Alibaba Cloud | alibaba | aliyun |
| 菜鸟、Cainiao | alibaba | cainiao |
| 闲鱼、Xianyu | alibaba | xianyu |
| 1688 | alibaba | 1688 |

### 腾讯旗下
| 关键词 | 集团 ID | 子品牌 ID |
|---|---|---|
| 微信、WeChat | tencent | wechat |
| QQ | tencent | qq |
| 企微、企业微信、WeCom | tencent | wecom |
| 腾讯游戏 | tencent | tencent-games |
| 腾讯视频 | tencent | tencent-video |
| 腾讯云 | tencent | tencent-cloud |
| 腾讯音乐、TME | tencent | tme |
| 微信支付、WePay | tencent | wepay |

### 字节跳动旗下
| 关键词 | 集团 ID | 子品牌 ID |
|---|---|---|
| 抖音、Douyin | bytedance | douyin |
| TikTok | bytedance | tiktok |
| 飞书、Feishu、Lark | bytedance | feishu |
| 今日头条、Toutiao | bytedance | toutiao |
| 西瓜视频 | bytedance | xigua |
| 剪映、CapCut | bytedance | capcut |
| 火山引擎 | bytedance | volcengine |
| 番茄小说 | bytedance | fanqie |

### 美团旗下
| 关键词 | 集团 ID | 子品牌 ID |
|---|---|---|
| 美团外卖 | meituan | meituan-waimai |
| 大众点评、Dianping | meituan | dianping |
| 美团优选 | meituan | meituan-youxuan |
| 美团买菜、小象超市 | meituan | meituan-maicai |
| 摩拜、Mobike | meituan | mobike |
| 美团闪购 | meituan | meituan-shangou |

### 京东旗下
| 关键词 | 集团 ID | 子品牌 ID |
|---|---|---|
| 京东物流 | jd | jd-logistics |
| 京东健康 | jd | jd-health |
| 京东科技、京东金融 | jd | jd-tech |
| 京东云 | jd | jd-cloud |
| 京东工业 | jd | jd-industry |

### 百度旗下
| 关键词 | 集团 ID | 子品牌 ID |
|---|---|---|
| 爱奇艺、iQiyi | baidu | iqiyi |
| 文心一言、ERNIE | baidu | ernie |
| 百度地图 | baidu | baidu-map |
| 百度云、百度网盘 | baidu | baidu-cloud |
| 百度文库 | baidu | baidu-wenku |

### 华为旗下
| 关键词 | 集团 ID | 子品牌 ID |
|---|---|---|
| 荣耀、Honor | huawei | honor |
| 华为云 | huawei | huawei-cloud |
| 鸿蒙、HarmonyOS | huawei | harmonyos |
| 华为终端 | huawei | huawei-terminal |

### 小米旗下
| 关键词 | 集团 ID | 子品牌 ID |
|---|---|---|
| MIUI、澎湃OS | xiaomi | miui |
| 小米汽车 | xiaomi | xiaomi-car |
| 小米生态链 | xiaomi | xiaomi-eco |
| Redmi | xiaomi | redmi |

### 网易旗下
| 关键词 | 集团 ID | 子品牌 ID |
|---|---|---|
| 网易游戏 | netease | netease-games |
| 网易云音乐 | netease | netease-music |
| 网易邮箱、163邮箱 | netease | netease-mail |
| 有道、Youdao | netease | youdao |
| 网易严选 | netease | yanxuan |

### 滴滴旗下
| 关键词 | 集团 ID | 子品牌 ID |
|---|---|---|
| 花小猪 | didi | huaxiaozhu |
| 青桔单车 | didi | qingju |
| 滴滴货运 | didi | didi-cargo |
| 小桔充电 | didi | xiaoju-charge |

### 微软旗下
| 关键词 | 集团 ID | 子品牌 ID |
|---|---|---|
| Azure | microsoft | azure |
| Office、M365 | microsoft | office |
| Teams | microsoft | teams |
| GitHub | microsoft | github |
| LinkedIn | microsoft | linkedin |
| Copilot | microsoft | copilot |

### 谷歌旗下
| 关键词 | 集团 ID | 子品牌 ID |
|---|---|---|
| YouTube | google | youtube |
| Android | google | android |
| Chrome | google | chrome |
| Google Cloud | google | google-cloud |
| Gmail | google | gmail |
| Gemini | google | gemini |

### 苹果旗下
| 关键词 | 集团 ID | 子品牌 ID |
|---|---|---|
| iPhone | apple | iphone |
| Mac、macOS | apple | mac |
| iPad | apple | ipad |
| Apple Watch | apple | apple-watch |
| App Store | apple | app-store |
| iCloud | apple | icloud |

---

## Logo 文件索引

| 品牌 ID | 白色版 | 彩色版 | 状态 |
|---|---|---|---|
| ant-group | `./logos/logo-antgroup-white.png` | `./logos/logo-antgroup-blue.png` | 已有 |
| alipay | `./logos/logo-alipay-white.png` | `./logos/logo-alipay-blue.png` | 已有 |
| mybank | `./logos/logo-mybank-white.png` | `./logos/logo-mybank-color.png` | 已有 |
| tencent | `./logos/tencent-white.png` | `./logos/tencent-blue.png` | 已有 |
| alibaba | 无（深色页用彩色版 + `filter:brightness(0) invert(1)`） | `./logos/alibaba.png` | 已有 |
| 其余品牌 | — | — | 待补充 |

---

## 双 Logo 展示规则

核心原则：子品牌出现时，始终展示「集团 logo ＋ 分隔线 ＋ 子品牌 logo」。

> **重要：所有 logo 统一放在 `#globalLogoGroup`，禁止在任何 `<section>` 内写 logo 代码。**
> 深色/浅色切换由 CSS（`body.on-blue`）自动处理：`.logo-light` 在白底页显示，`.logo-dark` 在深色页显示。
> 具体 HTML 写法见 `SKILL.md` Step 3.2 ③。

### 情况一：集团 + 子品牌 logo 均存在
在 `#globalLogoGroup` 中使用 `class="logo-group-dual"`，各放 `.logo-light`（彩色版）和 `.logo-dark`（白色版）两张 img，中间加 `<span class="logo-divider"></span>`。

### 情况二：子品牌 logo 待补充，只展示集团 logo（无分隔线）
在 `#globalLogoGroup` 中使用 `class="logo-group-single"`，只放集团 logo 的 `.logo-light` 和 `.logo-dark`。

### 情况三：只有集团，展示集团 logo
同情况二。

### 情况四：均无 logo，省略所有 logo 元素
`<div id="globalLogoGroup" class="logo-group-single" style="display:none;"></div>`
