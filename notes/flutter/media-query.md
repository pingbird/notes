---
share: true
title: MediaQuery
---

One of the most common questions asked by beginners in Flutter help channels is "How do I make my widget responsive?" or "How do I make my UI look the same on all devices?" and the most common answer is to use MediaQuery or a similar package that sizes widgets based on a fraction of the screen size.

In this article we will break down common misconceptions that led to MediaQuery unfortunaztely becoming so common and why fractional sizing is bad practice in geneal.

This applies to not just MediaQuery but also the popular `sizer`, `responsive_sizer`, and `flutter_screenutil` packages, which change the size of widgets proportional to the size of the screen.

## Fractional Sizing

You might find this surprising but fractional sizing is generally bad design, it is much better to use the powerful [built-in layout widgets](https://docs.flutter.dev/ui/widgets/layout) and breakpoints to achieve a desired layout.

Here's what a typical use of MediaQuery looks like:

```dart
SizedBox(
  width: MediaQuery.of(context).size.width * 0.4,
  height: MediaQuery.of(context).size.width * 0.1,
  child: ElevatedButton(
    onPressed: () {},
    child: const Text('Do a Thing'),
  ),
),
```

### "I want my app to look the same regardless of the size of device"

This is not something you want in practice, responsiveness is all about how your layouts adapt to the strengths and weaknesses of the device, simply scaling the same layout up and down does not create a good experience.

To illustrate this, I've created a news app about my favorite bird that properly utilizes built-in layout widgets:

![](https://i.tst.sh/1685589984433963.png)

This is a scale model of a phone and tablet, we can see that text and padding is pretty much the same between the two and the layout adapts well to the extra space. This works as intended without any special tweaks, Flutter (just like native apps) use a pixel scaling factor set by the device manufacture which ensures text and other UI elements are a similar *physical* size regardless of platform.

Our Amazing Pigeons app adapts to different screen dimensions in a few different ways here:

1. Padding is automatically applied to the AppBar to keep the status bar and notch out of the way.
2. The welcome text uses a combination of SizedBox, Row, and Expanded to let the text wrap on small displays but not look too wide on bigger ones.
3. The Wrap widget is used to distribute tag chips according to how much space is available.
4. The carousel uses an AspectRatio to prevent the images from being smushed on big or small screens.
5. The carousel tiles use a Stack to overlay text while still allowing it to wrap, supplying the left/right parameters to Positioned ensures the text has the right constraints.
6. Just like the welcome text, the "Join the Flock" button and its accompanying text field are wrapped in a SizedBox and Center to ensure it isn't too wide.
7. Everything is wrapped in a ListView so we can add more content vertically without overflowing.

Understanding Flutter's layout protocl and getting in the habit of applying these techniques is well worth it, most of the time you don't need to think about overflows or screen sizes, just let the framework do its thing.

If we take the lazy approach instead and use a package like `sizer` to scale the whole app, it looks like this:

![](https://i.tst.sh/1685590653573075.png)

Everything now has the same relative size but is way too big on the tablet, the app bar, text, and buttons take up twice as much space as they would in a native application. The difference in aspect ratio also means less content is visible on the tablet than the phone, yuck!

### "Our design used fractional sizes and I need it to be pixel-perfect"

This excuse is often given when you were handed a design and told to implement it exactly as shown, this unfortunately a very common situation that I sympathize with. Perhaps this article can persuade you (or your teammates) to break out of the mindset of everything having to be pixel-perfect.

Building apps is always going to be an iterative process, both the wireframes and code are going to evolve over time as you get feedback or develop new features. Suggesting (and implementing) changes to a design is important part of that process, it might just require more effective communication.

The finished product is always going to look slightly different from what was envisioned, it's up to you (the app developer) to make those slight differences lead to a better user experience.

### "Wouldn't text be too small on bigger displays?"

Not at all. Widgets might consume a smaller fraction of the space but bigger displays also consume more of the user's field of view.

Text sizing is the most common thing I see initial app designs get wrong, it's difficult to know what feels good until you've played with it on a physical device and switched to and from other apps. Generally you should refer to the material guidelines described in [TextTheme](https://api.flutter.dev/flutter/material/TextTheme-class.html) which puts body text at 14px:

![](https://i.tst.sh/1685597520289625.png)

If you still aren't convinced, compare these text sizes to a Google search:

![](https://i.tst.sh/1685600070803315.png)
Google doesn't seem to think 14px fonts are too small, and neither should you. Consistency with other apps leads to a better user experience and users can configure text sizes in system settings if they prefer it to be bigger or smaller.
