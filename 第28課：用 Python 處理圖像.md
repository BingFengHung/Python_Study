# 第 28 課：用 Python 處理圖像

## 基礎知識
1. 顏色
在電腦中，可將紅、綠、黃三種色光以不同的比例疊加起來組合成其他顏色，因此，這三種顏色就是色光三原色，他們是不能再分解的基本顏色。在電腦系統中，通常會將一個顏色表示為一個 RGB 值或 RGBA 值 (其中的 A 表示 Alpha 通道，決定了透過這個圖像的像素，也就是透明度)。

 |    名稱     |      RGB        |     名稱     |     RGB       |
 | :---------: | :-------------: | :----------: | :-----------: |
 | White（白） | (255, 255, 255) |  Red（紅）   |  (255, 0, 0)  |
 | Green（綠） |   (0, 255, 0)   |  Blue（藍）  |  (0, 0, 255)  |
 | Gray（灰）  | (128, 128, 128) | Yellow（黃） | (255, 255, 0) |
 | Black（黑） |    (0, 0, 0)    | Purple（紫） | (128, 0, 128) |

2. 像素
對於一個由數字序列表示的圖像來說，最小的單位就是圖像上單一顏色的小方格，這些小方塊都有一個明確的位置和被分配的色彩數值，而這些一小方格的顏色和位置決定了該圖像最終呈現出來的樣子，他們是不可分割的單位，通常稱之為像素 (pixel)。每一個圖像都包含了一定量的像素，這些像速決定圖像在畫面上所呈現的大小。

## 用 Pillow 處裡圖像
Pillow 是由著名的 Python 圖像處裡函數庫 PIL 發展出來的一個分支，透過 Pillow 可以實現圖像壓縮與圖像處裡各種操作。

```bash
pip install pillow
```
Pillow 中最重要的就是 `Image` 類別，可以透過 `Image` 模組的 `open` 函數來讀取圖像並取得 `Image` 類型的物件。

1. 讀取和顯示圖像

   ```py
   from PIL import Image
   
   # 讀取圖像獲得 Image 物件
   image = Image.open('guido.jpg')
   # 透過 Image 物件的 format 屬性取得圖像的格式
   print(image.format) # JPEG
   # 透過 Image 物件的 size 屬性取得圖像的尺寸
   print(image.size)   # (500, 750)
   # 透過 image 物件的 mode 屬性獲取圖像的模式
   print(image.mode)   # RGB
   # 透過 Image 物件的 show 方法顯示圖像
   image.show()
   ```

2. 剪裁圖像

   ```py
   # 透過 Image 物件的 crop 方法指定剪裁區域剪裁圖像
   image.crop((80, 20, 310, 360)).show()
   ```

3. 生成縮圖

   ```py
   # 透過 Image 物件的 thumbnail 方法產生指定尺寸的縮圖
   image.thumbnail((128, 128))
   image.show()
   ```

4. 縮放和黏貼圖像

   ```Python
   # 選取指定照片取得 Image 物件
   image1 = Image.open('image1.png')
   image2 = Image.open('image2.jpg')
   # 從 image2 裁剪出想要的部位
   part = image2.crop((80, 20, 310, 360))
   width, height = part.size
   # 使用 Image 物件的 resize 方法修改圖像的尺寸
   # 使用 Image 物件的 paste 方法將 part 黏貼到 image1 上面
   image1.paste(part.resize((int(width / 1.5), int(height / 1.5))), (172, 40))
   image1.show()
   ```

5. 旋轉和翻轉

   ```py
   image = Image.open('guido.jpg')
   # 使用 Image 物件的 rotate 方法實現圖像的旋轉
   image.rotate(45).show()
   # 使用 Image 物件的 transpose 方法實現圖像翻轉
   # Image.FLIP_LEFT_RIGHT - 水平翻轉
   # Image.FLIP_TOP_BOTTOM - 垂直翻轉
   image.transpose(Image.FLIP_TOP_BOTTOM).show()
   ```

6. 操作像素

   ```py
   for x in range(80, 310):
       for y in range(20, 360):
           # 透過 Image 物件的 putpixel 方法修改圖像指定像素點
           image.putpixel((x, y), (128, 128, 128))
   image.show()
   ```

7. 濾鏡效果

   ```py
   from PIL import ImageFilter
   
   # 使用 Image 物件的 filter 方法對圖像進行濾鏡處裡
   # ImageFilter 模組包含了諸多預設的濾鏡也可以自定義濾淨
   image.filter(ImageFilter.CONTOUR).show()
   ```

## 使用 Pillow 繪圖

Pillow 中有一個名為 `ImageDraw` 的模組，該模組的 `Draw` 函數會返回一個 `ImageDraw` 物件，透過 `ImageDraw` 物件的 `arc`、`line`、`rectange`、`ellipse`、`polygon` 等方法，可在圖像上繪製出圓弧、線條、矩形、多邊形等形狀，也可以透過該物件的 `text` 方法在圖像上加入文字。

```py
import random

from PIL import Image, ImageDraw, ImageFont


def random_color():
    """產生隨機顏色"""
    red = random.randint(0, 255)
    green = random.randint(0, 255)
    blue = random.randint(0, 255)
    return red, green, blue


width, height = 800, 600
# 建立一個 800*600 的圖像，背景色為白色
image = Image.new(mode='RGB', size=(width, height), color=(255, 255, 255))
# 建立一個 ImageDraw 物件
drawer = ImageDraw.Draw(image)
# 透過指定字體的大小取得 ImageFont 物件
font = ImageFont.truetype('Kongxin.ttf', 32)
# 透過 ImageDraw 物件的 text 方法繪製文字
drawer.text((300, 50), 'Hello, world!', fill=(255, 0, 0), font=font)
# 透過 ImageDraw 物件的 line 方法繪製兩條對角直線
drawer.line((0, 0, width, height), fill=(0, 0, 255), width=2)
drawer.line((width, 0, 0, height), fill=(0, 0, 255), width=2)
xy = width // 2 - 60, height // 2 - 60, width // 2 + 60, height // 2 + 60
# 透過 ImageDraw 物件的 rectangle 方法繪製矩形
drawer.rectangle(xy, outline=(255, 0, 0), width=2)
# 透過 ImageDraw 物件的 ellipses 方法繪製橢圓形
for i in range(4):
    left, top, right, bottom = 150 + i * 120, 220, 310 + i * 120, 380
    drawer.ellipse((left, top, right, bottom), outline=random_color(), width=8)

# 顯示圖像
image.show()

# 儲存圖像
image.save('result.png')
```
