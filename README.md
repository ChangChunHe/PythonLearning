# 类


类提供了将数据和函数捆绑在一起的手段. 创建一个新类也即为创建一个新类型的对象，允许创建该类型的新实例. 每个类实例都可以附加属性以保持其状态。 类实例也可以有方法（由其类定义）来修改其状态.

与其它的编程语言比较而言, Python的类的机制增加了最少的类的新语法和语义. 它是C++和Modula-3中的类机制的混合体. Python
类提供面向对象编程的所有标准功能:类继承机制允许多个基类, 派生类可以重写其基类或类的任何方法, 以及方法可以调用具有相同名称的基类的方法. 对象可以包含任意数量和种类的数据. 与模块一样, 类也参与了Python的动态特性: 它们是在运行时被创建出来, 并且可以在创建后进一步修改.

在C ++术语中, 通常类成员(包括数据成员)是公共的（除了下面的私有变量), 并且所有的成员函数都是虚拟的. 和Modula-3一样, 没有什么简短的说法从其方法中引用对象的成员: 方法函数用明确的第一个声明表示对象的参数, 它由调用隐式提供. 就像在Smalltalk中一样，类自己是对象. 这为导入和重命名提供了语义. 与C ++和Modula-3不同, 它们是内置类型可以作为用户扩展的基类.  另外, 就像在C ++中一样，大多数内置的运算符(算术运算符，下标等)都是特殊的可以为类实例重新定义语法.

(由于缺少被普遍接受的关于讨论类的专业术语, 我将偶尔使用Smalltalk和C++术语, 我将会使用Module-3中的术语, 因为与C++相比, 面向对象的语句与Python更相似， 但是我期望读者在此之前也听说过这些术语.)

### 1. 一句话关于名称和对象

对象具有独立的, 并且可以将多个名称（在多个作用域中）绑定到同一个对象. 这被称为其他语言中的别名. 在Python中这通常不会在第一眼被注意到，并且在处理不可变基本类型(数字, 字符串, 元组)时可以安全地忽略. 但是，别名对涉及可变对象(如列表, 字典和大多数其他类型)的Python代码的语义可能会有惊人的影响. 这通常用于程序的好处, 因为别名在某些方面表现得像指针.  例如, 传递一个对象很便捷的， 因为只有一个指针被传递; 如果一个函数修改了一个作为参数传递的对象,调用者就会看到这个变化 - 这就消除了在Pascal中两个不同的参数传递机制的需要.


### 2. Python的作用域和命名空间

在介绍类之前, 我不得不首先告诉你一些关于Python变量作用域的规则. 类的定义在命名空间上有着一些很简洁的技巧, 并且你需要知道作用域和命名空间如何工作, 从而充分理解到底发生了什么. 顺便一提, 关于该主题的知识对于任何高级的Python程序都很有用.


让我们以一些名词的定义开始本节.


一个命名空间 (namespace) 是一个从名称到对象的映射. 目前大多数命名空间都是使用Python的字典 (dict) 实现的, 但通常不会以任何方式显示（除了性能, 字典查找起来很快的!). 并且在将来可能会更改. 命名空间的例子是：内置名称集合（包含诸如abs（）的函数和内置的异常名称); 模块中的全局命名; 和函数调用中的本地命名. 从某种意义上说，对象的一组属性也构成一个名称空间。 了解名称空间的重要之处在于，不同名称空间中的名称之间绝对没有关系; 例如，两个不同的模块都可以定义一个maximiza的函数, 但不会混淆 ---- 使用模块的用户必须在模块名称前加前缀.


顺便一提, 我用了属性这一个名词来表示任意一个点后面的名称 - 例如, 在表达式z.real 中, real 是对象z的属性. 严格地说，对模块中名称的引用是属性引用: 在表达式 modname.funcname 中，modname是模块对象，funcname是它的属性. 在这种情况下，模块的属性和定义在模块中的全局命名(变量)恰好存在一个直接的映射关系: 它们共享相同的命名空间!\footnote{ Except for one thing. Module objects have a secret read-only attribute called \_ dict\_ which returns the dictionary used to implement the module’s namespace; the name \_ dict\_ is an attribute but not a global name. Obviously, using this violates
the abstraction of namespace implementation, and should be restricted to things like post \- mortem debuggers.}

