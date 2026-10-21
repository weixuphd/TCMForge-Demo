# TCMForge（方证）· 网页测试版 v0.8（混淆静态演示）

「不公开源码、网页可访问」的浏览器演示：单页、纯前端、数据快照内置、无后端。

- 公开访问：`https://weixuphd.github.io/TCMForge-Demo/`
- 数据：`tcm_snapshot.json`（约 28MB，含 33,361 方剂 / 737,024 成分计数 / 22,191 靶点 / 21,561 药效，以及 AI-Ready 指标快照）
- 逻辑：编译并混淆后的 `bundle.js`（普通访问者读到的是不可读形式，不公布可读源码）
- 平台真源码（FastAPI 后端等）保存在**私有仓库**，不在此公开仓库出现。
- v0.8 新增「AI-Ready能力★」页签：能力指标清单、7 模态 / 25 格式、14 工具、6 类智能体、专病数据集与 100 亿 Token 语料构成。

## 目录

```
TCMForge-Demo/
├── publish/                 # ← 唯一需要发布到 GitHub Pages 的内容
│   ├── index.html            #   页面（不含源码逻辑，仅引用 bundle.js）
│   ├── bundle.js             #   压缩+混淆后的全部逻辑
│   └── tcm_snapshot.json     #   数据快照
└── src/                      # 开发源码（.gitignore 忽略，不推送到公开仓库）
    ├── app.js                #   纯逻辑（检索/证据链/孪生/AI规则）
    └── ui.js                 #   渲染层
```

## 发布步骤（一次性设置后才执行）

```bash
# 1) 一次性登录（浏览器出现设备码后到你手机/电脑确认）
gh auth login

# 2) 以 publish 内容创建公开仓库并启用 Pages
cd publish
git init -b main
git add index.html bundle.js tcm_snapshot.json
git commit -m "TCMForge web demo (obfuscated)"
gh repo create TCMForge-Demo --public --source=. --push

# 3) 启用 GitHub Pages（也可以用 gh api，或网页 Settings→Pages→Deploy from branch main）
#    https://github.com/weixuphd/TCMForge-Demo/settings/pages
#    → Branch: main, folder: /(root)，Save

# 4) 访问
#    https://weixuphd.github.io/TCMForge-Demo/
```

> 注意：`src/` 在 `.gitignore` 中被忽略，不会进入公开仓库；若要重新构建混淆包，见下节。

## 重新构建 bundle.js

```bash
cd TCMForge-Demo
cat src/app.js src/ui.js > _c.js
npx terser _c.js -c -m -o _m.js
npx javascript-obfuscator _m.js --output publish/bundle.js --rename-globals false
rm -f _c.js _m.js
```

## 本地预览

GitHub Pages 为 HTTPS，`fetch` 加载本地 JSON 需要走 HTTP：

```bash
cd publish && python3 -m http.server 8080
# 打开 http://127.0.0.1:8080
```

## 诚实提示

- 混淆 ≠ 加密：浏览器必须能执行代码，决心逆向的人理论上仍可还原，本方案达成的是「不公布**可读**源码」。
- 若希望访问者完全无法获取任何代码 / 数据，请改用「私有仓库 + 服务器部署真实 WebUI」路线（另见部署说明）。
- 数据为演示子集（精选精标数据 + COCONUT 样例 + 每日追踪记录），不含完整 73 万条天然产物大库与平台治理代码。