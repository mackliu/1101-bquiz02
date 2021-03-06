# 題組二解題步驟

#### 解題影片(2020-7-19更新)
* **[影片列表](https://www.youtube.com/watch?v=tMRObqljV_g&list=PLL26U2k-yzXu8WswH50RWxXHkJGxfZ5uf)**
* **[解題影片程式碼](https://github.com/mackliu/10901-bquiz02-v)**


## 步驟一：將素材目錄複製到崗位目錄下，確認素材內容與抽題題號一致
監評長按下倒數計時後，可以先把桌面上素材目錄中的題目素材複製一份到自己的工作目錄下，這時要確認自己複製的題目和抽到的題目是一致的，之後都在工作目錄下來取用相關的素材，這樣比較不容易出錯；在安裝軟體的準備時間裏，也要確認一下電腦桌面中是否有包含了素材這個目錄，並且四個題組的素材都在其中。

---

## 步驟二：將版型檔案及相關素材複製到網站根目錄下，並進行相應的更名及整理
  1. 開立./css, ./js, ./icon等常用目錄以利檔案分類及管理
  2. 將素材檔中的.css, .js, 及icon圖檔複製到相應的目錄下
  3. 更改版型素材的相關檔名，以符合解題的需要，雖然素材僅提供一個html檔，但根據題意，我們需要有一個後台的功能在，只不過標題區和登入及頁尾區的內容都是一樣的，因此我們會把這些地方的功能完成後，再直接複製成兩個檔案一個做為前台的版型，一個做為後台的版型。
      * 02P01.htm => index.php
      * 02P01.htm => admin.php (先不做，之後做完共同的功能後再複製改名)
  4. 更改版型素材的相關連結及匯入檔內容
      * 修改 `index.php` 中 `<link>` 及 `<script>` 中的連結路徑，指向正確的位置
      * 修改 `./css/css.css` 中的圖片 `url` ，指向根目錄下的 `../icon` 目錄
      * 刪除 `index.php` 中的 `<iframe>...</iframe>` ，我們會使用 `ajax` 來取代 `<iframe>`
  5. 開啟 `xampp` 及 `apache` 伺服器，使用 `localhost` 或 `127.0.0.1` 檢視網頁是否正確顯示，css 的載入是否正確

---

## 步驟三：進行前後台的檔案整理及切版，分離出共用的區塊或功能。
  1. 建立 `frontend`及 `backend` 兩個目錄，一個代表前台的相關檔案，一個代表後台的相關檔案，前後台共用的元件則先放在根目錄下，或另開一個 `comm` 目錄用來存放共用的元件。
  2. 分離出頁首標題及頁尾頁腳的區塊成為 `header.php` 及 `footer.php`，不過因為此題的頁首頁尾是寫死的，所以不切出來也沒有關係。
  3. 在 `index.php` 中找到右側主要內容區的區塊。
  4. 使用 `include` 指令來重新組合 `index.php` 頁面，並加上判斷式來確保要組合的檔案是存在的。
  5. 以 `get` 的方式來傳遞各頁面要組合的元件內容，比如 `do=login` 表示要看到的是登入頁面，因此在前台的 `include` 中可以併入 `login.php` 來呈現。
  6. 在 `./frontend` 及 `./backend` 目錄中，先建立一個 `home.php` 來當成預設內容，隨便打幾個字讓頁面不會出現找不到檔案的錯誤就可以了。

---

## 步驟四：建立資料庫連線檔及常用函式。
  1. 建立 `base.php` 檔，用來放共用的設定及函式。
  2. 採用類別方式來包裝整個db的連線及資料表的存取函式
```php
class DB{

//類別內容

}
```
  3. 設定好PDO的連線參數 `$this->pdo=new PDO($setting,$user,$pw,$config)`
```php
    private $dsn="mysql:host=localhost;charset=utf8;dbname=db66";
    private $root="root";
    private $password="";
    private $pdo;

    //設定建構式
    public function __construct($table){

        //將建立物件時代入的資料表名稱代入類別中的屬性table
        $this->table=$table

        //建立pdo的連線資訊，並將pdo連線指定給類別內的屬性pdo
        $this->pdo=new PDO($this->dsn,$this->root,$this->password); 
    }
```
  4. 啟用session `session_start()`
  5. 建立全域變數或是共用函式
      * find(\$arg) - 尋找特定條件的單筆資料或第一筆資料
      * all(...\$arg) - 取得資料表的全部資料或是特定條件的全部資料
      * count(...\$arg) - 計算符合條件的資料筆數
      * save(\$arg) - 新增或更新單筆資料
      * del(...\$arg) - 刪除特定條件的全部資料
      * q(\$sql) - 簡化 \$pdo->query(\$sql)->fetchAll() 的使用;
      * to(\$url) - 簡化 header("location:xxxxxx") 的使用;
```php
class DB{
    //......

    public function __construct($table){

        //.....

    }

  public function all(...$arg){
    //...... 
  }
  public function find($arg){
    //......
  }
  public function count(...$arg){
    //......
  }
  public function save($arg){
    //......
  }
  public function del($arg){
    //......
  }


}

function to($url){
    //......
}

```

  6. 做好以上工作後，可以先建一張簡單的資料表，把資料庫連線及所有自訂函式功能先測試一次，以確保後續使用不會有問題。

---

## 步驟五：建立資料表及預設資料。
每個題組依狀況不同，在這一步有不同的做法，視自己對題目的熟悉程度來做應變，可以一次把全部資料表建完，也可以視解題的進度來逐步建立或修改資料表。
根據題意，題組二會需要用到以下的資料表：
* user - 會員資料表(含管理者帳號)
* total - 訪客統計資料表
* news - 文章資料表
* que - 問卷資料表
* log - 按讚紀錄表

1. 依序建立功能需要的五張資料表:
  * user

    | name |  type  |  pk | default | A_I |   note   |
    |:----:|:------:|:---:|:-------:|:---:|:--------:|
    |id    |int(10) | yes |         | yes | 流 水 號 |
    |acc   |text    |     |         |     | 帳　　號 |
    |pw    |text    |     |         |     | 密　　碼 |
    |email |text    |     |         |     | 電子郵件 |
    
  * total

    | name |  type  |  pk | default | A_I |   note  |
    |:----:|:------:|:---:|:-------:|:---:|:-------:|
    |id    |int(10) | yes |         | yes | 流 水 號 |
    |date  |date    |     |         |     | 日　　期 |
    |total |int(10) |     |         |     | 訪客人數 |

  * news
  
    | name |  type  |  pk | default | A_I |  note  |
    |:----:|:------:|:---:|:-------:|:---:|:------:|
    |id    |int(10) | yes |         | yes | 流水號 |
    |title |text    |     |         |     | 標　題 |
    |text  |text    |     |         |     | 內　容 |
    |type  |int(5)  |     |         |     | 分　類 |
    |good  |int(5)  |     |   0     |     | 按讚數 |  
    |sh    |int(1)  |     |   1     |     | 顯　示 |

  * que

    | name |  type  |  pk | default | A_I |   note   |
    |:----:|:------:|:---:|:-------:|:---:|:--------:|
    |id    |int(10) | yes |         | yes | 流 水 號 |
    |text  |text    |     |         |     | 文字內容 |
    |parent|int(5)  |     |         |     | 題目 id  |
    |count |int(5)  |     |   0     |     | 統　　計 |

  * log

    | name |  type       |  pk | default | A_I |   note  |
    |:----:|:-----------:|:---:|:-------:|:---:|:--------:|
    |id    |int(10)      | yes |         | yes | 流水號   |
    |news  |int(5)       |     |         |     | 文章id   |
    |user  |varchar(128) |     |         |     | 會員帳號 |

2. 為了解題順利，可以把資料表中的一些欄位設為可接受空值的狀況，這樣即使未設定內容，也能正常新增或更改資料，不過這個做法只是為了先求解題完成而做的取巧，實務上應該根據需求及功能來決定欄位是否可以接受空值，並在程式端檢查來源資料是否為空值

---

## 步驟六：建置網站標題區。
這邊我們先來處理網站標題區，這邊比較麻煩的是瀏灠人次不是採總計，而是分日計算然後再總計；雖然題目沒有指示是否要和第一題一樣的機制，但我們在設計上為了避免瀏灠人次一直跳動，所以還是採用session來紀錄使用者的進站狀況，關閉瀏灠器再開才會重新計算；

1. 在 `index.php` 中的最前頭 include `base.php` 檔，我們會利用 `base.php ` 中的各項函式來處理資料
2. 在 `index.php` 中 `id='title'` 的區塊 中設定標題圖片
3. 在 `base.php` 中判斷瀏灠人次session變數的存在並進行瀏灠人次資料的取得或新增
4. 計算當日瀏灠人次及計算總人次然後顯示在頁面上
5. 在 `index.php.php` 中 `id='title` 加入 **回首頁** 的連結，採用 `float:right`的方式定位在最右側

在檢查時，記得更改一下windows的日期來做檢查，評分時也會被要求更改日期，來確認瀏灠人次是會依日期重新計算，同時可以總計不同日期的總人次

---

## 步驟七：建置主選單區及動態文字廣告。
這邊其實幾乎算送分了，因為主選單的內容己經在版型檔案中先做好了，連背景圖都設定好了，所以這邊唯一需要做的是把動態文字廣告的字打上去即可，這邊要注意的是動態文字廣告區的右側要留點空間給登入資訊使用。
* 在 `index.php` 中加入動態文字廣告區的文字，我們使用 `<marquee></marquee>` 標籤來完成即可
* 將 `<marquee>` 標籤設定為寬度80%的inline-block區塊和會員登入的文字區塊放在一起

---

## 步驟八：建置會員登入/登出/註冊及忘記密碼功能。
這邊我們會一鼓作氣的把會員註冊/登入/登出及忘記密碼都做完，一共11個項目可以拿到55分，依據題意，這邊要使用ajax來製作會比較適當，也比較好呈現題目要求的各項提示訊息：
1. 在 `./frontend/` 目錄下建立 `login.php`、`forget.php`、`reg.php`三個檔案，並建立需要的頁面HTML碼
2. 使用 `<fieldset><legend></legend></fieldset>` 來產生群組外框標題字的效果
3. 建立 `./api/` 目錄並建立 `chk_acc.php`、`chk_pw.php`、`find_pw.php`,`reg.php`,`logout.php`五個api檔
4. 根據題意撰寫需要的前端js程式碼及後端的php程式碼

```
由於版型素材提供的css並不是全部都可用，因此如果要畫面美觀的話，很多的css要自己寫，
美化的工作建議可以等功能都做完時再回頭來改就可以了。
```

----

## 步驟九：處理會員登入顯示及建置後台主頁。
在上一步時我們完成了會員登入相關的功能，題組二前後台共用的部份只剩下登入顯示：
* 必須有一個狀態用來紀錄登入的使用者，這邊我們使用session
* 在前面進行會員登入功能製作時，我們尚未做完登出及前後台登入區的顯示，我們放在這一步一起完成

1. 在 `./api/chk_pw.php` 中增加一個session用來紀錄會員的登入狀態，其中session的值我們直接紀錄會員帳號
2. 修改 `index.php` 中的會員登入按鈕，依據session的狀態來決定要顯示的內容，這裹也要一併考慮管理者登入時的顯示內容
3. 將 `index.php` 複製一份成為 `admin.php`
4. 修改 `admin.php` 中，include `./front/` 的路徑改成 `./admin/`

```
"admin.php"的選單文字內容及include的路徑可以等到做後台功能時再來處理即可，
當然，如果覺得順手的話，也可以在複製後直接進行相應的修改
```

--

## 步驟十：建置主題內容顯示區／分類網誌及頁尾版權區。
這邊頁尾版權區算是送分了，只要確認一下圖片的位置沒有錯就可以了，再把年份改一下就拿分了，至於主題內容區分兩個部份來說，一個是頁籤形式的首頁內容，在解題時，我是建議直接用DreamWeaver的頁籤功能來做就可以了，不用五分鐘可以搞定，一方面也是因為題目並沒有說明頁籤的內容是否要從資料庫來取得，從參考圖來看則感覺是純靜態的頁面。
而分類網誌的部份比較麻煩一點，首頁是題目給出的參考圖並沒有顯示文章內容的示意圖，但是因為文字描述中有提到要顯示文章內容，所以在功能上還是得秀出內容來，再者是資料是否要和資料庫相關這邊也沒說明，也就是說，分類網誌要做成純前端的形式似乎也可以，由於考慮到後續的最新文章功能也會需要使用到資料庫的文章，因此這邊的解題我個人是採用從資料庫取得文章內容的方式來解題：
1. 在 `index.php` 及 `admin.php` 的頁尾區修改年份及確認圖片
2. 在 `./frontend/main.php` 中，使用DreamWeaver來建立頁籤頁面，並置入相應的文章內容
3. 在 `./frontend/` 目錄中建立 `po.php` 檔案，用來處理分類網誌功能
4. 在 `news` 資料表中加入四個文章檔案的內容，每篇文章建立兩筆資料
5. 在 `./api/` 目錄中建立 `getlist.php` 及 `getnews.php` 兩個檔案用來做為取得文章列表及文章內容的API
6. 撰寫 `getlist.php` 及 `getnews.php` 的程式內容，分別會回傳分類的文章列表及文章內容
7. 在 `./frontend/po.php` 中建立題意所指示的分類列表及文章列表HTML
8. 在導覽列的位置按插一個區塊用來放置分類文字，使用jQuery來達到點擊分類時，導覽文字會跟著變化的效果
9. 使用ajax來完成取得列表及文章內容的功能，並注意在功能切換時，相應的區塊內容要跟著做變化，比如顯示列表時，記得要先清空文章區塊的內容，顯示文章內容時，要記得先清空列表區塊的內容，同時文章列表的標題區塊也要記得跟著做變換

```
如果是使用**DreamWeaver**來建立主題內容區的話，DreamWeaver會另外在工作目錄下
加入需要的CSS及JS程式(放在/SpryTabbedPanels.js目錄中)，
如果覺得DreamWeaver加入的css不好看，可以自行進行修改。
```

---

## 步驟十一：建置最新文章區及人氣文章區文章列表。
最新文章區和人氣文章區的功能非常類似，只差在顯示文章內容和排序方式而，所以先做最新文章區，做完後可以直接複製到人氣文章區做修改即可，這邊比較麻煩的地方就在於登入前和登入後的顯示內容及功能會不同，而且同時會影響到三張資料表，因此在製作上要格外小心和細心：
1. 在 `./frontend/` 目錄下建立 `news.php` 檔案
2. 在 `news.php` 檔案中撰寫列出文章及分頁功能的程式碼
3. 在 `news.php` 檔案中撰寫控制顯示文章內容的js程式碼
4. 複製 `news.php` 檔案，並更名為 `pop.php` 做為人氣文章功能的基礎
5. 修改 `pop.php` 的排序規則及導覽列文字以符合題意
6. 將 `index.php` 最前面的 `id=alerr` 區塊中的css單獨取出來做為人氣文章區的css，並刪除原本的 `<div id="alerr">..</div>` 區塊
7. 修改 `pop.php` 的文章彈出視窗功能以符合題意
8. 修改 `pop.php` 的js程式碼來控制彈出視窗

```html
這邊我們先把兩個功能的文章列表及分頁功能做出來，這樣就拿到了7個項目的分數了，
剩下的登入後按讚功能比較複雜，花時間又容易錯，
我們可以先放著最後再來做或是先去做其它相對簡單的功能再回來補
```
    參考語法:
    如果不想另外增加計數人氣的欄位，可以使用子查詢的語法來從log表中得到人氣文章的計算結果，
    再做排序
    SELECT * 
    FROM `news` 
    left join 
    　(SELECT `news` as 'news_id' ,
    　　　　 count(*) as 'count' 
    　 from `log` 
       group by `news`)A 
    on A.news_id=news.id 
    order by A.count desc
----

## 步驟十二：處理會員登入及文章按讚紀錄
在上一步時我們完成了文章的列表，這一步我們要來處理會員登入後的不同功能，我們需要考慮以下的議題：
* 這邊我們使用session來做為判斷使用者是否登入的依據，依此來決定畫面顯示的內容及可執行的功能
* 必須紀錄按讚的文章及那個會員按了讚，按讚紀錄我們存在 `log` 資料表
* 在顯示文章列表時，必須針對登入的會員去判斷要顯示的是 `讚` 還是 `收回讚`
* 在人氣文章區需要即時在畫面上對於人氣做加1 減1 的統計顯示

1. 在 `./frontend/news.php` 中加入對登入狀態的判斷，並根據會員在 `log` 資料表中是否有按讚紀錄來決定要顯示的內容
2. 搭配 `./js/js.js` 檔案中的按讚js程式碼，我們在前端的頁面中加入相應的內容來達到按讚的視覺效果，並同時將按讚紀錄以ajax的方式傳送到後台去更新資料表
3. 在 `./api/` 目錄中建立 `good.php` 檔案，用來做為更新按讚紀錄的api
4. 在 `./api/` 目錄中建立 `logout.php` 檔案，用來撰寫清除session的行為，以達到登出的效果
5. 修改 `./js/js.js` 檔案中的api路徑，指向 `./api/good.php`
6. 撰寫 `./api/good.php` 中的程式碼，依據 `type` 來決定是要新增一筆紀錄還是刪除一筆紀錄
7. 在更新完 `log` 資料表的內容後，同時也更新一下 `news` 資料表中的 `good` 欄位
8. 複製按讚相關的程式碼到 `./frontend/pop.php` 中並做相應的修改

---

## 步驟十三：後台頁面及後台帳號管理
題組二比較難的地方在問卷調查的功能，而問卷的新增則需要先由後台來新增，因此前端做完文章的相關功能後，我們先完成後台的要求，再回頭去做問卷調查的功能。
題組二的後台相較題組一簡單許多，甚至沒有新增文章的功能，所以我們只能手動先把文章匯入到資料庫去，也因此所以題組二的後台容易拿分，我會安排在解題的中段時間來把後台完成，確認這部份的分數是有先到手的。

1. 依題意，後台有一個提示選擇管理項目的頁面，我們直接把這段文字放在 `./backend/home.php` 中，使用 H1 級的標籤及置中的 class 就可以了
2. 依題意，修改 `admin.php` 左側選單的文字和連結

```html
  <div class="hal" id="lef">
    <a class="blo" href="?do=user">帳號管理</a>
    <a class="blo" href="?do=po">分類網誌</a>
    <a class="blo" href="?do=news">最新文章管理</a>
    <a class="blo" href="?do=know">講座管理</a>
    <a class="blo" href="?do=que">問卷管理</a>
  </div>
```
3. 在 `./backend/` 目錄下建立 `user.php` 檔案來製作帳號管理功能
4. 在 `./backend/` 目錄下建立 `po.php` 及 `know.php` 兩個檔案，內容只須註明"依題意此功能不需製作"即可
5. 在 `./backend/user.php` 中依題意來製作帳號管理的頁面內容及相關功能
6. 在 `./api/` 目錄中新增 `usermanage.php` 檔案來處理帳號刪除的功能
7. 帳號管理功能的頁面下方，新增會員的功能和前台的註冊會員一模一樣，因此可以直接複製 `./frontend/reg.php` 的程式碼來修改使用即可

```
在"user.php"中，我們在列出使用者帳號時排除了 "admin" 這個帳號的顯示，
題目雖然沒有要求這麼做，但如果"admin"帳號是可以被刪除的，
那這個網站的安全性會有問題，因此我個人在解題時會多一個判斷來保護"admin"
這個帳號不會被誤刪，不做這個動作並不會影響分數。
替代的做法是把管理者帳號的登入另外寫死，不從資料表來做判斷。
```

---

## 步驟十四：製作最新文章管理功能
最新文章管理功能的步驟算是很簡單的應用，和題組一的後台幾乎一模一樣，只差在沒有內容編輯的要求而已，因此只要熟悉題組一的解法，這邊就照著做即可：
1. 在前台的文章功能中已經先匯入文章到資料庫了，因此項目一算是送分
2. 依題意製作文章列表及分頁功能，編號功能可以考慮使用 `<ol>` 的序列清單來製作，也可以使用表格方式來製作，後者需自行計算分頁後的項目編號起始值
3. 在 `./api/` 目錄中新增 `news.php` 檔案，用來撰寫控制文章顯示及刪除的程式碼

---

## 步驟十五：製作問卷管理功能
後台的問卷管理功能只有做新增問卷而已，算是簡單的應用，唯一特別的要求是要前端可以直接點擊 `更多` 按鈕後在畫面上增加選項，而這個功能和題組一的 **編輯次選單** 一樣，所以我們只需要參照題組一的做法即可。
1. 在 `./backend/` 目錄下建立 `que.php` 檔案
2. 在 `que.php` 中建立新增問卷需要的表單格式
3. 建立一個 `更多` 按鈕，並設定onclick事件
4. 撰寫 `javascript` 函式 `moreOpt()` 用來在指定的位置插入選項
5. 在 `./api/` 目錄下建立 `createque.php` 檔案做為新增問卷的php程式檔
6. 在 `createque.php` 中撰寫新增問卷的程式碼
 
```html
這邊要注意的是因為我們是同時把題目和選項一起送出的，而資料庫在儲存時會依照
 "parent" 這個欄位來判斷這筆資料是題目還是選項，因此我們在撰寫 api 時要注
意資料處理的先後順序，先處理題目的新增，才能取得題目的 id 做為選項的
 parent 值
```

---

## 步驟十六：製作前台問卷調查功能
前台問卷調查雖然也有區分登入前和登入後的不同狀態，但是並沒有限制會員一人只能投一票，因此做完此功能後可以先多投幾票，讓長條圖的效果顯示出來，可以節省評閱的時間。
1. 在 `./frontend/` 目錄下增加 `que.php` 檔案，並撰寫問卷問題列表及會員功能限制
2. 在 `./frontend/` 目錄下增加 `result.php` 檔案，並撰寫問卷結果功能
3. 問卷結果的長條圖我們直接利用 `<div></div>` 就可以了，長度的計算根據投票比例去換算再寫入到style中
4. 在 `./frontend/` 目錄下增加 `vote.php` 檔案，並撰寫投票功能
5. 在 `./api/` 目錄下增加 `vote.php` 檔案，並撰寫接受投票及更新資料庫的功能
6. 為了簡化前台的程式碼，所以我們在做資料表更新時，除了更新選項的計數外，也會同時去更新題目的計數，這樣就不用在 `result.php` 中再去計算一次總票數了。


## 最後的檢查
題組二雖然每個功能都不用花太多時間製作，但是在檢查時要格式小心，大多數監評都會檢查註冊功能及登入是否正常，之後也會驗證文章和問卷功能中和會員有關的判斷是否都正常，因此如果提早做完了功能，那就要記得從頭到尾的功能細節都要檢查過一次。

另外，`2019年`修訂的素材中把第二題的css從原本的高度**888px**改成了**768px**，隨之而來的問題是加上了標題圖片和尾頁的QR code後，整個版面還是被撐過了768px的大小，由於這個問題在新版本的素材中會很明顯，擔心會造成評閱時的爭議，因此建議最後有空時修改一下CSS的設定，把整體版面控制在 **1024px x 768px** 的範圍會比較好：
```css
css檔中第二個 #mm 的地方
#mm
{
	min-height:540px; /* 570 -> 540 */
}

#lef
{
	width:18.2%;
	padding:2px;
	background:url(../icon/02B04.png) repeat-y;
	/*background-size:100% auto;*/
	min-height:538px;  /* 568 -> 538 */
	display:inline-block;
	vertical-align:top;
	position:relative;
	top:-3px;
}
#main
{
	display:inline-block;
	width:78%;
	height:538px;  /* 565 -> 538 */
	padding:4px;
	border:solid 1px #000000;
	overflow:auto;
	position:relative;
	left:1%;
}
#bottom
{
	text-align:right;
	padding:0 24px; /* 24px -> 0 24px */
}
```