# Fronter产品搬运 + 导航栏下拉菜单 实施计划

## 一、背景分析

### Fronter产品结构（中文站更详细）
| 分类 | 中文名 | 英文名 | 产品数 |
|------|--------|--------|--------|
| ID:2  | 战术服 | Tactical Suit | 7 |
| ID:3  | 军警制服 | Military/Police Uniforms | 8 |
| ID:4  | 制服 | Uniform | 2 |
| ID:7  | 蛙服 | Frog Suit | 3 |
| ID:8  | 战术服(子) | Tactical Suit (sub) | 4 |
| ID:11 | 军服 | Military Uniform | 6 |
| ID:13 | 羽绒服 | Down Jacket | 1 |
| ID:18 | 棉服 | Cotton Jacket | 2 |
| ID:23 | 冲锋衣 | Tactical Jacket | 5 |
| ID:24 | 狩猎服装 | Hunting Clothing | 4 |
| ID:6  | 户外休闲服 | Outdoor Clothing | 2 |

**去重后总产品数：29个**
**产品图片：** 存放在 `omo-oss-image.thefastimg.com` CDN，每个产品1-3张图
**产品详情页：** JS动态渲染，数据在 `data-imglist` 属性中

### 当前和兴网站结构
- 已有分类：vests(防弹衣)、helmets(头盔)、plates(防弹插板)、shields(防弹盾牌)、uniforms(制服)、combat-boots(战靴)、tactical-backpack(背包)
- 已有产品：约128个md文件（防弹装备为主）
- 导航栏：平铺4个链接（首页/产品中心/关于/联系），无下拉

---

## 二、导航栏改造方案

### 当前问题
- Products只有一个链接，进入后所有分类平铺
- 分类越来越多后会非常混乱

### 改造目标
- Products链接悬停时自动下拉显示所有分类
- 分类支持子分类（如：服装鞋靴 → 军服/战术服/羽绒服/棉服/冲锋衣/蛙服）
- 鼠标移动到子分类上平滑展开
- 移动端保持汉堡菜单

### 导航结构设计
```
Products（悬停下拉）
├── Bulletproof Equipment（防弹装备）
│   ├── Bulletproof Vests（防弹衣）
│   ├── Ballistic Helmets（防弹头盔）
│   ├── Ballistic Plates（防弹插板）
│   └── Ballistic Shields（防弹盾牌）
├── Military & Police Uniforms（军警制服）
│   ├── Military Uniforms（军服）
│   ├── Tactical Suits（战术服）
│   ├── Frog Suits（蛙服）
│   └── Security Uniforms（安保服）
├── Tactical Clothing（战术服装）
│   ├── Tactical Jackets（冲锋衣）
│   ├── Down Jackets（羽绒服）
│   ├── Cotton Jackets（棉服）
│   └── Hunting Clothing（狩猎服装）
├── Clothing & Footwear（服装鞋靴）
│   └── Combat Boots（作战靴）
├── Tents & Backpacks（帐篷背包）
│   └── Tactical Backpacks（战术背包）
├── Night Vision（夜视仪）
└── Military Vehicles（军用车辆）
```

### 技术方案
1. **hugo.toml** — 增加带parent的二级菜单配置（Hugo支持 `parent` 参数）
2. **header.html** — 改造为支持多级下拉的导航模板
3. **CSS** — 添加 dropdown 样式（纯CSS hover实现，无需JS）

---

## 三、产品搬运方案

### 数据源
- **中文信息**：`zh.fronter-group.com/products_list/{id}.html`
- **英文信息**：`www.fronter-group.com/products_list/{id}.html`
- **图片**：`omo-oss-image.thefastimg.com` CDN（可直链引用或下载到本地）

### 搬运策略
1. **用Python爬虫脚本**批量获取所有29个产品的：
   - 产品名称（中英文）
   - 产品图片URL（每个1-3张）
   - 所属分类
   - 产品描述（如有）
   - 规格参数（如有）

2. **图片处理**：
   - 下载到 `static/images/products/fronter/` 目录
   - 产品主图作为卡片展示图

3. **生成Hugo md文件**：
   - 中文：`content/zh/products/{category}/{产品名}.md`
   - 英文：`content/en/products/{category}/{产品名}.md`
   - front matter包含：title, description, category, images, specs

4. **分类映射**（Fronter → 和兴网站）：
   - 军警制服/军服/制服/安保服 → uniforms（军警制服）
   - 战术服/蛙服 → tactical-suits（战术服）
   - 冲锋衣/羽绒服/棉服/狩猎服/户外服 → tactical-clothing（战术服装）

### 执行步骤
1. ✅ 分析fronter页面结构
2. 写Python爬虫脚本批量抓取29个产品数据
3. 下载产品图片到本地
4. 生成Hugo md文件（中英文）
5. 改造导航栏为多级下拉菜单
6. 更新hugo.toml菜单配置
7. 本地测试 → git push部署

---

## 四、风险评估

| 风险 | 等级 | 应对 |
|------|------|------|
| fronter WAF封IP | 中 | curl + 自定义UA，已验证可绕过 |
| 产品图片版权 | 低 | 杨总说先原样搬，后续审核 |
| 分类过多导致下拉太长 | 低 | 用二级菜单分组，限制宽度 |
| 移动端下拉体验 | 中 | 移动端用accordion折叠，不搞hover |

## 五、预计工作量

- 导航栏改造：2-3小时
- 产品数据抓取+图片下载：1小时
- md文件生成：30分钟
- 测试+部署：30分钟
- **总计约4-5小时**

## 六、执行顺序

**先改导航栏，再搬产品** — 因为新增的分类需要导航栏先支持
