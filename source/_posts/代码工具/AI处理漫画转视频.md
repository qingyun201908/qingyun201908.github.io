---
title: AI处理漫画转视频
date: 2025-04-09 23:32:53
tags: 代码工具
---
AI处理漫画转视频

第一步 从漫画PDF文件读取图片
第二部 图片信息剪裁
第三步 OCR识别处理图片，获取漫画对应的文本信息
第四步 运用阿里云通义大模型千文处理提取的文本信息更符合文本语言
第五步 运用FishVideo大模型将文本信息转变为对应的语音
第六步 图片转视频处理将图片与语音结合并添加转场
第七步 合并视频段
# 从PDF文件读取图片
```python
import fitz  # PyMuPDF
import os

def extract_images_from_pdf(pdf_path, output_folder):
    """
    从PDF文件中提取所有图片并按顺序保存到指定文件夹
    
    参数:
        pdf_path (str): PDF文件路径
        output_folder (str): 图片保存目录
    """
    # 创建输出目录
    os.makedirs(output_folder, exist_ok=True)
    
    # 打开PDF文件
    doc = fitz.open(pdf_path)
    
    # 初始化图片计数器
    image_count = 0
    
    # 遍历每一页
    for page_num in range(len(doc)):
        page = doc.load_page(page_num)
        
        # 获取当前页所有图片列表
        image_list = page.get_images(full=True)
        
        # 遍历当前页的图片
        for image_index, img in enumerate(image_list, start=1):
            xref = img[0]  # 获取图片xref
            base_image = doc.extract_image(xref)  # 提取图片信息
            image_data = base_image["image"]      # 获取图片二进制数据
            ext = base_image["ext"]               # 获取图片扩展名
            
            # 生成图片文件名
            image_filename = f"image_{image_count + 1}.{ext}"
            image_path = os.path.join(output_folder, image_filename)
            
            # 保存图片
            with open(image_path, "wb") as img_file:
                img_file.write(image_data)
            
            image_count += 1
    
    print(f"成功提取 {image_count} 张图片到目录: {output_folder}")

if __name__ == "__main__":
    # 配置参数（根据实际情况修改）
    pdf_file = "D:\\BaiduNetdiskDownload\\一-人-之-下第051-100话.pdf"  # PDF文件路径
    
    # 自动生成输出文件夹路径（PDF文件名 + _images）
    base_name = os.path.splitext(os.path.basename(pdf_file))[0]  # 去除扩展名
    folder_name = f"{base_name}_images"  # 生成文件夹名称
    output_dir = os.path.join(os.path.dirname(pdf_file), folder_name)  # 完整输出路径
    
    # 执行提取
    extract_images_from_pdf(pdf_file, output_dir)
```


### 代码解析

这段代码的主要功能是从指定的PDF文件中提取所有图片，并将这些图片按顺序保存到指定的文件夹中。下面是代码的详细分析：

#### 导入库
```python
import fitz  # PyMuPDF
import os
```
- `fitz`是PyMuPDF库的一个模块，用于处理PDF文件，包括读取和提取内容。
- `os`模块用于文件和目录的操作。

#### 提取函数
```python
def extract_images_from_pdf(pdf_path, output_folder):
```
- 定义一个名为`extract_images_from_pdf`的函数，接受两个参数：`pdf_path`表示PDF文件的路径，`output_folder`表示保存提取图片的文件夹。

##### 创建输出目录
```python
os.makedirs(output_folder, exist_ok=True)
```
- 使用`os.makedirs`创建保存图片的目录，`exist_ok=True`表示如果目录已存在则不抛出异常。

##### 打开PDF文件
```python
doc = fitz.open(pdf_path)
```
- 使用`fitz.open`打开PDF文件，并将其赋值给`doc`对象。

##### 初始化计数器
```python
image_count = 0
```
- 初始化`image_count`变量，用于统计提取的图片数量。

##### 遍历每一页
```python
for page_num in range(len(doc)):
    page = doc.load_page(page_num)
```
- 使用`for`循环遍历PDF的每一页，`doc.load_page(page_num)`加载当前页。

##### 获取和处理图片
```python
image_list = page.get_images(full=True)
```
- 获取当前页的所有图片，并将其存储在`image_list`中。

```python
for image_index, img in enumerate(image_list, start=1):
    xref = img[0]  # 获取图片xref
    base_image = doc.extract_image(xref)  # 提取图片信息
    image_data = base_image["image"]      # 获取图片二进制数据
    ext = base_image["ext"]               # 获取图片扩展名
```
- 对于每个图片，提取图片的引用（xref），然后获取其详细信息，包括二进制数据和扩展名。

##### 保存图片
```python
image_filename = f"image_{image_count + 1}.{ext}"
image_path = os.path.join(output_folder, image_filename)

with open(image_path, "wb") as img_file:
    img_file.write(image_data)

image_count += 1
```
- 生成图片文件名，构造完整的文件路径，并使用`with open`将图片数据写入文件。

##### 提示成功信息
```python
print(f"成功提取 {image_count} 张图片到目录: {output_folder}")
```
- 在控制台输出提取成功的图片数量及其保存路径。

#### 主程序块
```python
if __name__ == "__main__":
```
- 仅当该脚本作为主程序运行时，以下代码才会执行。

##### 配置参数
```python
pdf_file = "D:\\BaiduNetdiskDownload\\一-人-之-下第051-100话.pdf"  # PDF文件路径
```
- 设置要提取图片的PDF文件路径。

##### 自动生成输出文件夹路径
```python
base_name = os.path.splitext(os.path.basename(pdf_file))[0]
folder_name = f"{base_name}_images"
output_dir = os.path.join(os.path.dirname(pdf_file), folder_name)
```
- 自动生成输出文件夹的名称，格式为`PDF文件名_images`。

##### 执行提取
```python
extract_images_from_pdf(pdf_file, output_dir)
```
- 调用提取函数，开始提取图片。

### Markdown 文件内容

```markdown
# 从PDF提取图片的Python脚本

## 概述
该脚本用于从指定的PDF文件中提取所有图片并将其按顺序保存到指定的文件夹中。使用了`fitz`库（PyMuPDF）来处理PDF文件。

## 代码解析

### 导入库
```python
import fitz  # PyMuPDF
import os
```

### 提取函数
```python
def extract_images_from_pdf(pdf_path, output_folder):
```
- **参数**:
  - `pdf_path`: PDF文件的路径。
  - `output_folder`: 存储提取图片的文件夹路径。

### 创建输出目录
```python
os.makedirs(output_folder, exist_ok=True)
```

### 打开PDF文件
```python
doc = fitz.open(pdf_path)
```

### 初始化计数器
```python
image_count = 0
```

### 遍历每一页
```python
for page_num in range(len(doc)):
    page = doc.load_page(page_num)
```

### 获取和处理图片
```python
image_list = page.get_images(full=True)
```

### 保存图片
```python
image_filename = f"image_{image_count + 1}.{ext}"
image_path = os.path.join(output_folder, image_filename)

