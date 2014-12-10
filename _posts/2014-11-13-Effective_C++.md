---
layout: post
title:  "《剑指Offer》读书笔记"
date:   2014-10-04 12:00:00
tags:	tech
myTag:	interview

---

# "Effective C++" 读书笔记

-----------------------------

### Rule 04: 确定对象在被使用前已被初始化

初始化(initialization)和赋值(assignment)是两个容易混淆的概念。考虑一个class，其构造函数如下:

	class PhoneNumber{...};
	class ABEntry{
	public:
		ABEntry(const std::string& name, const std::string& address, 
				const std::list<PhoneNumber>& phones);
	private:
		std::string theName;
		std::string theAddress;
		std::list<PhoneNumber> thePhones;
		int numTimesConsulted;
	};
	ABEntry::ABEntry(const std::string& name, const std::string& address, 
					 const std::list<PhoneNumber>& phones){
		theName = name				//	(assignment)
		theAddress = address;		//	(assignment)
		thePhones = phones;			//	(assignment)
		numTimesConsulted = 0;		//	(assignment)
	}

ABEntry构造函数内，theName, theAddress, thePhones的改变都是赋值而不是初始化。初始化发生在更早的阶段。初始化发生在这些成员变量的default构造函数被自动调用的时候。但对于numTimeConsulted，由于它是内置类型，不保证一定在你所看到的那个赋值动作之前获得初值。

ABEntry构造函数一个较佳的写法是使用member initialization list(成员初值列)替代赋值动作:

	ABEntry::ABEntry(const std::string& name, const std::string& address, 
					 const std::list<PhoneNumber>& phones):
		theName(name),				//	(assignment)
		theAddress(address),		//	(assignment)
		thePhones(phones),			//	(assignment)
		numTimesConsulted(0)		//	(assignment)
	{}								//	(empty in construct function)

赋值版本的初始化首先调用default构造函数为theName, theAddress和thePhones设初值，然后再立即对它们赋予新值。default构造函数的一切作为因此浪费了。成员初值列(member initialization list)避免了这一问题。初值列中针对各个成员变量而设的实参被拿去作为各个成员变量之构造函数的实参。theName以name为初值进行copy构造，theAdderss以address为初值进行copy构造，thePhones以phones为初值进行copy构造。

对于内置函数如numTimesConsulted，其初始化和赋值的成本相同，但为了一致性，最好也是通过成员初值来初始化。

如果面对的成员变量属于内置类型但是是const或references，它们就一定需要初值，而不能被赋值。最简单的做法就是:总是使用成员初值列。

成员初始化次序总是相同的: base classes更早于其derived classes被初始化，而class成员变量总是以其声明次序被初始化。在本例中，theName成员最想被初始化，然后是theAddress，再来是thePhones，最后是numTimesConsulted。**即使它们在成员初值列中以不同次序出现**。

### Rule 05: 了解C++默默编写并调用那些函数

编译器会给一个空类声明一个copy构造函数，一个copy assignment操作符和一个析构函数。如果没有声明任何构造函数，编译器也会为它声明一个default构造函数。这些都是public且inline的。因此，如果你写下:

	class Empty{};

相当于这样的代码;

	class Empty{
	public:
			Empty(){...}
			Empty(const Empty& rhs){...}
			~Empty(){...}

			Empty& operator=(const Empty& rhs){...}
	};

### Rule 06: 若不想使用编译器自动生成的函数，就应该明确拒绝

如果我们希望禁止class的拷贝，我们因该怎么做呢？如果我们不声明copy构造函数和copy assignment操作符，那么编译器将会自动帮我们生成，如果我们声明，那么我们的class还是支持copying了。

禁止copy的关键是: 所有编译器产出的函数都是public，为了防止这些函数被创建出来，我们可以将copy构造函数或copy assignment操作符声明为private。

一般而言，这样还不是绝对安全的，因为member函数和friend函数还是可以调用private函数。**将成员函数声明为private并且故意不实现它们**这一计俩是如此被大家接受的。

