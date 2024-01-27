---
date: 2023-11-23 19:35:00 +/-0900
title: "[Python] Python convert PDF to Image"
categories: [Develop, python]
tags: [개발(develop), 파이썬(python), 이미지(image), pdf]

---
## 개요

안녕하세요.

이번 글에서는 파이썬을 이용해 PDF 파일을 이미지로 변환하는 방법을 살펴보겠습니다.

---
## 들어가기 앞서

### 배경과 결론

PDF 파일을 이미지로 변환이 필요한 업무가 있었습니다.

파이썬에서는 이를 지원하는 다양한 라이브러리가 존재하는데요.

어떤 라이브러리를 사용할지 찾아본 내용을 정리했습니다.

결론부터 말씀드리자면 환경에 따라 `PyMuPDF`와 `pdf2image` 중 하나의 라이브러리를 추천드립니다.

자, 그럼 천천히 살펴보겠습니다.

### PDF 포맷

PDF(Portable Document Format)은 ISO 32000으로 표준화된 문서 포맷입니다.

어도비(Adobe)사에서 1992년에 개발된 PDF는 응용 프로그램과 하드웨어, 운영 체제에 독립적으로 텍스트 및 이미지를 포함한 문서를 표시합니다.

보통 벡터 그래픽, 텍스트, 비트맵 그래픽으로 이루어지며 버전 업데이트를 통해 멀티미디어의 삽입할 수 있습니다.

편집, 배포, 출판 등 기본적인 역할부터 편집을 제한하거나 암호를 설정하는 등 문서를 보호하는 기능까지 다양하게 지원합니다.

---
## 라이브러리 비교

### 1. pypdf

#### 설치
- 라이브러리 설치: `pip install pypdf`
- 그 외에 추가로 필요한 사항이 없습니다.

#### 상세
- 가장 오래되고, 가장 많은 star를 받은 라이브러리
- 파이썬만으로 동작
- 다양한 기능이 존재하나 페이지를 이미지로 변환하는 기능 부재
  - PDF 메타데이터
  - 텍스트, 이미지 추출
  - PDF 자르기, 회전하기, 늘리거나 줄이기
  - 스탬프나 워터마크 추가
  - 스트리밍 형태로 PDF 읽기
- 약간의 역사가 존재
  - PyPDF와 포크 버전인 PyPDF2가 꽤나 오랜 기간 공존했습니다.
  - PyPDF2가 PyPDF 3.0.x 버전부터 merge 되었습니다.
  - 따라서 `pypdf` 라이브러리로 이용 가능합니다.
  - 하지만 PyPDF2가 필요한 경우 `pypdf2` 라이브러리를 이용하면 됩니다.

### 2. PyMuPDF

