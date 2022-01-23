# FileBrowser_notes
FileBrowser的使用筆記

## 軟體來源
+ 官方網站: https://filebrowser.org/
+ 官方Github: https://github.com/filebrowser/filebrowser
+ 各版本下載頁面: https://github.com/filebrowser/filebrowser/releases
+ Windows版下載點: https://github.com/filebrowser/filebrowser/releases/download/v2.20.1/windows-amd64-filebrowser.zip

## 環境
+ 後續的筆記是使用Windows版本。
+ 在Windows中比較沒有資料夾使用權限的問題，只要避開讀寫有限制的資料夾就好了，比較適合測試與學習。

## 心得
+ 在官網下載的zip中，只有主程式「filebrowser.exe」這個檔案是必要的。「filebrowser.exe」是一個編譯好的獨立執行檔，會以命令提示字元的方式運行。
+ 官方網站的說明其實不太好懂，範例太少，可以配合查詢程式本身提供的說明。呼叫方式:
    ```
    filebrowser.exe -h
    ```
    或
    ```
    filebrowser.exe --help
    ```
    以上兩行擇一使用即可。
+ 基本運行伺服器只要直接執行主程式即可。呼叫方式:
    ```
    filebrowser.exe
    ```
    但實際上有許多參數可以使用，通常不會用這麼簡單的方式運行伺服器。
    
## 觀念
+ 主程式filebrowser.exe在當作伺服器運行時，會將許多要用到的資訊存在資料庫檔案(DB檔案)裡，後續的所有程式參數使用都是以DB檔案為主。因此原作者建議把DB檔案建立好之後執行主程式就好，不要每次執行後又強迫把設定再次匯入DB檔裡面。
+ 主程式也可以當作一個DB管理工具，可以透過config系列命令來操作DB。例如:
    + 建立新的初始DB
        ```
        filebrowser.exe config init
        ```
    + 變更DB內的參數
        ```
        filebrowser.exe config set
        ```
    + 匯出參數(DB到檔案中)
        ```
        filebrowser.exe config import
        ```
    + 匯入參數(檔案到到DB中)
        ```
        filebrowser.exe config export
        ```
    + 同樣也有users、cmds、rules等系列命令可以操作DB。  
    
    > 當伺服器運行時，DB檔案有可能會被鎖定，操作時會有錯誤提示。
    > 前述介紹只是提醒大致有這些命令，在官網說明書都有提到相對應的意義，但是缺乏範例，不太容易知道如何操作。
    > 不過這個程式有設計管理員的機制，以管理帳號登入網頁後可以透過網頁按鈕達到上述大部分的功能，所以上面的使用方法並非一定要詳讀學會。
    > 當然，要能登入網頁進行就必須先把伺服器運行起來，且需要指定監聽的IP與Port。

+ 主程式執行時，以預設路徑尋找DB檔案。若找不到則進入「快速設定模式」，自動以預設值製作DB檔案並監聽預設連接埠。
+ 雖然主要參數都存放在DB檔案中，但仍然提供命令列呼叫時帶參數的方式設定，且參數有時優先於DB檔案被採用。比較麻煩的是這樣帶入的參數並非總是會被存到DB中。
+ 因為可設定的參數相當多，也提供運行期讀取config檔案(檔案被要求名稱為「.filebrowser.json」，實際上也接受其他附檔名，但這裡不討論)的方式來設定參數。同樣地這樣帶入的參數並非總是會被存到DB中。
    > 使用命令列帶參數的方式與使用「.filebrowser.json」的方式帶入參數都可以達到相同的效果，我建議使用上盡量使用「.filebrowser.json」的方式。但因大多數重要參數仍在DB中存放，所以這個「.filebrowser.json」的內容僅選擇網頁不易設定的參數即可，其餘參數由網頁介面設定。

