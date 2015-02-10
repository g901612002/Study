#JavaScript 優良部分

###文法
1.  註解採用 //、/* block */ 兩種方式，當使用 /**/ 需注意，如果 block 中有包含 regular expression 如：/a*/.match 這樣會有錯誤 (這部分我想 IDE 可以幫忙注意)
2.  只有一種數值 (number)，Number 類別為 64 bit 的浮點數，等同於 Java 中的 double
3.  沒有整數型別，1 與 1.0 是一樣的 (Overflow in short int 的問題可完全避免)，使用上只要知道這是數值就可以完全不須理睬他佔用多少空間。
4.  NaN 屬於數值 (number)，代表無法計算的結果。NaN 不代表任何數值 、包含 NaN 也就是說 NaN != NaN，請使用 isNaN(number)p.8
5.  字串 (string) 屬於 16 bit 長度 (當時 Unicode 還只是 16 bit，現在已經… 32 bits 了) p.9
6.  字串永不改變 (與 Java 一樣)，所以 'c'+'a'+'t' === 'cat' p.9
    >講個訣竅：如果要 concat 很多字串，可以先放入陣列再使用 .join() 的方法，有助於效能提升。['c', 'a', 't'].join()
另外，在 Java 中則可以利用 StringBuffer 來操作。

-----------------------------
###物件 (不熟)
1. 參考會被四處傳遞，物件不會被複製。
2. 擷取物件的方法
3. typeof / hasOwnProperty

```javascript
//1.參考會被四處傳遞，物件不會被複製。
var a = {}, b = {}, c = {};     // a, b, c 屬於不同物件
var d = e = f = {};             // d, e, f 指向相同物件

//2.擷取物件的方法
var stooge = {
firstName:"Joe",
firstName:"Howard"
};
stooge["firstName"];    //Joe
stooge.firstName;       //Joe

//3.typeof / hasOwnProperty
typeof stooge.firstName;                //string
stooge.hasOwnProperty('firstName');     //true ( 物件擁有指定特性時會回傳true )
```
---------------------------------
###函式 (不熟)
1. 函式都是物件
2. 函式實字的建立
3. 呼叫模式又可以分為下面四種 ( method / function / constructor / call & apply )
4. try catch ( 處理例外狀況，主要是用來偵錯用的 )
``` javascript
// 俗稱匿名函式 (不利於 debug)
var func = function () { 
  //執行
}
// 命名為 nameFunc 的函式
var func_named = function nameFunc() { 
  //執行
}
//呼叫模式
// 1. method invocation
var obj = {
  add: function(a, b) {
    return (a+b);
  }
}
obj.add(1,2);
// 2. function invocation
function add(a, b) {
  return (a + b);
}
add(1,2);
// 3. constructor invocation
var Add = function(a, b) {
  return (a+b);
}
var add = new Add(a, b);
// 4. call & apply invocation
/*
  fun.call(thisArg[, arg1[, arg2[, ...]]])
  fun.apply(thisArg, [argsArray])
*/
var obj = {
  print: function(arg1, arg2) {
    console.log(this.a);
    console.log(this.b);
    console.log(arg1);
    console.log(arg2);
  }
};
obj.call({a: 1, b: 1});
  /*
    1
    1
    undefined
    undefined
  */
obj.apply({a: 3, b:4}, ["arg1", "arg2"]);
  /*
    3
    4
    arg1
    arg2
  */
  
  //try catch ( 處理例外狀況，主要是用來偵錯用的 )
  try{
... 要判斷的程式碼放這裡 ...
}catch( err ){
... 若 try 的時候有拋出錯誤訊息，則執行這裡的程式碼 ...
}
```