有了上述class定义，如果某些人不慎调用了函数，将会获得一个连接错误(linkage error)。将连接期错误移至编译期是可能的。只要将copy构造函数和copy assignment操作符声明为private就可以办到，但不是在你定义的类当中，而是在一个专门防止copying动作而设计的base class中。这个base class非常简单:

	class Uncopyable{
	protected:										//	allowing construct and distruct
		Uncopyable(){}
		~Uncopyable(){}
	private:										//	forbidding copying
		Uncopyable(const Uncopyable&);
		Uncopyable& operator=(const Uncopyable&);
	};

为了阻止class对象被拷贝，我们唯一需要做的就是继承Uncopyable:

	class MyClass: private Uncopyable{
		...
	};

此时，就算是member函数或者friend函数都无法调用copy构造函数和copy assignment操作符，这些调用会被编译器拒绝，因为其base class的拷贝构造函数是private。

下面是用不同方式继承，派生类能调用基类的方式:

			    public		protected		private
	公有继承	public		protected		不可见
	保护继承	protected	protected		不可见
	私有继承	private		private			不可见


### Rule 07: 为多态基类声明virtual析构函数

如果一个基类指针指向一个派生类，当我们delete这个指针的时候，很有可能出现这样一个结果: 对象的derived成分没有被销毁，而其base class成分通常会被销毁，于是造成了一个"局部销毁"的对象。这可能引起资源泄漏、败坏数据结构等结果。

消除这个问题的方法是**给base class一个virtual析构函数**。此后删除derived class对象将会销毁整个对象，包括所有derived class成分。

如果class不含virtual函数，通常表示它并不意图被用作一个base class。当class不企图被当作base class，令其析构函数为virtual往往是个馊主意。

欲实现出virtual函数，对象必须携带某些信息，主要用来在运行期决定那一个virtual函数该被调用。这份信息通常由一个所谓vptr(virtual table pointer)指针指出。vptr指向一个由函数指针构成的数组，称为vtbl(virtual table); 每一个带有virtual函数的class都由一个相应的vtbl。当对象调用某一virtual函数，实际上被调用的函数取决于该对象的vptr所指向的那个vtbl——编译器在其中寻找适当的函数指针。

如果class内含virtual函数，其对象的体积会增加(vptr的大小)。

因此，无端将所有class的析构函数声明为virtual和从未声明它们为virtual一样，都是错误的。许多人的心得是: 只有当class内至少含有一个virtual函数，才为它声明为virtual析构函数。

有时令class带一个pure virtual析构函数可能颇为便利。pure virtual函数导致抽象类不能被实体化。下面是一个例子:

	class AWOV{
	public:
		virtual ~AWOV() = 0;					//	pure virtual
	};

我们必须为pure virtual析构函数提供一分定义:

	AWOV::~AWOV(){ }

析构函数运作的方式是，最深层派生(most derived)的那个class其析构函数最先被调用，然后是每一个base class的析构函数被调用。编译器会在AWOV的derived classes的析构函数中创建一个对~AWOV的调用动作，所以你必须为这个函数提供一分定义，否则，连接器会报错。

如果class的设计目的不是作为base class使用，或者不是为了具备多态性(polymorphically)，那么就不该声明virtual析构函数。


### Rule 08: 别让异常逃离析构函数

C++并不禁止析构函数吐出异常，但它不鼓励你这样做。假设当前被销毁类有十个成员需要调用各种的析构函数。在析构第一个元素时，如果有异常被抛出，其他九个还是应该被销毁；但如果第二个元素在析构时也遇到抛出异常，C++会提早结束或者导致不明确行为。两个方法可以避免这一问题。

提早使用abort()函数结束程序。例如在一个数据库连接的类中:

	DBConn::~DBConn(){
		try{ db.close(); }
		catch (...) {
			std::abort();
		}
	}

强迫结束程序时一个合理选项，因为我们并不希望异常从析构函数传播出去，调用abort可以抢先将"不明确行为"置于死地。

