# 🌳 种树消消乐 - 完整开发文档

## 📱 项目简介

一款结合消除玩法和植树主题的微信小游戏。玩家通过三消游戏获得能量，用能量种植虚拟树木，打造自己的森林。

---

## 🎮 游戏玩法

### 核心循环
```
三消消除 → 获得能量 → 浇灌树木 → 树木成长 → 解锁新树种 → 继续游戏
```

### 消除规则
- 8x8 网格，6种植物元素
- 横向或纵向连续3个及以上相同元素可消除
- 消除获得分数和能量
- 每关有步数限制（30步）
- 达到目标分数即可过关

### 树木系统
- 每棵树有5个成长阶段
- 消耗能量浇水促进成长
- 6种树木逐步解锁
- 完成的树木计入森林统计

---

## 📂 项目结构

```
tree-match-game/
├── game.json                 # 小游戏配置文件
├── js/                       # 核心逻辑代码
│   ├── game-manager.js       # 三消游戏管理器
│   ├── tree-manager.js       # 树木成长管理器
│   └── ad-manager.js         # 广告管理器
├── pages/                    # 页面
│   ├── game/                 # 主游戏页面
│   │   ├── game.html         # 游戏界面
│   │   └── game.js           # 页面逻辑
│   ├── index/                # 首页（待开发）
│   └── forest/               # 森林页面（待开发）
├── assets/                   # 资源文件
│   ├── images/               # 图片资源
│   │   ├── elements/         # 游戏元素图标
│   │   ├── trees/            # 树木图标
│   │   └── ui/               # UI图标
│   └── audio/                # 音效
│       ├── match.mp3         # 消除音效
│       ├── success.mp3       # 成功音效
│       └── bgm.mp3           # 背景音乐
└── README.md                 # 本文档
```

---

## 🚀 快速开始

### 1. 环境准备

