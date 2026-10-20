<div align="center">

# YOUNG_WORKS

**AI Agent 技能与实践作品集 —— 把「链接」变成「可长期保存的内容」**

这里是我编写、维护并持续迭代的 AI Agent Skill 合集，目前收录 **2 个可直接安装使用的技能**，
分别解决「内容获取与结构化整理」和「社媒媒体直链解析」两条链路。

[![Skills](https://img.shields.io/badge/skills-2-4C8BF5?style=flat-square)](#技能一览)
[![Platforms](https://img.shields.io/badge/platforms-44%2B-8A2BE2?style=flat-square)](#media-link-resolver)
[![License](https://img.shields.io/badge/license-MIT-3DA639?style=flat-square)](./LICENSE)

</div>

---

## 这是什么

`YOUNG_WORKS` 不是一个传统意义的代码项目，而是一个 **Agent Skills 仓库**。

Skill（技能）是模块化的能力包：一个文件夹 + 一份 `SKILL.md`，就能让通用 AI Agent 获得某个垂直领域的
工作流、规范与工具链。本仓库把我在实际使用中沉淀下来的技能开源出来，每个技能都：

- **自包含**：整个文件夹复制到 Agent 的技能目录即可使用，无需改代码；
- **按需加载**：主文件只放路由与流程，长规范拆到 `references/`，Agent 执行到哪一步才读哪一份，避免上下文污染；
- **可验证**：技能内部写明了执行检查点与质量清单，产出有据可依，不靠模型自由发挥。

当前两个技能正好构成一条完整链路：

```
        视频 / 音频 / 网页链接
                 │
                 ▼
   ┌─────────────────────────────┐
   │  link-to-fieldnote          │  获取内容 → 转写 → 结构化整理
   │  链接 → Obsidian Field Note │  产出知识笔记 / 原生文稿 / 干净网页正文
   └─────────────────────────────┘
                 │
                 │  笔记里要引用的图片 / 视频 / 音频
                 ▼
   ┌─────────────────────────────┐
   │  media-link-resolver        │  签名链 / 短链 → 永久直链
   │  44+ 平台媒体直链解析        │  图片、动图、视频、音频、M3U8/MPD 流
   └─────────────────────────────┘
```

---

## 技能一览

| 技能 | 一句话说明 | 核心能力 | 状态 |
| --- | --- | --- | --- |
| [`link-to-fieldnote`](./agent-skills/link-to-fieldnote) | 把视频、音频、网页文章或本地音视频文件，转成结构化的 Obsidian 笔记或原生文稿 | 三类任务自动路由、字幕/转写证据链、Obsidian 格式规范、写作与质检标准 | ✅ 可用 |
| [`media-link-resolver`](./agent-skills/media-link-resolver) | 把各大社媒平台的 CDN 链接、签名链接、短链、网页链接，解析成永久或最优直链 | 44+ 条平台规则、猫抓式页面嗅探、M3U8/MPD 流解析、媒体类型三重识别 | ✅ 可用 |

> 详细的技能索引与目录约定见 [`agent-skills/README.md`](./agent-skills/README.md)。

---

## link-to-fieldnote

**把「一个链接」变成「一篇能进知识库的笔记」。**

### 三类任务自动路由

接到任务后先判定意图，再走对应流程，不会把「知识提炼」和「逐字稿」混为一谈：

| 类型 | 名称 | 典型说法 | 输出形态 |
| --- | --- | --- | --- |
| **A 类** | 知识笔记 Knowledge Field Note | 总结、提炼、做笔记、放知识库、讲清机制 | `Abstract` + `Key Points` + 二次加工的知识正文 |
| **B 类** | 原生文稿 Native Transcript | 逐字稿、转写稿、原文稿、保留原话 | 按时间轴/章节分段的纯文本，不删减、不改写 |
| **C 类** | 网页清理 Web Article Cleanup | 公众号、网页、文章、去广告、剪藏 | 去噪后的纯净正文 Markdown |

### 支持的内容来源

- **视频平台**：Bilibili、YouTube、抖音、小红书、播客 / RSS
- **网页文章**：微信公众号、博客、新闻页、任意可访问网页
- **本地文件**：用户上传的音频 / 视频，按需走 Whisper 转写

### 端到端流程

```text
判定任务类型 → 获取内容并锁定证据来源 → 判定/执行音频转写
            → 套模板与写作标准 → 排版与格式校验 → 质检后交付
```

### 设计约束（技能内置铁律）

- **不伪造内容**：拿不到完整内容就明确说明依据，绝不编造观点、数据或引文；
- **证据必须标注**：每篇产出注明来源（公开字幕 / 音频转写 / 页面章节 / 用户提供正文）；
- **转写宁多勿缺**：B 类必须转写，A 类在字幕不完整时也必须转写；
- **按层降级**：下载或反爬失败时按 L0→L4 矩阵换方案，禁止原地重试，降级整理前先征得用户同意；
- **信息守恒**：不因「精简」删掉关键案例、数据与出处，也不新增原稿没有的事实。

### 依赖

| 能力 | 工具 |
| --- | --- |
| 视频/音频下载 | `yt-dlp`、`ffmpeg` |
| 音频转写 | `openai-whisper`（首选）或 `faster-whisper`（CPU 更快） |
| 网页正文提取 | Defuddle（最佳）、Readability，兜底直接抓取 |
| 需登录/反爬平台 | 浏览器自动化 |

---

## media-link-resolver

**把「过几天就挂掉的链接」变成「可以长期引用的直链」。**

社媒平台的图片、视频往往带签名或防盗链，复制出来过一段时间就 403。这个技能内置 **44+ 条平台规则**，
覆盖国内 25+、海外 15+ 平台，按「能否永久化」给出不同策略。

### 覆盖与策略

| 分类 | 代表平台 | 策略 |
| --- | --- | --- |
| 国内图床 | 小红书、微博、百度、搜狐、腾讯、堆糖、花瓣、Soogif | 去签名 / 域名替换 → **永久** |
| 海外图床 | Pinterest、Reddit、Imgur | 原样保留 → **永久** |
| 视频嵌入 | Bilibili、YouTube、Vimeo、Dailymotion | 官方播放器嵌入链 → **永久** |
| 国内社交 | 知乎、微信公众号、简书、CSDN、掘金 | 图床永久，页面链接直出 |
| 有时效 | 抖音、TikTok、Instagram、Facebook、X | 无法永久 → `--save` 落盘本地 |
| 需嗅探 | Notion、SoundCloud、VK、Niconico | 页面嗅探提取媒体 |
| 无公开直链 | 爱奇艺、优酷、腾讯视频、斗鱼、虎牙、Twitch | 仅页面链接，注明限制 |

### 抖音专项（实测结论）

| 媒体 | 能否永久 | 做法 |
| --- | --- | --- |
| 视频流 | ✅ 可长期引用 | `video_id` → 永久转播入口链（免 Referer、无签名） |
| BGM 音频 | ✅ 永久 | `douyinstatic` music 域，无防盗链，自动提取 |
| 图片 | ❌ 签名约 30 天 | `--save` 落盘本地，或转存自己的图床 |

### 快速使用

```bash
# 单条 / 批量解析
python3 scripts/resolve_media_links.py "https://sns-webpic-qc.xhscdn.com/..."
python3 scripts/resolve_media_links.py --batch urls.txt

# 猫抓式页面嗅探：从网页提取所有媒体直链，并按 CDN 标注平台
python3 scripts/resolve_media_links.py --sniff "https://weibo.com/abc/xxx"

# 带签名媒体永久化落盘（下载到本地目录）
python3 scripts/resolve_media_links.py --save ./attachments "https://v.douyin.com/xxxx"

# 解析后自动 HEAD 验活，打印状态码与 Content-Type
python3 scripts/resolve_media_links.py --verify "https://v.douyin.com/xxxx/"

# 抖音 video_id → 永久转播入口链
python3 scripts/resolve_media_links.py --play-entry v0200f1a2b3c

# 查看全部支持平台
python3 scripts/resolve_media_links.py --platforms
```

### 媒体类型识别

扩展名与 CDN 特征 → HTTP 魔数探测 → 响应头嗅探，三重判定，覆盖 6 类媒体：

| 类型 | 示例 | 判定依据 |
| --- | --- | --- |
| 图片 | jpg / png / webp | `FFD8FF`、`89504E47`；WebP 无 `ANIM+ANMF` 帧 |
| GIF | gif / soogif | `GIF87a` / `GIF89a` |
| 动图 | Animated WebP / APNG / LivePhoto | `RIFF+WEBP+ANIM+ANMF`、`PNG+acTL` |
| 视频 | mp4 / mov / webm | `ftyp`、`1A45DFA3` |
| 音频 | mp3 / m4a / flac | `ID3`、`FFFB` |
| 流媒体 | m3u8 / mpd | `#EXTM3U`、MPD `BaseURL` |

### 依赖

| 能力 | 工具 |
| --- | --- |
| 基础解析 | Python 3 + `requests` |
| 抖音 / 小红书 / 微博兜底 | `playwright` + chromium（自动探测路径，无需环境变量） |
| 需登录的平台 | 已登录的浏览器会话 |

---

## 快速开始

### 1. 获取仓库

```bash
git clone https://github.com/YOUNG-FELIX/YOUNG_WORKS.git
```

### 2. 安装技能

把目标技能的**整个文件夹**复制到 Agent 的技能目录即可，不需要安装依赖包：

```bash
# 以 link-to-fieldnote 为例
cp -r YOUNG_WORKS/agent-skills/link-to-fieldnote <你的技能目录>/
```

常见 Agent 的技能目录：

| 环境 | 技能目录 |
| --- | --- |
| 豆包 / Codex 类 | `workspace/.user_skills/` |
| 其他 Agent | 参照对应文档指定的 skills 根目录 |

### 3. 触发使用

重启或刷新会话后，Agent 会根据 `SKILL.md` 的 `description` 自动识别场景，直接用自然语言下达任务即可：

```text
帮我把这个视频整理成知识笔记：https://www.bilibili.com/video/BVxxxx
把这个播客转成逐字稿，保留原话
把这个小红书笔记里的图片都换成永久直链
```

---

## 仓库结构

```text
YOUNG_WORKS/
├── README.md                          # 本文件
├── LICENSE                            # MIT
└── agent-skills/                      # 技能专区
    ├── README.md                      # 技能索引与编写约定
    ├── link-to-fieldnote/             # 链接 → Field Note
    │   ├── SKILL.md                   # 入口：任务路由、执行流程、全局铁律
    │   └── references/
    │       ├── task-types.md          # 三类任务的完整定义
    │       ├── content-fetching.md    # 分平台获取策略与降级矩阵
    │       ├── transcription.md       # Whisper 转写规范
    │       ├── templates.md           # A/B/C 三类模板
    │       ├── writing-standards.md   # 写作标准与去 AI 味
    │       ├── output-format-rules.md # Obsidian 格式与排版规范
    │       └── quality-assurance.md   # 检查点与质检清单
    └── media-link-resolver/           # 社媒媒体直链解析
        ├── SKILL.md                   # 入口：能力说明、CLI、平台规则
        ├── references/
        │   ├── platforms.md           # 各平台直链方法论
        │   └── platform-apis.md       # 平台 API 端点清单
        └── scripts/
            └── resolve_media_links.py # 解析器（单文件，1700+ 行）
```

---

## 技能编写约定

如果你也想按同样的方式组织自己的技能，本仓库遵循这几条约定：

- **一个技能一个文件夹**，入口固定为 `SKILL.md`，frontmatter 只保留 `name` 与 `description`；
- **`description` 写触发条件**，不是写功能简介——Agent 靠它判断「什么时候该用我」；
- **长内容拆到 `references/`**，并在 `SKILL.md` 中写清「第几步必须读哪一份」；
- **技能文件夹内不放 README / CHANGELOG**，索引统一维护在 `agent-skills/README.md`；
- **中间产物不入库**：音频、视频、模型、转写底稿、临时脚本一律不进版本库。

---

## 贡献

欢迎提 Issue 或 PR：

1. Fork 本仓库并新建分支；
2. 新增技能请放在 `agent-skills/<skill-name>/`，并在 `agent-skills/README.md` 的索引表中登记；
3. 修改现有技能时，请同步更新对应的 `references/` 文档，避免主文件与细则不一致；
4. 提交 PR 时说明改动动机与实际验证场景。

如果这个仓库对你有帮助，点个 Star 就是最好的支持。

---

## 许可证

本仓库内容采用 [MIT License](./LICENSE) 开源。

---

<div align="center">

维护者：[@YOUNG-FELIX](https://github.com/YOUNG-FELIX)

</div>