另一个方法是吞下调用close而发生的异常:

	DBConn::~DBConn(){
		try { db.close(); }
		catch (...) {
			if(!closed){
				try { db.close(); }
				catch (...) {
					...
				}
			}
		}
	}
	
更好的做法是重新设计DBConn接口，使得用户有机会在发生错误的时候，进行处理。

请记住**析构函数绝对不要吐出异常**，如果一个被析构函数调用的函数可能抛出异常，那么析构函数应该捕捉任何异常，然后吞下它们或者结束程序。class应该提供一个普通函数对某个函数运行期抛出的异常做出反应。


### Rule 10: 令operator=返回一个reference to *this

赋值可以写成连锁形式:

	int x, y, z;
	x = y = z = 15;

这是因为赋值采用右结合律，上述连锁赋值被解析为:

	x = (y = (z = 15));

连锁赋值的实现是因为**赋值操作符返回一个reference指向操作符的左侧实参**。

	class Widget{
	public:
		Widget& operator=(const Widget& ths){
			...
			return* this;
		}
		...
	}

这个协议不仅适用于赋值操作符，也适用于所有赋值相关运算。但是它只是个协议，并无强制性。


### Rule 11: 在operator=中处理"自我赋值"

"自我赋值"发生在对象赋值给自己时:

	class Widget { ... };
	Widget w;
	...
	w = w;

当然不会有人这么写，但是有的时候有些潜在的自我赋值不是那么容易看出来:

	a[i] = a[j];				//	i == j
	*px = *py;					//	px, py point to same object

这有可能导致在停止使用资源前意外释放的陷阱。

	Widget& Widget::operator= (const Widget& rhs){
		delete pb;
		pb = new Bitmap(*rhs.pb);
		return *this;
	}

如果函数内的rhs和*tis是同一个对象，那么在delete pb时，它也同时销毁了rhs的pb。欲阻止这种错误，我们需要在operator=最前面做一个"证同测试(identity test)"达到"自我赋值"的验证目的:

	Widget& Widget::operator= (const Widget& rhs){
		if(this == &rhs) return *this;

		delete pb;
		pb = new Bitmap(*rhs.pb);
		return *this;
	}

此外通过精心安排的语句顺序也可以解决这个问题:

	Widget& Widget::operator= (const Widget& rhs){
		Bitmap* pOrig = pb;
		pb = new Bitmap(*rhs.pb);
		delete pOrig;
		return *this;
	}

还可以通过copy-and-swap的方法解决这个问题:

	class Widget{
		...
		void swap(Widget& rhs);
		...
	};

	Widget& Widget::operator= (const Widget& rhs){
		Widget temp(rhs);
		swap(temp);
		return *this;
	}

这是由于temp是by value传递产生的rhs的一个副本，然后用这个副本与*this数据进行数据交换。


### Rule 12: 复制对象时勿忘其每一个成分

考虑基类:

	class Customer{
	public:
		...
		Customer(const Customer& rhs);
		Customer& operator=(const Customer& rhs);
	private:
		std:: string name;
		Date lastTransaction;
	};

一旦发生继承，而且由我们自己写copying函数，那么我们可能会写成这样:

	class PriorityCustomer: public Customer{
	public:
		...
		PriorityCustomer(const PriorityCustomer& rhs);
		PriorityCustomer& operator=(const PriorityCustomer& rhs);
		...
	private:
		int priority;
	};

	PriorityCustomer::PriorityCustomer(const PriorityCustomer& rhs): priority(rhs.priority){
		...
	}

	PriorityCustomer& PriorityCustomer::operator=(const PriorityCustomer& rhs){
		priority = rhs.priority;
		return *this;
	}

PriorityCustomer的copying函数看起来复制了PriorityCustomer内的每一个东西，但实际上它并没有复制PriorityCustomer继承的Customer的成员变量。因此PriorityCustomer对象的Customer成分会被Customer的default构造初始化。

