# e签宝 SaaS API V3 Java 版 Demo

本项目是 [e签宝开放平台](https://open.esign.cn) **SaaS API V3** 的 Java 示例工程（Maven 工程），演示如何对接 e签宝电子签名开放能力：覆盖认证授权、企业成员、印章、文件、模板、签署流程等常规接口，并附带完整的场景化签署流程与回调验签示例。

## 功能模块总览

| 模块目录 | Demo 类 | 功能说明 |
|---|---|---|
| `auth` | `AuthDemo` | 认证授权服务：获取个人 / 企业实名认证授权链接，查询个人 / 企业认证授权状态 |
| `members` | `MembersDemo` | 企业成员服务：创建企业成员、查询成员列表、查询企业管理员、移除企业成员 |
| `seal` | `SealDemo` | 印章服务：创建个人 / 企业模板印章、查询个人印章 / 企业自有印章 / 被外部企业授权印章列表、删除印章、印章跨企业授权与解除授权 |
| `fileAndTemplate` | `FileDemo` | 文件服务：获取文件上传地址（fileId + 上传链接）、上传文件、轮询文件转换状态 |
| `fileAndTemplate` | `TemplateDemo` | 模板服务：获取制作合同模板页面链接、获取编辑合同模板页面链接 |
| `sign` | `SignDemo` | 签署服务：基于文件发起签署、获取签署链接（长/短链）、批量签署页链接、流程开启 / 完结 / 撤销 / 延期、查询流程列表与详情、抄送人增删、附件增删、待签文件增删、签署区增删、下载已签署文件、文件核验（蚂蚁链 / 普通） |
| `scenedemo` | `PlatformSignToC` | 场景化流程：平台方自动盖章 + 个人用户手动签署 |
| `scenedemo` | `PlatformSignToB` | 场景化流程：平台方自动盖章 + 企业用户手动签署 |
| 根目录 | `CallbackCheckDemo` | e签宝回调通知验签示例 |
| `ep` | `epDemo` | 生态合作伙伴套餐服务：获取购买套餐链接、查询套餐余量、查询套餐订单列表 |

<details>
<summary>Demo 覆盖的 V3 接口清单（点击展开）</summary>

- **认证授权**：`/v3/psn-auth-url`、`/v3/persons/identity-info`、`/v3/org-auth-url`、`/v3/organizations/identity-info`
- **企业成员**：`/v3/organizations/`
- **印章**：`/v3/seals/psn-seals/create-by-template`、`/v3/seals/psn-seal-list`、`/v3/seals/psn-seal`、`/v3/seals/org-seals/create-by-template`、`/v3/seals/org-own-seal-list`、`/v3/seals/org-authorized-seal-list`、`/v3/seals/org-seal`、`/v3/seals/org-seals/external-auth`、`/v3/seals/org-seals/auth-delete`
- **文件**：`/v3/files/file-upload-url`、`/v3/files/`
- **模板**：`/v3/doc-templates/doc-template-create-url`、`/v3/doc-templates/`、`/v3/files/create-by-doc-template`
- **签署流程**：`/v3/sign-flow/create-by-file`、`/v3/sign-flow/`、`/v3/sign-flow/sign-flow-list`、`/v3/sign-flow/batch-sign-url`
- **文件核验**：`/v3/antchain-file-info`、`/v3/antchain-file-info/verify`
- **套餐服务**：`/v3/orders/org-place-order-url`、`/v3/orders/remaining-quantity`、`/v3/orders/order-list`

</details>

## 项目结构

```
SaaSAPI_V3_Demo_JAVA/
├── pom.xml                        # Maven 构建配置（依赖均在 pom 中以坐标引入，无本地 lib）
├── pdf/
│   └── 合同模板.pdf               # 示例合同文件，FileDemo 运行时按相对路径读取
├── logs/                          # 日志输出目录（运行时自动生成）
└── src/main/
    ├── java/
    │   ├── CallbackCheckDemo.java # 回调通知验签示例（默认包）
    │   ├── auth/                  # 认证授权服务
    │   ├── constant/              # Demo 配置（Host / AppId / AppSecret）
    │   ├── ep/                    # 生态合作伙伴套餐服务
    │   ├── fileAndTemplate/       # 文件 & 模板服务
    │   ├── members/               # 企业成员服务
    │   ├── scenedemo/             # 场景化签署流程
    │   ├── seal/                  # 印章服务
    │   ├── sign/                  # 签署服务
    │   └── cn/tsign/hz/           # 核心 SDK 封装（HTTP 请求、签名计算、异常）
    └── resources/
        └── log4j2.xml             # 日志配置
```

## 环境要求

- JDK 1.8 及以上
- Maven 3.x（或直接使用 IDEA 内置 Maven）
- e签宝开放平台开发者账号及已创建的应用

## 快速开始

### 1. 准备应用密钥

登录 [e签宝开放平台](https://open.esign.cn)，进入应用管理，获取 **AppId** 和 **AppSecret**。

### 2. 填写配置

编辑 `src/main/java/constant/EsignDemoConfig.java`：

```java
public class EsignDemoConfig {
    // 应用ID
    public static final String EsignAppId = "XXX";
    // 应用密钥
    public static final String EsignAppSecret = "XXX";
    // e签宝接口调用域名（模拟环境）
    public static final String EsignHost = "https://smlopenapi.esign.cn";
    // e签宝接口调用域名（正式环境）
    // public static final String EsignHost = "https://openapi.esign.cn";
}
```

> 注意：模拟环境与正式环境的域名和密钥不通用，联调请先用模拟环境。

### 3. 运行 Demo

**IDEA 方式（推荐）**：导入 Maven 工程，等待依赖下载完成后，打开任意 Demo 类（例如 `scenedemo.PlatformSignToB` 或 `sign.SignDemo`），右键 → `Run`。

**命令行方式**：

```bash
mvn compile exec:java -Dexec.mainClass="fileAndTemplate.FileDemo"
```

> 两种方式的默认工作目录都是项目根目录。`FileDemo` 通过「工作目录 + 相对路径」读取 `pdf/合同模板.pdf`，请勿在其他目录下启动，否则会提示文件不存在。

运行结果会输出到控制台，同时写入 `logs/eSign.log`。

## 典型签署流程

各模块 Demo 可独立运行，完整的电子签署业务通常按以下时序串联：

```
上传文件                     FileDemo.getUploadUrl / uploadFile / getFileStatus
（获取上传地址 → 上传 → 轮询转换状态，fileStatus 为 2 或 5 表示文件就绪）
        ↓
基于文件发起签署流程          SignDemo.createByFile
（配置签署人、签署顺序、签署区等，得到 signFlowId）
        ↓
获取签署链接下发给签署人      SignDemo.signUrl
（签署人在浏览器打开链接完成实名与签署）
        ↓
平台方自动盖章（可选）        签署区 autoSign = true，后台无感知落章
        ↓
流程状态回调通知             action = SIGN_FLOW_UPDATE
（服务端收到回调后用 CallbackCheckDemo 验签，再轮询 / 查询流程详情）
        ↓
下载已签署文件及附属材料      SignDemo.fileDownloadUrl
```

`scenedemo` 包下的 `PlatformSignToB`（平台自动 + 企业手动）、`PlatformSignToC`（平台自动 + 个人手动）就是按上述时序完整串联的示例，可直接作为业务对接模板。**注意：两个场景类中的待签文件路径目前写死为 `E:/合同模板/test-template1.pdf`，运行前请改成自己本地的文件路径。**

## 核心 SDK 封装说明（cn.tsign.hz 包）

该包封装了签名鉴权与网络请求，开发者无需关注签名算法细节，专注于业务 JSON 参数构造：

| 类 | 说明 |
|---|---|
| `EsignHttpHelper` | `signAndBuildSignAndJsonHeader` 构造签名鉴权请求头；`doCommHttp` 发起普通接口请求；`doUploadHttp` 发起文件上传请求 |
| `EsignEncryption` | HmacSHA256 签名、Content-MD5 计算、时间戳生成、回调验签 `callBackCheck` |
| `EsignFileBean` / `FileTransformation` | 文件信息封装：文件名、大小、MD5、字节内容 |
| `EsignHttpCfgHelper` | HTTP 连接配置：超时、连接池上限、重试次数、SSL 校验开关、代理 |
| `EsignRequestType` / `EsignHeaderConstant` | 请求方法与请求头常量（鉴权模式为 `Signature`） |
| `EsignDemoException` | 统一业务异常 |

所有 Demo 均按以下固定模式调用接口：

```java
// 1. 构造签名鉴权请求头（内部自动完成 Content-MD5 计算与 HmacSHA256 签名）
Map<String, String> header = EsignHttpHelper.signAndBuildSignAndJsonHeader(
        eSignAppId, eSignAppSecret, jsonParm, requestType.name(), apiaddr, true);
// 2. 发起接口请求
EsignHttpResponse response = EsignHttpHelper.doCommHttp(eSignHost, apiaddr, requestType, jsonParm, header, true);
```

生成的请求头主要包含：`X-Tsign-Open-App-Id`（应用ID）、`X-Tsign-Open-Ca-Timestamp`（时间戳）、`Content-MD5`（请求体摘要）、`X-Tsign-Open-Ca-Signature`（签名值）、`X-Tsign-Open-Auth-Mode`（鉴权模式）。

## 回调验签

e签宝的签署流程是异步推进的，流程状态变化（如签署完成）通过回调通知推送到你配置的回调地址。服务端收到回调后，**务必验签**防止伪造请求，参考根目录的 `CallbackCheckDemo`：

```java
boolean result = EsignEncryption.callBackCheck(timestamp, query, body, key, sig);
```

| 参数 | 来源 |
|---|---|
| `timestamp` | 回调请求头中的时间戳 |
| `query` | 回调地址中的 query 参数（按 key 排序后拼接 value） |
| `body` | 回调请求体原文 |
| `key` | 应用密钥（AppSecret） |
| `sig` | 回调携带的签名值，用于比对 |

## 日志

日志基于 Log4j2（配置文件 `src/main/resources/log4j2.xml`）：同时输出到控制台和滚动文件 `logs/eSign.log`（按天 + 100MB 滚动）。联调排错时建议查看该文件中的完整请求与响应。

## 常见问题

1. **请求返回签名 / 鉴权错误**：检查 `EsignDemoConfig` 中的 AppId、AppSecret 是否正确，`EsignHost` 与密钥是否属于同一环境（模拟环境密钥不能调正式环境，反之亦然）。
2. **FileDemo 提示文件不存在**：运行工作目录必须为项目根目录，否则相对路径 `pdf/合同模板.pdf` 找不到。
3. **上传文件后发起签署报文件未就绪**：文件上传后有异步转换过程，先轮询文件状态（`fileStatus` 为 2 或 5 表示就绪）再发起签署。
4. **流程一直停在等待签署**：签署流程为异步推进，需签署人打开签署链接完成签署；流程状态变化以回调通知 / 流程详情查询为准。
5. **密钥安全**：`EsignDemoConfig` 中不要提交真实的 AppSecret 到公开仓库。

## 参考文档

- [SaaS API V3 版对接指南](https://open.esign.cn/doc/opendoc/apiv3-guide/tfb6gn)
- [SaaS API V3 版时序图](https://open.esign.cn/doc/opendoc/apiv3-guide/dtsgz1)
- [SaaS API V3 版 API 列表](https://open.esign.cn/doc/opendoc/apiv3-guide/zkgi4m)
- [SaaS API V3 版常见场景对接说明](https://open.esign.cn/doc/opendoc/apiv3-guide/al7kcc)
- [e签宝开放平台官网](https://open.esign.cn)

## License

本项目基于 [Apache License 2.0](Apache License 2.0.txt) 开源。