## 建議使用方法
1. 下載主程式並取出執行檔「filebrowser.exe」，放置到沒有特殊權限限制的資料夾。
2. 自行製作config檔案，必須命名為「.filebrowser.json」且放在主程式所在目錄下。必填入幾項無法在網頁介面上設定的參數，隨時依照需求可以修改，每次執行主程式都搭配使用。內容包含四項重要參數，如下:
    ```json
    {
        "database":"./filebrowser.db",
    	"address":"127.0.0.1",
    	"port":8080,
    	"root":"."
    }
    ```
    說明:
    + 關鍵字「database」的設定值為DB檔案儲存的路徑及檔名，預設值為「./filebrowser.db」。
        > 此項不會被儲存在DB檔案之中，所以最好一定要放在config檔裡。資料夾不存在會自行創建，DB檔案不存在就進入快速設定模式建立DB檔案，檔案存在就直接使用該檔案。
        
    + 關鍵字「address」的設定值為要監聽的IP，預設值為「127.0.0.1」。
        > 此項只有快速設定模式下建立新DB檔案時會寫入DB檔案中，其他時候會被採用參數但不寫入DB，放在config檔裡可以方便修改，網頁不提供此項修改。
    
    + 關鍵字「port」的設定值為要監聽的port，預設值為「8080」。
        > 此項只有快速設定模式下建立新DB檔案時會寫入DB檔案中，其他時候會被採用參數但不寫入DB，放在config檔裡可以方便修改，網頁不提供此項修改。
        
    + 關鍵字「root」的設定值為主程式管理資料夾的本機端目錄，預設值為「.」。
        > 此項只有快速設定模式下建立新DB檔案時會寫入DB檔案中，其他時候會被採用參數但不寫入DB，放在config檔裡可以方便修改，網頁不提供此項修改。 
        > 不要對這個預設值恐慌，這個只是定義主程式可管理的根目錄，另外還有參數可以設定使用者使用的根目錄。
    
    + 其他關鍵字非必要但也可以放入，這裡不深入討論。    