with open(image_path, "wb") as img_file:
    img_file.write(image_data)

image_count += 1
```

### 提示成功信息
```python
print(f"成功提取 {image_count} 张图片到目录: {output_folder}")
```

## 主程序块
```python
if __name__ == "__main__":
```
- 配置PDF文件路径和输出文件夹。

## 使用说明
1. 安装PyMuPDF库：`pip install PyMuPDF`。
2. 修改`pdf_file`变量为要提取的PDF文件路径。
3. 运行脚本，提取的图片将存储在指定的文件夹中。

## 注意事项
- 确保PDF文件路径正确。
- 输出文件夹将自动创建，若已存在则会被忽略。
```

# 图片信息剪裁
```python
from PIL import Image
import os

def batch_crop_images(input_folder, output_folder, top, bottom):
    """
    批量裁剪图片（仅修改下边界，保持左右宽度不变）
    :param input_folder: 输入文件夹路径
    :param output_folder: 输出文件夹路径
    :param top: 裁剪区域上边界
    :param bottom: 裁剪区域下边界
    """
    # 确保输出文件夹存在
    if not os.path.exists(output_folder):
        os.makedirs(output_folder)

    # 遍历输入文件夹中的所有文件
    for filename in os.listdir(input_folder):
        if filename.lower().endswith(('.png', '.jpg', '.jpeg', '.bmp', '.gif')):
            image_path = os.path.join(input_folder, filename)
            with Image.open(image_path) as img:
                width, height = img.size
                # 保持左右不变（left=0, right=图片宽度）
                # 动态调整下边界，确保不超过图片高度
                adjusted_bottom = min(bottom, height)
                # 裁剪区域：左=0, 上=top, 右=width, 下=adjusted_bottom
                cropped_img = img.crop((0, top, width, adjusted_bottom))
                output_path = os.path.join(output_folder, filename)
                cropped_img.save(output_path)
                print(f"裁剪并保存: {output_path}")

# 示例调用
input_folder = "D:\\BaiduNetdiskDownload\\051-100_images"
output_folder = "D:\\BaiduNetdiskDownload\\051-100_images2"
top, bottom = 0, 1225  # 仅设置上边界和下边界

batch_crop_images(input_folder, output_folder, top, bottom)
```

### 代码解析

该代码的主要功能是批量裁剪指定文件夹中的图片，保持左右宽度不变，只调整上下边界。裁剪后的图片将保存到指定的输出文件夹中。以下是代码的详细分析：

#### 导入库
```python
from PIL import Image
import os
```
- `PIL`（Python Imaging Library）: 用于图像处理，主要用于打开、操作和保存图像文件。
- `os`: 用于与操作系统交互，进行文件和目录操作。

#### 批量裁剪函数
```python
def batch_crop_images(input_folder, output_folder, top, bottom):
```
- 该函数接受四个参数：
  - `input_folder`: 输入文件夹路径，包含待裁剪的图片。
  - `output_folder`: 输出文件夹路径，保存裁剪后的图片。
  - `top`: 裁剪区域的上边界。
  - `bottom`: 裁剪区域的下边界。

##### 确保输出文件夹存在
```python
if not os.path.exists(output_folder):
    os.makedirs(output_folder)
```
- 检查输出文件夹是否存在，如果不存在则创建该文件夹。

##### 遍历输入文件夹中的所有文件
```python
for filename in os.listdir(input_folder):
    if filename.lower().endswith(('.png', '.jpg', '.jpeg', '.bmp', '.gif')):
```
- 遍历输入文件夹中的所有文件，并筛选出支持的图片格式（.png, .jpg, .jpeg, .bmp, .gif）。

##### 打开和裁剪图片
```python
image_path = os.path.join(input_folder, filename)
with Image.open(image_path) as img:
    width, height = img.size
```
- 构造每个图片的完整路径，使用`Image.open()`打开图片，并获取其宽度和高度。

###### 动态调整裁剪区域
```python
adjusted_bottom = min(bottom, height)
```
- 确保下边界不超过图片的实际高度，避免裁剪超出图片范围。

###### 裁剪并保存图片
```python
cropped_img = img.crop((0, top, width, adjusted_bottom))
output_path = os.path.join(output_folder, filename)
cropped_img.save(output_path)
print(f"裁剪并保存: {output_path}")
```
- 使用`crop()`方法进行裁剪，裁剪区域为左=0，上=top，右=width，下=adjusted_bottom。裁剪后的图片保存到输出路径，并打印保存消息。

### 示例调用
```python
input_folder = "D:\\BaiduNetdiskDownload\\051-100_images"
output_folder = "D:\\BaiduNetdiskDownload\\051-100_images2"
top, bottom = 0, 1225  # 仅设置上边界和下边界

batch_crop_images(input_folder, output_folder, top, bottom)
```
- 指定输入文件夹、输出文件夹及裁剪的上下边界，然后调用`batch_crop_images`函数进行批量裁剪操作。

### Markdown 文件内容

```markdown
# 批量裁剪图片脚本

## 概述
该脚本用于批量裁剪指定文件夹中的图片，裁剪区域的上下边界可由用户自定义，保持左右宽度不变。裁剪后的图片将保存到指定的输出文件夹中。

## 代码解析

### 导入库
```python
from PIL import Image
import os
```
- `PIL`: 用于图像处理。
- `os`: 用于文件和目录操作。

### 批量裁剪函数
```python
def batch_crop_images(input_folder, output_folder, top, bottom):
```
- 函数接受输入文件夹路径、输出文件夹路径、上边界和下边界。

#### 确保输出文件夹存在
```python
if not os.path.exists(output_folder):
    os.makedirs(output_folder)
```

#### 遍历输入文件夹中的所有文件
```python
for filename in os.listdir(input_folder):
    if filename.lower().endswith(('.png', '.jpg', '.jpeg', '.bmp', '.gif')):
```

#### 打开和裁剪图片
```python
image_path = os.path.join(input_folder, filename)
with Image.open(image_path) as img:
    width, height = img.size
```

#### 动态调整裁剪区域
```python
adjusted_bottom = min(bottom, height)
```

#### 裁剪并保存图片
```python
cropped_img = img.crop((0, top, width, adjusted_bottom))
output_path = os.path.join(output_folder, filename)
cropped_img.save(output_path)
print(f"裁剪并保存: {output_path}")
```

## 示例调用
```python
input_folder = "D:\\BaiduNetdiskDownload\\051-100_images"
output_folder = "D:\\BaiduNetdiskDownload\\051-100_images2"
top, bottom = 0, 1225  # 仅设置上边界和下边界

batch_crop_images(input_folder, output_folder, top, bottom)
```

## 使用说明
1. 确保已安装Pillow库（可以使用`pip install Pillow`）。
2. 指定输入文件夹和输出文件夹路径。
3. 调整上边界和下边界参数。
4. 运行脚本，裁剪后的图片将保存到输出文件夹中。

## 注意事项
- 确保输入文件夹中存在支持的图片格式。
- 裁剪区域的上边界和下边界应确保合理，以避免裁剪超出图片范围。
```