属性可以是只读的或者可写的. 在后一种情况下, 可以分配属性. 模块属性是可写的: 你可以编写 modname the\_ answer = 42. 可写入的属性也可以用del语句删除. 例如, del modname.the\_ answer将从以modname命名的模块中删除属性the\_ answer.

命名空间是在不同的时刻创建的, 并且具有不同的生命周期. 包含内置名称的命名空间是在Python解释器启动时创建的，并且永远不会被删除. 对于一个模块的全局命名空间是在读取该模块定义的时候创建的; 通常, 模块名称空间也持续到解释器退出。 由解释器的顶层调用执行的语句, 要么从一个脚本文件中读取或者交互式地执行, 这些被认为是\_\_ main \_ \_ 模块的一部分, 所以它们有它们自己的
全局命名空间.（内置的命名实际上也存在于一个模块中;这被称为内建函数.）


函数的本地命名空间在调用函数时创建, 并在函数返回或者抛出一个无法处理的异常时被删除. （其实, 遗忘将是一个
更好的方式来描述实际发生的事情.）当然, 递归调用每个都有自己的本地命名空间.


作用域是Python程序的文本区域，可以直接访问命名空间.  “直接访问”, 这意味着对名称的非限定引用会尝试在名称空间中查找名称.


尽管作用域是静态创建的, 但是它们可以动态使用. 在执行过程中的任何时间,至少有三个嵌套的作用域可以直接访问.

- 最内部的作用域, 最先被搜索的, 包括本地命名空间
- 任何封闭函数的范围, 从最近的封闭范围开始搜索，包含nonlocal，但也包含non-global 名称
- 倒数第二层作用域包含当前模块的全局命名
- 最外层的作用域(最后搜索的)是包含内置命名的命名空间



如果一个命名被声明为全局的，那么所有的引用和赋值直接进入到包含该模块全局命名的中间作用域. 重新绑定在最内层作用域外发现的变量, 可以使用nonlocal语句; 如果没有声明为nonlocal，那些变量是只读的（试图写入这样的变量
将简单地在最内层的范围内创建一个新的本地变量，并不改变相同名称的外层变量.



通常, 本地作用域引用（文本）当前函数的本地名称. 在函数外面, 
本地作用域引用与全局作用域相同的名称空间: 模块的命名空间. 类的定义放置在本地作用域的另一个命名空间.


认识到范围是以文本方式确定是很重要的: 定义在一个模块中的函数的全局作用域就是该模块的命名空间, 无论从哪里或通过别名调用该函数. 在另一方面, 名称的实际搜索是在运行时动态完成的 - 但是，语言定义
正在向“编译”时期的静态命名解析发展，因此不要依赖动态命名解析!（事实上, 本地变量已经静态确定.）


Python的一个特别的问题是 - 如果全局声明没有生效 - 对名称的赋值总是会进入
最内层的范围. 分配不会复制数据 - 它们只是将名称绑定到对象. 删除也是同样如此: 语句del x 从本地作用域删除了域x绑定的命名空间. 事实上，所有引入新名称的操作都使用本地范围：特别是import语句和
函数定义将模块或函数名称绑定在本地作用域中。


Global 语句可以用来表明特定变量存在于全局范围内，并且应该在那里反弹;nonlocal 语句表示特定变量存在于封闭范围内
应该在那里反弹。

### 2.1 作用域和命名空间的例子

下面s一个展示怎样引用不同的作用域和命名空间, 以及全局变量(global)和非本地变量(nonlocal)变量是如何影响变量绑定的.

```python
def scope_test():
	def do_local():
		spam = "local spam"
def do_nonlocal():
	nonlocal spam
	spam = "nonlocal spam"
	def do_global():
		global spam
		spam = "global spam"
	spam = "test spam"
	do_local()
	print("After local assignment:", spam)
	do_nonlocal()
	print("After nonlocal assignment:", spam)
	do_global()
	print("After global assignment:", spam)
scope_test()
print("In global scope:", spam)
```

这个示例代码的输出为:
```python
After local assignment: test spam
After nonlocal assignment: nonlocal spam
After global assignment: nonlocal spam
In global scope: global spam
```

注意到本地赋值不会影响到函数"scope_test"中“spam”绑定的值. “nonlocal”赋值改变了“scope_test”中"spam"绑定的值, 而"global"赋值将会在模块级别上改变变量的绑定值.

