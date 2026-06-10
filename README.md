# wechat-url-to-markdown-skill

一个用于 [Claude Code](https://docs.claude.com/en/docs/claude-code) 的 Skill：把微信公众号文章链接（`mp.weixin.qq.com/s/...`）一键转换成 Markdown 文件，自动下载文章中的所有图片到本地。

装好之后，在 Claude Code 里直接说「把这个微信文章转成 markdown：<链接>」即可。

## 安装（30 秒）

**前置条件**：已装好 [Claude Code](https://docs.claude.com/en/docs/claude-code) 和 Python 3.8+。

**步骤**：把本仓库的 `skill/` 目录拷到 Claude 的 skills 目录。

Windows / macOS / Linux 通用命令：

```bash
git clone https://github.com/AlexDou-Y/wechat-url-to-markdown.git
cp -r wechat-url-to-markdown/skill ~/.claude/skills/wechat-url-to-markdown
```

完事了。第一次使用时，skill 会自动 `pip install` 底层抓取工具，无需手动操作。

## 怎么用

打开 Claude Code，进到你想保存文章的目录，然后随便用以下任一种说法：

- 把这个微信文章转成 markdown：https://mp.weixin.qq.com/s/xxx
- 下载这篇公众号文章 https://mp.weixin.qq.com/s/xxx
- 直接贴一个 `mp.weixin.qq.com/s/...` 链接

Claude 会在当前目录生成：

```
output/
└── <文章标题>/
    ├── <文章标题>.md     # 含标题、公众号、发布时间、原文链接
    └── images/            # 文章里所有图片
        ├── img_001.png
        └── ...
```

一次贴多个链接也行，会逐个转换，最后汇总报告。

## 首次使用的小提示

- 第一次会下载约 530MB 的浏览器内核（Camoufox，用于反爬抓取），需要几分钟，请耐心等
- 之后再用就秒级响应了
- 仅支持 `mp.weixin.qq.com/s/` 格式的微信公众号文章链接

## 致谢

底层抓取能力来自 [@jackwener](https://github.com/jackwener) 的开源项目 [wechat-article-to-markdown](https://github.com/jackwener/wechat-article-to-markdown)（MIT License）。本仓库只是一层 Claude Code skill 封装。

## License

[MIT](./LICENSE)
