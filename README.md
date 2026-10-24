# 黑马点评学习项目

## 文件与版本管理

项目根目录是唯一的 Git 仓库，统一管理后端和前端。目录保持课程原有布局，方便继续使用现有 Nginx 配置：

| 路径 | 内容 | 是否纳入 Git |
| --- | --- | --- |
| `src/main/java/` | 后端 Java 源码 | 是 |
| `src/main/resources/` | 应用配置、Mapper、初始化 SQL | 是 |
| `src/test/` | 测试源码 | 是 |
| `nginx-1.18.0/conf/` | Nginx 配置 | 是 |
| `nginx-1.18.0/html/` | 前端页面、脚本、样式与现有示例图片 | 是 |
| `pom.xml`、`README.md`、`.gitignore`、`.gitattributes` | 构建和项目管理文件 | 是 |
| `target/`、`.idea/` | 编译产物和个人 IDE 配置 | 否 |
| `nginx-1.18.0/logs/`、`temp/` | Nginx 日志、进程文件和缓存 | 否 |
| `nginx-1.18.0/nginx.exe`、`docs/`、`contrib/` | 本机 Nginx 程序及发行包附属文件 | 否 |

前端来自课程配套仓库 `https://gitee.com/huyi612/hmdp-web.git`。原前端子目录自带两次 2022 年的 Git 提交，属于课程作者的历史。2026-10-24 将旧 Git 元数据备份到项目外的 `../_git-backups/hm-dianping-时间戳/frontend.git`，并从项目根目录重新建立独立历史，保留全部现有源码与图片。

本地 Nginx 发行包被保留；在其他电脑检出项目后，需要另行准备相应 Nginx 程序以及 `logs/`、`temp/` 运行目录。新仓库未关联远程地址。

日常在项目根目录运行 `git status` 查看变更，使用 `git add <文件路径>` 暂存，再用 `git commit -m "说明"` 保存一次学习进度。

## 初始化与构建

项目保留课程使用的 Spring Boot 2.3.12 和 Java 8。运行应用时使用 JDK 8；该版本 Spring Boot 官方支持 Java 8–15，不适合直接用 JDK 21/25 运行。

本机已安装 JDK 8：`C:\Users\Rick\.jdks\corretto-1.8.0_504`，IDEA 中的 SDK 名称为 `corretto-1.8`。

IDEA 初始化步骤：

1. 在 Maven 面板点击 **Reload All Maven Projects**，加载更新后的依赖与注解处理配置。
2. 在 **Project Structure → Project** 中确认 Project SDK 为 `corretto-1.8`，语言级别为 8；模块 SDK 使用 Project SDK。
3. 在 **Settings → Build Tools → Maven** 中，将 Runner 的 JRE 和 Importing 的 JDK 设为 Project SDK；应用运行配置的 JRE 也使用 Project SDK。
4. 执行 **Build → Rebuild Project**。

在 PowerShell 中使用 JDK 8 构建（仅影响当前终端）：

```powershell
$env:JAVA_HOME = 'C:\Users\Rick\.jdks\corretto-1.8.0_504'
$env:Path = "$env:JAVA_HOME\bin;$env:Path"
mvn -version
mvn clean package
```

启动前按实际环境检查 `src/main/resources/application.yaml` 中的 MySQL、Redis 配置，并导入 `src/main/resources/db/hmdp.sql`。

## 本次编译兼容修复

- Spring Boot 父 POM 默认使用 Lombok 1.18.20，无法适配 JDK 21/25 的 javac 内部结构，可能出现 `JCTree$JCImport.qualid` 或 `TypeTag :: UNKNOWN` 异常。项目显式升级至支持 JDK 25 的 Lombok 1.18.42。
- 显式配置 Lombok 注解处理器，适配 JDK 23 起的注解处理行为。
- Maven Compiler Plugin 升级至 3.14.1，设置 `release=8`，约束生成的字节码和 Java API 版本；该配置同时支持 JDK 8 和较新的 JDK 构建。使用新 JDK 构建通过不代表旧版 Spring Boot 支持用该 JDK 运行。
- 本机 IDEA 项目 SDK 已改为 `corretto-1.8`。`.idea` 被忽略，其他电脑仍需自行选择 JDK 8。

依据：[Lombok 更新记录](https://projectlombok.org/changelog)、[Lombok Maven 配置](https://projectlombok.org/setup/maven)、[Maven release 配置](https://maven.apache.org/plugins/maven-compiler-plugin/examples/set-compiler-release.html)、[Spring Boot 2.3.12 系统要求](https://docs.spring.io/spring-boot/docs/2.3.12.RELEASE/reference/html/getting-started.html#getting-started-system-requirements)。
