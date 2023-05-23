# Octy team iOS developer initial guideline

Greetings stranger! If you read this, that means you are lucky to join our team ^_^. Before you start coding, you have to familiarize yourself with some internal rules and recommendations.

# What basic tools do we use for developing?
* [Xcode](https://developer.apple.com/xcode/) - IDE
* [Homebrew](https://brew.sh/index_ru) - MacOS package manager
* [Swiftlint](https://github.com/realm/SwiftLint) - Tool to enforce Swift style and conventions
* [Fork](https://git-fork.com/) - Free git UI Client



# What technology stack do we usually use?
* MVVM + Coordinator.
* [UIKit](https://developer.apple.com/documentation/uikit) 
* [SwiftUI](https://developer.apple.com/xcode/swiftui/)
* Alamofire for REST and Appollo for GraphQL.
* CoreData
* Snapkit
* Firebase services
* Swiftlint
* R Swift
* Floating pannel

# Swift codestyle
Naming
----------------
Именование переменных, названий классов и методов - одно из ключевых правил при написании любого кода, в том числе и на Swift. Когда люди в команде руководствуются общими правилами именования, можно сказать, они говорят на одном языке, который легче понимать во время чтения кода, а также во время общения друг с другом.

При именовании можно руководствоваться [Swift API Design Guidelines](https://www.swift.org/documentation/api-design-guidelines/).

Несколько важных правил, которые стоит держать в голове:

* **Читаемость и ясность являются главной целью**. Код мы пишем гораздо меньше, чем его читаем и используем в других местах. Правильно указав название метода или переменной один раз, потом проще понять его назначение другому человеку;
* **Ясность всегда имеет приоритет перед краткостью**. Несмотря на то, что в Swift можно написать действительно короткий код, это не должно быть целью. Название переменных, методов и классов, всегда должно отражать их назначение, даже если названия от этого становятся длиннее.

Class prefixes
----------------
Мы не используем префиксы классов.

Swift автоматически добавляет префиксы к названиям классов (по названию модуля), поэтому ситуации, когда два класса с одинаковыми названиями вступают в конфликт исключаются. Если же возникает конфликтная ситуация, можно использовать название модуля, чтобы явно указать на используемый класс

```
import SomeModule

let service: SomeModule.Service
```

Use of english
----------------
Названия переменных, классов, методов и тд. используют правила написания US English. Таким образом, наш код будет лучше соответствовать Apple API. 

```
// Use US "localizer" instead of British "localiser"
class Localizer {
	// Use US "color" instead of British "colour" and "gray" instead of "grey"
	var higlightedColor = UIColor.gray
}
```

Naming methods
----------------
* Если метод выполняет действие, его название должно отражать это действие плюс предмет этого действия:

	```
	// загрузить (= действие), детали пользователя (= предмет действия)
	func loadUserDetails()
 
	// начать (= действие) обновление в фоне (= предмет действия)
	func startBackgroundUpdates()
 
	// создать (= действие) view (= предмет действия)
	func makeView(at columnIndex: Int, for item: OutlineItem) -> View
	```

* Если метод чётко отражает действие на объекте, предмет действия может быть опущен:
	
	```
	let service = UserPositionShareService()
 
	// В данном случае запускается сервис, который сам является предметом действия
	service.start()
 
	// Однако, если в том же сервисе есть метод, который обновляет настройки с сервера в фоне:
	service.reloadServerSettings() // Correct
	service.reload() // Wrong (Из названия неочевидно, что будет обновляться)
	```

* Глагол get опускается для методов, которые возвращают результат значения:

	```
	class Container {
 	   func value() -> Int // Correct
 	   func getValue() -> Int // Wrong
	}

	```

* Если метод, возвращающий значение, выполняет тяжёлое действие по расчёту этого значения (например, синхронный web-запрос), это должно быть явно отражено в названии при помощи добавления глагола:

	```
	class Container {
   		 func obtainValue() -> Int {
    	    let request: NSURLRequest = // create request
    	    return request.loadAndBlockCurrentThread()
   		 }
	}
	```
* Если метод выполняет действие только при необходимости, к названию метода добавляется суффикс IfNeeded:


	```
	let storage = Storage()
	 
	// Saves data only when there is something to be saved
	storage.saveIfNeeded()
 
	let hintView = HintView()
 
	// Shows a hint only when a hint is not showed yet
	hintView.showIfNeeded() 
	```

Naming properties
-----------------
*   Если boolean свойство отражает **двоичное состояние объекта**, оно должно начинаться с вспомогательных глаголов **is** (для единственного числа) и **are** (для множественного числа):

	```
	class Document {
		var isOpened: Bool // Correct
		var opened: Bool // Wrong
	}
      
	class Settings {
	   var areAnimationsEnabled: Bool // Correct
	   var animationsEnabled: Bool // Wrong
	}
	```
    
*   Если boolean свойство отражает **возможность выполнения действия**, оно должно начинаться с модального глагола **can**:

	```
	class UserPositionSharingService {
		// Свойство указывает может ли сервис начать обновления в фоне в принципе.
		// Например, если пользователь разрешил отправлять свою позицию, плюс сервисы геопозиции включены на устройстве
		var canStartUpdates: Bool {
			return isPositionSharingAllowedByUser && areLocationServicesEnabled
		}
	}
	```

Naming delegates
----------------

*   Название делегата должно начинаться с названия делегирующего объекта и завершаться ключевым словом **Delegate**
 
	```   
	protocol ViewDelegate {
	}
 	   
	class View {
		weak var delegate: ViewDelegate?
	}
	```  
    
*   Если делегат создаётся под переиспользуемый (универсальный) объект, в качестве первого параметра методов должна выступать ссылка на вызывающий объект. Под универсальными объектами понимаются объекты, которые могут быть использованы в любом месте приложения и созданы специально для переиспользования. Например, к универсальным объектам можно отнести объект класса **AlertView**, либо **HttpRequest.** В API Apple можно встретить множество универсальных объектов, таких как UITableView, UITextField и другие.
    
	```
	protocol AlertViewDelegate {
		// В качестве первого параметра явно указывается ссылка на вызвавший событие объект alertView
		func alertViewDidConfirm(_ alertView: AlertView)
	}
	```   
    
*   Если делегат создаётся под объект, который по своей сути существует в единичном экземпляре, указание ссылки на объект в методах не требуется. Указание, однако, допускается в определённых случаях, когда этого требует конкретная задача. К объектам в единичном экземпляре можно отнести, например, **ViewController** либо **View,** использование которой ограничивается только единственным экземпляром на экране.

	```
	// Методы делегата не используют ссылку на объект, так как view является уникальной и используется всегда в единичном экземпляре на экране
	protocol UserProfileLogoutViewDelegate {
		func viewDidClick()
		func viewDidSelectInfoDetails()
	}
	```
      
    
*   Если делегат подразумевает возврат результат успешного выполнения либо ошибку, каждый параметр рекомендуется выносить в отдельный метод.
    
    ```
    // Correct
    protocol RequestDelegate {
    	func request(_ request: Request, didFinishWithData data: Data)
    	func request(_ request: Request, didFinishWithError error: Error)
    }
    
    // Wrong
    protocol RequestDelegate {
    	func request(_ request: Request, didFinishWithData data: Data?, error: Error?)
    }
    ```
    
*   Если делегатный метод не возвращает результат в виде объекта, а только указывает на факт завершения, error параметр сливается в один метод, как опциональный

```
	protocol RequestDelegate {
		// Метод указывает на завершение запроса в принципе, но не возвращает результат значения. Error добавляется как optional
		func request(_ request: Request, didFinishWithError error: Error?)
	}
```
    
Use of brackets
---------------
В общем случае "{" не переносится на новую строку. Примеры:

*   Во время условных операций if, else:

	```
	if let user = users.first {
		doStuff()
	} else {
		doOtherStuff()
	}
	```
    
*   Во время циклов for, while:

    ```
    for item in items {
    	item.doStuff()
    }
    ```
    
*   При определении классов, протоколов:
    
    ```
    protocol ServiceDelegate {
    	func service(_ service: Service, didFinishLoading error: Error?)
    }
    ```
    
*   При выделении MARK
    
    ```
    class ViewPresenter {
    	// MARK: - Private
    	private var isShown = false
    
    	// MARK: - Public
    	func show() {
    		isShown = true
    	}
    }
    ``` 
    
*   При использовании switch

    ```
    switch self {
    case .case1: 
    	return true
    case .case2: 
    	return false
    }
    ```

Object definitions
------------------
При объявлении объектов (классы, протоколы) между названием и началом объявления нет переноса строки

```
class Formatter {
	// MARK: - Private
	var definition
}
```
Guard statements
----------------
При использовании **guard-**выражений, руководствуемся общими правилами переноса строк

```
func loadData() throws {
	guard !isLoaded else { 
		throw Exception.alreadyLoaded 
	}
}
```
Однако, также допускается написание без переноса строки, если результирующее выражение не слишком длинное

```
func createViewIfNeeded() {
	guard view == nil else { return }
}
```

Visibility modifiers
----------------
Модификаторы области видимости внутри классов имеют приоритет в написании (пишутся в первую очередь):

```
class Priority {
	private let constant = 10.0
	private(set) var isReadonly = true
	fileprivate final lazy var alwaysTrue = true

	private final func method() {
	}
}
```

Исключением являются особые модификаторы, начинающиеся с @

```
class MixedCodeView {
	@objc let constant = 10.0
	@IBOutlet private(set) var titleLabel: UILabel!
	@IBAction private func cancelButtonClicked(sender: UIButton) {
	}
}
```

Readonly
--------
При необходимости сделать одно из свойств объекта readonly, используется модификатор **private(set)**

```
class Weather {
	private(set) var temperature: Temperature
}
```

Однако, если свойство задаётся при инициализации объекта и не планируется изменяться в жизненном цикле, используется **let**

```
class Request {
	let timeout: TimeInterval

	init(timeout: TimeInterval) {
		self.timeout = timeout
	}
}
```

Public, Open, Internal
----------------------
Внутри кода Sherlock'a, модификаторы **open, public, internal** не используются (исключением являются отдельные библиотеки)

Fileprivate
-----------
Модификатор **fileprivate** может использоваться только в том случае, когда переменная или метод должны быть использованы одним из расширений объекта внутри файла, либо связанным fileprivate-объектом. Однако, нужно чётко разделять области видимости **private** и **fileprivate.**

Final
-----
Модификатор final **должен быть** использован на объектах, которые не планируется наследовать (т.е. на большинстве объектов)

```
final class Service {
}
```

Class vs struct
----------------
На проектах с Objective-C предпочтение должно отдаваться использованию классов вместо структур. В первую очередь это связано с поддержкой Objective-C кода, где Swift структуры не могут быть представлены.

Использование структур допускается только в случаях, когда заранее известно, что видимость объекта ограничивается Swift кодом, а также задачей требуется добиться оптимизации за счёт выделения памяти не в куче, а на стеке. Такое использование должно быть явно аргументировано задачей и исходить из требований, а не желанием разработчика сделать преждевременную оптимизацию (premature optimization).

На проектах, где используется чистый Swift структуры должны быть использованы для представления данных, а классы для создания объектов-сервисов, view, controller'ов и тд.

Types with shorthand names
----------------
Массивы, словари и необязательные типы пишутся с использованием короткой формы.

Полная форма должна использоваться только в тех случаях, когда этого требует компилятор, например, при приведении типов.

```
let array: \[String\] // Correct
let array: Array<String> // Wrong

let dictionary: \[String: String\] // Correct
let dictionary: Dictionary<String, String> // Wrong

let optional: Int? // Correct
let optional: Optional<Int> // Wrong

// Проверка на тип
if array is \[String\] {
}
```

Type inference
----------------
Если при объявлении переменной тип объекта может быть автоматически определён из контекста, указывать тип не нужно

```
let locationManager = LocationManager.create() // Correct
let locationManager: LocationManager = LocationManager.create() // Wrong
```

Explicit init usage
----------------
Явное указание **init** метода при инициализации объекта не используется

```
let service = Service() // Correct
let service = Service.init() // Wrong

let locationService = LocationService(configuration: Configuration) // Correct
let locationService = LocationService.init(configuration: Configuration) // Wrong
```

Если объект передаётся в параметр метода, использование .init также не допускается напрямую:

```
func doSomething(object: ObjectType) {
}

// Correct
doSomething(object: ObjectType())
doSomething(object: ObjectType.makeInstance())

// Wrong
doSomething(object: .init())
```

Code structure
----------------
В общем случае каждый класс выносится в отдельный .swift файл.


Delegate declaration
----------------
Объявление делегата прописывается в том же файле, где находится связанный объект, но всегда выше этого объекта.

```
protocol ViewDelegate {
}

class View {
}
```

Delegate conformance
----------------
При адаптации объекта к методам делегата создаётся отдельное расширение ниже объявления самого объекта. Адаптация каждого делегата выносится в отдельное расширение и отмечается дополнительным MARK с названием адаптируемого delegate.

```
class View {
}

// MARK: - HttpRequestDelegate
extension View: HttpRequestDelegate {
}

// MARK: - DataProviderDelegate
extension View: DataProviderDelegate {
}
```

Internal types
----------------
В одном файле могут находится несколько классов, если классы являются **небольшими** вспомогательными для основного класса (**fileprivate**), либо являются внутренними подклассами. Внутренние подклассы должны быть выделены в отдельные расширения.

```
class View {
}

// MARK: - NestedTypes
fileprivate extension View {
	enum State {
		case on
		case off
	}

	class Item {
	}
}
```

Computed properties
----------------
При использовании вычисляемых свойств, модификатор **get** опускается в пользу короткой записи

```
class Circle {
	// Correct
	var diameter: Double {
		return radius \* 2.0
	}

	// Wrong
	var diameter: Double {
		get {
			return radius \* 2.0
		}
	}
}
```

Use of self
----------------
Нужно избегать использования ключевого слова **self.** Исключением являются блоки кода, в которых это явно необходимо (например, closure).

Use of return
----------------