你也可以看到在"global" 赋值之前, "spam"是没有任何绑定的值的(因为在”spam“没有global之前, 只有在scope_test函数中才可以调用"spam", 函数外面的范围都无法调用, 而global将其作用范围拓展到整个模块级别, 从而最后一句代码可以找到"spam"绑定的值).


### 3. 初见类

类引入了一些新的语法, 三种新的对象类型, 和一些新的语义.

#### 3.1 定义类的语法
下面是一个简单的定义类的形式:
```python
class ClassName:
	<statement-1>
	.
	.
	.
	<statement-N>
```

定义类, 类似于函数的定义(def 语句)必须在它们有作用之前执行. (你可以想象将一个类定义放在if语句的一个分支中，或者放在一个函数中.)


实际上, 在类内部的语句通常都是函数定义, 当然也允许其它语句的存在和一些有用的信息--我们在后面还会讲到这个. 在类中的函数定义对于输入参数通常有一个特定的形式, 由方法的调用来决定 -- 再次声明, 后面会解释的.


当进入一个类的定义时, 就会创建一个新的命名空间, 并且使用本地的作用域 -- 因此, 所有对于本地变量的赋值都会进入这个新的命名空间. 特别地, 函数定义与这里的新的函数的命名相绑定.

当一个类正常退出时, 就会创建一个类(class)对象. 这是一个通过函数定义创建的关于命名内容的装饰器; 我们可以将会在下一节学到更多关于类对象的知识. 最初的本地作用域(在进入类定义之前起作用的作用域)被恢复，并且类对象在这里被绑定到类定义头部(示例中的ClassName)中给出的类名。

#### 3.2 类对象

类对象支持两种操作: 属性引用和实例化.

在Python中, $Attribute reference$ 对于所有的属性引用都是通过标准的的语法实现的: **obj.name**. 有效的属性名称是当创建类对象以后, 在类的命名空间下所有的命名. 所以, 吐过类的定义看起来像这样:
```python
class MyClass:
	"""A simple example class"""
	i = 12345
	def f(self):
		return 'hello world'
```
接着, **MyClass.i**和**MyClass.f**就是有效的属性引用了, 各自返回一个整数类型和一个函数类型. 类的属性也可以被赋值, 所以你可以通过赋值来改变**MyClass.i**的数值. **__doc__**也是一个有效的属性, 返回属于这个类的文档说明:"A simple example class".


类的实例化使用函数记号. 就只是假装类对象是一个无参数函数并且返回这个类的新的实例. 例如(在存在上面这个的假设下):
```python
x = MyClass()
```
这就实现了创建类的一个新的实例并且将其赋值给一个本地变量**x**.


这个实例化操作(调用一个类对象)创建了空的对象. 许多类喜欢创建具有定制到特定初始状态的实例的对象. 因此一个类旺旺定义了一个特殊的方法, **__init__()**, 就像这样:
```python
def __init__(self):
	self.data = []
```
当一个类定义了**__init__**方法以后, 对于新创建的类实例来说, 实例化类就自动触发了**__init__**方法. 所以在这个例子中, 一个新的, 初始化的实例可以通过
```python
x = MyClass()
```
来得到. 当然, 为了更强大的灵活性, **__init__**方法也可以有输入参数. 在那样的情形下, 实例化类的参数将会传入**__init__**. 例如:
```python
>>> class Complex:
... def __init__(self, realpart, imagpart):
... self.r = realpart
... self.i = imagpart
...
>>> x = Complex(3.0, -4.5)
>>> x.r, x.i
(3.0, -4.5)
```

#### 3.3 实例对象

那么现在我们可以用实例对象做什么呢? 实例对象理解的操作知识属性引用. 有两张有效的属性名称, 数据属性(attribute)和方法(method).


$data~~attributes$ 对应于Smalltalk中的“实例变量”, 以及C ++中的“数据成员”.  数据属性不需要声明; 像局部变量一样, 它们在第一次分配时就会弹出. 例如，如果x是上面创建的MyClass的实例, 则下面的一段代码将打印值16, 并且不留下任何痕迹：
```python
x.counter = 1
while x.counter < 10:
	x.counter = x.counter * 2
print(x.counter)
del x.counter
```


