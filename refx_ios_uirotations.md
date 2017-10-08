# refx ios uirotations

## Manual UIVew positioning and rotation

How create and manage a `UIView` that won't be squashed or stretched in the process of changing the UI orientation.  In particular, the view should not change size and should be positioned such that it appears to rotate around its center during the UI rotation.

This is necessary for any non-square view, and might be necessary for a square view as well.  Additionally, because the UIView will not be resized during the rotation animation, it may overlap additional UI elements in an undesired manner.

Overview:

- Determine the portrait and landscape view frames at runtime
- Disable all flags pertaining to UIKit automatically managing the view
- Manually set the initial view geometry for the given orientation
- _Optional: switch to a different view geometry during the rotation_
- Specify the geometry of the view after rotation

Create the UIView as normal in Interface Builder.  Leave the autoresizing masks enabled so you can easily switch between portrait and landscape orientation.

### Determine the view frame at runtime

To avoid hard-coding values, determine the portrait and landscape view frames at runtime. The most basic requires the [`UIScreen.bounds`][UIScreenBounds] [#UIScreen][] to determine the full view sizes, and accounting for the [status bar size][UIStatusBar] (from `UIApplication` [#UIApplication][]) at the top of the screen:

```objective-c
CGRect screenBounds 	= UIScreen.mainScreen.bounds;
CGRect statusBar		= [UIApplication sharedApplication].statusBarFrame;

// assuming screen doesn't change in dimensions when rotating
NSUInteger minDim = MIN( screenBounds.size.width, screenBounds.size.height );
NSUInteger maxDim = MAX( screenBounds.size.width, screenBounds.size.height );

portRect = CGRectMake( 0, 0, minDim, maxDim - statusBar.size.height );
landRect = CGRectMake( 0, 0, maxDim, minDim - statusBar.size.height  );
```

Also account for other UI elements, in this case a toolbar at above the view:

```objective-c
NSUInteger barHeight	= self.toolBar.frame.size.height;
portRect.origin.y 		+= barHeight;
portRect.size.height	-= barHeight;
landRect.origin.y 		+= barHeight;
landRect.size.height	-= barHeight;
```

[UIScreenBounds]:	http://developer.apple.com/library/ios/documentation/uikit/reference/UIScreen_Class/Reference/UIScreen.html#//apple_ref/occ/instp/UIScreen/bounds
[UIStatusBar]:	http://developer.apple.com/library/ios/DOCUMENTATION/UIKit/Reference/UIApplication_Class/Reference/Reference.html#//apple_ref/occ/instp/UIApplication/statusBarFrame

### Disabling UIKit view management

For a given view, in this case `glView`, disable all UIKit automatic layout by setting the `UIView`'s [autoresizing mask][] and [content mode][] [#UIView][] in the view controller's `-viewDidLoad` method:

[autoresizing mask]:	http://developer.apple.com/library/ios/#documentation/UIKit/Reference/UIView_Class/UIView/UIView.html#//apple_ref/occ/instp/UIView/autoresizingMask

[content mode]:		http://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/UIView/UIView.html#//apple_ref/doc/uid/TP40006816-CH3-SW99

```objective-c
glView.autoresizingMask		= UIViewAutoresizingNone;
glView.contentMode			= UIViewContentModeCenter;
```

Programmatically setting the autoresizingMask makes it easier to edit elements in IB, but means the layout in Interface Builder may not reflect the code.

### Manually setting the initial geometry

In the `-viewDidLoad` routine of the parent `UIViewController`, specify the desired initial view geometry of the view by either setting the `frame` or `bounds` and `center` [#UIViewGeometry][] of the `UIView`, depending on the desired view geometry.[^1]

The code required ends up being exactly the same as that for [responding to interface rotations][RespondToRotation].  From `-viewDidLoad`, call:

```objective-c
[self didRotateFromInterfaceOrientation:UIDeviceOrientationUnknown];
```

to use the method we specify below.  The `UIDeviceOrientationUnknown` flag is to force the method to perform the rotation.

[^1]: Different iOS versions having different methods of specifying the initial rotation.  In iOS 6, the actual rotation is set by the time `-viewDidLoad` called.  Before iOS 6, the orientation was always portrait at the time `-viewDidLoad` was called.  If the device was in landscape orientation, a separate notification was send to the view controller to instruct it to rotate.

### Optional: switch to a different view geometry during the rotation

_NOTE: If this step is omitted, the view will jump from the geometry of the old orientation to the geometry for the new orientation._

If desired, the view geometry can be changed for the duration of the rotation animation.  This can be used to do animate from one orientation geometry to another, or do more complicate steps.  To do so, the parent view controller needs to implement `-willRotateToInterfaceOrientation:` and manually set the view geometry for the new orientation.  The `-willRotateToInterfaceOrientation:` method will be called before UIKit begins the orientation rotation animation.

The view geometry only needs to be updated if changing between portrait and landscape. Don't do anything if a 180-degree rotation.

```objective-c
BOOL oldLandscape = 
	UIDeviceOrientationIsLandscape( self.interfaceOrientation );
BOOL newLandscape = 
	UIDeviceOrientationIsLandscape( toInterfaceOrientation );

if( oldLandscape != newLandscape ) {
	...
}
```

Determine the view geometry for the current orientation and the new orientation

```objective-c
CGSize startView;
CGSize endView;
if( newLandscape ) {
	startView	= portRect.size;
	endView		= landRect.size;
} else {
	startView	= landRect.size;
	endView		= portRect.size;
}
```

Update the view geometry.  The following code instantaneously changes the bounds of the view to a larger size, then animates a change in the center point of the view for the specified duration of the rotation.  Additionally, it instructs the view to do any changes necessary to handle the new geometry (in this case via the `-rotateScreenFromView:` method).  

```objective-c
[glView rotateScreenFromView:startView toView:endView duration:duration];

glView.bounds = {
	.origin.x = 0,
	.origin.y = 0,
	.size = {1024, 1024}
};
	
CGPoint center = CGPointMake( 
	viewRect.size.width / 2.  + viewRect.origin.x,
	viewRect.size.height / 2. + viewRect.origin.y 
);
	            
[UIView animateWithDuration:duration
				 animations:^{
	glView.center = center;
}];
```

_NOTE: `EAGLView` implicitly rerenders the scene into the frame buffer whenever the view bounds have changed.  `-rotateScreenFromView:` and similar methods that update the view properties need to be called before changing the view bounds._

### Specify the geometry of the view after rotation [RespondToRotation]

The geometry of the view needs to be updated after the device is rotated to an alternate orientation.  To do so, the parent view controller needs to implement `-didRotateToInterfaceOrientation:` and manually set the view geometry for the new orientation.  The `-didRotateToInterfaceOrientation:` method will be called before UIKit begins the orientation rotation animation.

_NOTE: To rotate, the view controller's `-shouldAutorotateToInterfaceOrientation:` must return `YES` for the appropriate orientations._

Choose the previously determined view rectangle appropriate for the new orientation.  Again, don't do anything if a 180-degree rotation.  Additionally, instruct the view to do any changes necessary to handle the new geometry (in this case via the `-didRotateScreenFromView:` method).  

```objective-c
if( oldLandscape != newLandscape ) {
	GCRect viewRect;
	if( UIDeviceOrientationIsLandscape( self.interfaceOrientation ) ) {
		viewRect = landRect;
	}
	    
	if( UIDeviceOrientationIsPortrait( self.interfaceOrientation ) ) {
		viewRect = portRect;
	}

	[glView didRotateScreenFromView:startView toView:endView duration:duration];
	glView.frame 	= viewRect;
}
```

_NOTE: Like before, `-didRotateScreenFromView:` needs to be called before changing the view bounds._

## Foreground orientation changes

### UIViewController

`UIViewController-shouldAutorotateToInterfaceOrientation:`  
Returns `YES` or `NO` on whether the device should rotate to a given orientation.  Return `YES` for all orientations you support.

`UIViewController-willRotateToInterfaceOrientation:duration:`  
Called before the UI rotation animation begins.

`UIViewController-willAnimateRotationToInterfaceOrientation:duration:`
Called during the UI rotation animation.  Properties that need to be animated during the rotation could be specified here.  Not used in this case because some properties are changed immediately and some are animated.

`UIViewController-didRotateFromInterfaceOrientation:`
Called after the UI rotation animation completes.

### Order of operations on a UI rotation

__Before rotation__

- `UIViewController-willRotateToInterfaceOrientation` called
- `-willRotateToInterfaceOrientation` changes `MainEditorView.frame`
- `MainEditorView.frame` change calls `layoutSubviews`
- calls `EAGLView` and `MainEditorView` `-frameBufferSetup`
- updates the GL framebuffer to match the new `CALayer` size
- calls `MainEditorView-drawView`
- Rotation animation occurs

__After rotation__

- `UIViewController-didRotateFromInterfaceOrientation` called
- `-willRotateToInterfaceOrientation` changes the `MainEditorView.frame`
- `MainEditorView.frame` change calls `layoutSubviews`
- calls `EAGLView` and `MainEditorView` `-frameBufferSetup`
- updates the GL framebuffer to match the new `CALayer` size
- calls `MainEditorView-drawView`

### Notes

`UIViewContentModeRedraw` calls `setNeedsDisplay` but that method does not do anything when called on a `CAEAGLLayer`

## Background orientation changes

The device orientation may change while the app is in the background.  To correctly display content when the app is resumed, the view controller must respond to `UIDeviceOrientationDidChangeNotification`.  

The notification will be sent any time there is an orientation change.  However, it only needs to be handled if the app is in the background.  The above code addresses all foreground orientation cases.

The view geometry will be set up at the time the app is resumed.  All that needs to be done here is any code specific to the view for the new orientation.  The `-didRotateScreenFromView:` method used above provides that functionality.

```objective-c
CGSize startView;
CGSize endView;
if ([glView inBackground]) {
    [glView didRotateScreenFromView:startView toView:endView];
}
```

# references

[notcited][#VCProg]
[notcited][#ViewProg]
[notcited][#UIViewController]
[notcited][#UIView]

[#UIViewGeometry]: _The Relationship of the Frame, Bounds, and Center Properties_.
Apple Developer documentation.
[URL][Frame Bounds and Center]

[Frame Bounds and Center]:	http://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewPG_iPhoneOS/WindowsandViews/WindowsandViews.html#//apple_ref/doc/uid/TP40009503-CH2-SW6

[#VCProg]: _View Controller programming guide for iOS_. 
Apple Developer documentation. 
[URL][View Controller programming guide for iOS].

[View Controller programming guide for iOS]: http://developer.apple.com/library/ios/#featuredarticles/ViewControllerPGforiPhoneOS/

[#ViewProg]: _View Programming Guide for iOS_.
Apple Developer documentation. 
[URL][View Programming Guide for iOS].

[View Programming Guide for iOS]:	http://developer.apple.com/library/ios/#documentation/WindowsViews/Conceptual/ViewPG_iPhoneOS/

[#UIApplication]: _UIApplication class reference_.
Apple Developer documentation.
[URL][UIApplication class reference]

[UIApplication class reference]:	http://developer.apple.com/library/ios/DOCUMENTATION/UIKit/Reference/UIApplication_Class/

[#UIScreen]: _UIScreen class reference_.
Apple Developer documentation.
[URL][UIScreen class reference]

[UIScreen class reference]:	http://developer.apple.com/library/ios/documentation/uikit/reference/UIScreen_Class/

[#UIViewController]: _UIViewController class reference_.
Apple Developer documentation. 
[URL][UIViewController class reference].

[UIViewController class reference]: http://developer.apple.com/library/ios/#documentation/UIKit/Reference/UIViewController_Class/

[#UIView]: _UIView class reference_.
Apple Developer documentation. 
[URL][UIView class reference].

[UIView class reference]:	http://developer.apple.com/library/ios/#documentation/UIKit/Reference/UIView_Class/