#### 必需工具
- **微信开发者工具**: [下载地址](https://developers.weixin.qq.com/miniprogram/dev/devtools/download.html)
- Node.js (可选，用于本地测试)

#### 申请微信小游戏
1. 注册微信小程序账号
2. 申请小游戏类目
3. 获取 AppID

### 2. 本地运行

#### 方法一：使用微信开发者工具
```bash
1. 打开微信开发者工具
2. 导入项目：选择 tree-match-game 文件夹
3. 填写 AppID
4. 点击"编译"运行
```

#### 方法二：浏览器测试（无广告功能）
```bash
# 直接打开 HTML 文件
open pages/game/game.html

# 或使用简单 HTTP 服务器
cd tree-match-game
python3 -m http.server 8000
# 访问 http://localhost:8000/pages/game/game.html
```

### 3. 配置广告

修改 `js/ad-manager.js` 中的广告位ID：

```javascript
this.adConfig = {
  rewardedVideoAdUnitId: 'adunit-YOUR_REAL_AD_UNIT_ID',  // 激励视频
  bannerAdUnitId: 'adunit-YOUR_REAL_AD_UNIT_ID',         // Banner
  interstitialAdUnitId: 'adunit-YOUR_REAL_AD_UNIT_ID'    // 插屏
};
```

**获取广告位ID**:
1. 登录[微信公众平台](https://mp.weixin.qq.com/)
2. 进入"流量主" → "广告管理"
3. 创建广告位，获取ID

---

## 💰 盈利配置

### 1. 激励视频广告

**场景**:
- 获得额外能量
- 游戏复活
- 加速树木成长

**实现**:
```javascript
adManager.showRewardedVideo((watched) => {
  if (watched) {
    // 用户看完广告，给予奖励
    currentEnergy += 100;
  }
}, 'energy');
```

### 2. Banner 广告

**位置**: 游戏底部

**实现**:
```javascript
// 显示
adManager.showBanner();

// 隐藏
adManager.hideBanner();
```

### 3. 插屏广告

**时机**: 关卡结束、返回首页

**实现**:
```javascript
adManager.showInterstitial();
```

### 4. 分享传播

**实现**:
```javascript
// 分享到好友/群
adManager.shareToFriend({
  title: '快来和我一起种树！',
  imageUrl: 'your_share_image_url',
  query: 'from=share'
});
```

---

## 🎨 美术资源需求

### 游戏元素 (6种)
- 🌰 种子 - 64x64 PNG
- 💧 水滴 - 64x64 PNG
- ☀️ 阳光 - 64x64 PNG
- 🍃 叶子 - 64x64 PNG
- 🌸 花朵 - 64x64 PNG
- 🌰 果实 - 64x64 PNG

### 树木阶段 (每种树5个阶段)
- 阶段0: 种子 - 128x128 PNG
- 阶段1: 发芽 - 128x128 PNG
- 阶段2: 小树苗 - 128x128 PNG
- 阶段3: 小树 - 128x128 PNG
- 阶段4: 大树 - 128x128 PNG

### UI 图标
- 能量图标 - 64x64 PNG
- 道具图标 - 64x64 PNG each
- 按钮背景 - 9-patch PNG

### 背景
- 游戏背景 - 1080x1920 PNG
- 森林背景 - 1080x1920 PNG

### 音效
- match.mp3 - 消除音效 (< 100KB)
- success.mp3 - 成功音效 (< 100KB)
- tree_grow.mp3 - 树木成长音效 (< 100KB)
- bgm.mp3 - 背景音乐 (< 500KB)

**资源规范**:
- 格式: PNG (透明背景), MP3
- 大小: 尽可能小（小游戏有4MB限制）
- 分辨率: 2x图（适配高分屏）

---

## 🔧 核心代码详解

### GameManager (游戏管理器)

**职责**: 三消游戏核心逻辑

**关键方法**:
```javascript
initGrid()           // 初始化网格
swapElements()       // 交换元素
checkMatches()       // 检查匹配
processMatches()     // 处理消除
fillGrid()           // 填充空缺
draw()               // 绘制游戏
```

**使用示例**:
```javascript
const gameManager = new GameManager(canvas);
gameManager.onLevelCompleteCallback = (score, energy) => {
  console.log('关卡完成！', score, energy);
};
gameManager.gameLoop();
```

### TreeManager (树木管理器)

**职责**: 树木种植和成长

**关键方法**:
```javascript
plantTree(type)       // 种植新树
waterTree(id, energy) // 浇水
getCurrentTree()      // 获取当前树
getTreeIcon(tree)     // 获取树图标
getForestStats()      // 获取森林统计
```

**使用示例**:
```javascript
const treeManager = new TreeManager();
const tree = treeManager.plantTree('pine');
treeManager.waterTree(tree.id, 50);
```

### AdManager (广告管理器)

**职责**: 广告和分享

**关键方法**:
```javascript
showRewardedVideo(callback, scene) // 显示激励视频
showBanner()                       // 显示Banner
hideBanner()                       // 隐藏Banner
showInterstitial()                 // 显示插屏
shareToFriend(options)             // 分享到好友
```

---

## 📊 数据存储

### 本地存储

使用 `wx.setStorageSync()` 和 `wx.getStorageSync()`:

```javascript
// 保存
wx.setStorageSync('treeData', {
  trees: this.trees,
  currentTreeId: this.currentTreeId
});

// 加载
const data = wx.getStorageSync('treeData');
```

### 云存储（可选）

使用微信云开发存储用户数据：

```javascript
wx.cloud.database().collection('users').add({
  data: {
    openid: userOpenId,
    trees: treeManager.trees,
    contribution: treeManager.getTotalContribution()
  }
});
```

---

## 🎯 功能扩展建议

### 阶段1 - MVP ✅
- [x] 三消核心玩法
- [x] 树木成长系统
- [x] 能量收集
- [x] 激励视频广告

### 阶段2 - 社交
- [ ] 好友排行榜
- [ ] 分享得能量
- [ ] 好友森林参观
- [ ] 赠送能量

### 阶段3 - 增值
- [ ] 每日签到
- [ ] 成就系统
- [ ] 道具商店
- [ ] VIP会员

### 阶段4 - 公益
- [ ] 虚拟树兑换真树
- [ ] 与环保组织合作
- [ ] 显示真实种树数量
- [ ] 植树证书

---

## 📈 数据分析建议

### 关键指标

1. **用户留存**
   - 次日留存率
   - 7日留存率
   - 30日留存率

2. **变现指标**
   - ARPU (平均每用户收入)
   - 广告展示率
   - 广告点击率
   - 内购转化率

3. **游戏指标**
   - 平均游戏时长
   - 关卡通过率
   - 树木完成数量

### 数据上报

```javascript
// 使用微信数据分析
wx.reportAnalytics('game_start', {
  level: gameManager.level
});

wx.reportAnalytics('tree_complete', {
  tree_type: tree.type
});

wx.reportAnalytics('ad_watched', {
  scene: 'energy'
});
```

---

## 🐛 常见问题

### Q1: 游戏在微信开发者工具中运行正常，但真机上无法显示？
**A**: 检查域名白名单配置。在小程序后台添加你的服务器域名。

### Q2: 广告无法加载？
**A**:
1. 确认已开通流量主
2. 检查广告位ID是否正确
3. 确认小游戏已上线（开发版无法显示真实广告）

### Q3: Canvas 绘制模糊？
**A**: 设置高分屏适配：
```javascript
const dpr = wx.getSystemInfoSync().pixelRatio;
canvas.width = canvas.width * dpr;
canvas.height = canvas.height * dpr;
ctx.scale(dpr, dpr);
```

### Q4: 触摸事件不响应？
**A**: 检查 canvas 的 `touch-action` CSS 属性：
```css
#gameCanvas {
  touch-action: none;
}
```

---

## 📝 上线流程

### 1. 测试阶段
- [ ] 在微信开发者工具中测试所有功能
- [ ] 真机调试（iOS + Android）
- [ ] 性能优化（帧率、内存）
- [ ] 适配不同屏幕尺寸

### 2. 审核准备
- [ ] 准备游戏截图（至少3张）
- [ ] 准备游戏简介（< 120字）
- [ ] 设置游戏分类和标签
- [ ] 准备隐私政策和用户协议

### 3. 提交审核
```
1. 登录微信公众平台
2. 版本管理 → 开发版本 → 提交审核
3. 填写审核信息
4. 提交审核申请
5. 等待审核（通常1-7天）
```

### 4. 发布上线
```
1. 审核通过后
2. 版本管理 → 审核版本 → 发布
3. 确认发布
4. 线上监控
```

---

## 🚀 优化建议

### 性能优化

1. **Canvas 优化**
   ```javascript
   // 使用离屏Canvas
   const offscreenCanvas = wx.createOffscreenCanvas();

   // 减少重绘
   if (dirty) {
     this.draw();
     dirty = false;
   }
   ```

2. **图片优化**
   ```javascript
   // 预加载图片
   const images = {};
   function preloadImages(urls) {
     urls.forEach(url => {
       const img = wx.createImage();
       img.src = url;
       images[url] = img;
     });
   }
   ```

3. **减少包体大小**
   - 压缩图片（TinyPNG）
   - 使用 SVG 替代 PNG
   - 音频转为低码率 MP3
   - 代码压缩混淆

### 用户体验优化

1. **引导新手**
   - 首次进入显示教程
   - 高亮可操作区域
   - 提示下一步操作

2. **反馈优化**
   - 添加音效
   - 消除动画
   - 树木成长动画
   - 粒子效果

3. **减少等待**
   - 预加载资源
   - 进度条显示
   - 后台数据请求

---

## 📞 技术支持

### 微信小游戏官方文档
- [开发文档](https://developers.weixin.qq.com/minigame/dev/guide/)
- [API文档](https://developers.weixin.qq.com/minigame/dev/api/)
- [社区论坛](https://developers.weixin.qq.com/community/minigame)

### 常用资源
- [Canvas教程](https://developer.mozilla.org/zh-CN/docs/Web/API/Canvas_API/Tutorial)
- [游戏开发指南](https://developers.weixin.qq.com/minigame/dev/guide/best-practice.html)

---

## 📜 开源协议

MIT License

---

## 🎉 祝你游戏大卖！

如有问题，欢迎提交 Issue 或联系作者。

**Good luck! 🚀**