如果我们自行编写copying函数，那么我们需要小心的复制其base class成分。

	PriorityCustomer::PriorityCustomer(const PriorityCustomer& rhs): Customer(rhs), priority(rhs.priority){
		...
	}

	PriorityCustomer& PriorityCustomer::operator=(const PriorityCustomer& rhs){
		Customer::operator=(rhs);
		priority = rhs.priority;
		return *this;
	}


### Rule 13: 以对象管理资源

许多资源被动态分配于heap内而后被用于单一区域或函数内。它们应该在控制流离开那个区域或函数时被释放。标准程序库提供了auto_ptr指针，它是个类指针对象，它是个智能指针，其析构函数自动对其所指对象调用delete。使用auto_ptr可以避免f函数潜在的资源泄漏可能性:

	void f(){
		std::auto_ptr<int> p(new int);
		...
	}

管理对象运行析构函数确保资源被释放。不论控制流如何席开区块，一旦对象被销毁，其析构函数就会自动调用，于是资源被释放。

由于auto_ptr被销毁时会自动删除它所指之物，所以注意不可以让多个auto_ptr指向同一个对象。如果这是这样，对象会被删除超过一次，造成未定义行为。为了防止这个问题，auto_ptr有一个不寻常的性质: 若通过copy构造函数或copy assignment操作符复制它们，它们会变成null，而复制所得的指针将取得资源的唯一拥有权。

受auto_ptr管理的资源必须绝对没有一个以上的auto_ptr同时指向它，意味着auto_ptr并非管理动态分配资源的很好工具，例如，STL容器要求其元素发挥"正常的"复制行为，因此这些容器不能用auto_ptr。

auto_ptr的替代方案是"引用计数型智慧指针"(reference-counting smart pointer; RCSP)。RCSP指针能够持续追踪共有多少对象指向某资源，并在无人指向它时自动删除该资源。RCSP提供的行为类似于垃圾回收(garbage collection)，不同的是RCSP无法打破环状引用(两个其实已经没有被使用的对象彼此互指)。

TR1的tr1::shared_ptr就是个RCSP:

	void f(){
		...
			std::tr1::shared_ptr<int> p(new int);
		...
	}

这段代码看起来合使用auto_ptr的版本相同，但是shared\_ptr的复制行为正常多了。由于tr1::shared\_ptr的复制行为"一如预期"，所以它们能够被用于STL容器或其他auto_ptr不适用的语境上。

**auto\_ptr和tr1::shared\_ptr两者都在其析构函数内做delete而不是delete[]动作**。那意味着在动态分配而得得array上使用auto_ptr或tr1::shared_ptr不是个好主意。boost::scoped\_array和boost::shared\_array 类可能是个更好的选择。


### Rule 14: 在资源管理类中小心coping行为

有时你可能需要建立自己的资源管理类。

例如我们使用C API函数处理类型未Mutex得互斥器对象(mutex object)，共有lock和unlock两函数可用:

	void lock(Mutex* pm);
	void unlock(Mutex* pm);

为了确保不会忘记将一个被锁住得Mutex解锁，我们可以建立一个类来管理所，这个类由RAII守则支配，即在资源构造期获得，在析构期间释放:

	class Lock{
	public:
		explicit Lock(Mutex* pm): mutexPtr(pm);{
			lock(mutexPtr);
		}
		~Lock(){
			unlock(mutexPrt);
		}
	private:
		Mutex *mutexPtr;
	};

用户对Lock的用法也符合RAII方式:

	Mutex m;
	...
	{
		Lock ml(&m);
		...
	}

如果Lock对象被复制，RAII对象由两种可能选择。

	Lock ml1(&m);
	Lock ml2(ml1);

+ 禁止复制。例如将copying操作符声明未private。
+ 对底层资源使用"引用计数法"。例如tr1::shared_ptr。

tr1::shared\_ptr的缺省行为是"当引用次数为0时删除其所指物"，这不是我们想要的，当我们使用一个Mutex，我们希望做的是解除锁定而非删除。

