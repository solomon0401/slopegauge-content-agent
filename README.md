[README.md](https://github.com/user-attachments/files/34514841/README.md)
# slopegauge-content-agent
slopegauge-content-agent
# SlopeGauge Content Agent

用于 SlopeGauge Instagram 官号的内容运营工作流：搜集北美滑雪新闻，推荐一个方向，展开六页中英提案，经确认后制作品牌图片与 caption。

这是可由 Codex 等支持本地 Skills 的 Agent 读取的工作流包，不是双击运行的独立应用。GitHub 用于保存和分享，上传不会启动运营或自动发布到 Instagram。

开始使用

1. 解压整个文件夹，在 Codex 中打开这个文件夹作为项目。

2. 把下面这句话发送给 Codex：

   > 请阅读 README.md，用 scripts/install.py 安装本项目的 Skills 到项目内。不要覆盖已有同名 Skill，不要启动新闻研究或生图。安装后检查是否能识别 slopegauge-operator。

3. 安装完成后，如果技能还没有出现，重新打开项目或重启 Codex。

4. 开始运营时发送：

   > 使用 $slopegauge-operator 开始今天的 SlopeGauge 运营。先只给我一个最佳方向。

首次使用研究引擎可能需要设置来源；按实际可用能力配置。未配置的平台不能宣称已经搜索，也不能把其他平台讨论量称为 IG 热度。

如果你会使用终端，在本项目根目录运行：

```sh
python3 scripts/install.py --check
python3 scripts/install.py
```

默认安装到本项目的 `.agents/skills/`，不写入全局配置。不联网、不下载模型、不获取账户信息。重复安装相同文件会跳过；发现不同的同名 Skill 会停止，不覆盖。更新版本时让 Agent 比较差异并由你决定处理方式。

本地技能位置及发现机制参考 [OpenAI 官方说明](https://learn.chatgpt.com/docs/build-skills)。也可将 `skills/` 内的完整子文件夹手动复制到项目 `.agents/skills/`，请一起保留配套依赖；不同宿主请使用其支持的技能目录。

## 使用流程

| 你说的话                                  | Agent 做到哪里                          |
| ------------------------------------- | ----------------------------------- |
| 开始今天的运营                               | 新研究 → 只推荐一个最佳方向 → 等你选择              |
| 就这个，展开                                | 六页视觉顺序、英文文案、中文对照、caption 和来源 → 等你批准 |
| 可以，生成图片                               | 按批准稿制作六张图片和 caption.txt             |
| 只修改第五页                                | 只处理对应修改，不重跑整个流程                     |
| 使用 $slopegauge-delivery-review 审查这套成品 | 一次独立总审查，只报告，不自动修改                   |

完整提案用五部分沟通：**headline、内容、caption、text、口号**。确认选题不等于确认完整提案，更不等于允许发布。

## 需要哪些运行能力

| 能力                             | 用途                              |
| ------------------------------ | ------------------------------- |
| 可读取 Skills 的 Agent             | 执行工作流、策划和写文案                    |
| 网页搜索与打开原文                      | 新闻及产品事实核查                       |
| Python 3.12+、网络及本地文件读写         | 运行随包 last30days 引擎              |
| 看图及图像生成/编辑工具，宿主 imagegen Skill | 制作与检查六页图片                       |
| PNG 导出和尺寸/色彩检查能力               | 确保最终 1080×1350 RGB；可由宿主已有图像工具提供 |

包内没有模型、API Key、登录 Cookie、付费额度，也没有复制宿主系统的 imagegen Skill。第三方来源的账户和授权由使用者自己配置。缺少生图能力时可以完成选题与文案，但不能声称已生成图片。需要确定性裁切、原图贴入时遵守当前用户授权及宿主工具规则，不继承原作者历史会话的授权。

## 已保存的品牌规则

- 面向北美英语滑雪和单板受众；美国、加拿大优先。
- 新闻最多回溯五天，天气覆盖未来十天；没有合适新闻再用常青主题。
- 默认交付时区为 Asia/Hong_Kong，可由用户明确修改；天气使用目的地当地时间。
- 深蓝 `#102746`、少量紫色 `#7650D6`、Arial/Helvetica 类字体、真实 Logo。
- 先用户素材，再找合适授权背景，找不到再生成；产品截图只在直接相关时出现。
- 不改产品截图数据；示例和生成山景有清晰标注；产品功能每次按官方证据核实。
- 不自动发布；总审查只在手动要求时触发。

默认素材在 `skills/slopegauge-image-producer/assets/social-kit/`，视觉基准在同目录下的 `assets/style/`。运行时使用所加载 producer 的实际路径。用户提供新素材时优先新素材，避免手动修改已安装副本和源文件两处导致版本不一致。

## 交付格式

```text
outputs/
├── YYYY-MM-DD/
│   ├── 01.png
│   ├── 02.png
│   ├── 03.png
│   ├── 04.png
│   ├── 05.png
│   ├── 06.png
│   └── caption.txt
└── YYYY-MM-DD.zip
```

图片为 1080×1350 RGB。caption.txt 只有可直接发布的英文正文、CTA、hashtags 和必要署名，没有编辑标签或中文。ZIP 只有日期目录下这七个文件。研究、批准记录、提示词和版本记录保存在 `work/`；这些运行数据默认不进入 Git。

## 包内文件

| 路径                                       | 内容                |
| ---------------------------------------- | ----------------- |
| `skills/slopegauge-operator/`            | 统一入口，按需路由         |
| `skills/ski-news-research/`              | 滑雪新闻研究            |
| `skills/slopegauge-content-planner/`     | 选题、六页策划与文案        |
| `skills/slopegauge-image-producer/`      | 图片制作规范、品牌素材和风格参考  |
| `skills/slopegauge-delivery-review/`     | 手动触发的独立总审查        |
| `skills/last30days/`、`skills/humanizer/` | 已安装第三方依赖的快照，保留许可证 |
| `scripts/`                               | 本地安装与完整性检查        |
| `examples/usage.txt`                     | 可复制的操作示例          |
| `provenance.json`                        | 来源文件哈希与本包修改记录     |
| `SHA256SUMS.json`                        | 发布包文件完整性清单        |

## 上传 GitHub 时

上传这个文件夹的内容，不是日常的图片交付 ZIP。含第三方引擎的完整包超过 GitHub 网页单次 100 文件限制，建议使用 GitHub Desktop 上传，或按子目录分批上传。保留根目录 `.gitignore`；Mac Finder 按 Command+Shift+. 可显示隐藏文件。`.gitignore` 不会过滤网页手动拖入的文件，因此只上传干净的发布包，不拖入安装后生成的 `.agents/`、`work/`、`outputs/` 或本地凭据。

本次只整理本地包，没有创建远程仓库，也没有上传。README 是 GitHub 仓库首页说明。

## 许可与验证

第三方代码授权见 [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md)。SlopeGauge 品牌素材与定制工作流没有因为打包而被授予公共开源许可；见 [LICENSE](LICENSE)。

运行 `python3 scripts/check_package.py` 可检查文件是否与发布清单一致。安装和完整性校验不等于实时研究、生图的端到端测试；新环境首次使用仍需确认实际工具和来源可用。