#### 설치
- 라이브러리 설치: `pip install pymupdf`
- 자세한 설치 방법은 다음 링크([https://pymupdf.readthedocs.io/en/latest/installation.html](https://pymupdf.readthedocs.io/en/latest/installation.html))를 참고해주세요.
  - 윈도우의 경우 "C/C++ development tools"가 필요합니다.

#### 상세
- 가장 빠르게 업데이트되는 라이브러리
- `MuPDF` 기반
- `pypdf`와 거의 유사한 기능을 제공하지만 안내 페이지의 가이드가 더 상세

### 3. pdf2image

#### 설치
- 라이브러리 설치
  1. `pip install poppler`
  2. `pip install pdf2image`
- 자세한 설치 방법은 다음 링크([https://github.com/Belval/pdf2image](https://github.com/Belval/pdf2image))를 참고해주세요.
  - `poppler`가 필요합니다.

#### 상세
- 단순 이미지 변환 라이브러리
- `poppler`(PDF 변환 패키지, `xpdf` 기반)의 명령어 pdftoppm(더 과거)와 pdftocairo(그나마 최신)를 활용
- 공식 지원 OS가 한정되어 있으므로 사용에 앞서 확인 필요

---
## 실행시간 테스트

전체 PDF 페이지를 1개의 이미지로 저장하는 코드를 통해 실행시간을 비교해보겠습니다.

참고로 `pdf2image` 라이브러리가 `subprocess.Popen`을 통해 작업을 수행하므로 `tracemalloc` 만으로는 메모리 확인이 어려워 생략했습니다.

자식 프로세스의 메모리까지 확인하는 방법을 아는 분은 댓글 부탁드려요!

### 테스트 코드

```python
import os
import io
import datetime
from math import ceil
from logging import Logger, Formatter, StreamHandler, FileHandler


# PyMuPDF
import fitz
# pdf2image
from pdf2image import convert_from_path
# 2개 라이브러리 모두 PIL의 Image 객체로 변환
from PIL import Image


def create_logger(
    log_file_path:str = ".",
    logging_format_str:str = "[%(asctime)s] - [%(levelname)s] - %(message)s",
) -> Logger:
    """Logger 객체 생성 및 반환.
    파일(현재 디렉터리/log/20230921.log 형태)과 stderr 핸들러 설정.

    Args:
        log_file_path (str, optional): 로그 파일을 저장할 경로. Defaults to ".".
        logging_format_str (str, optional): 로그 포맷. Defaults to "[%(asctime)s] - [%(levelname)s] - %(message)s".

    Returns:
        Logger: 로거 객체
    """
    logger = Logger("testing")
    
    logging_formatter = Formatter(logging_format_str)

    file_handler = FileHandler(log_file_path, encoding="utf-8")
    file_handler.set_name("log_file_handler")
    file_handler.setFormatter(logging_formatter)
    stream_handler = StreamHandler()
    stream_handler.set_name("stderr_handler")
    stream_handler.setFormatter(logging_formatter)

    logger = Logger("test")
    logger.addHandler(file_handler)
    logger.addHandler(stream_handler)
    
    return logger


def run_pdf_convert(module_name:str) -> None:
    """각 라이브러리 실행

    Args:
        module_name (str): 실행을 원하는 PDF 관련 라이브러리
    """
    # 각 페이지의 이미지들. 2개 라이브러리 기준 모두 PIL.Image 객체
    page_images = []

    logger.info("Module::%s - Start!" % (module_name))
    start_time = datetime.datetime.now().timestamp()

    # pymupdf 모듈
    if module_name == "pymupdf":
        doc = fitz.open(pdf_path)
        page_images = [Image.open(io.BytesIO(page.get_pixmap(matrix=mat_dpi).tobytes())) for page in doc]
    # pdf2image 모듈
    elif module_name == "pdf2image":
        page_images = convert_from_path(pdf_path, dpi=dpi)

    # 이미지를 결합한 빈 Image 생성
    combined_image_width = ceil((page_images[0].width))
    combined_image_height = ceil(sum(page_image.height for page_image in page_images))
    combined_image = Image.new("RGB", (combined_image_width, combined_image_height))
    y_offset = 0

    # 빈 Image에 각 페이지의 이미지 결합해 PDF 전체 이미지 생성
    for image in page_images:
        combined_image.paste(image, (0, y_offset))
        y_offset += image.height

    # PDF 전체 이미지 저장
    combined_image.save(os.path.join(image_dir, module_name + "_" + os.path.splitext(pdf_file_name)[0] + ".png"))

    end_time = datetime.datetime.now().timestamp()    
    logger.info("Module::%s - Runtime::%f" % (module_name, end_time-start_time))
    logger.info("Module::%s - End!" % (module_name))


if __name__ == "__main__":
    ####################
    #####
    source_dir = "."
    image_dir = "image"
    image_extension = ".png"

    ymd = datetime.datetime.now().strftime("%Y%m%d")
    epoch = 10
    
    pdf_file_name = "MZ세대를 위한 대학도서관 메타버스 서비스 사례.pdf"
    # pdf_file_name = "adventuresofsher00doylrich.pdf"
    pdf_path = os.path.join(source_dir, pdf_file_name)
    log_file_path = os.path.join(".", "log", ymd+".log")

    os.makedirs(os.path.split(log_file_path)[0], exist_ok=True)
    os.makedirs(image_dir, exist_ok=True)
    #####

    #####
    dpi = 72
    # dpi = 200
    pymupdf_convert_dpi = dpi / 72
    mat_dpi = fitz.Matrix(pymupdf_convert_dpi, pymupdf_convert_dpi)
    #####

    #####
    logger = create_logger(log_file_path=log_file_path)
    #####
    ####################
    
    ####################
    logger.info("[[[ Test Start ]]]")
    logger.info("target::%s" % (pdf_file_name))
    logger.info("dpi::%d" % (dpi))

    for module_name in ("pdf2image", "pymupdf", ):
        for _ in range(epoch):
            run_pdf_convert(module_name)

    logger.info("[[[ Test End ]]]")
    ####################

```

### 샘플 파일

- adventuresofsher00doylrich.pdf
  - 용량: 26.1MB
  - 파일: [링크](/assets/file/develop/3013/adventuresofsher00doylrich.pdf){:target="_blank"}
- MZ세대를 위한 대학도서관 메타버스 서비스 사례.pdf
  - 용량: 3.8MB
  - 파일: [링크](/assets/file/develop/3013/MZ세대를%20위한%20대학도서관%20메타버스%20서비스%20사례.pdf){:target="_blank"}

### 테스트 결과

일반적으로 `MuPDF`(`PyMuPDF`)가 `Xpdf`(`pdf2image`)보다 빠르다고 알려져있습니다.

하지만 PDF 페이지 수나 변환되는 이미지의 dpi에 따라 결과가 상이했습니다.

따라서 변환을 원하는 PDF 중 몇 건을 직접 테스트하는 것이 가장 좋다고 판단됩니다. 

전체 결과는 아래의 표와 같으며 변환 시간은 10회 실행의 평균값입니다.

환경|파일|dpi|pymupdf|pdf2image
:---:|:---:|:---:|:---:|:---:
MacBook Pro M1|MZ세대를 위한 대학도서관 메타버스 서비스 사례.pdf|72|1.2689914|1.190654
MacBook Pro M1|MZ세대를 위한 대학도서관 메타버스 서비스 사례.pdf|200|6.175516|4.704655
MacBook Pro M1|adventuresofsher00doylrich.pdf|72|112.511795|122.892233

---
## Bing AI와의 질의응답

끝으로 Bing AI에게 다음 2가지 질문을 물어봤습니다.

### 질문: difference PyPDF and PyMuPDF and pdf2image

> Library|Description|Features
> ---|---|---
> PyPDF|A Python library for working with PDF files.|Extracting text and metadata from PDFs.
> PyMuPDF|A Python binding to MuPDF, a lightweight PDF, XPS, and eBook viewer, renderer, and toolkit.|Extracting text from PDFs.
> pdf2image|A Python library that converts PDF files to images.|Converting PDF pages to images.
> 
> Please note that PyMuPDF is considered better than PyPDF2 in terms of extracting text from PDFs. However, if you are looking for a library that converts PDF files to images, you can use pdf2image.
> 
> 1. Python Extract Text From PDF: PyPDF2 or PyMuPDF? Which is Better?, Tutorial Example, [https://www.tutorialexample.com/python-extract-text-from-pdf-pypdf2-or-pymupdf-which-is-better-python-tutorial/](https://www.tutorialexample.com/python-extract-text-from-pdf-pypdf2-or-pymupdf-which-is-better-python-tutorial/)
> 2. PyMuPDF, PyPI, [https://pypi.org/project/PyMuPDF/](https://pypi.org/project/PyMuPDF/)
> 3. Which the efficient python library that convert pdf to images apart from Wand,ImageMagick?, Stack Overflow, [https://stackoverflow.com/questions/48760628/which-the-efficient-python-library-that-convert-pdf-to-images-apart-from-wand-im](https://stackoverflow.com/questions/48760628/which-the-efficient-python-library-that-convert-pdf-to-images-apart-from-wand-im)
> 4. PyPDF2 vs X, PyPDF2 documentation, [https://pypdf2.readthedocs.io/en/3.0.0/meta/comparisons.html](https://pypdf2.readthedocs.io/en/3.0.0/meta/comparisons.html)
> 5. PyMuPDF : pdf2image, 공부하려고 만든 블로그, [https://welcome-to-dewy-world.tistory.com/114](https://welcome-to-dewy-world.tistory.com/114)

### 질문: difference between MuPDF and Xpdf

> MuPDF and Xpdf are both software frameworks for parsing and rendering PDF documents. Here are some details about each:
> 
> MuPDF: MuPDF is a free and open-source software framework written in C that implements a PDF, XPS, and EPUB parsing and rendering engine1. It is primarily used to render pages into bitmaps, but it also provides support for other operations such as searching and listing the table of contents and hyperlinks1. MuPDF is known for its high-performance rendering capabilities2.
> 
> Xpdf: Xpdf is a PDF rendering library based on the Xpdf project3. It is used by various software applications to parse and render PDF documents. Xpdf provides similar functionality to MuPDF, including rendering pages into bitmaps3.
> 
> In terms of performance, MuPDF has been reported to be faster than Xpdf in rendering images from PDF pages2. However, the actual performance may vary depending on the specific PDF file being processed2.
> 
> Please note that these frameworks have their own features and use cases. The choice between MuPDF and Xpdf may depend on your specific requirements and preferences.
> 
> 1. MuPDF, widipedia, [https://en.wikipedia.org/wiki/MuPDF](https://en.wikipedia.org/wiki/MuPDF)
> 2. is MuPdf library faster than xpdf/poppler at rendering images from pdf pages?, Stack Overflow, [https://stackoverflow.com/questions/7322768/is-mupdf-library-faster-than-xpdf-poppler-at-rendering-images-from-pdf-pages](https://stackoverflow.com/questions/7322768/is-mupdf-library-faster-than-xpdf-poppler-at-rendering-images-from-pdf-pages)
> 3. PDF, PS and DjVu, ArchWiki, [https://wiki.archlinux.org/title/PDF,_PS_and_DjVu](https://wiki.archlinux.org/title/PDF,_PS_and_DjVu)
> 4. Appendix 1: Performance, PyMuPDF Documentation, [https://documentation.help/PyMuPDF/app1.html](https://documentation.help/PyMuPDF/app1.html)
> 5. MuPDF, MuPDF, [https://mupdf.com/](https://mupdf.com/)
> 6. Poppler vs MuPDF, Johnny Huang, [https://hzqtc.github.io/2012/04/poppler-vs-mupdf.html](https://hzqtc.github.io/2012/04/poppler-vs-mupdf.html)
> 7. PDF rendering engine performance and fidelity comparison, Alfresco Hub, [https://hub.alfresco.com/t5/alfresco-content-services-blog/pdf-rendering-engine-performance-and-fidelity-comparison/ba-p/287618](https://hub.alfresco.com/t5/alfresco-content-services-blog/pdf-rendering-engine-performance-and-fidelity-comparison/ba-p/287618)

---
## 마무리하며

이번 글에서는 파이썬을 이용한 PDF를 이미지로 변환하는 방법을 알아보았습니다.

위에서 살펴본 라이브러리를 각자의 특징이 존재합니다.

예를 들어 `pypdf`는 파이썬만으로 동작하므로 OS 독립적입니다.

또한 `PyMuPDF`는 `MuPDF`를 기반으로 우수한 OS 호환과 일반적으로 가장 좋은 퍼포먼스를 보여줍니다.

마지막으로 `pdf2image`는 `Poppler`를 기반으로 단순 이미지 변환에 강점을 보입니다.

이 글이 각자의 상황에 맞는 라이브러리 선택에 도움이 되기를 바랍니다.

감사합니다. 😀

---
## 참고 문헌

- PDF, Wikipedia, [https://en.wikipedia.org/wiki/PDF]()https://en.wikipedia.org/wiki/PDF
- Welcome to pypdf, pypdf 3.16.1 documentation, [https://pypdf.readthedocs.io/en/latest/index.html](https://pypdf.readthedocs.io/en/latest/index.html)
- Welcome to PyMuPDF, PyMuPDF 1.23.3 documentation, [https://pymupdf.readthedocs.io/en/latest/](https://pymupdf.readthedocs.io/en/latest/)
- Belval_pdf2image, GitHub, [https://github.com/Belval/pdf2image](https://github.com/Belval/pdf2image)
