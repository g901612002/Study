#JavaScript - Code Reuse Patterns
JavaScript Pattern之Code Reuse Patterns筆記。  

<!-- more -->

JavaScript沒有class的概念，而物件也僅是名值對(key-value pair)，表示可以即時建立和改變。但JavaScript卻有建構式，類似其他語言(例如：Java)使用class的語法。

例如，在Java中，我們可以這樣寫：

	Person adam = new Person(); //這個「Person」是class

而在JavaScript我們是這樣寫：

	var adam = new Person(); //這個「Person」是建構式函式

因此，在JavaScript中，我們就利用建構式函式(constructor function)來模擬class的繼承，達成程式碼重用的目的。而使用模擬class的繼承方式，我們稱之為Classical模式，而非使用class的模式，例如物件複合的方式，我們就稱之為Modern模式。提醒，基本上永遠選用Modern模式而非Classical模式。

##Classical Inheritance - #1 The Default Pattern
我們來看Classical Inheritance的第一種方式 - 預設的模式。  

Classical Inheritance的實作目的是希望子建構式所建立的物件，能夠擁有父建構式的屬性。因此在預設模式下，就使用Parent()建構式建立一個物件，並將此物件指派給Child()的原型(prototype)。

	function Parent(name){
		this.name = name || 'Adam';
	};
	
	Parent.prototype.say = function(){ //--- ----------------------(1)
		return this.name;
	};
	
	function Child(name){
		//no-op
	};
	
	//使用Parent()建構式建立一個物件，並將此物件指派給Child()的原型
	function inherit(C, P){
		C.prototype = new P(); //----------------------------------(2)
	};
	
	inherit(Child, Parent);

	var kid = new Child(); //透過「「new Child()」」來讓這個模式生效---(3)
	console.log(kid.say()); //'Adam'

