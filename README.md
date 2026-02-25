# AI Calendar Data

节假日数据仓库，通过 jsDelivr CDN 分发给 AI Calendar 应用。

## 目录结构

```
ai-calendar-data/
├── holidays/           # 节假日 JSON 数据（按国家/年份）
│   ├── CN/
│   │   ├── 2024.json
│   │   ├── 2025.json
│   │   └── 2026.json   # 中国节假日 + 调休
│   ├── HK/
│   ├── TW/
│   ├── US/
│   └── ...
├── origin/             # 原始数据（可选，用于备份或转换）
├── subscriptions/      # iCal 订阅文件 + 公共日历目录
│   ├── catalog.json
│   ├── cn_holidays.ics
│   └── us_holidays.ics
└── README.md
```

## 部署与 CDN 分发

### 方式一：GitHub + jsDelivr（推荐）

1. **创建 GitHub 仓库**：`lanbingtech/ai-calendar-data`
2. **推送代码**：将本仓库内容 push 到 `main` 分支
3. **自动分发**：jsDelivr 会自动从 GitHub 拉取，无需额外配置

访问格式：
```
https://cdn.jsdelivr.net/gh/lanbingtech/ai-calendar-data@main/holidays/{country}/{year}.json
```

示例：
```
https://cdn.jsdelivr.net/gh/lanbingtech/ai-calendar-data@main/holidays/CN/2026.json
```

### 方式二：自建 CDN 或国内镜像

若需国内加速，可将仓库同步到自建 CDN 或使用 ghproxy 等镜像：

1. 克隆本仓库到你的服务器或镜像站
2. 确保目录结构不变：`holidays/CN/2026.json` 等
3. 在 AI Calendar 的 `config/*.json` 中配置 `CALENDAR_DATA_CDN_URL` 指向你的 CDN 根地址

### 更新流程

1. 修改 `holidays/CN/{year}.json` 等文件
2. 提交并 push 到 GitHub
3. jsDelivr 通常在几分钟内更新缓存；若需立即生效，可在 URL 加版本号如 `@main` 或使用 purge 接口

## 数据格式

### 中国节假日 JSON（isOffDay 格式）

支持两种结构，**推荐使用对象格式**（便于按日期查找）：

**对象格式**（key 为日期）：
```json
{
  "2026-01-01": {"date":"2026-01-01","name":"元旦","isOffDay":true},
  "2026-02-14": {"date":"2026-02-14","name":"春节调休","isOffDay":false}
}
```

**数组格式**（兼容旧版）：
```json
[
  {"date":"2026-01-01","name":"元旦","isPublicHoliday":true,"isWorkday":false}
]
```

**isOffDay 格式字段说明**：
- `date`: 日期 (YYYY-MM-DD)
- `name`: 节日名称（母语，如中文）
- `isOffDay`: `true`=放假（显示「休」），`false`=调休补班（显示「班」）

### 其他国家（数组格式）

```json
[
  {"date":"2026-01-01","name":"New Year's Day","localName":"元旦","isPublicHoliday":true,"isWorkday":false}
]
```

- `isPublicHoliday`: 法定假日
- `isWorkday`: 调休补班（仅中国等有调休的地区使用）

## 维护流程

### 中国调休数据

中国的调休安排每年由国务院发布，通常在前一年 11-12 月公布。

维护步骤：
1. 等待国务院发布下一年节假日安排
2. 更新 `holidays/CN/{year}.json`，使用 isOffDay 格式
3. 提交并 push 到 main 分支
4. jsDelivr 自动更新，App 下次拉取即可生效

### 其他国家

其他国家的数据由 Nager.Date API 作为备源，CDN 数据用于补充或覆盖。