以上是对代码的详细解析及生成的Markdown文件内容，您可以根据需要进行调整和使用。
# OCR识别处理图片，获取漫画对应的文本信息
```python
import easyocr
import cv2
import os
import torch
from gc import collect  # 内存回收

# ------------------- 配置区域 -------------------
input_folder = r'D:\BaiduNetdiskDownload\051-100_images2'
languages = ['ch_sim', 'en']
supported_exts = ['.jpg', '.jpeg', '.png']
# ----------------------------------------------

# GTX 1050Ti 优化参数
config = {
    'batch_size': 4,           # 显存有限，建议4-6
    'workers': 2,              # 避免过多线程竞争
    'fp16': False,             # 1050Ti不支持Tensor Core，关闭半精度
    'model_load': 'balanced'   # 显存优化模式
}

def merge_paragraphs(results):
    """轻量级段落合并算法"""
    if not results:
        return []
    
    # 按Y坐标排序
    sorted_results = sorted(results, key=lambda x: x[0][0][1])
    
    # 合并逻辑（简化版）
    paragraphs = []
    current_para = []
    prev_bottom = -100  # 初始间距
    
    for box, text, _ in sorted_results:
        top = box[0][1]
        # 行间距超过50像素视为新段落（根据漫画排版调整）
        if (top - prev_bottom) > 50:
            if current_para:
                paragraphs.append(' '.join(current_para))
                current_para = []
        current_para.append(text.strip())
        prev_bottom = box[3][1]  # 更新底部坐标
    
    if current_para:
        paragraphs.append(' '.join(current_para))
    
    return paragraphs

def process_image(reader, image_path, output_path):
    """处理单张图片并管理显存"""
    try:
        # 读取图片
        img = cv2.imread(image_path)
        if img is None:
            raise ValueError("图片读取失败")
        
        # 显存优化策略
        torch.cuda.empty_cache()
        
        # 执行OCR
        results = reader.readtext(
            img,
            batch_size=config['batch_size'],
            workers=config['workers'],
            detail=1,          # 必须为1才能获取坐标
            paragraph=False    # 禁用内置段落合并（使用自定义算法）
        )
        
        # 合并段落
        paragraphs = merge_paragraphs(results)
        
        # 保存结果
        with open(output_path, 'w', encoding='utf-8') as f:
            f.write('\n'.join(paragraphs))
        
        return True
    except Exception as e:
        print(f"处理失败: {str(e)}")
        return False
    finally:
        collect()  # 强制回收内存

if __name__ == "__main__":
    # 验证CUDA
    if not torch.cuda.is_available():
        raise RuntimeError("CUDA不可用，请检查驱动")
    
    # 初始化Reader（显存优化配置）
    reader = easyocr.Reader(
        lang_list=languages,
        gpu=True,
        detector=True,
        recognizer=True,
        model_storage_directory=None,
        download_enabled=False,
        user_network_directory=None
    )
    
    # 遍历处理
    total = len([f for f in os.listdir(input_folder) if any(f.endswith(ext) for ext in supported_exts)])
    processed = 0
    
    for filename in os.listdir(input_folder):
        if not any(filename.lower().endswith(ext) for ext in supported_exts):
            continue
        
        image_path = os.path.join(input_folder, filename)
        output_path = os.path.join(
            input_folder,
            f"{os.path.splitext(filename)[0]}.txt"
        )
        
        print(f"处理中 [{processed+1}\{total}]: {filename[:20]}...", end='', flush=True)
        
        if process_image(reader, image_path, output_path):
            print(" ✓")
            processed += 1
        else:
            print(" ✗")
    
    print(f"\n完成！成功处理 {processed}\{total} 张图片")  
```

### 代码解析

该代码的主要功能是使用EasyOCR库从指定文件夹中的图片中提取文本，并将提取的文本保存到相应的文本文件中。使用了OpenCV库来读取图像，并且通过PyTorch对GPU进行优化。以下是代码的详细分析：

#### 导入库
```python
import easyocr
import cv2
import os
import torch
from gc import collect  # 内存回收
```
- `easyocr`: 用于图像文本识别（OCR）。
- `cv2`: OpenCV库，用于图像处理。
- `os`: 用于文件和目录操作。
- `torch`: PyTorch库，用于深度学习相关操作。
- `collect`: 从`gc`模块中导入，用于强制内存回收。

#### 配置区域
```python
input_folder = r'D:\BaiduNetdiskDownload\051-100_images2'
languages = ['ch_sim', 'en']
supported_exts = ['.jpg', '.jpeg', '.png']
```
- `input_folder`: 输入图片的文件夹路径。
- `languages`: 指定OCR识别的语言，这里包括简体中文和英语。
- `supported_exts`: 支持的图片扩展名列表。

#### GPU优化参数
```python
config = {
    'batch_size': 4,           
    'workers': 2,              
    'fp16': False,             
    'model_load': 'balanced'   
}
```
- `batch_size`: 设置为4以适应显存限制。
- `workers`: 工作线程数，避免过多线程竞争。
- `fp16`: 由于GTX 1050Ti不支持Tensor Core，设置为False以关闭半精度。
- `model_load`: 使用平衡模式优化显存使用。

#### 段落合并函数
```python
def merge_paragraphs(results):
```
- 该函数负责将OCR识别的文本结果进行段落合并，以便更好地处理文本输出。

##### 合并逻辑
- 按Y坐标排序，并根据行间距（超过50像素视为新段落）将文本合并。

#### 图像处理函数
```python
def process_image(reader, image_path, output_path):
```
- 处理单张图片并管理显存，读取图片，执行OCR，合并段落并保存结果。

##### 读取和检查图片
```python
img = cv2.imread(image_path)
if img is None:
    raise ValueError("图片读取失败")
```

##### 执行OCR
```python
results = reader.readtext(
    img,
    batch_size=config['batch_size'],
    workers=config['workers'],
    detail=1,
    paragraph=False
)
```
- 使用EasyOCR的`readtext`方法提取文本，设置详细级别为1以获取坐标。

##### 保存结果
```python
with open(output_path, 'w', encoding='utf-8') as f:
    f.write('\n'.join(paragraphs))
```

##### 内存回收
```python
finally:
    collect()  # 强制回收内存
```

#### 主程序块
```python
if __name__ == "__main__":
```
- 确保该代码块仅在脚本作为主程序运行时执行。

##### 验证CUDA
```python
if not torch.cuda.is_available():
    raise RuntimeError("CUDA不可用，请检查驱动")
```
- 检查CUDA是否可用，以确保可以使用GPU进行加速。

##### 初始化Reader
```python
reader = easyocr.Reader(
    lang_list=languages,
    gpu=True,
    detector=True,
    recognizer=True,
    model_storage_directory=None,
    download_enabled=False,
    user_network_directory=None
)
```
- 初始化EasyOCR的Reader对象，配置使用GPU。