[看原始碼](https://github.com/cythilya/Code_Snippets/blob/master/functional_snippet/code_reuse_patterns_classical_inheritance_1_the_d%20efault_pattern.html)。  

這邊要注意的是，prototype要指向父建構式所建立的實體物件，而非指向建構式本身。意即，我們需要透過「new Child()」來讓這個模式生效。新物件就會透過prototype取得Parent()實體的功能，包含加在this中的name、prototype中的say()。  

接下來來看看這樣的預設模式的記憶體配置情況。

![JavaScript Patterns - Classical Inheritance - #1 The Default Pattern](https://lh3.googleusercontent.com/igTEWER_Lgsfwh6NQwAg4t2BB0F4sgGMyJiUTNVEPeI=w699-h361-no)  

(圖片來源：JavaScript Patterns)  

從上圖來看，當我們使用new Parent()建立物件的時候，即圖中(2)，它擁有name這個屬性。而當我們在使用inherit()後，經由 `var kid = new Child(); ` 建立新物件，即產生(3)區塊。由於這個物件沒有加入任何屬性，也沒有任何屬性加入Child.prototype，因此除了`__proto__`這個隱藏版屬性外皆是空的。當我們呼叫 `kid.say()` 時，由於在(3)找不到這個方法，因此回頭到(2)，但(2)也沒有這個方法，於是再到(1)，終於在(1)找到。而say()當中的 `this.name` 需要被判斷，而又從(3)開始找起，在(2)找到name的這個屬性，且其值為'Adam'。  

看到這個記憶體配置狀況與流程，我們可以歸納出兩個缺點：

- 無法選擇要重複使用的屬性。ㄧ般來說我們將可重用的部份放在prototype裡面。
- 無法傳遞參數給子建構式。也就是說，當我們傳了name的值'Lucky'，得到的依舊是'Adam'。而如果使用`son.name = Luck;`則會修改到父建構式，導致

		var son = new Child('Lucky');
		console.log(son.say()); //'Adam'

##Classical Pattern #3 - Rent and Set Prototype
「#2 - Rent-a-Constructor」實作的功能只有#3的一半(Rent)，所以直接跳到#3。首先借用建構式(Rent)，再將子建構式的原型指向父建構式的新實體(Set Prototype)。

- 借用建構式(Rent)：讓子建構式可傳遞參數給父建構式，見程式碼(2)。
- 再將子建構式的原型指向父建構式的新實體(Set Prototype)：取得指向父建構式原型成員的參考，也就是可重用的部份，見程式碼(1)。

我們將#1的程式碼稍做修改即可。  

	function Parent(name){
		this.name = name || 'Adam';
	};
	
	Parent.prototype.say = function(){
		return this.name;
	};
	
	//借用建構式，複製父建構式中加至this的屬性 ------------------------(2)
	function Child(name){
		var arguments = [];
		arguments.push(name);
		Parent.apply(this, arguments);
	};
	
	//取得prototype的成員 ------------------------------------------(1)
	function inherit(C, P){
		C.prototype = new P(); 
	};
	
	inherit(Child, Parent);
	
	var son = new Child('Lucky'); //-------------------------------(3)
	console.log(son.say()); //'Lucky'
	
	delete son.name;
	console.log(son.say()); //'Adam'-------------------------------(4)

[看原始碼](https://github.com/cythilya/Code_Snippets/blob/master/functional_snippet/code_reuse_patterns_classical_inheritance_3_rent_and_set_prototype.html)。 

![JavaScript Patterns - Classical Pattern #3 - Rent and Set Prototype](https://lh3.googleusercontent.com/BkbjHPOxBQjYFCaZg0Fguz_MWZhbc8vrdN8KtPyw0iI=w528-h316-no)  

(圖片來源：JavaScript Patterns) 

這樣的好處是改上#1的缺點

- 可以繼承父建構式的全部功能：包含父建構式中加至this的屬性、加至原型的成員。
- 修改自身屬性而不影響父建構式。
- 可傳遞參數給父建構式，如下程式碼：

		var son = new Child('Lucky');
		console.log(son.say()); //'Lucky'

但同時也有缺點 - 父建構式被呼叫兩次，效率較差，見程式碼(1)、(3)，導致自身屬性被繼承兩次。因此我們刪除了 `kid.name` 後，，執行 `kid.say()` ，得到的不是預期的undefined，而是 'Adam'，見程式碼(4)。
 
###多重繼承
另外來看看多重繼承 - 假設我們有個物件，希望能多重繼承就可以這麼撰寫 - 有一個混種(Mix)想要同時擁有貓(Cat)和鳥(Bird)的特性，我們可以使用兩個建構式Cat()和Bird()，再使用Mix()繼承它們就可以了。 

	function Cat(){
		this.legs = 4;
		this.say = function(){
			return 'mmeaowww';
		};
	};
	
	function Bird(){
		this.legs = 2;
		this.fly = true;
	};
	
	function Mix(){
		Cat.apply(this);
		Bird.apply(this);
	};
	
	var animal = new Mix();
	console.log(animal);

所以animal同時擁有Cat()和Bird()的屬性。  

[Note] 任何重複的屬性都是最後的贏，如下圖，執行 `console.log(animal)` 後的結果。由於最後才繼承Bird()，因此animal的屬性中legs值為2，而不是4。

![多重繼承](https://lh3.googleusercontent.com/RS7OL7NR50q0SVDxqqzLymycqYwv8kAKbl2L_cRB9kE=w179-h76-no)

##Classical Pattern #4 - Share the Prototype
「分享原型」(Share the Prototype)不像#3會呼叫兩次父建構式，或說根本不會呼叫父建構式 - 由於要重用的成員都放在prototype中，因此Child.prototype直接指向Parent.prototype。

	function Parent(name){
		this.name = name || 'Adam';
	};
	
	Parent.prototype.say = function(){
		return this.name;
	};
	
	function Child(name){
		var arguments = [];
		arguments.push(name);
		Parent.apply(this, arguments);
	};
	
	function inherit(C, P){
		C.prototype = P.prototype;
	};
	
	inherit(Child, Parent);
	
	var son = new Child('Lucky'); 
	console.log(son.say()); //'Lucky'
	
	Child.prototype.say = function(){
		return 'Apple'
	};
	console.log(son.say()); //'Apple'，被修改了...一旦孩子修改了prototype，其繼承的父輩的prototype都會被修改到。
	
	var child = new Child();
	console.log(child.say()); //預期是'Adam，結果得到被修改後的'Apple'

[看原始碼](https://github.com/cythilya/Code_Snippets/blob/master/functional_snippet/code_reuse_patterns_classical_inheritance_4_rent_and_share_the_prototype.html)。  

優點是由於每個物件都是分享同一個prototype，所以可以帶來快速的prototype chain查詢。而缺點是一旦孩子修改了prototype，其繼承的父輩的prototype都會被修改到。我們可以看到修改了 `Child.prototype.say` 後，即修改到 `Parent.prototype.say` ，讓後續新產生的物件 `child` 執行  `child.say()` 後得到非預期的結果 'Apple' 。

##Classical Pattern #5 - A Temporary Constructor
「暫時的建構式/代理建構式」(A Temporary Constructor)解決#4的問題 - 若子建構式修改到prototye，即會修改父prototype，導致其繼承的父輩的prototype都會被修改到的問題。解決方法是切斷parent prototype和child prototype間的連結。  

	function Parent(name){
		this.name = name || 'Adam';
	};
	
	Parent.prototype.say = function(){
		return this.name;
	};
	
	function Child(name){
		var arguments = [];
		arguments.push(name);
		Parent.apply(this, arguments);
	};
	
	var inherit = (function(C, P){
		var F = function(){};
		return function(C, P){
			F.prototype = P.prototype; //(4) -> (1)
			C.prototype = new F(); //(3) -> (4)
			C.uber = P.prototype;
			C.prototype.constructor = C;
		};
	})();
	
	inherit(Child, Parent);
	
	var kid = new Child('Lucky'); 
	console.log(kid.name); //'Lucky'
	console.log(kid.say()); //'Lucky
	console.log(kid.constructor.name); //Child
	console.log(kid.constructor); //function Child(name)

[看原始碼](https://github.com/cythilya/Code_Snippets/blob/master/functional_snippet/code_reuse_patterns_classical_inheritance_5_a_temporary_constructor.html)。  

我們使用一個空的函式F()，做為父/子建構式的代理(proxy)，將F()的建構式指向父建構式，而子建構式的prototype則是F()的實體。為了避免每次繼承都要建立暫時的建構式，並且，我們將inherit()優化 - 將其包在一個立即函式裡面，將proxy函式儲存在它的closure中。

![Classical Pattern #5 - A Temporary Constructor](https://lh3.googleusercontent.com/0d7VBGY1Rveru83gsf-u7z9-Nsb-BfTWD_VTN8ombfc=w529-h332-no)  

(圖片來源：JavaScript Patterns) 

註：Klass由於不建議使用，因此就不寫在本筆記裡面了。

##Prototypal Inheritance
基本上永遠使用Modern模式而非Classical模式。第一個Modern Inheritance來看「原型繼承」(Prototypal Inheritance)模式。這個模式沒有class的概念，物件繼承於其他物件。也就是說，若想重用某些功能來創造另一個物件，必定是繼承某個物件以取得這些功能。  

	function obj(o){
		function F(){};
		F.prototype = o; //-----------------(1)
		return new F(); //------------------(2)
	};
	
	var parent = {
		name: 'Papa'
	};
	
	var child = obj(parent);
	child.name = 'Lucky';
	console.log(child.name); //'Lucky'
	
	var son = obj(parent);
	console.log(son.name); //'Papa'

[看原始碼](https://github.com/cythilya/Code_Snippets/blob/master/functional_snippet/code_reuse_patterns_prototypal_inheritance.html)。  

經由obj()產生的子物件總是從一個空物件開始(2)，並經由`__proto__`連結而取得父物件的所有功能(1)。 

![Prototypal Inheritance](https://lh3.googleusercontent.com/JSCMxjVeZmc_kaU3eziHOqOoKYW6kdLdaGn64j4l04o=w528-h186-no)  

(圖片來源：JavaScript Patterns) 

##Inheritance by Copying Properties
「用複製屬性實作繼承」(Inheritance by Copying Properties)，利用迴圈尋訪父物件的每個成員並複製它們。

	function extend(parent, child) {
		var i,
			toStr = Object.prototype.toString,
			astr = "[object Array]";
	
		child = child || {};
	
		for (i in parent) {
			if (parent.hasOwnProperty(i)) {
				if (typeof parent[i] === "object") {
					child[i] = (toStr.call(parent[i]) === astr) ? [] : {};
					extend(parent[i], child[i]);
				} else {
					child[i] = parent[i];
				}
			}
		}
		return child;
	};
	
	var dad = {
		name: 'Adam',
		counts: [1, 2, 3],
		reads: {paper: true}
	};
	var kid = extend(dad);
	console.log(kid); //Object {name: "Adam", counts: Array[3], reads: Object}

[看原始碼](https://github.com/cythilya/Code_Snippets/blob/master/functional_snippet/code_reuse_patterns_inheritance_by_copying_properties.html)。  

##Mix-ins
「混搭」(Mix-ins)，我們不但從一個物件複製，還可以從任意數量的物件複製，並將它們混合到一個物件中。實作方法是使用一個迴圈跑過參數列，將傳遞進來的每一個物件的每個屬性都複製起來即可。例如我們傳入多個物件eggs、butter、flour、sugar，複製每個物件的每個屬性，並混合到一個物件cake中。  

	function mix() {
		var arg, prop, child = {};
		for (arg = 0; arg < arguments.length; arg += 1) {
			for (prop in arguments[arg]) {
				if (arguments[arg].hasOwnProperty(prop)) {
					child[prop] = arguments[arg][prop];
				}
			}
		}
		return child;
	};
	
	var cake = mix(
		{eggs: 2, large: true},
		{butter: 1, salted: true},
		{flour: "3 cups"},
		{sugar: "sure!"}
	);
	
	console.log(cake); //Object {eggs: 2, large: true, butter: 1, salted: true, flour: "3 cups"…}

[看原始碼](https://github.com/cythilya/Code_Snippets/blob/master/functional_snippet/code_reuse_patterns_mixins.html)。  

##Borrowing Methods
使用「借用方法」Borrowing Methods的原因是，我們可能只喜歡現有物件的其中一兩個方法，並想要重用它們，但又不希望建立父子物件繼承關係，因為不想繼承根本用不到的方法。實現的方法是利用call()或apply()向現有物件借用功能。  

例如：notmyobj這個物件中有一個很好用的方法doStuff()，但我們並不想要繼承notmyobj的所有功能，於是利用call()或apply()，使得myobj能暫時借用doStuff()這個方法。我們將myobj和參數傳入，使得借來的方法doStuff()的this綁定到myobj這個物件上。  

	//call() example
	notmyobj.doStuff.call(myobj, param1, p2, p3);
	
	//apply() example
	notmyobj.doStuff.apply(myobj, [param1, p2, p3]);

###EX 1: Borrow from Array
向陣列借方法，以下是arguments向陣列借slice()。

	function f() {
		var args = [].slice.call(arguments, 1, 3);
		return args;
	}
	var result = f(1, 2, 3, 4, 5, 6);
	console.log(result); //[2,3]

不一定要向空陣列借方法，也可以向陣列的原型借。  

	function f() {
		var args = Array.prototype.slice.call(arguments, 1, 3);
		return args;
	}
	var result = f(1, 2, 3, 4, 5, 6);
	console.log(result); //[2,3]

###EX 2: Borrow and Bind
以下範例有一個物件one，而另外一個物件two想要借用one的方法say()。而為了避免物件成為全域物件，因此另外使用bind()這個方法優化。  

	var one = {
		name: "object",
		say: function (greet) {
			return greet + ", " + this.name;
		}
	};
	console.log(one.say('hi')); // 'hi, object'
	
	
	//假設物件two想要借用one的方法say()
	var two = {
		name: "another object"
	};
	console.log(one.say.apply(two, ['hello'])); // 'hello, another object'
	
	//使用bind()優化這個方法，避免物件成為全域物件
	function bind(o, m) {
		return function () {
			return m.apply(o, [].slice.call(arguments));
		};
	}
	
	var twosay = bind(two, one.say);
	console.log(twosay('yo'));// "yo, another object"

###EX 3: Function.prototype.bind()
利用 `Function.prototype.bind()` 。用法如下：

	var newFunc = obj.someFunc.bind(myobj, 1, 2, 3);

範例如下：

	var one = {
		name: "object",
		say: function (greet) {
			return greet + ", " + this.name;
		}
	};
	
	var two = {
		name: "another object"
	};
	
	if (typeof Function.prototype.bind === "undefined") {
		Function.prototype.bind = function (thisArg) {
			var fn = this,
				slice = Array.prototype.slice,
				args = slice.call(arguments, 1);
			return function () {
				return fn.apply(thisArg, args.concat(slice.call(arguments)));
			};
		};
	}
	
	var twosay2 = one.say.bind(two);
	console.log(twosay2('Bonjour')); //"Bonjour, another object"
	
	var twosay2 = one.say.bind(two);
	console.log(twosay2('Bonjour')); //"Bonjour, another object"

[看原始碼](https://github.com/cythilya/Code_Snippets/blob/master/functional_snippet/code_reuse_patterns_borrowing_methods.html)。  

---
####推薦閱讀 / 參考資料
- [JavaScript Patterns](http://shichuan.github.io/javascript-patterns)：每個pattern都有範例程式碼，可以線上或下載下來玩玩看。
- [以上程式碼都可以在這裡找到 - Code Reuse Patterns系列...](https://github.com/cythilya/Code_Snippets/blob/master/functional_snippet/note.md)
- [Javascript 設計模式 教學文件與範例](https://github.com/g901612002/Study/blob/master/Javascript%20Patterns.md)