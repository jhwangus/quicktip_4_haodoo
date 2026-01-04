# 用 Google Doc 做批次 OCR

Google Doc 有一隱藏功能。當其第一次打開某個PDF檔案時，將會自動執行OCR，條件是檔案不能太大（少於80頁且小於40MB）；不符條件的話，將不會執行OCR，而是將每頁圖像直接插入於打開的Doc檔。此功能確認適用於直排書籍，橫排因該也可接受。

本流程利用此功能，先將多份掃描圖像檔製成 PDF 檔，再上載 Google Drive 後打開，進行批次 OCR。OCR後的結果呈橫排文字檔，再執行 OCR_FIX.py 程式，將文字檔裡標點符號及英文字母更換成全型，以符合好讀格式，準備進行校正編輯流程。

其步驟為：

1. 將書籍內容分割或製作成多份適量大小PDF檔（頁數小於50頁，大小不超過40MB）。
1. 用 Google Docs 打開多份PDF檔，同時執行 OCR。
1. 快速轉換英文標點符號及英文字母（含大小寫）成繁體中文全型格式，以利直式排版。

此流程的優點在於：

1. 讓製書過程流暢化，節省時間。例如，以一本三百頁的中文書而言，若用 Google Doc 逐一打開單頁掃描圖像檔進行 OCR，大約要花四個小時，而且容易遺漏。此流程第一、二步驟總共僅耗費約十五分鐘，且不會遺漏。
1. 第三、四步驟自動將標點符號、英文字母改成全型，減少手改編輯時間。同時將英文字間的空格改成全型，不會在 mPDB 中清除空白字元時被錯誤去除。
1. 使用PDF檔校對，比使用分散的掃描影像檔方便。

# 將書籍內容分割或製作成多份適量大小PDF檔

以下有兩種方式：

1. 如果原來就是一個PDF檔案，可以將其分割成多個PDF檔案。
1. 如果原來是多個單一的掃描圖像檔，可以將其組合製作成多個PDF檔。 

## 將一個PDF檔分割成多份PDF檔

利用免費軟體 PDFsam Basic（版本 ver. 5.2.9 以上）分割。

### 安裝

從網路下載 PDFsam Basic 免費版本並完成安裝。

### 執行

1. 執行後，選擇 Split 功能。
1. 視窗最上面，選擇要分割的 PDF 檔案。
1. 在 Split settings 裡，選擇最下面一項： Split by every "n" pages，並輸入50
1. Output settings 可以不改，使用跟原來 PDF 檔案同一個目錄位置
1. 最下面按 Run 按鍵
1. 檢查分割後的 PDF 檔案大小，如果超過 40MB，請將第三步驟的 "n" 改為更小的數字（如，40），並再跑一次。（注意先清除原先分割出來的PDF 檔案）。

## 將多個掃描圖像檔組合製成多個PDF檔

利用開源免費軟體 ImageMagick 的 convert 命令，將多份掃描圖像檔製成 PDF 檔。

### 安裝

1. 安裝 ImageMagick
    > 從 https://imagemagick.org/script/download.php 下載並安裝 ImageMagick。
1. 安裝 img2pdf.bat（見附錄）

### 轉換圖像檔成 pdf 檔（快速）

1. 打開一個命令列（command prompt）
1. 更換至圖像檔所在目錄
1. 執行 img2pdf.bat
   > 自動將 000***.jpg 或 000***.png 每50個檔轉成一個 PDF 檔，最多至 000999.*

### 轉換圖像檔成 pdf 檔（手動）

1. 打開一個命令列（command prompt）
2. 更換至圖像檔所在目錄。
3. 將圖像檔轉換為 PDF檔
    > convert 0000* 0001* 0002* 0003* 0004* book1.pdf

請確認一次不超過80個圖像檔，且最後 PDF 檔大小不超過40MB。

# 用 Google Docs 打開PDF檔，執行 OCR。

在此以分割後或組合後的 book1.pdf 為例：

1. 將 book1.pdf 檔上傳至 Google Drive。
2. 在 Google Drive 的 book1.pdf 上按滑鼠右鍵，選擇 Open with Google Doc，將會自動轉換成 book1.doc 文字檔。
   > 若未能轉換，應該是 book1.pdf 太大（超過40MB或超過80頁），請減少圖像檔數量。
4. OCR後文字字型超大。
   > 此時按 CTRL-A，選擇整個文檔。
   > 再用滑鼠點上面 MENU 的**格式 -> 清除格式**，即可恢復正常大小。

# 快速轉換英文標點符號及英文字母（含大小寫）成繁體中文全型格式

Google Doc 的 OCR 預設標點符號為英文標點符號，所以OCR產生的文檔內夾雜英文標點符號，而非我們想要的中文標點符號。例如，逗號、問號、驚嘆號、冒號、分號、左括號、右括號等等，都是以英文半形而非中文全型呈現（例如，逗號用英文","，而非中文"，" ）。

下面提供一個 Python 程式讓你在剪貼板內容中搜索和替換多個標點符號。你可以通過創建桌面捷徑的方式來執行此程式。另外，文字中若有英文，也會被轉成全型（例，"A" 轉成"Ａ"），以方便製作直式 updb 或 epub 檔。

## 特點

1. 同時替換多個標點符號及英文字母（含大小寫）和阿拉伯數字。
2. 英文字間空格轉為全型空格，不會被 mPDB 移除。
3. 可客製化。
4. 將修改後的內容保留在剪貼板中。

## 安裝

1. 安裝 Python 3（從 https://www.python.org/downloads/ 下載）
2. 安裝pyperclip 模組。在命令提示符或終端中運行
   > pip install pyperclip。