##### 遍历处理图片
```python
total = len([f for f in os.listdir(input_folder) if any(f.endswith(ext) for ext in supported_exts)])
```
- 统计输入文件夹中支持的图片数量。

##### 处理每张图片
```python
for filename in os.listdir(input_folder):
```
- 遍历输入文件夹中的每个文件，检查扩展名并调用`process_image`处理。

##### 打印处理状态
```python
print(f"处理中 [{processed+1}\{total}]: {filename[:20]}...", end='', flush=True)
```
- 输出当前处理进度。

```python
print(f"\n完成！成功处理 {processed}\{total} 张图片")
```
- 最后输出处理结果。

### Markdown 文件内容

```markdown
# 使用EasyOCR从图片中提取文本的Python脚本

## 概述
该脚本通过EasyOCR库从指定文件夹中的图片中提取文本，并将提取的文本保存到相应的文本文件中。使用OpenCV库读取图像，并通过PyTorch对GPU进行优化。

## 代码解析

### 导入库
```python
import easyocr
import cv2
import os
import torch
from gc import collect  # 内存回收
```

### 配置区域
```python
input_folder = r'D:\BaiduNetdiskDownload\051-100_images2'
languages = ['ch_sim', 'en']
supported_exts = ['.jpg', '.jpeg', '.png']
```

### GPU优化参数
```python
config = {
    'batch_size': 4,           
    'workers': 2,              
    'fp16': False,             
    'model_load': 'balanced'   
}
```

### 段落合并函数
```python
def merge_paragraphs(results):
```
- 合并OCR结果中的段落，以提高可读性。

### 图像处理函数
```python
def process_image(reader, image_path, output_path):
```
- 读取图片，执行OCR，合并段落并保存结果。

### 主程序块
```python
if __name__ == "__main__":
```
- 检查CUDA是否可用，并初始化EasyOCR的Reader对象。

### 遍历处理图片
```python
total = len([f for f in os.listdir(input_folder) if any(f.endswith(ext) for ext in supported_exts)])
```
- 统计待处理的图片数量，逐一处理并输出处理进度。

## 使用说明
1. 安装必要的库：`pip install easyocr opencv-python torch`。
2. 修改`input_folder`为要处理的图片文件夹路径。
3. 运行脚本，提取的文本将保存为相应的`.txt`文件。

## 注意事项
- 确保安装了支持CUDA的PyTorch版本。
- 确保输入文件夹中存在支持的图片格式。
```

# 运用阿里云通义千文处理提取的文本信息更符合文本语言
```python
import os
import glob
import datetime
from openai import OpenAI
import openpyxl

# 初始化OpenAI客户端
client = OpenAI(
    api_key="",
    base_url="https:\\dashscope.aliyuncs.com\compatible-mode\v1",
)

# 初始化Excel文件
excel_file = "D:\\text_processing_log.xlsx"
if os.path.exists(excel_file):
    wb = openpyxl.load_workbook(excel_file)
else:
    wb = openpyxl.Workbook()
ws = wb.active
ws.title = "处理记录"
if ws.max_row == 1:
    ws.append(["类型", "内容", "文件路径", "处理时间"])

def save_to_excel(question, answer, file_path):
    """保存到Excel文件"""
    timestamp = datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S")
    ws.append(["问题", question, file_path, timestamp])
    ws.append(["回答", answer, file_path, timestamp])
    wb.save(excel_file)

def print_dialog(question, answer, max_width=80):
    """在CMD界面美观打印对话"""
    sep_line = '─' * max_width
    print(f"\n{sep_line}")
    print(" 问题 ".center(max_width, '░'))
    print(f"{question}\n")
    print(" 回答 ".center(max_width, '░'))
    print(f"{answer}")
    print(sep_line)

def log_error(log_file, message, error=None):
    """统一记录错误日志"""
    timestamp = datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S")
    with open(log_file, 'a', encoding='utf-8') as f:
        log_entry = f"[{timestamp}] {message}"
        if error:
            log_entry += f"\n错误详情: {str(error)}"
        log_entry += "\n" + "-"*50 + "\n"
        f.write(log_entry)

def process_file(file_path, log_file):
    """处理单个文件"""
    try:
        if os.path.getsize(file_path) == 0:
            print(f"跳过空文件: {file_path}")
            return

        with open(file_path, 'r+', encoding='utf-8') as f:
            original_lines = f.readlines()
            
            if not any(line.strip() for line in original_lines):
                print(f"跳过内容为空的文件: {file_path}")
                return

            f.seek(0)
            f.truncate()
            
            for line_num, line in enumerate(original_lines, 1):
                try:
                    if not line.strip():
                        f.write(line)
                        continue
                    
                    original_text = line.strip()
                    
                    completion = client.chat.completions.create(
                        model="qwen-plus",
                        messages=[{
                            'role': 'user',
                            'content': f'''原本句子中存在错词，不常见词语。可以修改词语和文字不采用偏僻词，不涵盖英语词汇，请严格按正常语序重组句子，只返回修改后的句子，不要任何解释。需要修改的句子：
{original_text}'''
                        }]
                    )
                    
                    modified = completion.choices[0].message.content
                    f.write(modified + line[len(line.rstrip('\n\r')):])
                    
                    # 显示对话并保存
                    print_dialog(original_text, modified)
                    save_to_excel(original_text, modified, file_path)
                    
                except Exception as e:
                    error_msg = f"文件: {file_path} 第{line_num}行处理失败 - 保留原内容"
                    print(f"\n! 处理异常: {error_msg}")
                    log_error(log_file, error_msg, e)
                    f.write(line)

    except Exception as e:
        error_msg = f"文件处理失败: {file_path}"
        print(f"\n! 严重错误: {error_msg}")
        log_error(log_file, error_msg, e)

def process_folder(folder_path):
    """处理整个文件夹"""
    log_file = os.path.join(folder_path, "processing_errors.log")
    
    with open(log_file, 'w', encoding='utf-8') as f:
        f.write("文本处理异常日志\n")
        f.write(f"开始时间: {datetime.datetime.now().strftime('%Y-%m-%d %H:%M:%S')}\n")
        f.write("="*60 + "\n")
    
    txt_files = glob.glob(os.path.join(folder_path, '**', '*.txt'), recursive=True)
    
    if not txt_files:
        print(f"在 {folder_path} 中未找到txt文件")
        return
    
    print(f"开始批量处理，共发现 {len(txt_files)} 个文本文件")
    print(f"处理记录将保存至: {os.path.abspath(excel_file)}")
    print("="*60)
    
    for i, file_path in enumerate(txt_files, 1):
        print(f"\n正在处理文件 ({i}\{len(txt_files)})：{os.path.basename(file_path)}")
        process_file(file_path, log_file)
    
    print("\n" + "="*60)
    print(f"处理完成！错误日志已保存至: {log_file}")
    print(f"共处理 {len(txt_files)*2} 条记录到Excel文件")

if __name__ == "__main__":
    try:
        process_folder('D:\\BaiduNetdiskDownload\\001-050_images')
    finally:
        wb.close()
```


