

# 自製圖片轉txt小工具
> 參考資料: [Python趣味实用小工具](http://www.demodashi.com/demo/12918.html)

## 何謂套件
套件是別人寫好的工具，我們可以用這些工具提供給我們的api(application programming interface)來完成我們想做的事情。

## PIL套件介紹
PIL(Python Image Library)是一套處理圖片的套件，他可以調整圖片的大小、亮度、對比、色調、裁切圖片、旋轉圖片、加濾鏡、附加文字......等等，雖然沒有專業的圖像編輯軟體來的強大，但可以批次處理圖片，而且彈性較大。

## 圖片檔概念
圖像檔案保存的方式是這樣的，假設有一張200x100的圖片檔，顏色編碼為RGB，這是什麼意思呢?
200x100指的是像素，這張圖片的寬有200個像素、高有100，也就是說這張圖片總共有20000個像素
那每個像素究竟存了什麼值? 這邊看到RGB，代表每個像素存取的是RGB三個顏色的值，每個顏色一個Byte(8bit)

## 設計想法
* 目標: 製作一個圖片轉成文字檔的小工具
* 核心流程: 將圖片轉為黑白，讀取每個像素，是黑的就填上@符號，是白的就填上_符號

## 實作

> 完整程式碼: 
> 在開始之前先確定電腦有裝好python3、pip、Notepad++
> notepad++載點: https://notepad-plus-plus.org/download/v7.6.6.htmlNotepad++ 
> 選這個zip package 32-bit x86

1. 安裝並引入PIL套件
    使用pip(python的套件管理系統)進行安裝，這邊注意要用pillow作為名稱
    ```bash=
    C:\Users\Sam> pip install pillow
    ```
    在python檔案開頭使用這行程式碼可將PIL當中的Image模組引入
    ```python=
    from PIL import Image
    ```
2. 開啟圖片
    ```python=
    img = Image.open(imgName)
    ```
3. 顯示圖片資訊，如果色彩模式不是RGB則轉換為RGB
    ```python=
    print("圖片資訊: 大小為{}x{}, 格式為{}, 色彩模式為{}".format(img.size[0], img.size[1], img.format, img.mode))

    if img.mode != 'RGB':
        print("圖片顏色編碼不是RGB，進行轉換")    
        img = img.convert('RGB')
        print("轉換完成")
    ```
    * img.size是一個紀錄圖片大小的tuple，img.size[0]是寬、img.size[1]是高
    * img.mode是一個字串，紀錄圖片的編碼，常見的有這些
        * RGB: 三色，R紅、G綠、B藍(3x8bits)
        * RGBA: 跟RGB一樣，只是多了Alpha值，也就是透明度(4x8bits)
        * CMYK: 四色，C青、M洋紅、Y黃、K黑色(4x8bits)
        * 1: 黑白(1bit)
        * L: 256色灰階(8bits)
        * P: 256色彩色(8bits)
        > 參考: https://pillow.readthedocs.io/en/3.1.x/handbook/concepts.html#modes
4. 調整圖片大小
```python=
zoom = 0 # 縮小比率
if width >= maxSize:
    zoom = width / maxSize
    width = int(width / zoom) 
    height = int(height / zoom) 
    height = int(height / 2) # 除2讓比例看起來比較正確
    img = img.resize((width, height))
    print("圖片寬於{}，已縮小為{}x{}".format(maxSize, img.size[0], img.size[1]))
```
5. 將圖片轉為黑白
```python=
img = img.convert('1')
```
6. 開啟文字文件
```python=
txt = open(namestr, 'w')
```
7. 從上至下、從左至右讀取每個pixel的值
轉換成黑白顏色編碼後，每個pixel的值，不是1就是0，我們從上而下、由左而右讀取每個pixel，如果是1則在文字檔中寫入@代表黑色部分，如果是0則寫入_代表白色部分。
```python=
for y in range(height):
    for x in range(width):
        pixel = img.getpixel((x, y))
        # print('x= ', x, 'y= ', y, 'pixel= ', pixel)
        if pixel != 0:
            txt.write('_')
        else:
            txt.write('@')
    txt.write('\n')
```
8. 關閉文字文件
```python=
txt.close()
```