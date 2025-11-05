## Project Overview

`docx-format-translate` is a lightweight Python library for translating Microsoft Word `.docx` files while preserving formatting.

The library is designed to translate document text sentence-by-sentence without breaking the original document formatting (for example: fonts, bold/italic, paragraph styles, numbered/bulleted lists, table styles, etc.). It is suitable for integrating machine translation services or custom translation functions.

## Key Features

- Preserve original `.docx` text formatting and layout.
- Call a user-provided translation function on a single-sentence-to-single-sentence basis, making it easy to integrate any translation API (local models, cloud services, or custom rules).
- Support multiple languages (source and target languages are determined by the user's translation function).
- Simple and easy functional API, suitable for batch or interactive use.

## Installation

Install via pip:

```powershell
pip install docx-format-translate
```

## Quick Start

Here is a minimal example showing how to translate a source file `src_path` and write the result to `dst_path`. The `translate_en2cn` function in the example is a placeholder that performs sentence-to-sentence translation and currently returns the input unchanged — replace it with a call to any translation API.

```python
from docx_format_translate import translate_docx_with_format

def translate_en2cn(text):
    # text: a single sentence (source language)
    # should return the corresponding translated sentence (target language)
    return text

src_path = "example_src.docx"
dst_path = "example_dst.docx"
translate_docx_with_format(src_path, dst_path, translate_en2cn)
```

Usage notes:
- `translate_docx_with_format(src_path, dst_path, translate_fn)`:
  - `src_path`: source .docx file path (string).
  - `dst_path`: destination .docx file path (string). The function writes the translated content to this file. If the file exists it will be overwritten.
  - `translate_fn`: a user-provided translation function with signature `f(text: str) -> str`, which returns the translated sentence for each input sentence.

The library does not restrict how the translation function is implemented; you may call any third-party translation API inside `translate_fn` (for example: OpenAI, Bing, DeepL, Google, or a local model).

## Translation Function Contract

- Input: a single sentence string (`str`) that the library provides after attempting to split document text into sentences.
- Output: the corresponding translated sentence string (`str`). The user is responsible for semantic correctness and preserving necessary punctuation.
- Error handling: if the translation function raises an exception, the library will handle it according to configuration (default: skip the sentence and log the error). It is recommended to implement retries and error handling inside the translation function.

## Notes & Edge Cases

- Sentence splitting: the library attempts to split text into sentences intelligently, but complex abbreviations or unusual punctuation may lead to imperfect boundaries. You can merge or post-process sentences inside your translation function if needed.
- Tables and text boxes: common table and text box content is supported, but some complex nested elements may require additional testing.
- Style loss risk: the library tries to preserve styles, but some custom or non-standard styles may behave differently across Word versions — verify the final document in the target Word environment.

## Advanced Usage (Recommendations)

- Batch translation: when processing multiple files in parallel or sequentially, control concurrency to avoid rate limits from external translation APIs.
- Caching & deduplication: to reduce cost and improve speed, implement caching outside the translation function (e.g., Redis or a local key-value cache).
- Post-processing: you may need to apply small post-processing steps (date formats, localized numbers, etc.) after translation; consider doing that after `translate_fn` returns its result.

## Contributing & License

Issues and PRs are welcome. Follow the usual contribution flow: fork -> branch -> commit -> PR. The project uses the MIT license by default (or adjust based on the repository's actual license).

## Contact

For help or custom support, please open an Issue in the project repository or contact the maintainers.

---

If you'd like, I can also add a sample translation adapter that calls a public translation API or include a small unit test example — tell me which adapter or test you'd prefer and I will add it.