------------------------------------------
###矩陣
1. 新增 unshift / push
2. 刪除 shift / pop / delete / splice(位置,數量)
3. 轉換成字串符號分隔的字串：join() 
4. 連接多個矩陣：concat(array1、array2....)，另外也可以用來作字串連接 
5. 複製某一片段：slice(開始位置,結束位置) 
6. splice() : 矩陣.splice(起點,刪除幾個元素,加入的元素1,.....,加入的元素x)
7. reverse()： 反轉
8. 遍歷方式 ( forLoop / forEach / some )
9. form() : 將字串或輸入參數組成陣列 (非常方便)
10. every()：陣列比對，所有元素都是 true 才是 true (很好用)
11. map : 對陣列中的各元素進行操作，操作後的值會被寫入新的陣列中並返回
12. sort : 陣列排序
13. filter：過濾
``` javascript
//建立矩陣
var num = ["zero","one","two"];
var num = new Array("zero","one","two");
//新增值
num.unshift("stop");            //新增在第一個，num變成["stop","zero","one","two"]
num.push("go");                 //新增在最後一個，num變成["stop","zero","one","two","go"]
//刪除值
delete num[2];                  //num變成["zero","one",undefined,"go"]
num.splice(2,1);                //num變成["zero","one","go"]
num.shift();                    //刪除第一個值
num.pop();                      //刪除最後一個值
//轉換成字串符號分隔的字串
num.join(',');                  //"zero","one","go"
//連接多個矩陣
var a = [5,2,8];
var b = [9,10,11];
var n = a.concat(b);                    //[5,2,8,9,10,11]
//slice
var copyArr = a.slice(1,a.length);      //[2,8]
//splice
//(1)情況一：將2移除並在5跟8之間插入3、4的值
var a = [5,2,8] 
a.splice(1,1,3,4);
console.log(a);                         //[5,3,4,8]
//(2)情況二：第一個位置起全部刪除 
var a = [5,2,8]
a.splice(1);
console.log(a);                         //[5]
//reverse
var a = [5,2,8]
console.log(a.reverse());               //8,2,5
//遍歷方式
//(1)forLoop：這種方式是屬於較具效率的方式，但當內容越多，可讀性就較差，最大的優點就是可以使用break、continue等來中斷。
var a = [5,2,8];
for (var i = 0;i < a.length;i++){
     console.log(a[i]);
}
//(2)forEach：需要一個callback function,在遍歷每個元素的時候都會調用一次這個function，該function提供三個參數如下： 
//alue：元素值
//ndex：元素索引
//array：當前的陣列
var a = [5,2,8];
a.forEach(function(value,index,array){          
      console.log(value);
      console.log(index);
      console.log(array[index]);
})
//(3)some：陣列比對，只要有一個元素是 true，就返回 true (很好用)
//基本寫法
function isBiggerThan10(element, index, array) {
  return element > 10;
}
[2, 5, 8, 1, 4].some(isBiggerThan10);  // false
[12, 5, 8, 1, 4].some(isBiggerThan10); // true
//簡寫
[2, 5, 8, 1, 4].some(elem => elem > 10);  // false
[12, 5, 8, 1, 4].some(elem => elem > 10); // true
//form
function f() {
  return Array.from(arguments);
}
f(1, 2, 3);                             //  [1, 2, 3]
//字串分割
Array.from("foo");                      // ["f", "o", "o"]  <-- 超方便
// 應用
Array.from([1, 2, 3], x => x + x);      // [2, 4, 6]
//every
function isBigEnough(element, index, array) {
  return element >= 10;
}
[12, 5, 8, 130, 44].every(isBigEnough);   // false
[12, 54, 18, 130, 44].every(isBigEnough); // true
//map
var arr = [1, 2, 3, 4, 5, 6];
var arr2 = arr.map(function (element,index,array) {
return element * 2;
});
arr2.join("、");                        // 2、4、6、8、10、12
//sort
//allback(a,b) < 0，a會排在b前面。
//allback(a,b) = 0，不會改變。
//allback(a,b) > 0，a會排在b後面。
var arr = [5, 9, 1, 3, 2, 6];
arr.sort(function (a,b) {
return a - b;
})
// [1,2,3,5,6,9]
//反過來排序的話
arr.sort(function (a,b) {
return b - a;
})
// [9,6,5,3,2,1]
//filter
var arr = [5,2,8]
var filterArr = arr.filter(function(value,index,array){
       return value > 5
})
console.log(filterArr)              //[8]
```
-------------------------------------
###正規式 (p.67)

###字串
1.  search：尋找字串
2.  match：傳回字串內相符的字串
3.  length：取得字串長度
4.  indexOf：尋找某字串在字串內的位置
5.  toUpperCase()  / toLowerCase() 變更英文字大小寫
6.  charAt：傳回字串內某位置的字元(位置由0開始)
7.  substring / substr (傳回字串內某位置區間的字串)
8.  replace：字串取代
9.  concat：組合字串
10. slice：取得切割字串
11. split：分割字串
``` javascript
//search ：stringObject.search (尋找的字串) 大小寫必須相符
var str = "test String";
alert(str.search("Str"));       //輸出結果：5
alert(str.search("str"));       //輸出結果：-1
//match：無相符字串則傳回null，大小寫要相符
var str = "test String";
alert(str.match("TEST"));       //輸出結果：null
alert(str.match("Str"));        //輸出結果：Str
//length
var str = "test String";
alert(str.length);              //輸出結果：11
//indexOf：stringObject.indexOf (尋找的字串,開始尋找的位置)
var str = "test String";
alert(str.indexOf("Str"));      //輸出結果：5
alert(str.indexOf("Str",6));    //輸出結果：-1
//變更英文字大小寫
var str = "test String";
alert(str.toUpperCase());       //輸出結果：TEST STRING
alert(str.toLowerCase());       //輸出結果：test string
//charAt
var str = "test String";
alert(str.charAt(3));           //輸出結果：t
alert(str.charAt(7));           //輸出結果：r
//substring / substr
//stringObject.substring(開始位置,結束位置)
//stringObject.substr(開始位置,字串長度)
alert("0123456789".substring(4,6))      //輸出結果：45
alert("0123456789".substr(4,6))         //輸出結果：456789
//replace：stringObject.replace(要找尋的字串,換成新的字串) ，大小寫必須相符
var str = "test String";
alert(str.replace("string", "Text"));   //輸出結果：test String
alert(str.replace("String", "Text"));   //輸出結果：test Text
//concat：stringObject.concat(字串A,字串B,...,字串X)
var strA = "test ";
var strB = "String";
alert(strA.concat(strB));               //輸出結果：test String
//slice：stringObject.slice(開始位置,結束位置)
var str = "test String";
alert(str.slice(5));                    //輸出結果：String
alert(str.slice(0,4));                  //輸出結果：test
//split：stringObject.split(分割字串, 分割後各字串的字元數)
var str = "test String";
alert(str.split(""));                   //輸出結果：t,e,s,t, ,S,t,r,i,n,g
alert(str.split(" ",4));                //輸出結果：test,String
```
----------------------------
###不良的部分
1. == 
2. with (不要用)
3. eval (不要用)
```javascript
//1.==
alert("" == "0"); // 顯示 false
alert(0 == ""); // 顯示 true
alert(0 == "0"); // 顯示 true
//2.with
with (obj) {a = b;}
//以上代表以下四種可能性：
//a = b;
//a = obj.b;
//obj.a = b;
//obj.a = obj.b;
```
