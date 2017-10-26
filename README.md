# 6 Steps for Using Facebook Pop in Swift 4
This is an updated quick guide to using Facebook's [Pop](https://github.com/facebook/pop) animation library in Swift 4. Structure lifted from https://github.com/maxmyers/FacebookPop.


```swift
// 1. Pick Animation Type:  POPBasicAnimation POPSpringAnimation POPDecayAnimation
let animation = POPBasicAnimation()
animation.duration = 0.5

// 2. Decide which property will be animated
animation.property = POPAnimatableProperty.property(withName: kPOPViewFrame) as! POPAnimatableProperty

// 3. Set the value being animated to or from using an appropriate type
animation.toValue = CGRect(x:50, y:50, width:100, height:100)

// 4. Set any additional animation properties
animation.beginTime =  CACurrentMediaTime() + 1

// 5. Optionally set closures, delegates or assign a name
animation.name = "DelayedBasicAnimation"
animation.completionBlock = {(animation, finished) in
    print("completed \(animation?.name)")
}

// 6. Add the animation
testView.pop_add(animation, forKey: "UniqueAnimationKeyForObject")
```

## Step 1: Pick Animation Type


### POPBasicAnimation
```swift
let basicAnimation = POPBasicAnimation()
basicAnimation.timingFunction = CAMediaTimingFunction(name: kCAMediaTimingFunctionEaseIn) //Default: kCAMediaTimingFunctionDefault
basicAnimation.duration = 0.5 //Default: 0.4
        
// Convenience initializer
let basicAnimation: POPBasicAnimation = POPBasicAnimation.easeOut() 
```
##### More initializers:
`.default()` `.linear()` `.easeIn()` `.easeOut()` `.easeInEaseOut()` `.easeOut()` 
`.easeOut()`

### POPSpringAnimation
```swift
let springAnimation = POPSpringAnimation()
springAnimation.velocity = 1000 // initial velocity (value/second)
springAnimation.springBounciness = 10 // 0-20, default:4 (higher = more bounce)
springAnimation.springSpeed = 10	// 0-20, default:12 (higher = faster dampening/shorter bounce)
    
//Advanced: set these values instead of springBouniness and springSpeed
springAnimation.dynamicsTension = 342 //default: 0
springAnimation.dynamicsMass = 30.5 //default: 0
springAnimation.dynamicsFriction = 1 //default: 0
```
**Note:** Dynamics values are converted from speed/bounciness when set, the values above are the converted default values

**About Velocity:** This value represents the change in units per second of animation, it will generealy be of the same value type as your `to` value. So `kPOPLayerTranslationY` could have a velocity of `20` while `kPOPLayerTranslationXY` could use `NSValue(cgSize: CGSize(width: 0.5, height: 1))`

### POPDecayAnimation
```swift
let decayAnimation = POPDecayAnimation()
decayAnimation.velocity = 233 //initial velocity (value/second)
decayAnimation.deceleration = 0.833 //0-1, default:0.998 (lower = faster)
```
**Velocity types:** `kPOPValuePoint`, `kPOPValueInteger`, `kPOPValueFloat`, `kPOPValueRect` & `kPOPValueSize`

## Step 2: Pick Animatable Property