3. 執行主程式，會看到命令提示字元卡在那。此時因為DB檔案不存在所以會透過快速設定模式建立DB檔案。預設有一個管理員可登入網頁介面，帳號:admin，密碼:admin。
4. 利用瀏覽器訪問「 http://127.0.0.1:8080/ 」，以管理員帳號登入。帳號:admin，密碼:admin。
5. 管理員預設會看到根目錄，以上述設定下可看到主程式的執行檔及參數config檔，右上方存在一個「Toogle Shell」的按鈕。
6. 選擇左邊的「設定」頁面，把「Profile Settings」的「Language」設定為「 中文 (繁體) 」。剩下的設定就輕鬆了。
7. 現在頁面變成「個人設定」，請依照個人需求操作「更改密碼」。
8. 到「使用者管理」標籤頁，在使用者的區塊找到「新」的按鈕，依照指示設定使用者帳號。注意，這時候尤其要注意的就是「目錄範圍」，為了安全性請不要使用「.」，應改用「./foldername」，會自動建立「foldername」資料夾，用戶可見的範圍將僅限該資料夾中。「執行命令」選項不打勾，就不會讓該使用者看到「Toogle Shell」的按鈕。
9. 依照個人需求可以照網頁設定不同使用方式。
10. 到「全域設定」標籤頁，在「品牌」的部分，若把「禁止外部連結（幫助文件除外）」打勾，則頁面中「File Browser 2.20.1」的連結就不可按。
11. 到「全域設定」標籤頁，在「品牌」的部分，「例項名稱」可以設定登入畫面下的文字，留白則是預設值。
12. 到「全域設定」標籤頁，在「品牌」的部分，「品牌資訊資料夾路徑」可以指定一個取代網頁資訊的資料夾，相對於。資料夾裡必須存在檔案「custom.css」、資料夾「img」。  
    + 以設定為「style」為例，必須將相關檔案放置為如下檔案結構:
        ```
        C:\filebrowser
        │  .filebrowser.json
        │  filebrowser.exe
        │  
        └─style
            │  custom.css
            │  
            └─img
                    logo.svg
        ```
        > 「img」資料夾內結構及對應檔名請參考: https://github.com/filebrowser/frontend/tree/master/public/img  
    
    + 高手做的「custom.css」請參考: https://github.com/jniggemann/filebrowser-css  
    
    ```css
    /* 
        filebrowser-css - a custom stylesheet for filebrowser
        that adds colors and changes some icons
        https://github.com/jniggemann/filebrowser-css
    
        Copyright (C) 2021 Jan Niggemann
    
        This program is free software: you can redistribute it and/or modify
        it under the terms of the GNU Affero General Public License as
        published by the Free Software Foundation, either version 3 of the
        License, or (at your option) any later version.
    
        This program is distributed in the hope that it will be useful,
        but WITHOUT ANY WARRANTY; without even the implied warranty of
        MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
        GNU Affero General Public License for more details.
    
        You should have received a copy of the GNU Affero General Public License
        along with this program.  If not, see <https://www.gnu.org/licenses/>.
    
    */
    
    
    /* remove a bit of padding from the file list entries */
    #listing.list .item {
        padding: 0.7em;
    }
    
    /* make folders yellow-ish */
    #listing .item[data-dir=true] div i {
        color: #ffc84b;
    }
    
    /* packed files - colorize and change icon */
    #listing .item[aria-label$=".7z"] div i {
        color: #a268a1;
    }
    #listing .item[aria-label$=".7z"] .material-icons {
        visibility: hidden;
    }
    #listing .item[aria-label$=".7z"] .material-icons::before {
        content: "archive";
        visibility: visible;
    }
    
    #listing .item[aria-label$=".arj"] div i {
        color: #a268a1;
    }
    #listing .item[aria-label$=".arj"] .material-icons {
        visibility: hidden;
    }
    #listing .item[aria-label$=".arj"] .material-icons::before {
        content: "archive";
        visibility: visible;
    }
    
    #listing .item[aria-label$=".zip"] div i {
        color: #a268a1;
    }
    #listing .item[aria-label$=".zip"] .material-icons {
        visibility: hidden;
    }
    #listing .item[aria-label$=".zip"] .material-icons::before {
        content: "archive";
        visibility: visible;
    }
    
    #listing .item[aria-label$=".gz"] div i {
        color: #a268a1;
    }
    #listing .item[aria-label$=".gz"] .material-icons {
        visibility: hidden;
    }
    #listing .item[aria-label$=".gz"] .material-icons::before {
        content: "archive";
        visibility: visible;
    }
    
    #listing .item[aria-label$=".tar"] div i {
        color: #a268a1;
    }
    #listing .item[aria-label$=".tar"] .material-icons {
        visibility: hidden;
    }
    #listing .item[aria-label$=".tar"] .material-icons::before {
        content: "archive";
        visibility: visible;
    }
    
    
    #listing .item[aria-label$=".bz"] div i {
        color: #a268a1;
    }
    #listing .item[aria-label$=".bz"] .material-icons {
        visibility: hidden;
    }
    #listing .item[aria-label$=".bz"] .material-icons::before {
        content: "archive";
        visibility: visible;
    }
    
    #listing .item[aria-label$=".bz2"] div i {
        color: #a268a1;
    }
    #listing .item[aria-label$=".bz2"] .material-icons {
        visibility: hidden;
    }
    #listing .item[aria-label$=".bz2"] .material-icons::before {
        content: "archive";
        visibility: visible;
    }
    
    #listing .item[aria-label$=".xz"] div i {
        color: #a268a1;
    }
    #listing .item[aria-label$=".xz"] .material-icons {
        visibility: hidden;
    }
    #listing .item[aria-label$=".xz"] .material-icons::before {
        content: "archive";
        visibility: visible;
    }
    
    #listing .item[aria-label$=".tbz"] div i {
        color: #a268a1;
    }
    #listing .item[aria-label$=".tbz"] .material-icons {
        visibility: hidden;
    }
    #listing .item[aria-label$=".tbz"] .material-icons::before {
        content: "archive";
        visibility: visible;
    }
    
    
    /* office files */
    /* PDF - colorize and change icon /*
    /* Note: This is yellow because I use SumatraPDF */
    #listing .item[aria-label$=".pdf"] div i {
        color: #FFEE00;
    }
    #listing .item[aria-label$=".pdf"] .material-icons {
        visibility: hidden;
    }
    #listing .item[aria-label$=".pdf"] .material-icons::before {
        content: "picture_as_pdf";
        visibility: visible;
    }
    
    /* word processors - colorize and change icon */
    /* Word */
    #listing .item[aria-label$=".doc"] div i {
        color: #185ABD;
    }
    #listing .item[aria-label$=".doc"] .material-icons {
        visibility: hidden;
    }
    #listing .item[aria-label$=".doc"] .material-icons::before {
        content: "description";
        visibility: visible;
    }
    
    #listing .item[aria-label$=".docx"] div i {
        color: #185ABD;
    }
    #listing .item[aria-label$=".docx"] .material-icons {
        visibility: hidden;
    }
    #listing .item[aria-label$=".docx"] .material-icons::before {
        content: "description";
        visibility: visible;
    }
    
    /* OpenOffice Writer */
    #listing .item[aria-label$=".odt"] div i {
        color: #185ABD;
    }
    #listing .item[aria-label$=".odt"] .material-icons {
        visibility: hidden;
    }
    #listing .item[aria-label$=".odt"] .material-icons::before {
        content: "description";
        visibility: visible;
    }
    
    /* LibreOffice Writer */
    #listing .item[aria-label$=".sxw"] div i {
        color: #185ABD;
    }
    #listing .item[aria-label$=".sxw"] .material-icons {
        visibility: hidden;
    }
    #listing .item[aria-label$=".sxw"] .material-icons::before {
        content: "description";
        visibility: visible;
    }
    
    
    /* PowerPoint */
    #listing .item[aria-label$=".ppt"] div i {
        color: #D35230;
    }
    #listing .item[aria-label$=".pptx"] div i {
        color: #D35230;
    }
    #listing .item[aria-label$=".pps"] div i {
        color: #D35230;
    }
    /* OpenOffice Impress */
    #listing .item[aria-label$=".odp"] div i {
        color: #D35230;
    }
    
    
    /* Excel */
    #listing .item[aria-label$=".xls"] div i {
        color: #107C41;
    }
    #listing .item[aria-label$=".xlsx"] div i {
        color: #107C41;
    }
    #listing .item[aria-label$=".ods"] div i {
        color: #107C41;
    }
    #listing .item[aria-label$=".sxc"] div i {
        color: #107C41;
    }
    #listing .item[aria-label$=".wri"] div i {
        color: #336eff;
    }
    
    
    /* sound files - colorize */
    #listing .item[aria-label$=".mp3"] div i {
        color: #F47900;
    }
    #listing .item[aria-label$=".wav"] div i {
        color: #F47900;
    }
    #listing .item[aria-label$=".flac"] div i {
        color: #F47900;
    }
    #listing .item[aria-label$=".ogg"] div i {
        color: #F47900;
    }
    #listing .item[aria-label$=".opus"] div i {
        color: #F47900;
    }
    #listing .item[aria-label$=".m4a"] div i {
        color: #F47900;
    }
    
    /* video files - colorize */
    #listing .item[aria-label$=".mp4"] div i {
        color: #F47900;
    }
    
    /* text files - change icon*/
    #listing .item[aria-label$=".txt"] .material-icons {
        visibility: hidden;
    }
    #listing .item[aria-label$=".txt"] .material-icons::before {
        content: "description";
        visibility: visible;
    }
    #listing .item[aria-label$=".md"] .material-icons {
        visibility: hidden;
    }
    #listing .item[aria-label$=".md"] .material-icons::before {
        content: "description";
        visibility: visible;
    }
    
    /* various other files*/
    /* iCal - change icon*/
    #listing .item[aria-label$=".ics"] .material-icons {
        visibility: hidden;
    }
    #listing .item[aria-label$=".ics"] .material-icons::before {
        content: "event";
        visibility: visible;
    }
    ```
13. 重新啟動主程式，這次之後都會採用config內的參數，並找到DB檔案載入其他參數。瀏覽器有時候因為快取仍在可能替換的CSS與圖片會顯示舊的，請手動強制清除瀏覽器暫存資料後重試。
14. 所有使用者的密碼是轉換成湊雜碼存起來的，忘記的話就砍掉DB檔案重新設定新的吧，不提供查出密碼的功能。
