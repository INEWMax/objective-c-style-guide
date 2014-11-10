
# Sysdata Italia - Objective-C Style Guide

This style guide outlines the coding conventions of the iOS team at Sysdata Italia S.p.A.

## Introduction

Here are some of the documents from Apple that informed the style guide. If something isn't mentioned here, it's probably covered in great detail in one of these:

* [The Objective-C Programming Language](http://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html)
* [Cocoa Fundamentals Guide](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CocoaFundamentals/Introduction/Introduction.html)
* [Coding Guidelines for Cocoa](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html)
* [iOS App Programming Guide](http://developer.apple.com/library/ios/#documentation/iphone/conceptual/iphoneosprogrammingguide/Introduction/Introduction.html)

Some following rules can be automatically applied using uncrustify Xcode plugin via [Alcatraz plugin manager](https://github.com/sysdataitalia/Alcatraz) and following uncrustify [configuration](https://github.com/sysdataitalia/objective-c-style-guide/blob/master/uncrustify.cfg)


## Table of Contents

* [Dot-Notation Syntax](#dot-notation-syntax)
* [Spacing](#spacing)
* [Conditionals](#conditionals)
  * [Ternary Operator](#ternary-operator)
* [Error handling](#error-handling)
* [Methods](#methods)
* [Variables](#variables)
* [Naming](#naming)
* [Comments](#comments)
* [Init & Dealloc](#init-and-dealloc)
* [Literals](#literals)
* [CGRect Functions](#cgrect-functions)
* [Constants](#constants)
* [Enumerated Types](#enumerated-types)
* [Private Properties](#private-properties)
* [Image Naming](#image-naming)
* [Booleans](#booleans)
* [Singletons](#singletons)
* [User Interface (WIP)](#user-interface)
* [Xcode Project (WIP)](#xcode-project)

## Dot-Notation Syntax

Dot-notation should **always** be used for accessing and mutating properties. Bracket notation is preferred in all other instances.

**For example:**
```objc
view.backgroundColor = [UIColor orangeColor];
[UIApplication sharedApplication].delegate;
```

**Not:**
```objc
[view setBackgroundColor:[UIColor orangeColor]];
UIApplication.sharedApplication.delegate;
```

## Spacing

* Indent using 4 spaces. Never indent with tabs. Be sure to set this preference in Xcode.
* Method braces and other braces (`if`/`else`/`switch`/`while` etc.) always open and close on a new line.

**For example:**
```objc
if (user.isHappy)
{
//Do something
}
else
{
//Do something else
}
```
* There should be exactly one blank line between methods to aid in visual clarity and organization. Whitespace within methods should separate functionality, but often there should probably be new methods.
* `@synthesize` and `@dynamic` should each be declared on new lines in the implementation.

## Conditionals

Conditional bodies should always use braces even when a conditional body could be written without braces (e.g., it is one line only) to prevent errors. These errors include adding a second line and expecting it to be part of the if-statement. Another, [even more dangerous defect](http://programmers.stackexchange.com/a/16530) may happen where the line "inside" the if-statement is commented out, and the next line unwittingly becomes part of the if-statement. In addition, this style is more consistent with all other conditionals, and therefore more easily scannable.

**For example:**
```objc
if (!error)
{
    return success;
}
```

**Not:**
```objc
if (!error)
    return success;
```

or

```objc
if (!error) return success;
```

### Ternary Operator

The Ternary operator, ? , should only be used when it **increases** **clarity** or code **neatness**. A single condition is usually all that should be evaluated. Evaluating multiple conditions is usually more understandable as an if statement, or refactored into instance variables.

**For example:**
```objc
result = a > b ? x : y;
```

**Not:**
```objc
result = a > b ? x = c > d ? c : d : y;
```

## Error handling

When methods return an error parameter by reference, switch on the returned value, not the error variable.

**For example:**
```objc
NSError *error = nil;
if (![self trySomethingWithError:&error]) 
{
    // Handle Error
}
```

**Not:**
```objc
NSError *error = nil;
[self trySomethingWithError:&error];
if (error) 
{
    // Handle Error
}
```

Some of Apple’s APIs write garbage values to the error parameter (if non-NULL) in successful cases, so switching on the error can cause false negatives (and subsequently crash).

## Methods

In method signatures, there should be a space after the scope (-/+ symbol) and after the return type. 
There should be a space between the method segments.
No space between parameters type and *.

**For Example**:
```objc
- (void) setExampleText:(NSString*)text image:(UIImage*)image;
```
## Variables

Variables should be named as descriptively as possible. Single letter variable names should be avoided except in `for()` loops.

Asterisks indicating pointers belong with the type, e.g., `NSString* text` not `NSString *text` or `NSString * text`, except in the case of constants.

Property definitions should be used only for attributes that should be accessed from outside the object. They are part of public object's interface.
Instance variables (*ivar*) should be used for internal object state variable, and they never should be @public.

**For example:**

```objc
@interface SDSection: NSObject
{
    NSString* privateAttribute;
}

@property (nonatomic) NSString* publicAttribute;

@end
```

**Not:**

```objc
@interface SDSection : NSObject 
{
    @public
    NSString* publicAttribute;
}
```

```objc
@interface SDSection : NSObject 

@property (nonatomic) NSString* privateAttribute;
```

## Naming

Apple naming conventions should be adhered to wherever possible, especially those related to [memory management rules](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/MemoryMgmt/Articles/MemoryMgmt.html) ([NARC](http://stackoverflow.com/a/2865194/340508)).

Long, descriptive method and variable names are good.

**For example:**

```objc
UIButton *settingsButton;
```

**Not**

```objc
UIButton *setBut;
```

A two letter prefix (e.g. `SD`) should always be used for common Sysdata library classes and widgets. 

Constants should be UPPER_CASE with underscore separator.
Prefixed by the related class name only if the constant is related only to that class.
Using `#define` for basic type constants (numbers and strings) and `enum` for constants enumerations.
Enum should be typed with **t** prefix and values should be named including the type name and the **k** prefix.

**For example:**

```objc
#define ARTICLE_VIEW_CONTROLLER_NAVIGATION_FADE_ANIMATION_DURATION 0.3;
```

```objc

typedef NS_ENUM(NSInteger, tApplicationWorkflowTransition)
{
    kApplicationWorkflowTransition_SplashScreen = 0,
    kApplicationWorkflowTransition_GenericBack,
    kApplicationWorkflowTransition_GenericHome
};
```

**Not:**

```objc
#define ArticleViewControllerNavigationFadeAnimationDuration 0.3;
```

```objc
typedef enum
{
    SplashScreen = 0,
    GenericBack,
    GenericHome
} Transition;
```

Properties and local variables should be camel-case with the leading word being lowercase. 

Instance variables should be camel-case with the leading word being lowercase.
Don't add an underscore prefix to instance variables.
**Don't synthesize variable if LLVM can synthesize the variable automatically** (you need to sithesize only when you define manually the getter and setter and in other few cases).

**For example:**

```objc
@interface SDSection : NSObject 
{
  id descriptiveVariableName;
}
```

**Not:**

```objc
@interface SDSection : NSObject 
{
  id _descriptiveVariableName;
}
```

## Comments

When they are needed, comments should be used to explain **why** a particular piece of code does something. Any comments that are used must be kept up-to-date or deleted.

Block comments should generally be avoided, as code should be as self-documenting as possible, with only the need for intermittent, few-line explanations. This does not apply to those comments used to generate documentation.

`#pragma mark - SECTION` should be used to group methods inside class implementation:
* To group all ViewController lifecycle methods
* To group all specific protocol method (UITableViewDelegate and datasource).
* To group methods by scope or context

## init and dealloc

`dealloc` methods should be placed at the top of the implementation, directly after the `@synthesize` and `@dynamic` statements. 
`init` should be placed directly below the `dealloc` methods of any class.

`init` methods should be structured like this:

```objc
- (instancetype)init 
{
    self = [super init]; // or call the designated initalizer
    if (self) 
    {
        // Custom initialization
    }

    return self;
}
```

## ViewControllers methods

All view controllers lyfecycle methods should be placed direcly after `init` methods.
i.e. viewDidLoad, viewDidUnload, etc. 


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

**Important:**
When variables instead of constants are used in literals, make sure they are not NIL, otherwise a runtime exception is raised.
I.e.
```objc
NSString *nameKate = nil;
NSString *nameKamal = @"Kamal";
NSDictionary *productManagers = @{@"iPhone" : nameKate, @"iPad" :nameKamal}
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

Constants are preferred over in-line string literals or numbers, as they allow for easy reproduction of commonly used variables and can be quickly changed without the need for find and replace. Constants should be declared as `#define`s.
`#define`s should be placed in:
* .pch file if it's a global constant
* In the interface (.h) file if it's a constant specific to that class

**For example:**

```objc
#define ABOUT_VIEWCONTROLLER_ROW_HEIGHT 50.0f;

self.rowHeight = ABOUT_VIEWCONTROLLER_ROW_HEIGHT;
```

**Not:**

```objc
self.rowHeight = 50.0f;
```


## Enumerated Types

When using `enum`s, it is recommended to use the new fixed underlying type specification because it has stronger type checking and code completion. The SDK now includes a macro to facilitate and encourage use of fixed underlying types — `NS_ENUM()`

**Example:**

```objc
typedef NS_ENUM(NSInteger, SDAdRequestState)
{
    SDAdRequestStateInactive,
    SDAdRequestStateLoading
};
```

## Private Properties

**Avoid using private properties, use ivars instead.**
Properties should be used to define the public interface for an object, not to define internal state attributes.
If strictly necessary private properties should be declared in class extensions (anonymous categories) in the implementation file of a class. 

**For example:**

```objc
@interface SDAdvertisement ()

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
if (!someObject) 
{
}
```

**Not:**

```objc
if (someObject == nil) 
{
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
+ (instancetype)sharedInstance 
{
   static id sharedInstance = nil;

   static dispatch_once_t onceToken;
   dispatch_once(&onceToken, ^{
      sharedInstance = [[self alloc] init];
   });

   return sharedInstance;
}
```
This will prevent [possible and sometimes prolific crashes](http://cocoasamurai.blogspot.com/2011/04/singletons-your-doing-them-wrong.html).

## User Interface

Use XIB files always when possible.

Define user interface in code only when strictly necessary.

Graphical elements style attributes (like colors, strings/texts, fonts) must be set in code, in the proper ViewController/View setup methods. 

XIB files should only be used to set example style attributes, but the final style should be set in code. This because in this way is siplier to change application theme.

Font and Colors sould be defined as macros in .pch file and never defined spead in the code.


## Xcode project

### Project Structure

The `.xcodeproj` project file should be in the project root directory.
At the same level should be present a `.xcworkspace` workspace file and the Pods directory (if CocoaPods is used).

All certificates and `.mobileprovision` files should be placed in the `Certificates` directory.
Jenkins build jobs will search for such directory to automatically install mobile provisions in builder server.

All the projects specific files should be put inside the `Project` directory.

Because of separation of phisical and logical files organization, the project should be more organized logically in Xcode (using files groups) than phisically (using file system directories).

####Phisical Organization

The structure inside the `Project` directory should be as simple as possible, ideally only following sub-directories should be present:

 - Classes
 - External
 - Assets
	 - Common
		 - Images
		 - Fonts
		 - ...
	 - BrandName (Branded Assets)
		 - Images
		 - Fonts
		 - ...

All implementation files should be all phisically put inside `Classes` folder, .xib included.

####Logical Organization

Phisical filesystem directories defined above should be replicated in Xcode with logical groups which are linked to the related directories in the filesystem.

Inside `Classes` group, implementation files are organized in following main groups:

 - DataModel
 - Common
 - Main
 - Functionalities

Inside `Functionalities` group the files are organized by funcionality using subgroups depending on project peculiarity.

In general `.xib` files should be always be near to `.h` and `.m` files, never in a separate directory.
Is better to have file oganized in this order: `.h`, `.m`, `.xib`.

### Deploy Configuration

One Target for each deploy configuration should be used.
The target should be named with a clear patter useful to identifying:
 - The App name and/or Brand (see Multi Brand Projects)
 - The App Environment

Each Target is linked to a specific Info.plist file and .pch configuration with the same name as the Target name.

The `PRODUCT_NAME` XCode configuration should be named in the same way.
Customize the `CFBundleDisplayName` or the `CFBundleName` in the Info.plist if the app name need to be different from `PRODUCT_NAME`.

**For example:**

Project Name: Kitchen

Targets Names: 
 - KitchenDev (development server, in-house)
 - KitchenTest (test server, client environment, UAT)
 - KitchenProd (production server, client environment for Client final acceptance)
 - KitchenAppStore (production server, client environment, AppStore configuration)

Configuration Files:
 - KitchenDev.plist & KitchenDev.pch
 - KitchenTest.plist & KitchenTest.pch
 - KitchenProd.plist & KitchenProd.pch
 - KitchenAppStore.plist & KitchenAppStore.pch

Product Names:

 - KitchenDev.app
 - KitchenTest.app
 - KitchenProd.app
 - KitchenAppStore.app

### Multi Brand Projects

When a project involves some similar apps with only small functional difference it's recommended to use the same code base as much as possible.
In that way it will be easy to maintain the code among all apps during all the project lifecycle (es. sharing bugfixes or enhance functionalities).

The XCode project it's unique for all apps and the specific Brand configuration is made by defining different Targets as per Deploy Configuration.

**For example:**

Project Name: 

 - Kitchen

Brands Names:

 - DeLonghi
 - Kenwood

Targets Names: 

 - KenwoodDev 
 - DeLonghiDev
 - ...

Configuration Files:

 - KenwoodDev.plist & KenwoodDev.pch
 - DeLonghiDev.plist & DeLonghiDev.pch
 - ...

Product Names:

 - KenwoodDev.app
 - DeLonghiDev.app
 - ...

The Xcode Asset group should contain one subfolder for each Brand.
Every Brand subfolder should have the same substructure that groups Seed Database, Fonts, Images, .xcassets, etc.
Elements contained in Brand's subfolders should be linked only to proper Brand's Target.
In this way only Brand assets will be included in the bundle at compile time.

### Warnings

When possible, always turn on "Treat Warnings as Errors" in the target's Build Settings and enable as many [additional warnings](http://boredzo.org/blog/archives/2009-11-07/warnings) as possible. If you need to ignore a specific warning, use [Clang's pragma feature](http://clang.llvm.org/docs/UsersManual.html#controlling-diagnostics-via-pragmas).

# Other Objective-C Style Guides

If ours doesn't fit your tastes, have a look at some other style guides:

* [NYTimes](https://github.com/NYTimes/objective-c-style-guide)
* [Google](http://google-styleguide.googlecode.com/svn/trunk/objcguide.xml)
* [GitHub](https://github.com/github/objective-c-conventions)
* [Adium](https://trac.adium.im/wiki/CodingStyle)
* [Sam Soffes](https://gist.github.com/soffes/812796)
* [CocoaDevCentral](http://cocoadevcentral.com/articles/000082.php)
* [Luke Redpath](http://lukeredpath.co.uk/blog/my-objective-c-style-guide.html)
* [Marcus Zarra](http://www.cimgf.com/zds-code-style-guide/)

