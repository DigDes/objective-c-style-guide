# DD Objective-C Style Guide

Это руководство по стилю кода, основанное на руководстве для разработчиков в The New York Times(https://github.com/NYTimes/objective-c-style-guide/contributors)

## Введение

Ниже представлены документы Apple по этой же теме:

* [The Objective-C Programming Language](http://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html)
* [Cocoa Fundamentals Guide](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CocoaFundamentals/Introduction/Introduction.html)
* [Coding Guidelines for Cocoa](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html)
* [iOS App Programming Guide](http://developer.apple.com/library/ios/#documentation/iphone/conceptual/iphoneosprogrammingguide/Introduction/Introduction.html)

## Содержание

* [Использование точек](#dot-notation-syntax)
* [Отсутупы](#Отступы)
* [Условия](#Условия)
  * [Тернарный оператор](#Тернарный-оператор)
* [Обработка ошибок](#Обработка-ошибок)
* [Методы](#Методы)
* [Переменные](#Переменные)
* [Правила именования](#Правила-именования)
  * [Подчеркивания](#Подчеркивания)
* [Комментарии](#Комментарии)
* [Инициализация и уничтожение объектов](#Инициализация-и-уничтожение-объектов)
* [Литералы](#Литералы)
* [CGRect функции](#cgrect-functions)
* [Константы](#Константы)
* [Тип перечисление](#Тип-перечисление)
* [Private свойства](#private-properties)
* [Именование графических ресурсов](#Именование-графических-ресурсов)
* [Логический тип BOOL](#booleans)
* [Синглтоны](#Синглтоны)
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

* Для отсупа используйте табы.
* Фигурные скобки в методах и других конструкциях (например `if`/`else`/`switch`/`while`) всегда всегда должны открываться в той же строке, что и условие. Но закрываться должны на другой строке.

**Например:**
```objc
if (user.isHappy) {
//Do something
}
else {
//Do something else
}
```
* Всегда стоит добавлять пустую строку между методами - это придаст больше четкости коду. Отступы внутри методов всегда должны отделять функциональность, которую можно было бы вынести в отдельные методы.
* `@synthesize` и `@dynamic` должны начинаться на новой строке.
* Модификаторы области видимости типа `@public`, `@protected` и подобные (типа `@optional`) , в протоколах нужно сдвигать на один пробел вправо.

**Например:**
```objc
@interface AFKSection : NSObject {
 @private
	NSString *_headline;
 @protected
	NSInteger _count;
}
```

## Условия

Тело условия необходимо писать в фигурных скобках даже в том случае, если это можно и не делать (когда оно может быть написано в одну строку) [errors](https://github.com/NYTimes/objective-c-style-guide/issues/26#issuecomment-22074256). Это исключит ошибки, возникающие когда следующая строка не попадает под условие, хотя должна (так думает разработчик). В другом случае, ошибка может возникнуть когда строка внутри условного оператора закоментирована и следующая строка становится телом условного оператора . К тому же, данный стиль лучше согласуется с оформлением других блоков и становится лучше читаемым.

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

## Обработка ошибок

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
Некоторые методы из API Apple возвращают ошибку(не NULL) даже в случае успешного выполнения, по этой причине не анализируйте ошибочное значение.


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

**Хорошо:**

```objc
@interface AFKSection : NSObject {
 @private
    NSString *_headline;
}

@property (strong, nonatomic) NSString *publicHeadline;
```

**Плохо:**
```objc
@interface AFKSection: NSObject

@property (strong, nonatomic) NSString *publicHeadline;
@property (strong, nonatomic) NSString *headline;

@end
```


## Правила именования

По возможности старайтесь соблюдать соглашение Apple о именовании [memory management rules](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/MemoryMgmt/Articles/MemoryMgmt.html) ([NARC](http://stackoverflow.com/a/2865194/340508)).

Используйте подробные имена переменных.

**Хорошо:**

```objc
UIButton *settingsButton;
```

**Плохо:**

```objc
UIButton *setBut;
```
Трехбуквенные префиксы (например 'AFK') всегда используйте для имен классов и констант, однако ими можно пренебречь для сущностей Core Data 
Константы должны именоваться ВерблюжьемРегистре http://ru.wikipedia.org/wiki/CamelCase, где все слова начинаются с большой буквы и начинаться с буквы `k` и префикса класса, в котором описаны.


**Хорошо:**

```objc
static const NSTimeInterval kAFKArticleViewControllerNavigationFadeAnimationDuration = 0.3;
```

**Плохо:**

```objc
static const NSTimeInterval fadetime = 1.7;
```

Свойства должны именоваться тоже в ВерблюжьемРегистре, только первое слово в нижнем регистре.
**Если Xcode  автоматически синтезировал перменные, оставьте их** В противном случае, согласно выше сказанному, имена переменных для этих свойств должны начинаться с подчеркивания и первое слово должно быть в нижнем регистре. В таком формате Xcode обеспечивает связь между свойствами и переменными. 

**Хорошо:**

```objc
@synthesize descriptiveVariableName = _descriptiveVariableName;
```

**Плохо:**

```objc
id varnm;
```

### Подчеркивания

Когда используете свойства, instance-переменные всегда должны быть доступны и изменяемы через "self." Это значит что все свойства должны визуально отличаться, когда к ним обращаются через "self.". Локальные переменные не должны содержать с подчеркивания.


## Комментарии


Когда вам необходимо, используйте комментарий, который должен объяснять почему часть кода делает что-то. Любые комментарии, которые используются, должны быть сохранены или удалены.
Необходимо избегать блоки комментариев, код должен быть самодокументирован(и не требовать излишних комментариев), только если требуется -  несколько строчек объясняющих. Это не распространяется на комментарии, которые используются для генерации документации.

## Инициализация и удаление объектов

Удаление: `dealloc` методы должны помещаться вверху реализации, сразу после  `@synthesize` и `@dynamic` . Инициализация: метод `init` должен быть расположен сразу после `dealloc` методов в любых классах.

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

## Литералы

Для создания неизменяемых объектов (immutable objects) используйте литералы классов `NSString`, `NSDictionary`, `NSArray`, и `NSNumber`. Обратите внимание, что `nil` значения не могут быть переданны в объекты-литералы классов `NSArray` и `NSDictionary`, так как их передача приведет к ошибке.

**Хорошо:**

```objc
NSArray *names = @[@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul"];
NSDictionary *productManagers = @{@"iPhone" : @"Kate", @"iPad" : @"Kamal", @"Mobile Web" : @"Bill"};
NSNumber *shouldUseLiterals = @YES;
NSNumber *buildingZIPCode = @10018;
```

**Плохо:**

```objc
NSArray *names = [NSArray arrayWithObjects:@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul", nil];
NSDictionary *productManagers = [NSDictionary dictionaryWithObjectsAndKeys: @"Kate", @"iPhone", @"Kamal", @"iPad", @"Bill", @"Mobile Web", nil];
NSNumber *shouldUseLiterals = [NSNumber numberWithBool:YES];
NSNumber *buildingZIPCode = [NSNumber numberWithInteger:10018];
```

## CGRect Functions

Вместо прямого доступа к полям `x`, `y`, `width`, или `height` структуры `CGRect`, всегда используйте [функции `CGGeometry`](http://developer.apple.com/library/ios/#documentation/graphicsimaging/reference/CGGeometry/Reference/reference.html).

Из документации Apple `CGGeometry`:
  
> Описанные в данном документе функции, принимающие структуру CGRect в качестве аргумента, неявно приводят эти струкутры к стандартному виду. Поэтому, избегайте прямого доступа к полям структуры CGRect при написании программы. Вместо этого для работы со структурами и для получения значений отдельных полей используйте функции, описанные в данном руководстве.

**Хорошо:**

```objc
CGRect frame = self.view.frame;

CGFloat x = CGRectGetMinX(frame);
CGFloat y = CGRectGetMinY(frame);
CGFloat width = CGRectGetWidth(frame);
CGFloat height = CGRectGetHeight(frame);
```

**Плохо:**

```objc
CGRect frame = self.view.frame;

CGFloat x = frame.origin.x;
CGFloat y = frame.origin.y;
CGFloat width = frame.size.width;
CGFloat height = frame.size.height;
```

## Константы

Избегайте использования "магических чисел"(magic numbers) и строковых литералов. Вместо этого определяйте их как переменные-константы. Это позволяет сделать код намного понятней и облегчает их изменение. Объявляйте константы с модификатором `static` и не объявляйте при помощи `#define`. Используйте `#define` для макросов.
Названия констант (#defin-ов, enum-ов, локальных переменных-констант, и т.д.) должны начинаться со строчной буквы k. Для разделения слов используйте mixed case.
Для целочисленных (integer) констант испоьзуйте NS_ENUM.

**Хорошо:**

```objc
static NSString * const kAFKAboutViewControllerCompanyName = @"The New York Times Company";

static const CGFloat kAFKImageThumbnailHeight = 50.0;

NS_ENUM(NSInteger, AFKClassNameTableSize) {
    kAFKClassNameTableSizeRowsCount = 10
};
```

**Плохо:**

```objc
 #define CompanyName @"The New York Times Company"

 #define thumbnailHeight 2
```

## Тип перечисление

When using `enum`s, it is recommended to use the new fixed underlying type specification because it has stronger type checking and code completion. The SDK now includes a macro to facilitate and encourage use of fixed underlying types — `NS_ENUM()`
<!--При использовании `enum`ов рекомендуется использовать нововведеный фиксированный спецификатор типа т к он имеет более строгую проверку типов и лучшее автозаполнение кода. Теперь SDK включает в себя макрос, облегчающий и улучшающий для использования фиксированного идентификатора типов - `NS_ENUM()`
-->
**Хорошо:**

```objc
typedef NS_ENUM(NSInteger, NYTAdRequestState) {
    kNYTAdRequestStateInactive,
    kNYTAdRequestStateLoading
};
```

## Private свойства

Объявляйте private-свойства в class extension (анонимных категориях) в файле с реализацией класса (.m). Используйте именованные категории (такие как `NYTPrivate` или `private`) только для расширения других классов.

**Хорошо:**

```objc
@interface NYTAdvertisement ()

@property (nonatomic, strong) GADBannerView *googleAdView;
@property (nonatomic, strong) ADBannerView *iAdView;
@property (nonatomic, strong) UIWebView *adXWebView;

@end
```

## Именование графических ресурсов

Image names should be named consistently to preserve organization and developer sanity. They should be named as one camel case string with a description of their purpose, followed by the un-prefixed name of the class or property they are customizing (if there is one), followed by a further description of color and/or placement, and finally their state.

**Хорошо**

* `RefreshBarButtonItem` / `RefreshBarButtonItem@2x` and `RefreshBarButtonItemSelected` / `RefreshBarButtonItemSelected@2x`
* `ArticleNavigationBarWhite` / `ArticleNavigationBarWhite@2x` and `ArticleNavigationBarBlackSelected` / `ArticleNavigationBarBlackSelected@2x`.

Images that are used for a similar purpose should be grouped in respective groups in an Images folder.

## Логические типы

Не сравнвайте напрямую с `nil` в условных операторах. Никогда не делайте сравнения с `YES`, потому что `YES` определен как `(BOOL)1`, и если вы будете сравнивать `YES` со значением длиной более чем 1 байт (например short или int), то при сравнении будет использован только младший байт этого значения.

Следование этип правилам позволяет добиться единобразия в коде и улучшить его читаемость.

**Хорошо:**

```objc
if (!someObject) {
}
```

**Плохо:**

```objc
if (someObject == nil) {
}
```

-----

**Хорошо(для типа `BOOL`):**

```objc
if (isAwesome)
if (![someObject boolValue])
```

**Плохо(для типа `BOOL`):**

```objc
if ([someObject boolValue] == NO)
if (isAwesome == YES) // Never do this.
```

-----

Если имя `BOOL`-свойства интерпретируется как прилагательное, то префикс "is" можно опустить, но обязательно укажите его в названии геттера.

```objc
@property (assign, getter=isEditable) BOOL editable;
```
Текст и примеры взяты из [Cocoa Naming Guidelines](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingIvarsAndTypes.html#//apple_ref/doc/uid/20001284-BAJGIIJE).


## Синглтоны

Для реализации синглтонов используйте потокобезопасный шаблон
```objc
#import <Foundation/Foundation.h>

@interface MySingleton : NSObject

+(instancetype) sharedInstance;

+(instancetype) alloc __attribute__((unavailable("alloc not available, call sharedInstance instead")));
-(instancetype) init __attribute__((unavailable("init not available, call sharedInstance instead")));
+(instancetype) new __attribute__((unavailable("new not available, call sharedInstance instead")));

@end

#import "MySingleton.h"

@implementation MySingleton

+(instancetype) sharedInstance {
    static dispatch_once_t pred;
    static id shared = nil;
    dispatch_once(&pred, ^{
        shared = [[super alloc] initUniqueInstance];
    });
    return shared;
}

-(instancetype) initUniqueInstance {
    return [super init];
}

@end
```
Использование шаблона предотвратит [возможные многочисленные ошибки](http://cocoasamurai.blogspot.com/2011/04/singletons-your-doing-them-wrong.html).
 

## Xcode-проект

Физическая структура файлов должна соответствовать логической структуре файлов в Xcode. Любые группы (groups) в Xcode-проекте должны соответствовать папкам в файловой системе.
Для большей ясности группируйте код не только по типу, но и по функциональности.

Всегда, при возможности, включайте "Treat Warnings as Errors" в настройках (Build Settings) таргета и включайте как можно больше [дополнительных warning-ов](http://boredzo.org/blog/archives/2009-11-07/warnings). Если вам необходимо проигнорировать какой то конкретный warning, используйте [Clang's pragma feature](http://clang.llvm.org/docs/UsersManual.html#controlling-diagnostics-via-pragmas).


# Другие руководства по стилю кода Objective-C


* [Google](http://google-styleguide.googlecode.com/svn/trunk/objcguide.xml)
* [GitHub](https://github.com/github/objective-c-conventions)
* [Adium](https://trac.adium.im/wiki/CodingStyle)
* [Sam Soffes](https://gist.github.com/soffes/812796)
* [CocoaDevCentral](http://cocoadevcentral.com/articles/000082.php)
* [Luke Redpath](http://lukeredpath.co.uk/blog/my-objective-c-style-guide.html)
* [Marcus Zarra](http://www.cimgf.com/zds-code-style-guide/)