### 代码解析

该代码的主要功能是批量处理指定文件夹中的文本文件，使用OpenAI的API对文本内容进行处理，然后将处理的记录保存到Excel文件中，并记录错误日志。以下是代码的详细分析：

#### 导入库
```python
import os
import glob
import datetime
from openai import OpenAI
import openpyxl
```
- `os`: 用于文件和目录操作。
- `glob`: 用于查找符合特定规则的文件路径名。
- `datetime`: 用于处理日期和时间。
- `OpenAI`: 用于与OpenAI API交互。
- `openpyxl`: 用于处理Excel文件。

#### 初始化OpenAI客户端
```python
client = OpenAI(
    api_key="",
    base_url="https:\\dashscope.aliyuncs.com\compatible-mode\v1",
)
```
- 初始化OpenAI API客户端，需提供API密钥。

#### 初始化Excel文件
```python
excel_file = "D:\\text_processing_log.xlsx"
if os.path.exists(excel_file):
    wb = openpyxl.load_workbook(excel_file)
else:
    wb = openpyxl.Workbook()
ws = wb.active
ws.title = "处理记录"
if ws.max_row == 1:
    ws.append(["类型", "内容", "文件路径", "处理时间"])
```
- 检查Excel文件是否存在，若不存在则创建新的Excel工作簿，并设置工作表标题和表头。

#### 保存数据到Excel
```python
def save_to_excel(question, answer, file_path):
```
- 该函数接受问题、答案和文件路径，并将它们写入Excel文件中，同时记录当前时间。

#### 打印对话
```python
def print_dialog(question, answer, max_width=80):
```
- 在命令行窗口美观地打印问题和答案，使用分隔线以增强可读性。

#### 记录错误日志
```python
def log_error(log_file, message, error=None):
```
- 记录错误信息到日志文件中，格式化输出时间和错误详情。

#### 处理单个文件
```python
def process_file(file_path, log_file):
```
- 该函数负责读取文本文件，处理每一行文本，并调用OpenAI的API进行文本修改。

##### 检查文件内容
```python
if os.path.getsize(file_path) == 0:
    print(f"跳过空文件: {file_path}")
    return
```
- 跳过空文件和内容为空的文件。

##### 读取和处理文本
```python
with open(file_path, 'r+', encoding='utf-8') as f:
    original_lines = f.readlines()
```
- 读取文件所有行，并逐行处理。

##### 调用OpenAI API
```python
completion = client.chat.completions.create(
    model="qwen-plus",
    messages=[{
        'role': 'user',
        'content': f'''原本句子中存在错词，不常见词语。可以修改词语和文字不采用偏僻词，不涵盖英语词汇，请严格按正常语序重组句子，只返回修改后的句子，不要任何解释。需要修改的句子：
{original_text}'''
    }]
)
```
- 使用OpenAI的API进行文本修改，API模型为`qwen-plus`。

##### 保存修改后的文本
```python
f.write(modified + line[len(line.rstrip('\n\r')):])
```
- 将修改后的文本写回文件，同时保留行末的换行符。

#### 处理整个文件夹
```python
def process_folder(folder_path):
```
- 遍历指定文件夹中的所有文本文件，并调用`process_file`进行处理。

##### 日志记录
```python
log_file = os.path.join(folder_path, "processing_errors.log")
```
- 创建一个日志文件来记录处理过程中出现的错误信息。

##### 查找文本文件
```python
txt_files = glob.glob(os.path.join(folder_path, '**', '*.txt'), recursive=True)
```
- 使用`glob`模块查找指定文件夹下的所有文本文件。

#### 主程序
```python
if __name__ == "__main__":
```
- 该部分保证代码在作为主程序运行时执行文件夹处理函数。

### Markdown 文件内容

```markdown
# 文本处理脚本

## 概述
该脚本用于批量处理指定文件夹中的文本文件，使用OpenAI的API对文本内容进行处理，并将处理记录保存到Excel文件中，同时记录错误日志。

## 代码解析

### 导入库
```python
import os
import glob
import datetime
from openai import OpenAI
import openpyxl
```

### 初始化OpenAI客户端
```python
client = OpenAI(
    api_key="",
    base_url="https:\\dashscope.aliyuncs.com\compatible-mode\v1",
)
```

### 初始化Excel文件
```python
excel_file = "D:\\text_processing_log.xlsx"
if os.path.exists(excel_file):
    wb = openpyxl.load_workbook(excel_file)
else:
    wb = openpyxl.Workbook()
ws = wb.active
ws.title = "处理记录"
if ws.max_row == 1:
    ws.append(["类型", "内容", "文件路径", "处理时间"])
```

### 保存数据到Excel
```python
def save_to_excel(question, answer, file_path):
```
- 保存处理记录到Excel文件。

### 打印对话
```python
def print_dialog(question, answer, max_width=80):
```
- 在命令行窗口美观地打印问题和答案。

### 记录错误日志
```python
def log_error(log_file, message, error=None):
```
- 记录错误信息到日志文件中。

### 处理单个文件
```python
def process_file(file_path, log_file):
```
- 读取文本文件，处理每一行文本，并调用OpenAI的API进行文本修改。

### 处理整个文件夹
```python
def process_folder(folder_path):
```
- 遍历指定文件夹中的所有文本文件，并调用`process_file`进行处理。

## 使用说明
1. 确保安装了必要的库：`pip install openai openpyxl`。
2. 填写OpenAI API密钥。
3. 修改文件夹路径以处理文本文件。
4. 运行脚本，处理记录将保存到Excel文件中，错误日志将保存在文件夹中。

## 注意事项
- 确保OpenAI API服务可用。
- 检查文件夹路径和文件权限。
```

# 运用FishVideo大模型将文本信息转变为对应的语音
```python
import os
import logging
from fish_audio_sdk import Session, TTSRequest, ReferenceAudio

# 配置日志记录异常
logging.basicConfig(
    filename='tts_errors.log',
    level=logging.ERROR,
    format='%(asctime)s - %(levelname)s - %(message)s'
)

def process_directory(target_dir: str):
    """处理指定目录下的文本文件
    
    Args:
        target_dir: 包含文本文件和生成音频文件的目录
    """
    session = Session("")
    
    processed = 0
    skipped = 0
    errors = 0

    for filename in os.listdir(target_dir):
        if not filename.endswith(".txt"):
            continue

        file_path = os.path.join(target_dir, filename)
        base_name = os.path.splitext(filename)[0]
        audio_path = os.path.join(target_dir, f"{base_name}.mp3")

        try:
            # 读取文本内容
            with open(file_path, "r", encoding="utf-8") as f:
                text = f.read().strip()

            # 跳过空文件
            if not text:
                logging.info(f"跳过空文件: {filename}")
                skipped += 1
                continue

            # 生成语音文件
            with open(audio_path, "wb") as f:
                for chunk in session.tts(TTSRequest(
                    reference_id="7f92f8afb8ec43bf81429cc1c9199cb1",
                    text=text
                )):
                    f.write(chunk)

            print(f"生成成功: {filename} -> {os.path.basename(audio_path)}")
            processed += 1

        except Exception as e:
            logging.error(f"文件 {filename} 处理失败: {str(e)}", exc_info=True)
            print(f"错误: {filename} 转换失败，详见日志")
            errors += 1

    # 输出统计报告
    print(f"\n处理完成: 成功 {processed} 个 | 跳过 {skipped} 个 | 失败 {errors} 个")

if __name__ == "__main__":
    # 使用示例：处理当前目录下的文件
    process_directory(target_dir="D:\\BaiduNetdiskDownload\\001-050_images")
```

