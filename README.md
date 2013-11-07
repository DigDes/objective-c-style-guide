# DD Objective-C Style Guide

Это руководство по стилю кода, основанное на руководстве для разработчиков в The New York Times(https://github.com/NYTimes/objective-c-style-guide/contributors)

## Введение

Ниже представлены документы Apple по этой же теме:

* [The Objective-C Programming Language](http://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html)
* [Cocoa Fundamentals Guide](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CocoaFundamentals/Introduction/Introduction.html)
* [Coding Guidelines for Cocoa](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html)
* [iOS App Programming Guide](http://developer.apple.com/library/ios/#documentation/iphone/conceptual/iphoneosprogrammingguide/Introduction/Introduction.html)

## Содержимое

* [Использование точек](#dot-notation-syntax)
* [Отсутупы](#spacing)
* [Условия](#conditionals)
  * [Тернарный оператор](#ternary-operator)
* [Передача ошибок](#error-handling)
* [Методы](#Методы)
* [Перменные](#Переменные)
* [Правила именования](#naming)
  * [Подчеркивания](#underscores)
* [Комментарии](#comments)
* [Инициализация и уничтожение](#init-and-dealloc)
* [Литералы](#literals)
* [CGRect функции](#cgrect-functions)
* [Константы](#constants)
* [Тип перечисление](#enumerated-types)
* [Private свойства](#private-properties)
* [Именование шрафических ресурсов](#image-naming)
* [Тип boolean](#booleans)
* [Синглтон](#singletons)
* [Xcode проект](#xcode-project)

## Использование точек

Используйте точки только для доступа к свойствам. Во всех остальных случаях используйте квадратные скобки.

**Хорошо:**
```objc
view.backgroundColor = [UIColor orangeColor];
[UIApplication sharedApplication].delegate;
```

**Плохо:**
```objc
[view setBackgroundColor:[UIColor orangeColor]];
UIApplication.sharedApplication.delegate;
```

## Отступы

* Для отсутпа используйте 4 пробела. Никогда не используйте табы. Хотя так и настроен Xcode.
* Фигурные скобки в методах и других конструкциях(например `if`/`else`/`switch`/`while`) всегда всегда долдны открываться в той же строке, что и условие. Но закрываться должны на другой строке.

**Хорошо:**
```objc
if (user.isHappy) {
//Do something
}
else {
//Do something else
}
```
* Всегда стоит добавлять пустую строку между методами- это придаст больше четкости коду. Отступы внутри методов всегда должны отделять функциональность, которую можно было бы вынести в отдельные методы.
* `@synthesize` и `@dynamic` должны начинаться но новой строке.

## Условия

Тело условия необходимо писать в фигурных скобках даже в том случае, если это можно и не делать(когда оно может быть написано в одну строку) [errors](https://github.com/NYTimes/objective-c-style-guide/issues/26#issuecomment-22074256). Это исключит ошибки возникающие, когда следующая строка не попадает под условие, хотя должна(так думает разработчик). В другом случае, ошибка может возникнуть когда строка внутри условного оператора закоментирована и следующая строка становится телом условного оператора . К тому же, данный стиль лучше согласуется с оформлением других блоков и становится лучше читаемым.

**Хорошо:**
```objc
if (!error) {
    return success;
}
```

**Плохо:**
```objc
if (!error)
    return success;
```

или

```objc
if (!error) return success;
```

### Тернарный оператор

Тернарный оператор, ? , используйте только, когда это улучшает читаемость и понятность кода. В том случае, когда используется одно условие. Для нескольких условий более предпочтительно использовать оператор "if" или переменные.

**Хорошо:**
```objc
result = a > b ? x : y;
```

**Плохо:**
```objc
result = a > b ? x = c > d ? c : d : y;
```

## Передача ошибок

Когда метод возвращает ошибку по ссылке, проверяйте возвращаемое значение, а не ошибку.

**Хорошо:**
```objc
NSError *error;
if (![self trySomethingWithError:&error]) {
    // Handle Error
}
```

**Плохо:**
```objc
NSError *error;
[self trySomethingWithError:&error];
if (error) {
    // Handle Error
}
```
Некоторые методы из API Apple возвращают ошибку(не NULL) даже в случае успешного выполнения, по этой причине не анализируйте оишбочное значение


## Методы

В определениях методов пробелы должны стоять после типа методов (символы - и +). Так же пробелами нужно разделять параметры методов.

**Например**:
```objc
- (void)setExampleText:(NSString *)text image:(UIImage *)image;
```
## Переменные

Переменные нужно называть максимально информативно. Имена переменных, состоящие из одной буквы допускается только для счётчика в цикле `for()`.

Звёздочку указателя нужно ставить впритык к имени переменной, например `NSString *text` а не  `NSString* text` или `NSString * text`, исключение составляют [константы](#Константы).

Для private и protected переменных (которые используются исключительно внутри класса, и/или внутри подклассов) необходимо использовать instance-переменные, а не свойства. Не забывайте указывать область видимости переменной, так как по-умолчанию область видимости instance-переменных - protected.

Свойства можно использовать только для public-переменных, доступ к которым нужно предоставить снаружи класса. Исключения составляют свойства, для которых необходимо переопределить сеттер и/или геттер. Их нужно хранить в `.m` файле, в безымянной private-категории.

**Например:**

```objc
@interface DDSection : NSObject {
 @private
    NSString *_headline;
}

@property (strong, nonatomic) NSString *publicHeadline;
```

**А не:**
```objc
@interface DDSection: NSObject

@property (strong, nonatomic) NSString *publicHeadline;
@property (strong, nonatomic) NSString *headline;

@end
```


## Именование

ПО возможности старайтесь соблюдать соглашение Apple о именовании[memory management rules](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/MemoryMgmt/Articles/MemoryMgmt.html) ([NARC](http://stackoverflow.com/a/2865194/340508)).

Используйте подробные имена перменных.

**Хорошо:**

```objc
UIButton *settingsButton;
```

**Плохо:**

```objc
UIButton *setBut;
```
Трехбуквенные префиксы (например 'NYT') всегда используйте для имен классов и констант, однако ими можно пренебречь для сущностей Core Data 
Константы должны именоваться ВерблюжьемРегистре http://ru.wikipedia.org/wiki/CamelCase, где все слова начинаются с большой буквы и начинаться с префикса класса, в котором описаны.


**Хорошо:**

```objc
static const NSTimeInterval NYTArticleViewControllerNavigationFadeAnimationDuration = 0.3;
```

**Плохо:**

```objc
static const NSTimeInterval fadetime = 1.7;
```

Свойства должны именоваться тоже в ВерблюжьемРегистре, только первое слово в нижнем регистре.
**Если Xcode  автоматически синтезировал перменные, оставьте их** В противном случае, согласно выше сказанном, имена переменных для этих свойств должны начинаться с подчеркивания и первое слово должно быть в нижнем регистре. В таком формате Xcode обеспечивает связь между свойствами и переменными. 

**Хорошо:**

```objc
@synthesize descriptiveVariableName = _descriptiveVariableName;
```

**Плохо:**

```objc
id varnm;
```

### Подчеркивания

Когда используете свойства, переменные класса всегда должны быть доступны и изменяемы через "self." Это значит что все свойства должны визуально отличаться, когда к ним обращаются через "self.". Локальные переменные не должны содержать с подчеркивания.


## Комментарии


Когдав вам необходимо, используйте комментарий, который должен объяснять почему часть кода делает что-то. Любые комментарии, которые используются, должны быть сохранены или удалены.
Необходимо избегать блоки комментариев, код должен быть самодокументирован(и не требовать излишних комментариев), только если требуется -  несколько строчек объясняющих. Это не распространяется на комментарии, которые используются для генерации документации.

## Инициализация и удаление

Удаление: `dealloc` методы должны помещаться вверху реализации, сразу после  `@synthesize` и `@dynamic` . Инициализация: vtnjl `init` должен быть расположен сразу после `dealloc` методов в любых классах.

Структура методов `init` должны быть такой:

```objc
- (instancetype)init {
    self = [super init]; // or call the designated initalizer
    if (self) {
        // Custom initialization
    }

    return self;
}
```

## Literals

`NSString`, `NSDictionary`, `NSArray`, and `NSNumber` literals should be used whenever creating immutable instances of those objects. Pay special care that `nil` values not be passed into `NSArray` and `NSDictionary` literals, as this will cause a crash.

**For example:**

```objc
NSArray *names = @[@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul"];
NSDictionary *productManagers = @{@"iPhone" : @"Kate", @"iPad" : @"Kamal", @"Mobile Web" : @"Bill"};
NSNumber *shouldUseLiterals = @YES;
NSNumber *buildingZIPCode = @10018;
```

**Not:**

```objc
NSArray *names = [NSArray arrayWithObjects:@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul", nil];
NSDictionary *productManagers = [NSDictionary dictionaryWithObjectsAndKeys: @"Kate", @"iPhone", @"Kamal", @"iPad", @"Bill", @"Mobile Web", nil];
NSNumber *shouldUseLiterals = [NSNumber numberWithBool:YES];
NSNumber *buildingZIPCode = [NSNumber numberWithInteger:10018];
```

## CGRect Functions

When accessing the `x`, `y`, `width`, or `height` of a `CGRect`, always use the [`CGGeometry` functions](http://developer.apple.com/library/ios/#documentation/graphicsimaging/reference/CGGeometry/Reference/reference.html) instead of direct struct member access. From Apple's `CGGeometry` reference:

> All functions described in this reference that take CGRect data structures as inputs implicitly standardize those rectangles before calculating their results. For this reason, your applications should avoid directly reading and writing the data stored in the CGRect data structure. Instead, use the functions described here to manipulate rectangles and to retrieve their characteristics.

**For example:**

```objc
CGRect frame = self.view.frame;

CGFloat x = CGRectGetMinX(frame);
CGFloat y = CGRectGetMinY(frame);
CGFloat width = CGRectGetWidth(frame);
CGFloat height = CGRectGetHeight(frame);
```

**Not:**

```objc
CGRect frame = self.view.frame;

CGFloat x = frame.origin.x;
CGFloat y = frame.origin.y;
CGFloat width = frame.size.width;
CGFloat height = frame.size.height;
```

## Constants

Constants are preferred over in-line string literals or numbers, as they allow for easy reproduction of commonly used variables and can be quickly changed without the need for find and replace. Constants should be declared as `static` constants and not `#define`s unless explicitly being used as a macro.
Constant names (#defines, enums, const local variables, etc.) should start with a lowercase k and then use mixed case to delimit words.
For integer constant use NS_ENUM.
**For example:**

```objc
static NSString * const kAFKAboutViewControllerCompanyName = @"The New York Times Company";

static const CGFloat kAFKImageThumbnailHeight = 50.0;

NS_ENUM(NSInteger, AFKClassNameTableSize) {
    kAFKClassNameTableSizeRowsCount = 10
};
```

**Not:**

```objc
 #define CompanyName @"The New York Times Company"

 #define thumbnailHeight 2
```

## Enumerated Types

When using `enum`s, it is recommended to use the new fixed underlying type specification because it has stronger type checking and code completion. The SDK now includes a macro to facilitate and encourage use of fixed underlying types — `NS_ENUM()`

**Example:**

```objc
typedef NS_ENUM(NSInteger, NYTAdRequestState) {
    kNYTAdRequestStateInactive,
    kNYTAdRequestStateLoading
};
```

## Private Properties

Private properties should be declared in class extensions (anonymous categories) in the implementation file of a class. Named categories (such as `NYTPrivate` or `private`) should never be used unless extending another class.

**For example:**

```objc
@interface NYTAdvertisement ()

@property (nonatomic, strong) GADBannerView *googleAdView;
@property (nonatomic, strong) ADBannerView *iAdView;
@property (nonatomic, strong) UIWebView *adXWebView;

@end
```

## Image Naming

Image names should be named consistently to preserve organization and developer sanity. They should be named as one camel case string with a description of their purpose, followed by the un-prefixed name of the class or property they are customizing (if there is one), followed by a further description of color and/or placement, and finally their state.

**For example:**

* `RefreshBarButtonItem` / `RefreshBarButtonItem@2x` and `RefreshBarButtonItemSelected` / `RefreshBarButtonItemSelected@2x`
* `ArticleNavigationBarWhite` / `ArticleNavigationBarWhite@2x` and `ArticleNavigationBarBlackSelected` / `ArticleNavigationBarBlackSelected@2x`.

Images that are used for a similar purpose should be grouped in respective groups in an Images folder.

## Booleans

Since `nil` resolves to `NO` it is unnecessary to compare it in conditions. Never compare something directly to `YES`, because `YES` is defined to 1 and a `BOOL` can be up to 8 bits.

This allows for more consistency across files and greater visual clarity.

**For example:**

```objc
if (!someObject) {
}
```

**Not:**

```objc
if (someObject == nil) {
}
```

-----

**For a `BOOL`, here are two examples:**

```objc
if (isAwesome)
if (![someObject boolValue])
```

**Not:**

```objc
if ([someObject boolValue] == NO)
if (isAwesome == YES) // Never do this.
```

-----

If the name of a `BOOL` property is expressed as an adjective, the property can omit the “is” prefix but specifies the conventional name for the get accessor, for example:

```objc
@property (assign, getter=isEditable) BOOL editable;
```
Text and example taken from the [Cocoa Naming Guidelines](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingIvarsAndTypes.html#//apple_ref/doc/uid/20001284-BAJGIIJE).

## Singletons

Singleton objects should use a thread-safe pattern for creating their shared instance.
```objc
+ (instancetype)sharedInstance {
   static id sharedInstance = nil;

   static dispatch_once_t onceToken;
   dispatch_once(&onceToken, ^{
      sharedInstance = [[self alloc] init];
   });

   return sharedInstance;
}
```
This will prevent [possible and sometimes prolific crashes](http://cocoasamurai.blogspot.com/2011/04/singletons-your-doing-them-wrong.html).

## Xcode project

The physical files should be kept in sync with the Xcode project files in order to avoid file sprawl. Any Xcode groups created should be reflected by folders in the filesystem. Code should be grouped not only by type, but also by feature for greater clarity.

When possible, always turn on "Treat Warnings as Errors" in the target's Build Settings and enable as many [additional warnings](http://boredzo.org/blog/archives/2009-11-07/warnings) as possible. If you need to ignore a specific warning, use [Clang's pragma feature](http://clang.llvm.org/docs/UsersManual.html#controlling-diagnostics-via-pragmas).

# Other Objective-C Style Guides

If ours doesn't fit your tastes, have a look at some other style guides:

* [Google](http://google-styleguide.googlecode.com/svn/trunk/objcguide.xml)
* [GitHub](https://github.com/github/objective-c-conventions)
* [Adium](https://trac.adium.im/wiki/CodingStyle)
* [Sam Soffes](https://gist.github.com/soffes/812796)
* [CocoaDevCentral](http://cocoadevcentral.com/articles/000082.php)
* [Luke Redpath](http://lukeredpath.co.uk/blog/my-objective-c-style-guide.html)
* [Marcus Zarra](http://www.cimgf.com/zds-code-style-guide/)
