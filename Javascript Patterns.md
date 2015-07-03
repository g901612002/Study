#Javascript 設計模式
讓程式「寫得更有效率、執行得更有效率、dubug 的時間縮短、日後維護更方便」
###精要
**1.容易維護的程式碼必須具備下列條件：**
- 可讀性
- 一致性
- 可預期的
- 看起來像同一個人寫的
- 文件化

> 第 1 點指使用大量空格、縮排，雖然增加檔案大小，但讓程式碼清晰易讀，日後 debug 可節省時間； 
> 第 2 點是指變數的命名、寫程式碼的規則與習慣等等必須統一； 
> 第 5 點是指重要程式碼加上註釋。

**2.減少全域變數**
才能避免與第三方程式碼衝突

**3.函式開頭使用單一個 var 敘述的優點**
- 找變數時只需要只一個地方
- 避免邏輯錯誤
- 減少全域變數的產生
- 減少程式碼

```javascript
//把變數集中在開頭宣告，且只需要一個 var 即可
var a = "hello",
    b = 3,
    c = [];
```
**4.For 迴圈**

不良的寫法
```javascript
//迴圈要每次都重新計算 wArray 的長度。 ( 影響效能 )
for (var i = 0; i < wArray.length; i++) {
  // code;
}
```
良好寫法
```javascript
//1.把變數拉出來
var i,
    max = wArray.length;
for (i = 0; i < max; i++) {
  // code;
}
//2.打包複製帶走的寫法
for (var i = 0, max = wArray.length; i < max; i++) {
  // code;
}
```
**5.For in 迴圈**

在列舉物件屬性時重要的事情：使用hasOwnProperty() 來過濾來自原型鏈的屬性。
```javascript
var man ={
    hands:2,
    legs:2,
    heads:1
}
//沒過濾
for(var i in man){
console.log(i,":",man[i]);
}
    //console結果
    //hands:2
    //legs:2
    //heads:1
    //clone:function()
//有過濾
for(var i in man){
    if(man.使用hasOwnProperty(i)){   //過濾
        console.log(i,":",man[i]);
    }
}
    //console結果
    //hands:2
    //legs:2
    //heads:1
```
**6.Switch**

case 與 switch 並排，case 內縮排，用 break 來結束每個 case，用 default 結束 switch。
```javascript
//此寫法可提升"可讀性"&"穩健性"
var excample = 0,
    result = '';
switch (excample){
case 0:
    result = "0";
    break;
case 1:
    result = "1";
    break;
default:
    result = "unknown";
}
```
**7.避免使用 eval()**
- 因為 eval 刮號內的字串需要被解析才能執行，嚴重拖慢效能
- 使用 eval 有安全性問題，很容易被駭

```javascript
//不良寫法
setTimeout("abc(1, 2, 3)", 2000);
//良好寫法
setTimeout(function () {
  abc(1, 2, 3);
}, 2000);
```
**8.編碼規範**
- 要縮排
- 永遠都應加上大括號
- 左括號跟程式碼放在同一行(不要換行)
- 使用空格
    - for 迴圈的分號之後、變數之間、for 的後面
    - 陣列物件的逗號後面、物件屬性間的逗號後面、物件屬性的值之前、函式參數的逗號後面
    - 大括號的左括號之前、"function" 後面
    - 運算元與運算子的前後，例如 + - * = < > == && || 等等
    - 大括號的右括號與 else、while 之間