POP can animate views, layers, as well as constraints. Below is a list of animatable properties (excluding NSView, NSWindow, and SceneKit - see full list: https://github.com/facebook/pop/blob/master/pop/POPAnimatableProperty.h)

### Example 
```swift
let animation = POPBasicAnimation()
animation.property = POPAnimatableProperty.property(withName: kPOPViewSize) as! POPAnimatableProperty

//Convenience initializer
let animation: POPBasicAnimation = POPBasicAnimation(propertyNamed: kPOPViewSize)
```
### Properties


| UIVIew |UIScrollView | Other |
| --- | --- | --- |
| kPOPViewAlpha | kPOPScrollViewContentOffset | **UINavigationBar**
| kPOPViewBackgroundColor | kPOPScrollViewContentSize | kPOPNavigationBarBarTintColor
| kPOPViewBounds | kPOPScrollViewZoomScale | **UIToolbar**
| kPOPViewCenter | kPOPScrollViewContentInset | kPOPToolbarBarTintColor 
| kPOPViewScaleX |  **UITableView** | **UITabBar**
| kPOPViewScaleXY | kPOPTableViewContentOffset  | kPOPTabBarBarTintColor
| kPOPViewScaleY | kPOPTableViewContentSize| **UILabel**
| kPOPViewFrame | **UICollectionView** | kPOPLabelTextColor
| kPOPViewSize |  kPOPCollectionViewContentOffset  | 
| kPOPViewTintColor | kPOPCollectionViewContentSize  | 



| CLayer | | |
| --- | --- | --- |
| kPOPLayerBackgroundColor | kPOPLayerScaleX	| kPOPLayerTranslationZ	|
| kPOPLayerBounds | kPOPLayerScaleXY	| kPOPLayerZPosition	|
| kPOPLayerCornerRadius | kPOPLayerScaleY	| kPOPLayerShadowColor	|
| kPOPLayerBorderWidth | kPOPLayerSize	| kPOPLayerShadowOffset	|
| kPOPLayerBorderColor | kPOPLayerSubscaleXY	| kPOPLayerShadowOpacity	|
| kPOPLayerOpacity | kPOPLayerSubtranslationX	| kPOPLayerShadowRadius	|
| kPOPLayerPosition | kPOPLayerSubtranslationXY	| 
| kPOPLayerPositionX | kPOPLayerSubtranslationY	| **CAShapeLayer**	|
| kPOPLayerPositionY | kPOPLayerSubtranslationZ	| kPOPShapeLayerStrokeStart	|
| kPOPLayerRotation | kPOPLayerTranslationX	| kPOPShapeLayerStrokeEnd	|
| kPOPLayerRotationX | kPOPLayerTranslationXY	| kPOPShapeLayerStrokeColor	|
| kPOPLayerRotationY | kPOPLayerTranslationY	| kPOPShapeLayerFillColor	|



| NSLayoutConstraint |
| --- |
| kPOPLayoutConstraintConstant |


## Step 3: Set `.toValue`  or `.fromValue` with Correct Type
Both the `.toValue` and `.fromValue` are `Any!` properties which need to be set with a value of the appropriate type (matching the animated property type). In addition to numeric values you will use the following structs: `CGRect`, `CGSize`, `CGPoint`, and `UIEdgeInset`. The *XY properties take a `CGPoint`.

If either property is `nil` the animation will use the current state of the animated property on the object. Use `fromValue` if you're animating an object which is currently in it's final position (for example views laid out in a XIB or Storyboard).

### Examples
```swift
let animation = POPBasicAnimation(propertyNamed:kPOPLayerOpacity)
animation.toValue = 0.5

let animation = POPBasicAnimation(propertyNamed:kPOPLayerBorderWidth)
let animation = POPBasicAnimation(propertyNamed:kPOPViewScaleX)
animation.toValue = 2

let animation = POPBasicAnimation(propertyNamed:kPOPLayerRotation)
animation.toValue = CGFloat.pi/4

let animation = POPBasicAnimation(propertyNamed:kPOPViewBackgroundColor)
animation.toValue = UIColor.orange

let animation = POPBasicAnimation(propertyNamed:kPOPViewBounds)
let animation = POPBasicAnimation(propertyNamed:kPOPViewFrame)
animation.fromValue = CGRect(x:10, y:10, width:100, height:100)

let animation = POPBasicAnimation(propertyNamed:kPOPViewSize)
animation.fromValue = CGSize(width:100, height:100)

let animation = POPBasicAnimation(propertyNamed:kPOPLayerPosition)
let animation = POPBasicAnimation(propertyNamed:kPOPViewCenter)
let animation = POPBasicAnimation(propertyNamed:kPOPViewScaleXY)
animation.fromValue = CGPoint(x:50,y:50)

let animation = POPBasicAnimation(propertyNamed:kPOPScrollViewContentInset)
animation.fromValue = UIEdgeInsets(top:10, left:20, bottom:10, right:20)
```

**More Info:** Check [POPAnimatableProperty.mm](https://github.com/facebook/pop/blob/master/pop/POPAnimatableProperty.mm) to see how values are handled.

## Step 4 Set Optional Properties
Set any properties that you may require for repeat, delays, etc.

```swift
let animation: POPBasicAnimation =  POPBasicAnimation(propertyNamed:kPOPLayerOpacity)
animation.toValue = 0.5

animation.fromValue = 0 //default: current value for property
animation.beginTime = CACurrentMediaTime() + 3 //default: 0 (seconds)
animation.removedOnCompletion = false //default: true
animation.autoreverses = true //default: false
animation.repeatCount = 10 //default: 0
animation.repeatForever = false //default: false
```

## Step 5 Set Closures, Delegates or Names
Closures or delegation can be used if needed, optionally a name can be set to identify the animation.

### Closure Example
```swift
let animation = POPBasicAnimation()
animation.name = "SomeObscureAnimationName"
animation.animationDidStartBlock = {(animation) in
    print("started \(animation?.name)")
}   
animation.animationDidApplyBlock = {(animation) in
    print("applied an animation frame \(anim.name)")
}
animation.animationDidReachToValueBlock = {(animation) in
    print("reached the to value \(animation?.name)")
}
animation.completionBlock = {(animation, finished) in
    print("completed \(animation?.name)")
}
```
### Delegation Example
Add the protocol and set the delegate property:

```swift 
class MyVC : UIViewController, POPAnimationDelegate {
	func animate() {
		let animation = POPBasicAnimation()
		animation.name = "SomeObscureAnimationName"
		animation.delegate = self	
	}
}

```

And implement any functions you need:

```swift
func pop_animationDidStart(_ anim: POPAnimation!) {
    print("started \(anim.name)")
}
func pop_animationDidApply(_ anim: POPAnimation!) {
    print("applied an animation frame \(anim.name)")
}
func pop_animationDidReach(toValue anim: POPAnimation!) {
    print("reached the to value \(anim.name)")
}
func pop_animationDidStop(_ anim: POPAnimation!, finished: Bool) {
    print("stopped \(anim.name)")
}
```

## Step 6: Add the Animation to the View
Adding the animation will start it immediately if no delay was set and `.fromValue` or `.toValue` are present.

### View Example
```swift
let testView = UIView.init(frame: self.view.bounds)
self.view.addSubview(testView)
    
let animation: POPBasicAnimation =  POPBasicAnimation(propertyNamed:kPOPViewFrame)
animation.toValue = CGRect.zero
testView.pop_add(animation, forKey: "UniqueAnimationKeyForObject")
```

### Layer Example
```swift
let testView = UIView.init(frame: self.view.bounds)
self.view.addSubview(testView)
    
let animation: POPBasicAnimation =  POPBasicAnimation(propertyNamed:kPOPLayerScaleXY)
animation.toValue = CGSize.zero
testView.layer.pop_add(animation, forKey: "UniqueAnimationKeyForObject")
```
## Pausing and Removing Animations
Completed animations without `.removeOnCompletion` set to `true` will persist in a paused state. 

###Example
```swift
let testView = UIView.init(frame: self.view.bounds)
self.view.addSubview(testView)
    
let animation: POPBasicAnimation =  POPBasicAnimation(propertyNamed:kPOPViewFrame)
animation.toValue = CGRect.zero
testView.pop_add(animation, forKey: "UniqueAnimationKeyForObject")

//Pause
animation.paused = true

//Remove
testView.pop_removeAnimation(forKey: "UniqueAnimationKeyForObject")
testView.pop_removeAllAnimations()
```
