下面是它现在实际能做的事，以及哪些是「代码支持但还没配好」。
达人发现与入库
- CSV 批量导入，带预览、去重、重复来源标记
- 公开搜索接入（Google CSE / SerpApi），可预览搜索结果再入库
- Prompt 抓取任务：生成可直接丢给 [$instagram-dm-outreach](C:\\Users\\Administrator\\.codex\\skills\\instagram-dm-outreach\\SKILL.md) 的提示词 + plan 文件，并列出历史 run 进度、可删除
- 达人网站识别器：读取公开网页 / link-in-bio，抓昵称、粉丝、邮箱、头像、标签
- 合规采集器：粘贴 Google 结果、HTML、URL 列表，提取 instagram.com/handle 和 @handle
- Hashtag 助手：自动填充、批量打开前 4 个标签页、复制标签
- Instagram Graph API 补全：拉头像、粉丝数、简介、网站
候选池 CRM
- 按状态 / 等级 / 关键词筛选，10 种排序
- 置顶、删除、删除全部（带 JSON 备份）
- 卡片显示评分、等级、粉丝、互动率、合作阶段标签
- 一键导出 CSV
评分与诊断
- 8 维度评分（粉丝区间、互动率、内容匹配、人群匹配、品牌安全、合作信号、可联系性、资料完整度），0-100 分 + S/A/B/C/D 分级
- 分数拆解条、手动检查清单、下一步建议（nextAction）
建联队列
- 从当前筛选结果按评分逐个处理
- 今日目标计数、进度条、今日已触达统计
- 打开主页、复制 DM、标记已发送进下一条、跳过、停止
- 只打开页面和复制文案，发送始终由你手动点
文案与回复
- DM / 邮件 / follow-up 三套文案，支持模板选择、语气、长度、切入点
- 建联文案优化器：按达人画像自动推荐模板和产品卖点
- 回复处理助手：本地规则分类对方意图（有兴趣 / 要报价 / 只要寄样 / 拒绝等）并生成回复草稿
- 模型接口：可接 OpenAI 兼容或 CPA 代理，配置存本地，可测试连通性
合作履约追踪
- 建联时间、跟进日期、合作阶段、报价 JPY、合作产品
- 样品状态、样品成本、收货人、邮编、地址、电话、订单号、物流单号
- 发货 / 收货时间、视频进度、帖子链接、优惠码、订单数、收入
- 自动算总成本、收入、ROI
任务看板
- 今日跟进、超期未跟、已回复待处理、报价待确认、资料待补全、待寄样、运输中、待拍摄、待发布，点击即筛选
当前实际状态
- 库里 37 个达人，全部 new / D 级，平均分 38，可建联 0
- Instagram API 配的是 Instagram Login token，只能补授权账号自己，补不了候选池里的达人（要换 Facebook/Page token + META_IG_USER_ID）
- Google CSE 和 SerpApi 都没配，公开搜索按钮点了会提示未配置
- 文案模型 base_url / name / key 为空，所以现在走本地规则文案，AI 生成不可用
简单说：发现、打分、CRM、队列、文案、履约全链路都在，但抓取和 AI 这两块的外部接口还没配，而且候选池里的达人缺粉丝数和联系方式，所以评分普遍偏低。要我用 [$instagram-dm-outreach](C:\\Users\\Administrator\\.codex\\skills\\instagram-dm-outreach\\SKILL.md) 补一批真实达人数据吗？

# SOSOVE Creator Outreach

Instagram and TikTok creator outreach workspaces for the SOSOVE Japan workflow.

The project is a private operator tool for candidate management, CSV import, public-search connectors, outreach copy drafting, reply handling, and partnership tracking. It does not log in to social accounts, scrape hidden APIs, or send unattended bulk DMs.

## Panels

- Instagram: `http://127.0.0.1:8796/`
- TikTok: `http://127.0.0.1:8796/tiktok/`
- Health check: `http://127.0.0.1:8796/api/health`

Instagram and TikTok use separate data files and endpoints.

## Install On Another Computer

Requirements:

- Git
- Python 3.11 or newer

Clone and start:

```powershell
git clone https://github.com/sosoveooo-bit/creator-outreach-desk.git
cd creator-outreach-desk
Copy-Item .env.example .env
python -m instagram_creator_outreach.server --host 0.0.0.0 --port 8796
```

On macOS or Linux:

```bash
git clone https://github.com/sosoveooo-bit/creator-outreach-desk.git
cd creator-outreach-desk
cp .env.example .env
python3 -m instagram_creator_outreach.server --host 0.0.0.0 --port 8796
```

The application uses the Python standard library only, so there is no `pip install` step for the core server.

## Docker

Create `.env` first:

```bash
cp .env.example .env
docker compose up -d --build
```

Change the public port when needed:

```env
PANEL_PORT=8796
```

Persistent candidate data is stored in `./data` on the host.

## Environment Variables

Start with [.env.example](.env.example). The most common integrations are:

```env
GOOGLE_CSE_API_KEY=
GOOGLE_CSE_CX=
SERPAPI_API_KEY=

META_ACCESS_TOKEN=
META_IG_USER_ID=

OUTREACH_COPY_MODEL_PROVIDER=cpa
OUTREACH_COPY_MODEL_BASE_URL=
OUTREACH_COPY_MODEL_NAME=
OUTREACH_COPY_MODEL_API_KEY=
OUTREACH_COPY_MODEL_AUTH_MODE=bearer
```

The model gateway is OpenAI-compatible. CPA proxy mode uses the `/chat/completions` endpoint and bearer authentication by default.

Keep `.env` private. The repository intentionally ignores `.env`, runtime data, logs, CSV exports, and candidate JSON files.

## Tests

```powershell
python -m unittest discover -s instagram_creator_outreach/tests -v
python -m unittest discover -s tiktok_creator_outreach/tests -v
```

## VPS Notes

The Docker command publishes the service directly on the selected port. If the VPS firewall is enabled, allow that port. For public internet exposure, place the panel behind an access-control layer such as a VPN, firewall allowlist, or reverse proxy with authentication.
