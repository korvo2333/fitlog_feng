# FitLog

移动端健身/营养记录 PWA，数据同步到 Google Sheet（通过 Google Apps Script 后端）。

## 迁移到新表 —— 操作清单

### 1. 后端 Apps Script（新表已自带一份复制的脚本）

打开新表 → **扩展程序 → Apps Script**，检查代码里获取表格的方式：

- 若用 `SpreadsheetApp.getActiveSpreadsheet()` → **无需改动**，自动指向新表。
- 若用 `SpreadsheetApp.openById('旧ID')` → 把 ID 换成新表：
  `1hPt0Xskr2RY365W5WK8_XfTAT272WUB9fQB4WFZ11vk`

然后**重新部署**（拿到新地址的关键步骤）：
1. 右上角 **部署 → 新建部署**
2. 类型 = **Web 应用**
3. 执行身份 = **我**；访问权限 = **任何人**
4. 部署 → 授权 → 复制结尾为 `/exec` 的 Web 应用地址

> 注意：每次改代码后，用「管理部署 → 编辑 → 版本：新版本」更新即可，地址不变。

### 2. 前端 index.html

打开 `index.html`，找到（约在 `<script>` 顶部）：

```js
const API = 'PASTE_YOUR_NEW_EXEC_URL_HERE';
```

把引号内替换成第 1 步的新 `/exec` 地址。其余不动。

### 3. 发布到 GitHub + GitHub Pages

1. GitHub → **New repository**（如 `fitlog`，Public）
2. **Add file → Upload files** → 上传 `index.html`（和本 README）→ Commit
3. **Settings → Pages** → Source 选 `Deploy from a branch` → 分支 `main` / 目录 `/ (root)` → Save
4. 等 1~2 分钟，页面地址形如 `https://<用户名>.github.io/fitlog/`
5. 手机浏览器打开该地址，即可「添加到主屏幕」当 App 用

## 后端需要支持的接口（若要从零重写 Apps Script 时参考）

前端通过 GET `?action=...` 调用，返回 JSON `{ ok: true, data: ... }`：

- `getCommonFoods` → 常用食物列表 `[{name,kcal,protein,fat,carb}]`
- `addCommonFood`（name,kcal,protein,fat,carb）
- `getExercises`（bodypart）→ `[{name}]`
- `addExercise`（bodypart,name）
- `getLastWorkout`（exercise）→ `{date,weight,reps,sets}`
- `addWorkout`（date,bodypart,exercise,weight,sets,reps,feeling）
- `deleteWorkout`（date,exercise）
- `getTodayWorkout`（date）→ `[{bodypart,exercise,weight,sets,reps,feeling}]`
- `addFood`（date,name,protein,fat,carb,kcal）
- `deleteFood`（date,name）
- `getTodayFood`（date）→ `[{name,protein,fat,carb,kcal}]`

## 设置

- 每日营养目标（热量/蛋白质/脂肪/碳水）与 Anthropic API Key 存在浏览器本地（localStorage），不上传。
- API Key 仅用于「拍照 AI 识别营养」，可选。