另一种实例属性引用是一种方法. 方法是“属于”对象的功能.(在Python中, 术语方法并不是唯一的类实例: 其他对象类型也可以有方法, 例如, list对象有append，insert，remove，sort等方法. 但在下面的讨论中, 除非另有明确说明, 否则我们将使用专业术语来表示类实例对象的方法.）


实例对象的有效方法名称取决于它的类. 根据定义, 作为函数对象的类的所有属性都定义其实例的相应方法. 所以在我们的例子中, x.f是一个有效的方法引用. 因为MyClass.f是一个函数, 但x.i不是, 因为MyClass.i不是. 但是x.f与MyClass.f不是一回事 - 它是一个方法对象，而不是函数对象。


#### 3.4方法对象
通常, 一个方法在绑定以后可以立刻调用:
```python
x.f()
```

在**MyClass**类的例子中, 这会返回字符串"hello world". 然而, 立刻调用一个方法是没有必要的:**x.f()**是一个方法对象, 并且可以先储存起来留以后来调用. 例如:
```python
xf = x.f
while True:
	print(xf())
```
将会持续打印**hello world**直到时间截止.


在方法调用的时候究竟发生了什么? 你可能已经注意到， 即使**f()**的函数定义指定了参数, **x.f()**也没有上述参数被调用. 这个参数发生了什么? 当一个需要参数的函数没有被调用时, Python当然会引发一个异常 - 即使这个参数没有被实际使用...

实际上, 您可能已经猜到了答案: 关于方法的特殊之处在于实例对象作为函数的第一个参数传递. 在我们的例子中, 调用**x.f()**与**MyClass.f()**完全等价. 一般来说, 调用带有n个参数的方法相当于使用一个参数列表来调用相应的函数，该参数列表是通过在第一个参数之前插入方法的实例对象而创建的.


如果你仍然不明白方法是如何工作的, 那么查看实现工具可能会澄清一些问题. 当引用不是数据属性的实例属性时，将搜索其类. 如果名称表示一个有效的类属性，它是一个函数对象，则通过打包（指向）实例对象和在抽象对象中一起找到的函数对象来创建方法对象: 这是方法对象. 当使用参数列表调用方法对象时, 会从实例对象和参数列表构造一个新参数列表, 并使用此新参数列表调用函数对象.

#### 3.5 类变量和实例变量
一般来说, 实例变量对于每一个实例都是专属的, 而类变量是针对类的所有实例共享的属性和方法.
```python
class Dog:

	kind = 'canine' # class variable shared by all instances
	
	def __init__(self, name):
		self.name = name # instance variable unique to each instance

>>> d = Dog('Fido')
>>> e = Dog('Buddy')
>>> d.kind 		# shared by all dogs
'canine'
>>> e.kind 		# shared by all dogs
'canine'
>>> d.name 		# unique to d
'Fido'
>>> e.name 		# unique to e
'Buddy
```

正如$A~word~about~names~and~bbjects$中所讨论的, 当涉及到list, dict等可变对象时, 共享数据可能会带来令人惊讶的影响.  例如, 以下代码中列表$trick$不应该用作类变量, 因为所有Dog实例共享的只有一个list. (实际上就是不同的实例共享这个可变对象$trick$导致$trick$可能不止携带一个实例的信息, 导致数据紊乱.)
```python
class Dog:

	tricks = [] 		# mistaken use of a class variable
	
	def __init__(self, name):
		self.name = name
		
	def add_trick(self, trick):
		self.tricks.append(trick)
		
>>> d = Dog('Fido')
>>> e = Dog('Buddy')
>>> d.add_trick('roll over')
>>> e.add_trick('play dead')
>>> d.tricks 		# unexpectedly shared by all dogs
['roll over', 'play dead']
```
这个类的正确涉及方式应该是用一个实例属性来替代
```python
class Dog:
	def __init__(self, name):
		self.name = name
		self.tricks = [] # creates a new empty list for each dog
		
	def add_trick(self, trick):
		self.tricks.append(trick)
		
>>> d = Dog('Fido')
>>> e = Dog('Buddy')
>>> d.add_trick('roll over')
>>> e.add_trick('play dead')
>>> d.tricks
['roll over']
>>> e.tricks
['play dead']
```
#### 3.6 随便说点

数据属性覆盖具有相同名称的方法属性; 为了避免意外的名称冲突, 这可能会在大型程序中导致难以发现的错误, 使用某种可以最小化冲突几率的约定是明智的. 可能的约定包括大写方法名称, 用小的唯一字符串(可能只是一个下划线)为数据属性名称加前缀, 或使用动词来表示方法和名词来表示数据属性.


数据属性可以由方法以及对象的普通用户(``客户端'')引用.  换句话说, 类不可用于实现纯粹的抽象数据类型. 实际上, Python中没有任何东西可以强制执行数据隐藏 - 它都基于约定. (另一方面, 用C编写的Python实现可以完全隐藏实现细节并在必要时控制对对象的访问的权限; 这可以通过用C编写的Python扩展来使用.）


用户应小心使用数据属性 - 用户可能会通过标记其数据属性来混淆方法维护的非变量. 注意到，客户端可以将自己的数据属性添加到实例对象，而不会影响方法的有效性，只要避免名称冲突 - 再次申明，命名约定可以在此节省大量时间与精力.


从方法中引用数据属性(或其他方法)没有快捷方式.  我发现这实际上增加了方法的可读性: 在浏览方法时, 不会混淆局部变量和实例变量.



通常, 方法的第一个参数称为**self**. 这只不过是一个约定: 名字self对Python来说绝对没有特殊含义. 但是, 请注意, 如果不遵循约定, 你的代码对其他Python程序员而言可能会不那么可读, 并且也可以设想一个类浏览器($class~~browser$) 可能会编写一个依赖于这种约定的程序.


任何作为类属性的函数对象都为该类的实例定义了一个方法. 没有必要将函数定义用文本方式包含在类定义中：将类对象分配给类中的局部变量也是可以的。 例如：

```python
# Function defined outside the class
def f1(self, x, y):
	return min(x, x+y)
	
class C:
	f = f1
	
	def g(self):
		return 'hello world'
		
	h = g
```


现在**f, g**和**h**都是类**C**的属性的函数对象, 并且因此他们都是**C**的实例方法 - **h**的完全的等价于**g**. 注意到这个试验经常导致读者被程序困惑.

方法也可以通过使用方法属性来调用其他的方法:
```python
class Bag:
	def __init__(self):
		self.data = []
		
	def add(self, x):
		self.data.append(x)
		
	def addtwice(self, x):
		self.add(x)
		self.add(x)
```

方法可以像普通函数一样引用全局名称. 与方法关联的全局作用域是包含其定义的模块. (一个类永远不会被用作全局作用域.) 虽然很少有人在方法中使用全局数据, 但全局范围有许多合法用途: 首先, 导入全局作用域的函数和模块可以 被方法以及在其中定义的函数和类所使用. 通常, 包含该方法的类本身是在全局范围内定义的, 在下一节中, 我们会找到一些很好的理由来说明为什么方法想要引用它自己的类.


每一个数值都是对象, 并且因此有一个类(也被称之为它的$~type$).  被存储为**object.__class__**中

### 4. 继承
当然, 如果不支持继承, 语言特性就不值得称为“类”.  派生类定义的语法如下所示:
```python
class DerivedClassName(BaseClassName):
	<statement - 1>
	.
	.
	.
	<statement - N>

```


名称BaseClassName必须在包含派生类定义的作用域中定义. 对于代替基类名称, 其他的表达式也是允许的. 例如, 当基类在另一个模块中定义时, 这可能很有用:
```python
class DerivedClassName(module.BaseClassName):
```


派生类定义的执行过程与基类相同. 当构造类对象时, 基类将被记住.  这用于解析属性引用: 如果在类中未找到请求的属性, 则搜索继续查找基类. 如果基类本身是从其他类派生的, 则此规则将递归应用(从左到右, 深度优先).


派生类的实例化没有什么特别的: DerivedClassName() 创建一个新的类实例. 方法引用解析如下: 如果需要, 搜索相应的类属性, 沿基类的链下降, 如果产生函数对象, 则方法引用是有效的.


派生类可以重写它们的基类的方法. 由于方法在调用同一对象的其他方法时没有特殊的权限, 因此调用另一个在同一基类中定义的方法的基类的方法可能最终会调用派生类的方法来覆盖它（对于C ++程序员: Python中的所有方法都是有效的.）



派生类中的重写方法事实上可能需要扩展而不是简单地替换同名的基类方法. 有一种简单的方法可以直接调用基类方法: 只需调用BaseClassName.methodname(self，arguments)即可. 这对客户也是偶尔有用的. (注意, 这只有在基类可以在全局范围内作为BaseClassName访问时才有效.）


Python有两个与继承一起工作的内置函数:

- 使用isinstance() 检查实例的类型: 只有当obj.__ class__是int或从int派生的某个类时, isinstance（obj, int）才会为True
- 使用issubclass() 检查类继承: 由于bool是int的子类, 因此issubclass(bool，int)为True. 但是, 由于float不是int的子类, 因此issubclass(float, int)为False


#### 4.1 多重继承
Python也支持多重继承. 通过多个基类定义的类像这样:
```python
class DerivedClassName(Base1, Base2, Base3):
	<statement -1>
	.
	.
	.
	<statement -N>

```

对于大多数情况, 在最简单的情况下, 你可以考虑搜索从父类继承的属性的搜索顺序为:深度优先, 从左到右, 而不是在层次结构中存在重叠的同一类中进行两次搜索. 因此, 如果在DerivedClassName中找不到属性, 则在Base1中搜索它, 然后(递归地)在Base1的基类中, 如果没有在Base1中找到它, 则在Base2中搜索它, 依此类推.


事实上, 它比这稍微复杂一些; 方法解析顺序动态变化以支持调用super()方法. 这种方法在一些其他多继承语言中是被称为
call-next-method并且比单继承语言中的超(super)调用更强大.


动态排序是必要的, 因为所有多重继承的情况都表现出一个或多个菱形关系(其中至少有一个父类可以通过多个路径从
最底层的类). 例如, 所有类都从object继承, 所以任何多重继承的情况提供了多条路径来达到object. 为了保持基类不被多次访问,
动态算法对搜索顺序进行线性化, 保证每个类中指定的从左到右的顺序, 每个父类只调用一次, 这是单调的(意味着一个类
可以在不影响其父类的优先顺序的情况下进行分类). 综合起来, 这些属性使设计具有多继承性的可靠和可扩展类成为可能。 有关更多细节，请参阅 [多重继承][1]

### 5. 私有变量

Python中不存在“私有”实例变量, 这些变量除了在对象内部以外不能访问. 但是, 大多数Python代码都遵循一个约定: 以下划线(例
如_spam)作为前缀的名称应被视为API的非公开部分(无论它是函数, 方法还是数据成员). 它应该被视为细节的实现, 如有更改, 恕不另行通知.


由于类私有成员有一个有效的用例(即为了避免名称与名称由子类定义的名称冲突), 所以对这种称为名称修改的机制的支持有限, 被称为名称销毁(name mangling). 任何形如\_\_spam的标识符(至少两个前导下划线, 最多一个尾随下划线)被\_classname\_\_spam文本替换使用, 其中classname是当前类名称, 并带有前导下划线. 只要它发生在类的定义内, 就不会考虑标识符的语法位置.

名称修改有助于让子类重写方法而不会破坏intraclass方法调用。例如：
```python
class Mapping:
	def __init__(self, iterable):
		self.items_list = []
		self.__update(iterable)
		
	def update(self, iterable):
		for item in iterable:
			self.items_list.append(item)
			
	__update = update 		# private copy of original update() method
	
class MappingSubclass(Mapping):
	def update(self, keys, values):
		# provides new signature for update()
		# but does not break __init__()
		for item in zip(keys, values):
			self.items_list.append(item)
```

请注意, 销毁规则的设计主要是为了避免冲突; 它仍然可以访问或修改被认为是私有的变量.  这在特殊情况下甚至是有用的, 例如在调试器中.



注意传递给**exec**()或**eval**()的代码并不将调用类的类名看做是当前类; 这与**global**语句的效果类似, 其效果同样局限于一起字节编译的代码. 对**getattr**(), **setattr**()和**delattr**()以及直接引用\_\_ dict\_\_ 也有同样的限制.


### 6. 零碎的知识

有时候, 拥有 类似于Pascal的"record"或者C的"struct"数据类型, 将几个命名的数据项绑定在一起会是很有用的. 空的类定义将会很好完成.

```python
class Employee:
	pass
	
john = Employee()		 # Create an empty employee record

# Fill the fields of the record

john.name = 'John Doe'
john.dept = 'computer lab'
john.salary = 1000
```

一段Python代码通常可以传递一个模拟该数据类型方法的类. 例如, 如果您有一个函数可以格式化文件对象中的某些数据, 则可以使用方法read() 和readline() 来定义一个类, 以便从字符串缓冲区中获取数据, 并将其作为参数传递(实际上这就是Python作为动态语言的一个特性).

实例方法对象也具有属性: m .\_\_ self\_\_ 是方法为m()的实例对象, m\.\_\_ func\_\_ 是与该方法对应的函数对象.


### 7. 迭代器

现在你可能已经注意到大多数容器对象可以使用for语句循环遍历:
```python
for element in [1, 2, 3]:
	print(element)
for element in (1, 2, 3):
	print(element)
for key in {'one':1, 'two':2}:
	print(key)
for char in "123":
	print(char)
for line in open("myfile.txt"):
	print(line, end='')
```

这种访问方式清晰, 简洁, 方便. 迭代器的使用贯穿并统一了Python. 在幕后, for语句在容器对象上调用iter().  该函数返回一个迭代器对象, 该对象定义某一时刻只能访问容器对象中的一个元素的方法 \_\_ next\_\_ ().  当没有更多的元素时, \_\_ next \_\_()引发一个StopIteration异常, 它告诉for循环终止. 你可以使用next()内置函数调用\_\_ next \_\_ ()方法; 这个例子展示了它是如何工作的:
```python
>>> s = 'abc'
>>> it = iter(s)
>>> it
<iterator object at 0x00A1DB50>
>>> next(it)
'a'
>>> next(it)
'b'
>>> next(it)
'c'
>>> next(it)
Traceback (most recent call last):
	File "<stdin>", line 1, in <module>
		next(it)
StopIteration
```

看到迭代器协议背后的机制, 很容易将迭代器行为添加到类中.  定义一个\_\_ iter \_\_ ()方法, 该方法使用\_\_ next \_\_ ()方法返回一个对象. 如果类定义了\_\_ next \_\_(), 则\_\_ iter \_\_ ()可以返回self:
```python
class Reverse:
"""Iterator for looping over a sequence backwards."""
	def __init__(self, data):
		self.data = data
		self.index = len(data)
	def __iter__(self):
		return self
	def __next__(self):
		if self.index == 0:
			raise StopIteration
		self.index = self.index - 1
		return self.data[self.index]
```

```python
>>> rev = Reverse('spam')
>>> iter(rev)
<__main__.Reverse object at 0x00A1DB50>
>>> for char in rev:
	... print(char)
	...
m
a
p
s
```

### 8. 生成器
Generator(生成器)是创建迭代器的简单而又强大的工具. 它们像常规函数一样编写, 但只要他们想返回数据就使用yield语句. 每次next()被调用时, 生成器器都会从停止的地方恢复(它记住所有的数据值以及上次执行的语句). 一个表明生成器可以轻而易举地创建的例子：
```python
def reverse(data):
	for index in range(len(data)-1, -1, -1):
		yield data[index]
```

```python
>>> for char in reverse('golf'):
	... print(char)
	...
f
l
o
g
```

任何可以用生成器完成的事情也可以用前面部分描述的基于类的迭代器完成. 使生成器如此紧凑的原因是 \_\_ iter \_\_()和
\_\_ next \_\_()方法是自动创建的.


另一个关键特性是本地变量和执行状态在调用之间自动保存. 这使得该函数更容易编写, 并且比使用self.index和self.data等实例变量的方法更加清晰.

除了自动方法创建和保存程序状态之外, 当生成器终止时, 它们会自动抛出StopIteration.  结合起来, 这些功能可以轻松创建迭代器, 而无需编写常规函数.

### 9. 生成器表达式

一些简单的生成器可以使用与列表解析相似的语法简洁地编码为表达式, 但带括号而不是方括号.  这些表达式适用于通过封闭函数立即使用生成器的情况.  生成器表达式比完整的生成器定义更紧凑但功能更少, 并且倾向于比等效的列表解析更具有内存友好性.


例子:
```python
>>> sum(i*i for i in range(10)) # sum of squares
285

>>> xvec = [10, 20, 30]
>>> yvec = [7, 5, 3]
>>> sum(x*y for x,y in zip(xvec, yvec)) 	# dot product
260

>>> unique_words = set(word for line in page for word in line.split())

>>> valedictorian = max((student.gpa, student.name) for student in graduates)

>>> data = 'golf'
>>> list(data[i] for i in range(len(data)-1, -1, -1))
['f', 'l', 'o', 'g']
```


  [1]: https://www.python.org/download/releases/2.3/mro/
