# qtcloud-course blueprint 使用指南

`qtcloud-course blueprint` 是一个命令行工具，帮你快速生成符合量潮教学风格的课程蓝图。你只需要告诉它一个主题，它就能基于 DeepSeek 生成一份包含核心困惑、设计溯源、课时组织的教学框架。

## 安装

### 前置依赖

- Rust 工具链（1.75 以上）
- DeepSeek API 密钥（设置环境变量 `LLM_API_KEY`）

### 编译安装

```bash
git clone https://github.com/quanttide/qtcloud-course.git
cd qtcloud-course/src/cli
cargo install --path .
```

安装后确认可用：

```bash
qtcloud-course blueprint --help
```

看到帮助信息即安装成功。

## 快速上手

### 生成一份蓝图

最简用法，直接指定主题：

```bash
qtcloud-course blueprint git
```

输出会打印到终端。你看到的是一份完整的教学框架——从初学者的具体困惑出发，回到设计源头解释，再分课时组织教学。没有提纲味，没有废话。

### 保存到文件

```bash
qtcloud-course blueprint docker --output-path docker-blueprint.md
```

输出写入文件，终端只打印确认信息。

### 提供原始资料

如果你有自己的教学笔记或参考资料，可以通过 `--input-path` 传入，LLM 会把这些素材作为上下文来生成更贴合的蓝图：

```bash
qtcloud-course blueprint kubernetes \
  --input-path my-notes.md \
  --output-path k8s-blueprint.md
```

`--input-path` 和 `--output-path` 都是可选参数，可以单独使用也可以组合使用。

## 示例

### 生成 Git 课程蓝图

```bash
qtcloud-course blueprint git
```

输出会找到 Git 教学中最核心的那个困惑——"为什么本地 commit 了还要 push？"——然后从分布式 vs 集中式的设计源头讲清楚它，最后分两个课时组织教学框架。

### 生成 Docker 课程蓝图

```bash
qtcloud-course blueprint docker
```

输出围绕另一个新手困惑展开——"为什么有镜像还要 docker run？"——从虚拟机做对比，讲清镜像=模板、容器=实例的设计哲学。

## 原理简介

工具的工作流程很简单：

1. 读取 prompt 模板，替换 `{topic}` 为你的主题
2. 如果提供了 `--input-path`，追加原始资料到 prompt
3. 调用 DeepSeek（通过 `quanttide-agent` 库）
4. 输出到 stdout 或 `--output-path` 指定的文件

整个工具是无状态的——不写缓存、不留日志、不依赖配置文件。每次运行都是一次独立的 LLM 调用。

## 环境变量

| 变量 | 说明 | 默认值 |
|------|------|--------|
| `LLM_API_KEY` | DeepSeek API 密钥 | 必填 |
| `LLM_MODEL` | 模型名 | `deepseek-v4-flash` |
| `LLM_BASE_URL` | API 地址 | `https://api.deepseek.com` |

## 下一步

- 阅读已有的课程蓝图：`data/profile/git/`、`data/profile/docker/`
- 用 `--input-path` 把你自己的教学笔记转成蓝图
- 试试不同的主题：`python`、`rust`、`react`、`sql`……
