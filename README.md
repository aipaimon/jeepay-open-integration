# Jeepay 开源版支付系统对接 Skill

[![Skill Version](https://img.shields.io/badge/version-1.0.0-blue.svg)](https://github.com)
[![Jeepay](https://img.shields.io/badge/Jeepay-开源版-green.svg)](https://gitee.com/jeequan/jeepay)
[![License](https://img.shields.io/badge/license-MIT-orange.svg)](LICENSE)

为 [Claude Code](https://claude.com/claude-code) 提供 [Jeepay 开源版](https://gitee.com/jeequan/jeepay) 支付系统的集成对接能力和问题排查指引。

## 功能概览

### 支付对接（功能一）

自动引导开发者完成 Jeepay 支付系统对接，覆盖完整流程：

| 业务场景 | 接口 | 代码示例 |
|----------|------|---------|
| 统一下单 | `api/pay/unifiedOrder` | Java / Python |
| 查询订单 | `api/pay/query` | Java / Python |
| 关闭订单 | `api/pay/close` | Java / Python |
| 发起退款 | `api/refund/refundOrder` | Java / Python |
| 查询退款 | `api/refund/query` | Java / Python |
| 异步通知验签 | — | Java / Python |
| 发起转账 | `api/transferOrder` | Java / Python |
| 查询转账 | `api/transfer/query` | Java / Python |
| 绑定分账用户 | `api/division/receiver/bind` | Java / Python |
| 发起分账 | `api/division/exec` | Java / Python |

**支持的支付方式**：聚合扫码（QR_CASHIER）、聚合条码（AUTO_BAR）、微信支付（JSAPI / 小程序 / APP / H5 / Native / 条码）、支付宝（二维码 / 小程序 / APP / WAP / PC / 条码 / 生活号 / 订单码）、云闪付（条码 / JSAPI）、银行卡（快捷支付 / 网银B2C / 网银B2B / 转账）。

### 问题排查（功能二）

根据用户描述的问题自动分流排查：

- **错误码排查**：查询系统错误码文档，定位具体原因
- **验签失败专项排查**：按签名算法流程逐项排查，避免级联误判
- **常见问题排查**：基于官方文档提供标准解决方案

## 安装

将 `jeepay-open-integration` 目录复制到 Claude Code 的 Skills 目录下：

```bash
# 项目级（仅当前项目可用）
cp -r jeepay-open-integration /your-project/.claude/skills/

# 用户级（所有项目可用）
cp -r jeepay-open-integration ~/.claude/skills/
```

## 使用方式

在 Claude Code 中通过 Skill 指令调用：

```
# 支付对接
/jeepay-open-integration 对接微信小程序支付

# 问题排查
/jeepay-open-integration 调用统一下单接口返回"通信秘钥错误"
```

或直接描述需求，Claude Code 会根据关键词自动匹配并调用。

## 执行流程

### 支付对接流程

```
步骤 1.1 确认支付方式 → 步骤 1.2 获取对接文档 → 步骤 1.3 获取接口文档
    → 步骤 1.4 代码自检 + 生成代码 → 步骤 1.5 输出安全红线 → 步骤 1.6 集成校验
```

每个步骤设有完成条件和阻塞确认点，确保流程规范：

- **B-01**：用户同意服务声明并确认接入产品
- **B-02**：已读取对接说明、技术规范、SDK&Demo
- **B-03**：用户确认是否需要进行集成校验

### 问题排查流程

```
用户问题 → 识别问题类型 → 错误码排查 / 验签失败专项排查 / 常见问题排查
```

## 文件结构

```
jeepay-open-integration/
├── SKILL.md                                # 技能主文件（路由规则 + 执行流程 + 确认点）
├── README.md                               # 本文件
└── references/
    ├── scene-routing.md                    # 支付方式路由表（决策树 + 关键词匹配 + 澄清话术）
    ├── api-routing.md                      # API 路由表（接口文档索引 + 调用流程）
    ├── checklist.md                        # 集成校验清单（10 大类校验项）
    ├── sdk-reminder.md                     # SDK 集成指引（初始化 + 签名算法 + 坑点）
    ├── doc-access.md                       # 文档访问规范（curl 脚本 + 页面ID映射）
    ├── log-guide.md                        # 日志输出指引（规范 + 示例代码）
    ├── docs/
    │   ├── Jeepay-错误码文档.md            # 系统错误码 + 业务错误码
    │   └── Jeepay-常见问题文档.md          # 问题排查索引
    └── code-examples/
        ├── interface-guide.md              # 代码示例交叉索引
        ├── java/                           # Java 代码示例（10 个接口）
        │   ├── 1_统一下单.md
        │   ├── 2_查询订单.md
        │   ├── 3_关闭订单.md
        │   ├── 4_退款.md
        │   ├── 5_查询退款.md
        │   ├── 6_绑定分账用户.md
        │   ├── 7_发起分账.md
        │   ├── 8_转账.md
        │   ├── 9_查询转账.md
        │   └── 10_异步通知.md
        └── python/                         # Python 代码示例（10 个接口）
            ├── 1_统一下单.md
            ├── 2_查询订单.md
            ├── 3_关闭订单.md
            ├── 4_退款.md
            ├── 5_查询退款.md
            ├── 6_绑定分账用户.md
            ├── 7_发起分账.md
            ├── 8_转账.md
            ├── 9_查询转账.md
            └── 10_异步通知.md
```

## 核心特性

### 严格的安全管控

- 私钥禁止存客户端、禁止记日志、禁止传公共仓库
- 异步通知必须先验签，前台支付结果不可信
- 代码生成前强制 7 项自检（apiKey 存储、签名方式、幂等处理、日志输出等）
- `isSuccess()` 必须传入 apiKey 以启用响应验签

### 结构化的流程控制

- 3 个阻塞确认点，防止未经确认的操作
- 6 个步骤完成条件，每步自检后方可继续
- 禁止跳步、合并步骤或编造数据

### 签名失败防误判

内置签名失败的级联误判链分析，避免常见错误推理：

- "通信秘钥错误" → 不一定是 apiKey 配错，应先检查签名算法流程
- "验签失败" → 不一定是 MD5 实现问题，应先检查 SDK 使用方式
- 首次集成报签名错误 → 禁止手动实现签名，应检查是否与 SDK 冲突

## 依赖

- [Jeepay Java SDK](https://gitee.com/jeequan/jeepay-manager/tree/master/jeepay-sdk-java) 1.6.1+
- [Jeepay 开源版](https://gitee.com/jeequan/jeepay) 后端服务已部署
- Claude Code CLI / Desktop / IDE

## 相关链接

- [Jeepay 官方文档](https://doc.jeequan.com)
- [Jeepay 开源版源码](https://gitee.com/jeequan/jeepay)
- [Jeepay SDK 源码](https://gitee.com/jeequan/jeepay-manager/tree/master/jeepay-sdk-java)

## 声明

- 本 Skill 主要提供 Jeepay 开源版支付系统的对接指引和问题排查指引
- 请开发人员审查 AI 生成的接入代码，自行确认代码逻辑，上线前充分测试确保其适用性与准确性
- 使用本 Skill 即表示您同意：遵守法律法规、自行审核测试并承担使用责任；禁止在代码、大模型对话等公网透露敏感信息

---

> Skill 版本：1.0.0 | 适用版本：Jeepay 开源版 | SDK 版本：1.6.1+
> 文档创建时间：2026-05-15 | 最后更新：2026-05-20

