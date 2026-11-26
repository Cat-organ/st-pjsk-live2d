# st-pjsk-live2d

SillyTavern「PJSK 剧情」前端用的**素材包 + 页面源文件**。

播放器不是独立应用：酒馆里一条正则把 `<game>` 块换成一个小加载器，
加载器用 `$('body').load()` 把 `regex/pjsk-game.html`（Cloudflare Pages）拉进沙箱，
页面再去 GitHub raw 取这里的模型 / 背景 / 动作。

## 目录

| 路径 | 内容 |
|---|---|
| `live2d/` | Live2D 模型（moc3 / 贴图 / physics3），2044 个文件 |
| `motions/` | 动作与表情 motion3，11057 个文件 |
| `background/` | 背景 webp，319 张 |
| `fonts/` | 剧中字体（子集化） |
| `ui/` | 对话框 / 按钮 / 转场贴图 |
| `Profile/` | 菜单和地图事件用的头像 |
| `fx/` | 全息、转场特效贴图 |
| `models.json` `backgrounds.json` `model_manifest.json` | 素材索引（播放器按它找文件） |
| `player/bundle.js` | 播放器本体（pixi + cubism4） |
| `regex/` | **Cloudflare Pages 的部署目录**：`pjsk-game.html` + `_headers` |
| `酒馆正则.json` | 直接导入酒馆的正则（替换串指向上面的 Pages 地址） |

## 两个地址

- 素材（GitHub raw，页面里的 `assetBase`）：
  `https://raw.githubusercontent.com/Cat-organ/st-pjsk-live2d/main/`
- 页面（Cloudflare Pages）：
  `https://st-pjsk-live2d.pages.dev/pjsk-game.html`

## 注意

- **文件名大小写不能改**：GitHub raw 和 Cloudflare Pages 都是区分大小写的，
  Windows 本地不区分，改完在本地能跑、线上 404。
- `regex/_headers` 里那行 `Access-Control-Allow-Origin: *` 不能删：
  酒馆和 Pages 不同源，没有它 `$('body').load()` 会被浏览器拦掉。
- 换页面地址（换 Pages 项目 / 换域名）要同时改两处：
  `regex/pjsk-game.html` 里的 `assetBase`（`build_player.py --asset-base` 重建）
  和 `酒馆正则.json` 里 `replaceString` 的那条 URL。

## 重新构建页面

在开发工作区的 `game-renderer/` 下：

```bash
python tools/build_player.py --asset-base https://raw.githubusercontent.com/Cat-organ/st-pjsk-live2d/main/
cp dist-player/pjsk-game.html <本仓库>/regex/pjsk-game.html
```

`build_player.py` 会连 `assetBase`、`playerBundle` 和构建时间戳一起写进页面。