3. 將ocr_fix.py 存在電腦上，如：
   > D:\py\ocr_fix.py。
5. 創建桌面捷徑
    1. 右鍵單擊桌面，選擇「新增」->「捷徑」。
    1. 在「位置」欄中輸入 python D:\py\ocr_fix.py
    1. 單擊「下一步」。
    1. 為桌面捷徑命名（例如，「OCR_FIX」）。
    1. 單擊「完成」。

## 用法

1. 將文本複製到剪貼板，如 CTRL-A，然後 CTRL-C。
2. 雙擊桌面捷徑 OCR_FIX。
    1. 程式將自動運行，在剪貼板內容中搜索指定標點符號，並將它們替換為相應的符號。
    1. 修改後的內容將被保留於剪貼板。
3. 在編輯的檔案內適當位置，按 CTRL-V 貼回。

### 客製化

可修改 ocr_fix.py 內列表，增加搜尋取代符號。

```
# Example usage
search_replace_pairs = [
    (" ", ""),
    ("...", "…"),
    ("・・・", "…"),
    (",", "，"),
    (";", "；"),
    (":", "："),
    ("?", "？"),
    ("!", "！"),
    ("(", "（"),
    (")", "）"),
    ("·", "・"),
    ("A", "Ａ"),
    ....
]
```

# 附錄

## 安裝 img2pdf.bat

將下列內容剪貼存入 img2pdf.bat，並將其儲存於 DOS 字串 PATH 內含的路徑中。

```
@echo off
setlocal ENABLEDELAYEDEXPANSION

rem (C)2024, Jack Hwang, All Rights Reserved
rem
rem    This DOS batch file uses ImageMagicK command "convert" to
rem    convert image files into PDF files.  Two caveats:
rem    1. It assume image filenames are in "000xxx.ext"
rem       format, e.g., 000100.jpg or 000201.png
rem    2. It does not include other pages with different
rem       prefixes, e.g., forward pages such as fow001.jpg.
rem       Please use the convert command to create their own
rem       respective PDF files.
rem    3. It handles up 0-999 pages. If there are more pages,
rem       please modify the script accordingly.
rem    4. If pages are not available, it will still try to
rem       convert but it won't generate the pdf file.  For
rem       example, if a book has only 400 pages, it will
rem       still try to convert 401~999 pages but there will
rem       not be respective pdf files generated.

set num1=0,1,2,3,4
set num2=5,6,7,8,9

for /L %%i in (0,1,9) do (
   set var=
   for %%j in (%num1%) do set var=!var! 000%%i%%j*
   rem echo !var!
   echo convert !var! book%%i_1.pdf
   convert -quiet !var! book%%i_1.pdf 2> nul
   set var=
   for %%j in (%num2%) do set var=!var! 000%%i%%j*
   rem echo !var!
   echo convert !var! book%%i_2.pdf
   convert -quiet !var! book%%i_2.pdf 2> nul
)
```

## img2pdf.bat 執行例子

```
F:\Documents\eBook\Work\haodoo\12823980 深海諜影\Images>..\..\img2pdf.bat
convert  00000* 00001* 00002* 00003* 00004* book0_1.pdf
convert  00005* 00006* 00007* 00008* 00009* book0_2.pdf
convert  00010* 00011* 00012* 00013* 00014* book1_1.pdf
convert  00015* 00016* 00017* 00018* 00019* book1_2.pdf
convert  00020* 00021* 00022* 00023* 00024* book2_1.pdf
convert  00025* 00026* 00027* 00028* 00029* book2_2.pdf
convert  00030* 00031* 00032* 00033* 00034* book3_1.pdf
convert  00035* 00036* 00037* 00038* 00039* book3_2.pdf
convert  00040* 00041* 00042* 00043* 00044* book4_1.pdf
convert  00045* 00046* 00047* 00048* 00049* book4_2.pdf
convert  00050* 00051* 00052* 00053* 00054* book5_1.pdf
convert  00055* 00056* 00057* 00058* 00059* book5_2.pdf
convert  00060* 00061* 00062* 00063* 00064* book6_1.pdf
convert  00065* 00066* 00067* 00068* 00069* book6_2.pdf
convert  00070* 00071* 00072* 00073* 00074* book7_1.pdf
convert  00075* 00076* 00077* 00078* 00079* book7_2.pdf
convert  00080* 00081* 00082* 00083* 00084* book8_1.pdf
convert  00085* 00086* 00087* 00088* 00089* book8_2.pdf
convert  00090* 00091* 00092* 00093* 00094* book9_1.pdf
convert  00095* 00096* 00097* 00098* 00099* book9_2.pdf

F:\Documents\eBook\Work\haodoo\12823980 深海諜影\Images>dir *.pdf
 Volume in drive F is Data2
 Volume Serial Number is C16E-57B0

 Directory of F:\Documents\eBook\Work\haodoo\12823980 深海諜影\Images

10/25/2024  10:55 AM         7,147,851 book0_1.pdf
10/25/2024  10:55 AM         5,743,305 book0_2.pdf
10/25/2024  10:55 AM        11,444,431 book1_1.pdf
10/25/2024  10:55 AM        11,031,206 book1_2.pdf
10/25/2024  10:55 AM         6,722,019 book2_1.pdf
10/25/2024  10:55 AM         5,464,710 book2_2.pdf
10/25/2024  10:55 AM         9,225,855 book3_1.pdf
               7 File(s)     56,779,377 bytes
               0 Dir(s)  502,353,453,056 bytes free
```
