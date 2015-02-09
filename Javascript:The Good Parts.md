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
###函式
1. 函式都是物件
2. 函式實字的建立
3. 呼叫模式又可以分為下面四種 ( method / function / constructor / call & apply )
4. try catch ( 處理例外狀況，主要是用來偵錯用的 )
```
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