- [壓縮 js 線上工具](http://ganquan.info/yui/)

```javascript
//for的使用空格範例
for (var i = 0, max = wArray; i < max; i++) {}
//陣列、物件、函式
var a = [1, 2, 3],
    b = {a: 1, b: 2},
    c = function (a, b, c) {};
```
**9.命名方式一致**
- 小駝峰式：例如 myAppFunc()
- 大駝峰式：例如 MyAppFunc()
- 全大寫：通常用於全域變數、或是值不會改變的變數(ex: var PI = 3.14;)

###實字與建構式
**1.建立各種物件比較有效率的方式**
```javascript
//1. 物件
    // 舊的方式 
    var car = new Object();
    car.color = "blue";
    car.wheels = 4;
    // 新的方式
    var car = {
    color: "blue",
    wheels: 4
    };
//2. 陣列
    // 舊的方式
    var brand = new Array("BMW", "HONDA", "CIVIC");
    // 新的方式
    var brand = ["BMW", "HONDA", "CIVIC"];
//3. 正規表示式
    // 舊的方式
    var re = new RegExp("\\\\", "gm");
    // 新的方式
    var re = /\\/gm;
```
**2.建構式**
- 不要使用 new Object()，可能會導致非預期的結果。
- 自訂建構式函式(用 new 呼叫建構式)
    - 建立一個空物件，參考至this變數，並繼承函式的原型
    - 藉由this的參考，將屬性&方法加入此物件
    - return 回傳值：預設是回傳this所參考的物件
- 忘了 new ，建構式中的 this 會指向 "全域物件"
- JSON

```javascript
//自訂建構式
var adam = new person("Adam");
adam.say();                 //"I am Adam"

var person = function(name){
    //幕後的動作：建立一個空物件
    //var this = {};
    //加屬性&方法
    this.name = name;
    this.say = function(){
        return "I am" + this.name;
    };
    //return this;
}

//return 回傳值：預設是回傳this所參考的物件
var test = function(){
    //這個name屬性會被忽略
    this.name = "This is it."
    
    //建立一個新物件
    var that = {};
    that.name = "That is that."
    return that;
}
var o = new test();
console.log(o.name);        //"That is that."

//忘了 new ，建構式中的 this 會指向 "全域物件"
    //建構式
    function food(){
        this.tastes = "yummy";
    }
    
    //一個新物件
    var good_food = new food();
    console.log(typeof good_food);  //"object"
    console.log(good_food.tastes);  //"yummy"
    
    //一個新物件
    var good_food = food();
    console.log(typeof good_food);  //"undefined"
    console.log(good_food.tastes);  //"yummy"
//JSON
    //JSON的字串
    var jstr = '{"mykey":"my value"}';
    //json轉物件
    var data = JSON.parse(jstr);
    console.log(data.mykey);            //"my value"
//物件、陣列轉JSON
    var dog = {
        name:"Fido",
        dob: new Data(),
        legs:[1,2,3,4]
    }
    //轉JSON
    var jsonstr = JSON.stringify(dog);  //{"name":"Fido","dob":"2010-01-11xxxxxx","legs":[1,2,3,4]}
```
###函式
**1.定義**
```javascript
// A. 具名函式表示式
var abc = function abc(x,y) {// code};
// B. 不具名函式表示式
var abc = function (x,y) {// code};
// C. 函式宣告式
function abc(x,y) {// code}
```
**2. 三者差別：**
- 函數會自動擁有 name 的屬性，不具名函式的 name 沒有值。
- 函式宣告式：未宣告就能執行，表示式無法。
- callback ( 函式可當作參數，傳遞給其他函式，在適當時機才執行 )

```javascript
// name 的屬性
當執行 alert(abc.name) 時，A 與 C 會彈出 "abc"，而 B 會彈出空字串。

//函式宣告式
alert(typeof abc); // 顯示 "undefined"
alert(typeof def); // 顯示 "function"
def(); // 顯示 2
abc(); // 無法執行
var abc = function () {alert(1);};
function def() {alert(2);}

//callback
    // 有名稱(name)的函式可直接把 name 當參數
    var abc = function () {alert(1);};
    setTimeout(abc, 1000);
    // 無名稱的(匿名)函式可直接當參數傳遞
    setTimeout(function() {
        alert(1);
    }, 1000);
    // 這是錯誤方式，把 name 加上 () 當參數傳遞會立刻執行，而非一秒後執行
    setTimeout(abc(), 1000);
```
**3.回傳函式**

函式常常用來回傳(return)數值、字串、陣列等，但函式也可回傳特定的函式內容。 (p.70)
```javascript
// 例(1) 
var a = function () {
    alert(1);
    return function () { alert(2);};
};
var b = a(); // 顯示 1 後，把另外定義的函式內容傳給 b。
b(); // 顯示 2

// 例(2) 回傳函式可儲存其他程式碼無法讀取的資料
var secret = function () {
    return function () {
        var pw = 1234; // 放在這裡的 pw 很安全，外部程式碼無法呼叫
        alert(pw);
    };
};
var tellMePw = secret(); // 只能經由 secret 這個函式才能──
tellMePw(); // 顯示 pw 為 1234
```
**4.自我定義的函式**

如果函式大部分內容只執行一次，只有小部分需要重複使用，可利用函式自我重新定義的方式，來減少函式的內容，同時也可大大減少記憶體的使用。
```javascript
var a = function () {
    alert(1);
    // 其他很多 code
    a = function () { alert(2);};
};
a(); // 第一次顯示 1，以及其他 code。
a(); // 第二次以後執行 a() 只會顯示 2
```
**5.立即函式**

定義後立即執行的函式，只會使用一次，最重要的用處是 "減少全域變數的使用"。
```javascript
(function(){
    //code
})();
```
###物件建立模式
**1.命名空間函式**
```javascript
//基本
    //不安全
    var a = {};
    //比較好
    if(typeof a === "undefined"){
        var a = {};
    }
    //有預設值
    var a = a || {};
    
//物件多層須設定
    //使用命名空間函式
    a.namespace('a.modules.module2');
    //等同於：
    var a = {
        modules:{
            module2:{}
        }
    }
```
**2.public & private**
```javascript
function GG(){
    //private
    name = 'ipaod';
    //public
    this.getName = function(){
        return name;
    }
}
var toy = new GG();
console.log(toy.name);          //undefined (name 是 private)
console.log(toy.getName());     //'ipaod'
```
###程式碼重用模式 (p.119)
[程式碼重用模式](code_reuse_patterns.md)

###設計模式(p.145)
- [Singleton 單體模式](singleton.md)
- [Factory 工廠模式](factory.md) 

```
###DOM 和瀏覽器的模式
**1.關注點分離**

網頁三個主要關注點為內容(HTML)、樣式(CSS)、行為(Javascript)，保持三者盡可能地分離，能兼顧各種瀏覽器版本、平台的執行結果。

**2.DOM 的操作**

DOM 的存取十分昂貴(耗時)，是最常見的 Javascript 效能瓶頸。以下為 DOM 操作的要點：
- 避免在迴圈中存取 DOM (將 DOM 的操作挪到迴圈外)
- 將 DOM 指派給區域變數操作，例如 var a = document.getElementById("abc") 之後使用 a 來操作節點(就不會重複存取該節點)。
- 減少瀏覽器重新繪製的次數，例如使用文件片段(document fragment) 來處理節點，最後再將文件片段加到 DOM 中。

```javascript
//迴圈
    //不良寫法
    for(var i = 0;i < 100; i++){
        document.getElementById("result").innerHTML += i + ",";
    }
    //好的寫法
    var i,count ="";
    for(i = 0;i < 100; i++){
        count += i + ",";
    }
    document.getElementById("result").innerHTML += count;
    
//DOM 指派給區域變數操作
    //不良寫法
    var padding = document.getElementById("result").style.padding,
        margin = document.getElementById("result").style.margin;
    //好的寫法
    var style = document.getElementById("result").style,
        padding = style.padding,
        margin = style.margini;
```

**3.script 元素的處理**
- 合併 js：HTTP 的請求很耗時，所以外部的 js 檔內容最好合併，可減少請求的次數。
- script 放在頁面最後面
- 延遲載入

```javascript
window.onload = function () { // code }
```