### 代码解析

该代码的主要功能是处理指定目录下的文本文件，利用`fish_audio_sdk`库将文本转换为语音，并生成相应的音频文件。以下是代码的详细分析：

#### 导入库
```python
import os
import logging
from fish_audio_sdk import Session, TTSRequest, ReferenceAudio
```
- `os`: 用于文件和目录操作。
- `logging`: 用于记录日志，方便后续的错误追踪和调试。
- `fish_audio_sdk`: 语音合成SDK，主要用于将文本转换为语音。

#### 配置日志
```python
logging.basicConfig(
    filename='tts_errors.log',
    level=logging.ERROR,
    format='%(asctime)s - %(levelname)s - %(message)s'
)
```
- 配置日志记录，日志将被写入`tts_errors.log`文件，记录错误信息及时间戳。

#### 处理目录函数
```python
def process_directory(target_dir: str):
```
- 该函数接受一个参数`target_dir`，表示包含文本文件和生成音频文件的目录。

##### 初始化变量
```python
session = Session("")
processed = 0
skipped = 0
errors = 0
```
- 创建一个`Session`对象以进行语音合成。
- 初始化计数器：`processed`用于记录成功处理的文件数量，`skipped`用于记录跳过的文件数量，`errors`用于记录处理失败的文件数量。

##### 遍历文件
```python
for filename in os.listdir(target_dir):
    if not filename.endswith(".txt"):
        continue
```
- 遍历指定目录下的文件，筛选出扩展名为`.txt`的文件。

##### 处理每个文件
```python
file_path = os.path.join(target_dir, filename)
base_name = os.path.splitext(filename)[0]
audio_path = os.path.join(target_dir, f"{base_name}.mp3")
```
- 拼接文件路径和音频文件的保存路径。

###### 读取文本内容
```python
with open(file_path, "r", encoding="utf-8") as f:
    text = f.read().strip()
```
- 读取文本文件内容，并去除首尾空格。

###### 跳过空文件
```python
if not text:
    logging.info(f"跳过空文件: {filename}")
    skipped += 1
    continue
```
- 如果文件内容为空，则记录日志并跳过处理。

###### 生成语音文件
```python
with open(audio_path, "wb") as f:
    for chunk in session.tts(TTSRequest(
        reference_id="7f92f8afb8ec43bf81429cc1c9199cb1",
        text=text
    )):
        f.write(chunk)
```
- 调用`session.tts`生成语音文件并写入到指定的路径。`TTSRequest`中包含了文本和一个参考ID。

###### 成功处理
```python
print(f"生成成功: {filename} -> {os.path.basename(audio_path)}")
processed += 1
```
- 打印生成成功的消息，并增加成功处理计数。

###### 错误处理
```python
except Exception as e:
    logging.error(f"文件 {filename} 处理失败: {str(e)}", exc_info=True)
    print(f"错误: {filename} 转换失败，详见日志")
    errors += 1
```
- 捕获异常，记录错误并增加失败计数。

##### 输出统计报告
```python
print(f"\n处理完成: 成功 {processed} 个 | 跳过 {skipped} 个 | 失败 {errors} 个")
```
- 在处理完成后输出统计报告。

#### 主程序
```python
if __name__ == "__main__":
    process_directory(target_dir="D:\\BaiduNetdiskDownload\\001-050_images")
```
- 主程序入口，调用`process_directory`处理指定目录下的文本文件。

### Markdown 文件内容

```markdown
# 文本转语音处理脚本

## 概述
该脚本用于处理指定目录下的文本文件，通过`fish_audio_sdk`将文本转换为语音，并生成对应的音频文件。处理过程中会记录成功、跳过和失败的文件数量，并将错误日志保存到文件中。

## 代码解析

### 导入库
```python
import os
import logging
from fish_audio_sdk import Session, TTSRequest, ReferenceAudio
```

### 配置日志
```python
logging.basicConfig(
    filename='tts_errors.log',
    level=logging.ERROR,
    format='%(asctime)s - %(levelname)s - %(message)s'
)
```
- 配置日志记录，记录错误信息到`tts_errors.log`文件中。

### 处理目录函数
```python
def process_directory(target_dir: str):
```
- 处理指定目录下的文本文件，并生成语音文件。

#### 初始化变量
```python
session = Session("")
processed = 0
skipped = 0
errors = 0
```

#### 遍历文件
```python
for filename in os.listdir(target_dir):
    if not filename.endswith(".txt"):
        continue
```
- 只处理扩展名为`.txt`的文件。

#### 处理每个文件
```python
file_path = os.path.join(target_dir, filename)
base_name = os.path.splitext(filename)[0]
audio_path = os.path.join(target_dir, f"{base_name}.mp3")
```
- 生成文本文件的路径和对应的音频文件路径。

#### 读取文本内容
```python
with open(file_path, "r", encoding="utf-8") as f:
    text = f.read().strip()
```

#### 跳过空文件
```python
if not text:
    logging.info(f"跳过空文件: {filename}")
    skipped += 1
    continue
```

#### 生成语音文件
```python
with open(audio_path, "wb") as f:
    for chunk in session.tts(TTSRequest(
        reference_id="7f92f8afb8ec43bf81429cc1c9199cb1",
        text=text
    )):
        f.write(chunk)