幸运的是tr1::shared\_ptr允许指定一个删除器(deleter)，那是一个函数或者函数对象，当引用次数为0时便被调用(auto\_ptr没有这一机制，它总是删除其指针)。删除器对tr1::shared\_ptr构造函数而言是可有可无的第二参数:

	class Lock{
	public:
		explicit Lock(Mutex* pm): mutexPtr(pm, unlock){
			lock(mutexPtr.get());
		}
	privaet:
		std::tr1::shared_ptr<Mutex> mutexPtr;
	};

本例中Lock不再需要声明析构函数，因为class析构函数会调用其non-static成员变量(mutexPrt)的析构函数。而mutexPtr的析构函数会在互斥器的引用为0时自动调用tr1::shared_ptr的删除器(unlock)。


### Rule 15: 在资源管理类中提供对原始资源的访问

使用资源管理类在传递产生的时候，例如我们需要的是int *指针，但是当前的却是类型为tr1::shared_ptr<int>的对象。所以我们需要将RAII class对象转换为其所内含之原始资源。

tr1::shared_ptr和auto_ptr都提供一个get成员函数，用来执行显式转换，也就是会返回智能指针内部的原始指针(的复件):

几乎所有指针(包括tr1::shared_ptr和auto_ptr)都重载了指针取值(pointer dereferencing)操作符(operator->和operator*)，它们允许隐式转换至底部原始指针:

	class Investment{
	public:
		bool isTaxFree() const;
		...
	};

	Invectment* createInvestment();

	std::tr1::shared_ptr<Investment> pi1(createInvestment());
	bool taxable1 = !(pi1->isTaxFree());
	...

	std::auto_ptr<Investment> pi2(createInvestment());
	bool taxable2 = !((*pi2).isTaxFree());
	...


### Rule 16: 成对使用new和delete时要采用相同形式

