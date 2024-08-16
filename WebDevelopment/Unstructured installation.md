

#### Installation steps

```shell
pipenv install "unstructured[doc,docx,pdf,md,local-inference]"
pipenv install "detectron2@git+https://github.com/facebookresearch/detectron2.git@v0.6#egg=detectron2"
pipenv install "layoutparser[layoutmodels,tesseract]"
pipenv install tesseract-ocr 
pipenv install tesseract-lang

pipenv install poppler-utils
pipenv install pytesseract
```

URL: https://github.com/Unstructured-IO/unstructured

Install it in the docker image of update-vector-store, because s3fileloader need it to process pdf files.

`pytesseract` 是一个 Python 封装库，用于调用 Tesseract-OCR。如果你的项目需要进行图像中的文本识别（OCR），并且你打算使用 Tesseract 进行这项工作，那么你确实需要安装 `pytesseract`。**安装 `pytesseract` 仅安装 Python 的封装库，不会安装实际的 Tesseract-OCR 引擎。** 如果你的系统还没有 Tesseract，你需要单独安装它。

在 `zsh` 中，当使用方括号时，它会试图匹配文件名，而不是简单地将其作为字符串传递给命令。所以要用引号引起来。



通过`tesseract --list-langs`，查看支持识别的语言

detectron2 = {ref = "v0.6", git = "https://github.com/facebookresearch/detectron2.git"}