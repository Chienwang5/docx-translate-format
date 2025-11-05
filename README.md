## 项目简介

`docx-format-translate` 是一个用于对 Microsoft Word `.docx` 文件进行“带格式”翻译的轻量级 Python 库。

它的目标是在不破坏原始文档格式（例如：字体、粗体、斜体、段落样式、编号/项目符号、表格样式等）的前提下，将文档中的文本按句子进行逐句翻译，适合机器翻译或自定义翻译函数接入。

## 主要特性

- 保留原始 `.docx` 的文本格式与排版。 
- 按“句子到句子”（single-sentence-to-single-sentence） 的方式调用用户提供的翻译函数，便于整合任何翻译 API（本地模型、云服务或自定义规则）。
- 支持多语言翻译（源语言与目标语言由用户的翻译函数决定）。
- 简单易用的函数式接口，适合批量或交互式使用。

## 安装

使用 pip 安装：

```powershell
pip install docx-format-translate
```

## 快速开始

下面是最小的使用示例，展示如何把一个源文件 `src_path` 翻译并输出到 `dst_path`。示例中的 `translate_en2cn` 是一个单句到单句的翻译函数，占位返回原文，你可以将其替换为调用任意翻译 API 的实现。

```python
from docx_format_translate import translate_docx_with_format

def translate_en2cn(text):
		# text: 单句字符串（源语言）
		# 应返回对应的目标语言字符串
		return text

src_path = "example_src.docx"
dst_path = "example_dst.docx"
translate_docx_with_format(src_path, dst_path, translate_en2cn)
```

示例说明：
- `translate_docx_with_format(src_path, dst_path, translate_fn)`：
	- src_path: 源 docx 文件路径（字符串）。
	- dst_path: 目标 docx 文件路径（字符串），函数会把翻译后的内容写入此文件。若文件存在会被覆盖。
	- translate_fn: 用户提供的翻译函数，签名为 `f(text: str) -> str`，针对传入的单句返回翻译后的单句。

库不会对翻译函数的内部实现做限制；你可以在 `translate_fn` 中调用任意第三方翻译 API（例如：OpenAI、Bing、DeepL、Google、或本地模型）。

## 翻译函数约定（契约）

- 输入：单个句子字符串（str），已从文档中按句子分割后提供（库会尽量保持句子边界）。
- 输出：对应的目标语言字符串（str），由用户保证语义对应且适当保留必要标点。 
- 错误处理：若翻译函数抛出异常，库将按配置（默认：跳过该句并在日志中记录）处理。建议在翻译函数中做重试与错误捕获。

## 注意事项与边界情况

- 句子拆分：库会尝试智能拆分句子，但复杂的缩写或特殊标点可能导致边界不完美。可在翻译函数内做额外合并/处理。
- 表格与文本框：支持常见的表格与文本框内容翻译，但某些复杂嵌套元素可能需要额外测试。
- 样式丢失风险：本库在设计时尽量保留样式，但某些自定义或非常规样式在不同 Word 版本间存在差异，建议在目标 Word 中检查最终效果。

## 进阶用法（建议）

- 批量翻译：对多个文件进行并行/序列化处理时，请控制并发量，避免外部翻译 API 的速率限制。
- 缓存与去重：为降低费用和提高速度，建议在翻译函数外部实现缓存（例如 Redis 或本地键值缓存）。
- 后处理：有时需对翻译后的句子进行小范围修正（日期格式、本地化数字等），可在 `translate_fn` 返回结果后做一次后处理。

## 贡献 & 许可

欢迎 Issue 与 PR。请遵循常规贡献流程：fork -> 新分支 -> 提交 -> PR。项目默认采用 MIT 许可（或根据仓库实际许可调整）。

## 联系

如需帮助或定制化支持，请在项目仓库中提交 Issue 或联系维护者。

---

如果你希望我同时生成一个示例翻译函数（例如，调用某个公共翻译 API 的简单适配器）或添加单元测试示例，我可以继续补充。 
