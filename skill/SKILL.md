---
name: wechat-url-to-markdown
description: 把微信公众号文章链接转换成 Markdown 文件，自动下载图片并保存到当前工作目录。当用户给出 mp.weixin.qq.com 链接、说"把这个微信文章转成 markdown"、"下载这篇公众号文章"、"转换公众号文章"、"保存这篇微信文章"，或要求把任何 weixin/微信公众号文章导出成 md 时，必须使用本 skill。即使用户没明说"转 markdown"，只要意图是把微信公众号文章下载到本地保存，也要触发。
---

# 微信公众号文章转 Markdown

把 `mp.weixin.qq.com/s/...` 链接转换成 Markdown 文件 + 本地图片，保存到当前工作目录。

底层使用 [wechat-article-to-markdown](https://github.com/jackwener/wechat-article-to-markdown) 命令行工具，无需 API key 或登录。

## 工作流程

### 第 1 步：检查工具是否已安装

```bash
wechat-article-to-markdown --help 2>&1 | head -3
```

如果命令找不到，进入第 2 步安装；如果可用，跳到第 3 步。

### 第 2 步：安装工具（仅首次）

按以下顺序尝试，使用第一个可用的：

```bash
# 优先：uv（如果已装）
uv tool install wechat-article-to-markdown

# 备选：pipx（如果已装）
pipx install wechat-article-to-markdown

# 兜底：pip 全局安装
pip install wechat-article-to-markdown
```

安装后再次运行 `wechat-article-to-markdown --help` 验证。如果三种方式都失败，告诉用户具体错误信息并停下来，不要瞎试。

### 第 3 步：执行转换

**Windows 必须设 `PYTHONIOENCODING=utf-8`**，否则工具用 GBK 输出 emoji 会崩（`UnicodeEncodeError: 'gbk' codec ...`）。

```bash
PYTHONIOENCODING=utf-8 wechat-article-to-markdown "<用户提供的链接>"
```

工具的**已知 bug**：`output/` 不会落在当前工作目录，而是落在 Python `site-packages/output/` 下（写死的相对路径 + 工具被 `pip install` 到全局）。

转换完成后，从工具的成功日志（最后一行 `已保存: <绝对路径>`）解析出实际位置，然后**把 `output/<文章标题>/` 整个目录搬到当前工作目录**：

```bash
# 从日志拿到的 site-packages 路径里，把 output 目录移过来
mv "<site-packages>/output" ./
```

最终用户看到的就是：

```
<当前工作目录>/output/
└── <文章标题>/
    ├── <文章标题>.md
    └── images/
        ├── img_001.png
        └── ...
```

首次运行会下载 Camoufox 浏览器内核（约 530MB），耐心等几分钟。后续运行会直接复用缓存。

### 第 4 步：向用户报告结果

告诉用户：
- 文章标题
- Markdown 文件的完整路径
- 图片数量（如果有）

如果工具报错，原样把关键错误信息给用户看，不要编造解释。常见原因：链接已失效、文章被删除、网络问题。

## 多链接处理

如果用户一次给多个链接，**逐个串行转换**，每个失败不影响下一个。最后汇总报告每个链接的结果（成功/失败 + 路径）。

## 注意事项

- **Windows 编码**：必须 `PYTHONIOENCODING=utf-8`，否则崩在 emoji 输出上
- **输出位置 bug**：工具会把 `output/` 写到 site-packages 而不是当前目录，必须事后 `mv` 过来
- 首次运行要下载 ~530MB 的 Camoufox 浏览器内核，耐心等待，不要中途打断
- **不要**自己去解析微信文章 HTML 或写抓取代码 —— 直接调用这个命令行工具
- 如果用户给的链接不是 `mp.weixin.qq.com/s/` 格式（比如短链或其他公众号平台），先告诉用户这个工具只支持微信公众号文章链接
