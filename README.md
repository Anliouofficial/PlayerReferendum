# PlayerReferendum
PlayerReferendum 插件介绍
概述
PlayerReferendum 是一个基于 Bukkit API 的 Minecraft 服务器插件，允许玩家通过民主投票机制发起和参与服务器事务决策。插件通过自定义投票类型、实时按钮交互和结果自动执行，实现玩家自治的玩法场景。

核心功能
​自定义投票系统​
支持多种投票类型（如天气变更、难度调整等）
每种类型可独立配置：
通过百分比阈值（如 >50% 同意）
执行命令（支持变量替换 %player%, %world%）

权限要求
默认描述文本
​玩家交互流程​
复制
sequenceDiagram
玩家A->>插件： /vote 天气 设置晴天
插件->>全服玩家： 弹出投票面板
玩家B->>插件： 点击[同意]
玩家C->>插件： 点击[拒绝]
Note over 插件： 30秒后自动统计
插件->>服务器： 执行"weather clear"命令
​
冷却机制​
防止刷屏：发起者需等待冷却时间（默认5分钟）
动态提示：请等待 120 秒后再发起新的投票

​跨世界隔离​
投票仅在同一世界的玩家间生效
不同维度（主世界/地狱/末地）独立计票
​音效反馈​
玩家投票时触发 BLOCK_NOTE_BLOCK_PLING 音效
强化操作反馈体验

技术亮点
​动态按钮生成​
java
运行
复制
// 创建可点击投票按钮
TextComponent createButton(String text, ChatColor color, String command) {
  TextComponent button = new TextComponent(text);
  button.setClickEvent(new ClickEvent(
    ClickEvent.Action.RUN_COMMAND, 
    command // 如 "/voteaccept d87f5a2d..."
  ));
}

​线程安全设计​
使用 ConcurrentHashMap 存储投票会话
异步处理投票计数，避免主线程阻塞
​智能命令解析​
java
运行
复制
// 命令动态替换变量
String cmd = type.getCommand()
  .replace("%player%", "玩家名")
  .replace("%world%", "world_nether");
​
配置驱动扩展​
示例 config.yml:
yaml
复制
vote-types:
  天气:
    command: "weather clear"
    pass-percent: 40.0  # 40%同意即通过
    permission: vote.weather

使用场景
场景类型	配置示例	应用效果
游戏规则修改	/gamerule doDaylightCycle	投票决定是否开启昼夜交替
经济系统操作	eco give %player% 1000	发起玩家奖金发放提案
活动事件触发	event start fireworks_show	节日活动全民公投
安装要求
​运行环境​： Bukkit/Spigot 1.13+
​依赖项​： 无硬依赖（内置 BungeeChat API）
​权限节点​：
voteplugin.* - 全权限
vote.<类型> - 特定投票权限
通过将决策权交给玩家社区，该插件能有效提升服务器活跃度和玩家参与感，特别适合生存服、小镇服等强调社区协作的场景。