很多编译器是这样实现new和delete的:

	单一对象	[Object]
	对象数组	[n][Object][Object][Object][...

当使用delete，唯一能够让delete知道内存中是否存在一个"数组大小记录"的方法就是: 由你来告诉它。如果你使用delete时加上中括号，delete便认定指针指向一个数组，否则就是指向单一对象。

	std::string* stringPtr1 = new std::string;
	std::string* stringPtr2 = new std::string[100];
	...
	delete stringPtr1;
	delete []stringPtr2;

如果使用new时使用[]，而delete时没有使用[]，或者当new时没有使用[]，而delete时使用了[]，那都会导致未由定义的行为。

对于typedef定义类型而言:

	typedef std::string AddressLines[4];

由于AddressLines是个数组，如果这样使用new:

	std::string* pal = new AddressLines;

这时就必须匹配"数组形式"的delete:

	delete [] pal;

为了避免此类错误，最好不要难过对数组做typedef动作。我们可以使用STL的vector等代替。


### Rule 17: 以独立语句将newed对象置入智能指针

	int priority();
	void processWidget(std::tr1::shared_ptr<Widget> (new Widget), priority());


与

	std::tr1::shared_ptr<Widget> pw(new Widget);
	processWidget(pw, proity());

这两个版本，前者有机会造成内存泄露。原因是前者实际上执行的是三个部分：

+ 调用priority
+ 执行"new Widget"
+ 调用tr1::shared_ptr构造函数

C编译器如何执行这些语句是不定的，唯一确定的是"new Widget"会在shared_ptr构造函数前执行。如果在执行了priority后"new Widget"发生了异常，将导致内存泄露。解决的方法是将语句如版本二那样，分开写。


### Rule 21: 必须返回对象时，别妄想返回其reference

当我们知道pass-by-reference相对于pass-by-value的好处后，我们可能会想着一心一意使用pass-by-reference。但试试上在返回值的问题上，我们有时必须使用pass-by-value。

函数内的返回值如果是在stack中生成的，那么在退出函数时就会销毁，所以这个时候使用pass-by-reference没有用。如果你是new在heap中生成:

	const Rational& operator* (const Rational& lhs, const Rational& rhs){
		Rational * result = new Rational(lhs.n*rhs.n, lhs.d*rhs.d);
		return *result;
	}

那么你需要注意返回后，哪一个对象负责delete操作。而且在某些情况下，它会没有方法很好避免:

	Rational w, x, y, z;
	w = x * y * z;

这个例子中，我们没有办法delete掉中间产生的值，这会导致资源泄露。

为了避免这种情况，我们最好的方式是返回pass-by-value。当然我们需要承受返回值的构造和析构函数成本，但这远比资源泄露造成的后果小得多。

请记住，绝对不要返回pointer或reference指向一个local stack对象或heap-allocated对象，或返回pointer或reference指向一个有可能需要拥有的local static对象。


### Rule 22: 将成员变量声明为private

我们之所以需要将成员变量成为为private最重要的原因是为了封装。假设你写的类被其他人引用了，而且直接使用了你的类的成员变量，当你需要更改内部逻辑时，有可能会导致引用你的类的程序崩溃。如果我们将成员变量声明为private，接口并没有改变，就不会造成这种影响。

成员变量声明为protected的话，使用你的类的程序可能没有问题，但是由你的类派生出来的类则有可能崩溃，所以最好的方式是，将成员变量声明为private。


### Rule 24: 若所有参数皆需类型转换，请为此采用non-member函数

假设一个有理数类如下

	class Rational{
	public:
		Rational(int numerator = 0, int denominator = 1);
		int numerator() const;
		int denominator() const;

		const Rational operator* (cosnt Rational& rhs) cosnt;
	};

这个设计能够让你将两个有理数以最轻松的方式相乘:

	Rational oneEighth(1, 8);					//	ok
	Rational oneHalf(1, 2);						//	ok
	Rational result = oneHalf * oneEighth;		//	ok
	result = result * oneEight;					//	ok

但是在混合算数中却会发生:

	result = oneHalf * 2;						//	ok
	result = 2 * oneHalf;						//	error

上面的式子相当于:

	result = oneHalf.operator*(2);				//	ok
	result = 2.operator*(oneHalf);				//	error

由于整数2并没有相应的class，所以没有operator * 成员函数。编译器也会尝试寻找可以被以下这般调用的non-member operator * (也就是在命名空间内或在global作用域内):

	result = operator* (2, oneHalf);			//	error

因为本例中没有这样一个函数，所以查找失败。

需要注意的是，`result = oneHalf.operator*(2);`之所以成功是因为发生了隐式类型转换。编译器知道你正在传递一个int，而函数需要的是Rational；但它也知道只要调用Rational构造函数并赋予你所提供的int，就可以变出一个适当的Rational来。于是它的调用在编译器眼中相当于:

	const Rational temp(2);
	result = oneHalf * temp;

因为涉及的是non-explicit构造函数，所以编译器才这么做。如果Rational构造函数是explicit，那么以下语句没有一个可通过编译:

	result = oneHalf * 2;						//	error
	result = 2 * oneHalf;						//	error

这里的结论是，**只有当参数被列于参数列(parameter list)中，这个参数才是隐式类型转换的合格参与者。**

为了完成混合算数，我们可以让operator*成为一个non-member函数:

	class Rational {
		...
	};

	const Rational operator* (const Rational& lhs, const Rational& rhs){
		return Rational(lhs.numerator() * rhs.numerator(),
						lhs.denominator() * rhs.denominator());
	}


### Rule 25: 考虑写一个不抛出异常的swap函数

swap是STL的一部分，其典型实现完全如你预期:

	namespace std{
		template<typename T>
		void swap(T& a, T& b){
			T temp(a);
			a = b;
			b = temp;
		}
	}

只要类型T支持copying(从过copy构造函数和copy assignment操作符完成)，缺省的swap代码就会帮你置换类型为T的对象。

但是这个实现版本十分平淡。有一种设计方法叫做"pimpl"(pointer to implementation)。以指针只想一个对象，内含真正的数据。

	class Widget{
	public:
		Widget(const Widget& rhs);
		Widget& operator=(cosnt Widget& rhs){
			...
			*pImpl = *(rhs.pImpl);
			...
		}
	private:
		WidgetImpl* pImpl;
	};

如果我们需要置换两个Widget对象，我们只需要置换其pImpl指针，但缺省的swap不知道，所以我们可以声明一个public的成员函数做这个真正的置换工作，然后将std::swap特化，令它调用该成员函数:

	class Widget{
	public:
		...
		void swap(Widget& other){
			using std::swap;
			swap(pImpl, other.pImpl);
		}
	};

	namespace std{
		template<>
		void swap<Widget>(Widget& a, Widget& b){
			a.swap(b);
		}
	}

这种做法不仅能通过编译，还和STL容器有一致性，因为STL容器也都提供有public swap成员函数和std::swap特花版本(用以调用前者)。

除此之外，我们还应该提供一个non-member swap用来调用member swap。(如果我们定义的Widget是个泛型函数)。


### Rule 27: 尽量少做转型动作

C风格的转型动作

	(T) expression						//	expression -> T

函数风格的转型动作

	T(expression)						//	expression -> T

这两种形式并没有差别，我们称这两种形式为"旧式转型"。

C++还提供了四种新式转型

	const_cast<T>(expression)
	dynamic_cast<T>(expression)
	reinterpret_cast<T>(expression)
	static_cast<T>(expression)

+ const_cast是将对象的常量性转除。它是唯一有此能力的C++-style转型操作符。
+ dynamic_cast是用来决定某对象是否归属继承体系中的某个类型。
+ reinterpret_cast的实际动作取决于编译器，也就是它不可移植。它意图执行低级转型。
+ static_cast用来强迫隐式转换，例如将non-const对象转为const对象，或将int对象转为double等等。


### Rule 30: 透彻了解inlining的里里外外

inline函数能够免除函数调用成本，但是会增加目标码(object code)的大小，这是因为inline函数都是以函数本体替换每一次调用。

明确声明inline函数的做法是在其定义式前加上关键字inline。例如标准的max template(来自< algorithm>)往往是这样实现出来的:

	template<typename T>
	inline const T& std::max(const T& a, const T& b){
		return a < b ? b : a;
	}

Template的具体实现和inlining无关。如果你认为所有根据此template具现出来的函数都应该inlined，那么就将此template声明为inline；否么就应该避免声明。

所有对virtual函数的调用都会使得inlining落空，因为virtual意味着等待到运行期才确定调用哪个函数，而inline意味执行前，先嗲用动作替换被调用函数得本体。

有时编译器有意愿inlining某个函数，还是可能为该函数产生一个函数本体。例如，程序需要inline函数的地址，编译器需要为此函数生成一个outlined函数本体，否则编译器没有能力提出一个指针只想并不存在的函数。与此并提的是，编译器通常不对"透过函数指针而进行的调用"实施inlining:

	inline void f(){...}
	void (* pf)() = f;
	...
	f();								//	inline
	pf();								//	not inline

程序设计者必须评估使用inline的冲击，因为inline函数无法随着程序库的升级而升级，如果一个inline函数的本体被改变了，所有用到这个函数的程序都必须重新编译。如果这个函数是个non-inline函数，那么所有用到这个函数的程序只需要重新链接就好了，这比重新编译的负担更少。


### Rule 33: 避免遮掩继承而来的名称

	class Base{
	private:
		int x;
	public:
		virtual void mf1() = 0;
		virtual void mf1(int);
		virtual void mf2();
		void mf3();
		void mf3(double);
		...
	};

	class Derived: public Base{
	public:
		virtual void mf1();
		void mf3();
		void mf4();
		...
	};

base class内所有名为mf1和mf3的函数都被derived class内的mf1和mf3函数掩盖掉了。从名称查找观点看，Base::mf1和Base::mf3不再被Derived继承。

	Derived d;
	int x;
	...
	d.mf1();							//	ok, call Derived::mf1
	d.mf1(x);							//	error, Derived::mf1 hide Base::mf1
	d.mf2();							//	ok, call Base::mf2
	d.mf3();							//	ok, call Derived::mf3
	d.mf3(x);							//	error, Derived::mf3 hind Base::mf3

我们可以使用using声明式达成目的:

	class Base{
	private:
		int x;
	public:
		virtual void mf1() = 0;
		virtual void mf1(int);
		virtual void mf2();
		void mf3();
		void mf3(double);
		...
	};

	class Derived: public Base{
	public:
		using Base::mf1;
		using Base::mf3;
		virtual void mf1();
		void mf3();
		void mf4();
		...
	};

	Derived d;
	int x;
	...
	d.mf1();							//	ok, call Derived::mf1
	d.mf1(x);							//	ok, call Base::mf1
	d.mf2();							//	ok, call Base::mf2
	d.mf3();							//	ok, call Derived::mf3
	d.mf3(x);							//	ok, call Base::mf3

有时我们可能不要遮掩基类的某些函数，那么我们可以使用简单的转交函数(forwarding function):

	class Base{
	public:
		virtual void mf1() = 0;
		virtual void mf1(int);
		...
	};

	class Derived: private Base{
	public:
		virtual void mf1(){				//	forwarding function
			Base::mf1();				//	implicit inline
		}
		...
	};

	Derived d;
	int x;
	d.mf1();							//	ok, call Derived::mf1
	d.mf1(x);							//	error, Base::mf1() is hidden


### Rule 42: 了解typename的双重意义

以下template声明式中，class和typename没有不同。

	template<class T> class Widget;
	template<typename T> class Widget;

对于代码

	template<typename C>
	void print2nd(const C& container){
		if(container.size() >= 2){
			C::const_iterator iter(container.begin());
			...
		}
	}

iter声明式只有在C::const_iterator是个类型的时候才合理，但我们并没有告诉C++说它是，于是C++假设它不是(因为C::const_iterator有可能只是C中的一个static变量名称)。为了告诉编译器C::const_iterator是一个类型，我们需要在紧邻它之前放置关键字typename:

	template<typename C>
	void print2nd(const C& container){
		if(container.size() >= 2){
			typename C::const_iterator iter(container.begin());
			...
		}
	}

typename只被用来言明嵌套从属类型名称，其他名称不应该有它的存在。例如:

	template<typename C>
	void f(const C& container),				//	no "typename"
		typename C::iterator iter);			//	has	"typename"