```

#### 输出统计报告
```python
print(f"\n处理完成: 成功 {processed} 个 | 跳过 {skipped} 个 | 失败 {errors} 个")
```

## 使用说明
1. 确保已安装`fish_audio_sdk`库。
2. 将要处理的文本文件放入指定目录。
3. 运行脚本，生成的音频文件将与文本文件保存在同一目录中。

## 注意事项
- 确保`fish_audio_sdk`的配置正确，API密钥和参考ID有效。
- 检查文件权限，确保可以读取文本文件和写入音频文件。
```
# 图片转视频处理将图片与语音结合并添加转场
```python
import os
import glob
import subprocess

# 支持的图片和音频扩展名
IMAGE_EXTENSIONS = ['.jpg', '.jpeg', '.png', '.bmp']
AUDIO_EXTENSIONS = ['.mp3', '.wav', '.ogg']

def find_audio_file(image_path):
    """查找与图片同名的音频文件"""
    base_name = os.path.splitext(image_path)[0]
    for ext in AUDIO_EXTENSIONS:
        audio_path = f"{base_name}{ext}"
        if os.path.exists(audio_path):
            return audio_path
    return None

def create_video(image_path, output_folder):
    """使用 FFmpeg 创建视频"""
    base_name = os.path.splitext(os.path.basename(image_path))[0]
    output_path = os.path.join(output_folder, f"{base_name}.mp4")
    
    cmd = [
        'ffmpeg',
        '-y',
        '-loop', '1',
        '-i', image_path,
    ]

    if audio_path := find_audio_file(image_path):
        cmd += ['-i', audio_path]
    else:
        cmd += [
            '-f', 'lavfi',
            '-i', 'anullsrc=cl=stereo:r=44100',
            '-t', '3'
        ]

    cmd += [
        '-vf', 
        'scale=trunc(iw\2)*2:trunc(ih\2)*2,fps=24,format=yuv420p',  # 关键修改点
        '-c:v', 'libx264',
        '-c:a', 'aac',
        '-shortest',
        '-movflags', '+faststart',
        output_path
    ]

    try:
        subprocess.run(cmd, check=True, capture_output=True, text=True)
        return True
    except subprocess.CalledProcessError as e:
        print(f"FFmpeg 错误: {e.stderr}")
        return False

def process_folder(folder_path):
    """处理整个文件夹"""
    # 收集所有图片文件
    image_files = []
    for ext in IMAGE_EXTENSIONS:
        image_files.extend(glob.glob(os.path.join(folder_path, '*' + ext)))

    # 创建输出目录
    output_folder = os.path.join(folder_path, "output_videos")
    os.makedirs(output_folder, exist_ok=True)

    # 处理每个图片文件
    for img_file in image_files:
        print(f"正在处理: {os.path.basename(img_file)}")
        if create_video(img_file, output_folder):
            print(f"成功创建: {os.path.basename(img_file)}.mp4")
        else:
            print(f"处理失败: {os.path.basename(img_file)}")
        print("-" * 50)

if __name__ == "__main__":
    target_folder = "D:\\BaiduNetdiskDownload\\001-050_images"
    
    if not os.path.exists(target_folder):
        print(f"错误: 目录不存在 - {target_folder}")
    else:
        process_folder(target_folder)
        print("全部处理完成！")
```

### 代码解析

该代码的主要功能是使用FFmpeg将指定文件夹中的图片转换为视频，并在视频中添加与图片同名的音频文件（如果存在）。以下是代码的详细分析：

#### 导入库
```python
import os
import glob
import subprocess
```
- `os`: 用于文件和目录操作。
- `glob`: 用于查找符合特定规则的文件路径名。
- `subprocess`: 用于执行外部命令，这里用来调用FFmpeg。

#### 支持的文件扩展名
```python
IMAGE_EXTENSIONS = ['.jpg', '.jpeg', '.png', '.bmp']
AUDIO_EXTENSIONS = ['.mp3', '.wav', '.ogg']
```
- 定义支持的图像和音频文件扩展名。

#### 查找音频文件
```python
def find_audio_file(image_path):
    """查找与图片同名的音频文件"""
    base_name = os.path.splitext(image_path)[0]
    for ext in AUDIO_EXTENSIONS:
        audio_path = f"{base_name}{ext}"
        if os.path.exists(audio_path):
            return audio_path
    return None
```
- 该函数接收图片路径，查找是否存在与之同名的音频文件，并返回找到的音频文件路径，如果未找到则返回`None`。

#### 创建视频
```python
def create_video(image_path, output_folder):
    """使用 FFmpeg 创建视频"""
    base_name = os.path.splitext(os.path.basename(image_path))[0]
    output_path = os.path.join(output_folder, f"{base_name}.mp4")
```
- 函数负责使用FFmpeg将图片转换为视频。

##### 构建FFmpeg命令
```python
cmd = [
    'ffmpeg',
    '-y',
    '-loop', '1',
    '-i', image_path,
]
```
- 使用`-loop 1`选项使图片循环显示。

###### 添加音频
```python
if audio_path := find_audio_file(image_path):
    cmd += ['-i', audio_path]
else:
    cmd += [
        '-f', 'lavfi',
        '-i', 'anullsrc=cl=stereo:r=44100',
        '-t', '3'
    ]
```
- 如果找到与图片同名的音频文件，则将其添加到FFmpeg命令中；否则，使用虚拟音频源（无声）生成长度为3秒的音频。

###### 设定视频输出参数
```python
cmd += [
    '-vf', 
    'scale=trunc(iw\2)*2:trunc(ih\2)*2,fps=24,format=yuv420p',
    '-c:v', 'libx264',
    '-c:a', 'aac',
    '-shortest',
    '-movflags', '+faststart',
    output_path
]
```
- 设置视频过滤器，确保宽高是偶数，并设置每秒帧数（fps）为24。视频编码器为`libx264`，音频编码器为`aac`。

###### 运行FFmpeg命令
```python
try:
    subprocess.run(cmd, check=True, capture_output=True, text=True)
    return True
except subprocess.CalledProcessError as e:
    print(f"FFmpeg 错误: {e.stderr}")
    return False
```
- 执行命令并处理可能出现的错误。

#### 处理文件夹
```python
def process_folder(folder_path):
    """处理整个文件夹"""
```
- 该函数遍历指定目录，处理所有支持的图片文件。

##### 收集图片文件
```python
image_files = []
for ext in IMAGE_EXTENSIONS:
    image_files.extend(glob.glob(os.path.join(folder_path, '*' + ext)))
```
- 使用`glob`查找所有支持的图片文件。

##### 创建输出目录
```python
output_folder = os.path.join(folder_path, "output_videos")
os.makedirs(output_folder, exist_ok=True)
```
- 创建一个名为`output_videos`的文件夹，用于保存生成的视频文件。

##### 处理每个图片文件
```python
for img_file in image_files:
    print(f"正在处理: {os.path.basename(img_file)}")
    if create_video(img_file, output_folder):
        print(f"成功创建: {os.path.basename(img_file)}.mp4")
    else:
        print(f"处理失败: {os.path.basename(img_file)}")
    print("-" * 50)
```
- 对每个图片文件调用`create_video`函数，并报告处理结果。

#### 主程序
```python
if __name__ == "__main__":
    target_folder = "D:\\BaiduNetdiskDownload\\001-050_images"
    
    if not os.path.exists(target_folder):
        print(f"错误: 目录不存在 - {target_folder}")
    else:
        process_folder(target_folder)
        print("全部处理完成！")
```
- 主程序入口，检查目标文件夹是否存在，并调用`process_folder`处理图像。

### Markdown 文件内容

