# CNtiaBirthday 使用指南

> Paper/Spigot 1.21.4+ 生日庆祝插件 · 版本 1.3.0

---

## 目录

- [简介](#简介)
- [安装与前置](#安装与前置)
- [快速开始](#快速开始)
- [命令大全](#命令大全)
- [权限节点](#权限节点)
- [配置文件详解](#配置文件详解)
- [礼物系统](#礼物系统)
- [PlaceholderAPI 变量](#placeholderapi-变量)
- [语言与消息自定义](#语言与消息自定义)
- [常见场景配置](#常见场景配置)
- [常见问题排查](#常见问题排查)
- [数据迁移](#数据迁移)

---

## 简介

玩家设置自己的生日后，插件会在生日当天自动：

- **全服广播** 庆祝消息
- 向寿星发送 **个人祝福**
- 播放 **庆祝音效**
- **发放生日礼物**（物品或命令）
- 在玩家名前显示 **生日前缀**（配合聊天气泡 / TAB 插件）

额外功能：

- 提醒未设置生日的玩家去设置
- 支持 SQLite 和 MySQL 两种存储方式
- 完全可定制的消息（中文 / 英文 / 自定义语言）
- 按权限组分发不同档位的礼物

---

## 安装与前置

### 服务器要求

| 项目 | 要求 |
|------|------|
| 服务端 | Paper 1.21.4+（或兼容的 Spigot 分支） |
| Java | 21+ |
| 可选前置 | PlaceholderAPI（用于变量扩展） |

### 安装步骤

1. 将 `CNtiaBirthday-1.3.0.jar` 放入服务器的 `plugins/` 目录
2. 启动服务器，插件会在 `plugins/CNtiaBirthday/` 下生成默认配置文件
3. 在 `config.yml` 中填入 `license-key`（联系作者获取）
4. 根据需要调整配置
5. 执行 `/cntiabirthday reload` 或重启服务器使配置生效

### 获取许可证

插件需要有效的许可证才能运行。启动时若未配置许可证密钥或验证失败，插件会自动禁用。

> 联系作者获取许可证密钥：QQ 1794262905

---

## 快速开始

### 最少配置即可使用

如果你是第一次使用，只需做两件事：

1. **填入许可证密钥**
```yaml
# config.yml
license-key: '你的密钥'
```

2. **重启服务器或执行** `/cntiabirthday reload`

默认使用 SQLite 存储，无需额外配置数据库。礼物系统默认开启 AUTO 模式，管理员可以通过命令添加礼物物品。

### 玩家使用流程

1. 玩家输入 `/birthday set 5 28` 设置生日（5月28日）
2. 每年 5月28日，玩家登录时自动触发庆祝
3. 全服玩家看到广播，寿星收到祝福和礼物

---

## 命令大全

主命令：`/cntiabirthday`，别名：`/birthday` `/cbd`

### 玩家命令

| 命令 | 说明 |
|------|------|
| `/cntiabirthday set <月> <日>` | 设置生日，每个玩家只能设置一次 |
| `/cntiabirthday view` | 查看自己的生日日期 |
| `/cntiabirthday claim` | 手动领取生日礼物（仅 CLAIM 模式） |

**日期格式说明**：
- 月份范围 1-12，日期范围 1-31
- 会自动校验非法日期（如 2月30日 会提示错误）
- 示例：`/cntiabirthday set 12 25` → 设置为 12月25日

### 管理员命令

| 命令 | 说明 |
|------|------|
| `/cntiabirthday reload` | 重载配置文件和语言文件 |
| `/cntiabirthday delete <玩家名>` | 删除指定玩家的生日数据 |
| `/cntiabirthday gift add` | 将手中的物品添加为礼物 |
| `/cntiabirthday gift remove <id>` | 按 ID 删除礼物 |
| `/cntiabirthday gift list` | 查看所有已添加的礼物 |
| `/cntiabirthday gift give <玩家名>` | 手动向在线玩家发放一次礼物 |
| `/cntiabirthday license` | 查看当前许可证状态 |

---

## 权限节点

| 权限 | 默认拥有者 | 说明 |
|------|------------|------|
| `cntiabirthday.player` | 所有玩家 | 允许查看/设置生日、领取礼物 |
| `cntiabirthday.admin` | OP | 允许重载配置、删除数据、管理礼物、查看许可证 |
| `cntiabirthday.group.vip` | 无 | VIP 权限组礼物（示例，可在配置中自定义） |
| `cntiabirthday.group.svip` | 无 | SVIP 权限组礼物（示例，可在配置中自定义） |

### LuckPerms 配置示例

```
# 给 Admin 组添加管理权限
/lp group admin permission set cntiabirthday.admin true

# 给 VIP 组添加专属礼物权限
/lp group vip permission set cntiabirthday.group.vip true
```

---

## 配置文件详解

完整配置文件位于 `plugins/CNtiaBirthday/config.yml`。

### 基础设置

```yaml
# 语言：zh_CN（中文）或 en_US（英文）
language: zh_CN

# 许可证密钥，启动前必须填写
license-key: ''
```

### 存储设置

```yaml
# SQLITE = 本地文件存储（默认，无需额外配置）
# MYSQL  = 远程数据库存储（多服互通选这个）
storage-type: SQLITE
```

**使用 SQLite**（默认）：数据存在 `plugins/CNtiaBirthday/data.db`，单服使用无需额外配置。

**使用 MySQL**（多服数据互通）：

```yaml
storage-type: MYSQL
database:
  host: localhost          # 数据库地址
  port: 3306               # 数据库端口
  database: cntia_birthday # 数据库名
  username: root           # 用户名
  password: your_password  # 密码
  pool:                    # 连接池设置，一般不用改
    maximum-pool-size: 10
    minimum-idle: 2
    connection-timeout: 5000
    idle-timeout: 600000
    max-lifetime: 1800000
```

### 生日庆祝设置

```yaml
birthday:
  # 定时检测间隔，单位 tick（20 tick = 1秒）
  # 默认 1200 = 60秒检测一次
  check-interval: 1200

  # true = 每天只庆祝一次（推荐）
  # false = 每次登录都庆祝（测试用）
  once-per-day: true

  # 生日前缀，生日当天通过 PAPI 变量显示
  # 支持 MiniMessage 格式（颜色、渐变等）
  prefix: "<yellow>「🎂 小寿星」</yellow>"

  # 庆祝音效
  sound:
    enabled: true
    name: "entity.experience_orb.pickup"  # 音效 ID
    volume: 1.0                            # 音量 0.0-1.0
    pitch: 1.2                             # 音调
```

> 常用音效 ID：`entity.player.levelup` `block.note_block.chime` `entity.firework_rocket.launch`
> 更多音效请查阅 Minecraft Wiki 或 Paper 文档。

### 礼物设置

```yaml
gifts:
  # 是否启用礼物功能
  enabled: true

  # 发放模式：
  # AUTO  - 生日当天自动发放
  # CLAIM - 玩家需手动点击消息领取（每日限一次）
  delivery-mode: AUTO

  # 生日时由控制台执行的额外命令
  # <player> 会被替换为玩家名
  commands:
    - "give <player> diamond 64"
    - "give <player> cake 1"
```

### 权限组礼物

给不同权限组的玩家发放不同档次的礼物，插件自动选择最高优先级匹配：

```yaml
permission-gifts:
  vip:
    permission-node: "cntiabirthday.group.vip"  # 匹配的权限
    priority: 1                                   # 优先级（越高越优先）
    commands:
      - "give <player> diamond 64"
      - "give <player> emerald 32"
  svip:
    permission-node: "cntiabirthday.group.svip"
    priority: 2
    commands:
      - "give <player> diamond 128"
      - "give <player> netherite_ingot 16"
      - "give <player> emerald 64"
```

> **注意**：权限组礼物的优先级高于通用礼物。如果玩家匹配到权限组，则**只**执行该组的命令，不会再加发通用礼物中的数据库物品和 `gifts.commands`。

---

## 礼物系统

### 礼物类型

| 类型 | 添加方式 | 说明 |
|------|----------|------|
| 物品礼物 | 管理员手持物品 → `/cntiabirthday gift add` | 物品的所有 NBT 数据（名称、附魔、Lore 等）都会被保留 |
| 命令礼物 | 在 `config.yml` 的 `gifts.commands` 或 `permission-gifts` 中编写 | 以控制台身份执行，支持 `<player>` 占位符 |

### 物品礼物管理

```
# 手持钻石剑，添加入库
/cntiabirthday gift add

# 查看所有礼物及其 ID
/cntiabirthday gift list

# 删除 ID 为 3 的礼物
/cntiabirthday gift remove 3

# 手动向玩家 Steve 发放一次礼物
/cntiabirthday gift give Steve
```

### 发放模式对比

| 模式 | 玩家体验 | 适用场景 |
|------|----------|----------|
| AUTO | 登录即自动到账，背包满则掉落地面 | 希望简单省心、玩家无需操作 |
| CLAIM | 登录后显示可点击消息，玩家主动领取，当天限一次 | 给玩家仪式感，玩家可自行选择领取时机 |

### 礼物发放优先级

1. 匹配到的**最高 priority** 权限组命令
2. 未匹配任何权限组 → 数据库中的物品礼物 + `gifts.commands`

---

## PlaceholderAPI 变量

安装 [PlaceholderAPI](https://www.spigotmc.org/resources/placeholderapi.6245/) 后可用。插件会自动注册，无需额外配置。

### 变量列表

| 变量 | 示例输出 | 说明 |
|------|----------|------|
| `%cntiabirthday_date%` | `05-28` | 玩家的生日日期 |
| `%cntiabirthday_prefix%` | `「🎂 小寿星」` | 生日当天显示配置的前缀，非生日当天为空 |
| `%cntiabirthday_daysuntil%` | `距离生日还有 180 天` | 距下次生日还有多少天（当天显示自定义祝福语） |
| `%cntiabirthday_playerdate_Steve%` | `12-25` | 查询指定玩家 Steve 的生日日期 |

### 常见用法

**TAB 列表显示生日前缀**：

```
# TAB 插件配置中
玩家名格式: %cntiabirthday_prefix%%player_name%
```

**计分板显示生日**：

```
# 计分板配置中
生日: %cntiabirthday_date%
```

**聊天格式显示前缀**：

```
# 聊天格式化插件中
格式: %cntiabirthday_prefix%%player_name% &7> &f
```

---

## 语言与消息自定义

### 切换语言

在 `config.yml` 中设置：

```yaml
language: zh_CN   # 中文
# language: en_US # 英文
```

### 消息文件位置

- 中文：`plugins/CNtiaBirthday/lang/zh_CN.yml`
- 英文：`plugins/CNtiaBirthday/lang/en_US.yml`

### 消息格式

所有消息使用 **MiniMessage** 格式，支持丰富的文本效果：

**常用颜色**：
```
<red> <green> <gold> <yellow> <gray> <aqua> <white> <dark_red>
```

**渐变色**：
```yaml
"<gradient:gold:yellow>生日快乐！</gradient>"
```

**可点击文字**：
```yaml
"<click:run_command:/cntiabirthday claim><aqua>[点击领取生日礼物]</aqua></click>"
```

### 可用占位符

| 占位符 | 替换为 | 可用范围 |
|--------|--------|----------|
| `<player>` | 玩家名 | 所有消息 |
| `<date>` | 生日日期 (MM-DD) | 所有消息 |
| `<daysuntil>` | 距下次生日天数 | 所有消息 |
| `<target>` | 目标玩家名 | 删除/发放命令 |
| `<id>` | 礼物 ID | 礼物列表/删除 |
| `<type>` | 礼物类型 | 礼物列表 |
| `<time>` | 礼物添加时间 | 礼物列表 |
| `<days>` | 天数数值 | daysuntil 消息 |

### 消息自定义示例

**修改生日广播颜色**（`zh_CN.yml`）：

```yaml
broadcast:
  enabled: true
  message:
    - "<gradient:red:gold>━━━━━━━━━━━━━━━━━━</gradient>"
    - "<yellow>🎂 今天是 <green><player></green> 的生日！</yellow>"
    - "<gold>大家一起祝TA生日快乐吧！🎉</gold>"
    - "<gradient:red:gold>━━━━━━━━━━━━━━━━━━</gradient>"
```

**禁用某个消息**：

```yaml
self-message:
  enabled: false   # 不发送个人祝福
```

**修改 CLAIM 领取提示**：

```yaml
claim-reminder:
  enabled: true
  message: "<gold>🎁 <player>，今天是你的生日！</gold> <click:run_command:/cbd claim><green>[点我领礼物]</green></click>"
```

### 添加新语言

1. 复制 `plugins/CNtiaBirthday/lang/zh_CN.yml` 为 `lang/ja_JP.yml`（或其他语言代码）
2. 翻译所有消息内容，**保留 MiniMessage 标签和 `<>` 占位符**
3. 修改 `config.yml` 中的 `language: ja_JP`
4. 执行 `/cntiabirthday reload`

---

## 常见场景配置

### 场景一：基础生存服

玩家设置生日，当天自动发礼物，不需要复杂配置。

```yaml
storage-type: SQLITE
gifts:
  enabled: true
  delivery-mode: AUTO
  commands:
    - "give <player> diamond 16"
    - "give <player> cake 1"
    - "give <player> golden_apple 5"
```

管理员手持特殊物品（如带命名的钻石剑）执行 `/cbd gift add`，这些物品也会在生日当天一并发放。

### 场景二：RPG/会员服 — 按等级发不同礼物

```yaml
gifts:
  enabled: true
  delivery-mode: AUTO
  commands:
    - "give <player> bread 10"   # 所有玩家都有基础礼物

permission-gifts:
  vip:
    permission-node: "group.vip"
    priority: 1
    commands:
      - "give <player> diamond 32"
      - "give <player> emerald 16"
  mvp:
    permission-node: "group.mvp"
    priority: 2
    commands:
      - "give <player> diamond 64"
      - "give <player> netherite_ingot 8"
      - "give <player> enchanted_golden_apple 4"
  legend:
    permission-node: "group.legend"
    priority: 3
    commands:
      - "give <player> netherite_block 2"
      - "give <player> dragon_egg 1"
      - "give <player> elytra 1"
```

玩家属于 VIP 和 MVP 两个组时，只会执行 MVP 的命令（priority 更高）。

### 场景三：多服互通 — MySQL 数据共享

多个子服共享生日数据，玩家在任意服务器设置生日后全服同步。

```yaml
storage-type: MYSQL
database:
  host: 10.0.0.1
  port: 3306
  database: cntia_birthday
  username: minecraft
  password: 你的密码
```

所有服务器使用相同的 MySQL 连接信息即可共享生日数据。

### 场景四：玩家自助领取礼物

```yaml
gifts:
  enabled: true
  delivery-mode: CLAIM
  commands:
    - "give <player> diamond 64"
```

玩家生日当天登录后会看到可点击的领取消息。点击自动执行 `/cbd claim`，当天限领一次。未领取也不会丢失，重新登录仍然可以领取。

---

## 常见问题排查

### 插件启动后自动禁用

**原因**：`license-key` 未配置或密钥无效。

**解决**：
1. 检查 `config.yml` 中 `license-key` 是否填写正确
2. 确认服务器可以访问外网（需连接 `api.licensegate.io`）
3. 执行 `/cntiabirthday license` 查看具体错误原因
4. 如密钥过期或失效，联系作者 QQ 1794262905

### 玩家生日当天没有收到庆祝

按以下顺序排查：

1. 确认玩家已设置生日：让玩家执行 `/cbd view` 查看
2. 确认 `birthday.once-per-day` 是否为 `true`，且今天还没庆祝过
3. 检查 `birthday.check-interval` 是否过大，玩家是否在检测间隔内登录
4. 玩家重新登录一次试试（登录事件会触发检测）
5. 检查后台是否有报错日志

### 礼物没有发放

1. 检查 `gifts.enabled` 是否为 `true`
2. 检查 `gifts.delivery-mode`：AUTO 模式是自动发，CLAIM 模式需要玩家手动 `/cbd claim`
3. 如果是 CLAIM 模式，检查 `claim-reminder.enabled` 是否开启（否则玩家看不到领取提示）
4. 检查礼物列表是否为空：执行 `/cbd gift list`
5. 检查是否有权限组礼物覆盖了通用礼物（权限组匹配的玩家不会收到通用礼物）
6. 观察服务器控制台是否有序列化相关的警告

### 消息不显示

1. 检查语言文件是否存在于 `plugins/CNtiaBirthday/lang/` 目录
2. 检查消息的 `enabled` 是否为 `true`
3. 确认 `language` 配置与文件名一致（不含 `.yml` 后缀）
4. 删除语言文件后重载插件会自动重新生成默认文件

### PlaceholderAPI 变量不工作

1. 确认已安装 PlaceholderAPI 插件
2. 执行 `/papi list` 确认列表中有 `cntiabirthday` 扩展
3. 执行 `/papi parse <玩家名> %cntiabirthday_date%` 测试变量是否返回正确值
4. 如果扩展未注册，尝试 `/papi reload` 或重启服务器
5. 确认玩家已设置生日（未设置时 `%cntiabirthday_date%` 返回空）

### 重载配置后插件异常

重载时插件会重新验证许可证。如果网络不通或密钥失效，重载会失败并给出提示。建议重载前先确认服务器网络正常。

---
---

## 联系与支持

| 联系方式 | |
|----------|------|
| 作者 | CN_tia |
| QQ | 1794262905 |
| 插件版本 | 1.3.0 |

> 作者亦承接服务端定制、插件定制等开发需求，欢迎咨询。