"typename必须作为嵌套从属类型名称的前缀词"的例外是，typename不可以出现在base classes list内的嵌套从属类型名称之前，也不可在member initialization(成员初值列)中作为base class修饰符:

	template<typename T>
	class Derived: public Base<T>::Nested{
	public:
		explicit Derived(int x): Base<T>::Nested(x){
			typename Base<T>::Nested temp;
			...
		}
		...
	};


### Rule 49: 了解new-handler的行为

当使用new抛出异常时，它首先会调用一个客户指定的错误处理函数new\_handler。为了指定这个函数，客户必须调用set\_new\_handler，那是声明于< new>的一个标准程序库函数:

	namespace std{
		typedef void (*new_handler)();
		new_handler set_new_handler(new_handler p) throw();
	}

set\_new\_handler的参数是一个指针，只想new无法分配内存时该调用的函数。其返回值也是一个指针，指向set\_new\_handler被调用前正在执行(马上就要被替换)的哪个new\_handler函数。

我们可以这样使用set\_new\_handler:

	void outOfMem(){
		std::cerr << "Unable to satisfy request for memory\n";
		std::abort();
	}

	int main(){
		std::set_new_handler(outOfMem);
		int* pBigDataArray = new int[100000000L];
		...
	}

当new无法分配足够空间，outOfMem就会被调用。如果outOfMem中要求分配内存，那么new会一直调用new\_handler，直到找到足够内存。