```markdown
# 图片转视频处理脚本

## 概述
该脚本用于将指定文件夹中的图片文件转换为视频，并在视频中添加与图片同名的音频文件（如果存在）。生成的视频文件将保存到一个单独的输出目录中。

## 代码解析

### 导入库
```python
import os
import glob
import subprocess
```

### 支持的文件扩展名
```python
IMAGE_EXTENSIONS = ['.jpg', '.jpeg', '.png', '.bmp']
AUDIO_EXTENSIONS = ['.mp3', '.wav', '.ogg']
```

### 查找音频文件
```python
def find_audio_file(image_path):
```
- 查找与图片同名的音频文件。

### 创建视频
```python
def create_video(image_path, output_folder):
```
- 使用FFmpeg将图片转换为视频。

#### 构建FFmpeg命令
```python
cmd = [
    'ffmpeg',
    '-y',
    '-loop', '1',
    '-i', image_path,
]
```

#### 添加音频
```python
if audio_path := find_audio_file(image_path):
    cmd += ['-i', audio_path]
else:
    cmd += [
        '-f', 'lavfi',
        '-i', 'anullsrc=cl=stereo:r=44100',
        '-t', '3'
    ]
```

#### 设置视频输出参数
```python
cmd += [
    '-vf', 
    'scale=trunc(iw\2)*2:trunc(ih\2)*2,fps=24,format=yuv420p',
    '-c:v', 'libx264',
    '-c:a', 'aac',
    '-shortest',
    '-movflags', '+faststart',
    output_path
]
```

### 处理文件夹
```python
def process_folder(folder_path):
```
- 处理指定目录中的所有图片文件，并生成视频。

### 主程序
```python
if __name__ == "__main__":
```
- 检查目标目录并执行处理。

## 使用说明
1. 确保已安装FFmpeg并将其路径添加到系统环境变量。
2. 将要处理的图片文件放入指定目录。
3. 运行脚本，生成的视频文件将保存到`output_videos`子目录中。

## 注意事项
- 确保文件夹路径和文件权限正确。
- 处理时请检查FFmpeg的输出信息以获取错误提示。
```

# 合并视频段
```python
import os
import subprocess

def merge_videos(folder_path, output_file):
    # 获取所有视频文件
    video_files = [f for f in os.listdir(folder_path) if f.endswith(('.mp4', '.mkv', '.avi', '.mov'))]
    video_files.sort()  # 根据名称排序，确保顺序正确

    # 生成 FFmpeg 需要的输入列表文件
    list_file = os.path.join(folder_path, 'file_list.txt')
    with open(list_file, 'w', encoding='utf-8') as f:
        for video in video_files:
            f.write(f"file '{os.path.join(folder_path, video)}'\n")

    # FFmpeg 合并视频
    ffmpeg_cmd = f'ffmpeg -f concat -safe 0 -i "{list_file}" -c copy "{output_file}"'
    subprocess.run(ffmpeg_cmd, shell=True, check=True)

    # 清理文件
    os.remove(list_file)
    print(f"合并完成：{output_file}")

# 使用示例
merge_videos("D:\\BaiduNetdiskDownload\\001-050_images\\output_videos", "output.mp4")
```
下面是对您提供的Python代码的详细解析，以及生成的Markdown文件内容。

### 代码解析


#### 导入库
```python
import os
import subprocess
```
- `os`: 用于文件和目录操作。
- `subprocess`: 用于执行外部命令，这里用来调用FFmpeg。

#### 合并视频函数
```python
def merge_videos(folder_path, output_file):
```
- 该函数接受两个参数：一个是包含视频文件的文件夹路径，另一个是合并后输出视频文件的名称。

##### 获取视频文件
```python
video_files = [f for f in os.listdir(folder_path) if f.endswith(('.mp4', '.mkv', '.avi', '.mov'))]
video_files.sort()  # 根据名称排序，确保顺序正确
```
- 获取指定文件夹中的所有视频文件，筛选出扩展名为`.mp4`、`.mkv`、`.avi`和`.mov`的视频文件，并将其按名称排序，以确保合并顺序正确。

##### 生成FFmpeg输入列表文件
```python
list_file = os.path.join(folder_path, 'file_list.txt')
with open(list_file, 'w', encoding='utf-8') as f:
    for video in video_files:
        f.write(f"file '{os.path.join(folder_path, video)}'\n")
```
- 创建一个名为`file_list.txt`的文本文件，FFmpeg将使用这个文件来读取需要合并的视频文件路径。每行包含一个视频文件的路径。

##### 执行FFmpeg命令
```python
ffmpeg_cmd = f'ffmpeg -f concat -safe 0 -i "{list_file}" -c copy "{output_file}"'
subprocess.run(ffmpeg_cmd, shell=True, check=True)
```
- 构建FFmpeg命令，使用`-f concat`选项来合并文件，并使用`-c copy`选项以快速地复制视频流而不进行重新编码。通过`subprocess.run`执行命令。

##### 清理临时文件
```python
os.remove(list_file)
print(f"合并完成：{output_file}")
```
- 合并完成后删除临时的`file_list.txt`文件，并打印合并完成的消息。

### 使用示例
```python
merge_videos("D:\\BaiduNetdiskDownload\\001-050_images\\output_videos", "output.mp4")
```
- 调用`merge_videos`函数，传入包含视频文件的文件夹路径和输出文件名。

### Markdown 文件内容

```markdown
# 视频合并脚本

## 概述
该脚本用于将指定文件夹中的多个视频文件合并为一个视频，使用FFmpeg工具来完成合并操作。

## 代码解析

### 导入库
```python
import os
import subprocess
```
- `os`: 用于文件和目录操作。
- `subprocess`: 用于执行外部命令，这里用来调用FFmpeg。

### 合并视频函数
```python
def merge_videos(folder_path, output_file):
```
- 该函数接受两个参数：包含视频文件的文件夹路径和合并后输出视频文件的名称。

#### 获取视频文件
```python
video_files = [f for f in os.listdir(folder_path) if f.endswith(('.mp4', '.mkv', '.avi', '.mov'))]
video_files.sort()  # 根据名称排序，确保顺序正确
```

#### 生成FFmpeg输入列表文件
```python
list_file = os.path.join(folder_path, 'file_list.txt')
with open(list_file, 'w', encoding='utf-8') as f:
    for video in video_files:
        f.write(f"file '{os.path.join(folder_path, video)}'\n")
```

#### 执行FFmpeg命令
```python
ffmpeg_cmd = f'ffmpeg -f concat -safe 0 -i "{list_file}" -c copy "{output_file}"'
subprocess.run(ffmpeg_cmd, shell=True, check=True)
```

#### 清理临时文件
```python
os.remove(list_file)
print(f"合并完成：{output_file}")
```

## 使用说明
1. 确保已安装FFmpeg并将其路径添加到系统环境变量。
2. 将要合并的视频文件放入指定文件夹。
3. 运行脚本，将多个视频合并为一个视频文件。

## 注意事项
- 确保所有视频文件格式相同，以避免合并时出现问题。
- 处理时请检查FFmpeg的输出信息以获取可能的错误提示。
```

仓库代码
[AIDrow](https:\\github.com\qingyun201908\AIDrowToVideo)

![alt text](https:\\raw.githubusercontent.com\qingyun201908\qingyun201908.github.io\images\images\AI处理漫画转视频\image_20250409202305.png)