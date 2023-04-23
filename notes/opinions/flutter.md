---
share: true
---

# Flutter

## Flutter Good

Flutter is an easy S tier, it does so many things right.

I'm mostly going to be comparing Flutter with the web and Android since those are the environments I have the most experience in, they also played a role in how Flutter was designed.

It's clear that much of Flutter's core design came from the desire to fix shortcomings of the web, which is not a coincidence, Dart was made by the creators of V8 (such as Lars Bak) and Flutter was made by people responsible for the modern web (such as Ian Hickson, founder of WHATWG and chief editor of HTML5).

The Flutter project has always played the long game, they re-invented everything from the ground up to create an ecosystem that is cohesive and futureproof.

The framework code is extremely high quality, the team consistently holds themselves to high engineering standards. The [style guide](https://github.com/flutter/flutter/wiki/Style-guide-for-Flutter-repo) on the Flutter wiki has a lot of good takes that shows their experience building software at scale.

These positives come with some obvious drawbacks though, new features take forever to land and bugs are often left open for months due to the team having so much on their plate.

## Rendering

Because rendering in browsers is done by an black-box web engine, CSS *has* to be bloated, otherwise lots of things would simply be impossible to build. What Flutter did to solve this problem was move rendering to userspace. The API is designed so well that you can change any aspect of rendering (other than compositing) from plain Dart.

Laying stuff out in Flutter is soooo simple when you get the hang of it, instead of having to memorize a dictionary's worth of css properties you can compose simple layout widgets to create complex layouts

Oh and LayoutBuilder is amazing, no other UI framework can replicate it AFAIK.

It is admittedly a pain in the butt to implement some layouts from scratch, I created the [Boxy](https://boxy.wiki/) package to overcome that.

## Package Ecosystem

The Dart team invested a lot of time in promoting a healthy package ecosystem, specifically:

* Everything is analyzed and publishes documentation, anything exported by a package is visible on Pub.
* Incentivizes code quality and documentation with a point system.
* No unpublishing, your project can pull the same version of a package indefinitely.
* Package manager comes with the SDK and isn't a hack.

It's stunning that none of the largest package repositories (npm, pip, nuget, maven) have unified package documentation, I should not have to read source code or crawl through tutorials to know what classes I can use.

A few years ago I said the biggest weakness of Flutter was it being new and the lack of packages, but boy there has been an explosion in new ones lately.

## State Management

Lots of people complain about the lack of consensus on state management patterns, but its not that big of a deal, patterns like MVVM and MVC are irrelevant because of Flutter's declarative widget system.

Just pick an architecture you are productive with and roll with it. My advice is to avoid packages that are just global variables (like GetX and get_it) and choose something that makes your code reliable and easy to test (like riverpod, bloc, provider, mobx).

Personally I use Riverpod for DI, a custom [improved version of hooks](https://gist.github.com/PixelToast/8ea4495637a366f340a3eca8bf528388), and rxdart. Built-in Futures and Streams are an underrated tool for state management, particularly when it comes to error handling.

FutureBuilder and StreamBuilder are admittedly super verbose and can't read values synchronously from an rxdart ValueStream, if you are looking for a drop-in replacement try the async_builder package.

I dislike redux style state management (actions / reducers on immutable data) it tends to be much too verbose because of Dart being strongly object oriented, using it over plain services with methods feels like a downgrade.

I really dislike result types in Dart (such as from fpdart), in practice they do nothing to help error management, the codebases that use them almost always ignore or rethrow Result errors in a fragile way.

My opinions on reducers and result types might change if/when dart has macros and discriminated unions, these patterns are much easier to work with in languages that do, like Rust.

## Future

I'm pretty confident it will surpass other UI frameworks because of the project's ability to consistently make successful long-term investments.

Re-implementing core widgets instead of using native ones is a good example of this, it sucked in the beginning but now that the material library is feature complete and cross-platform, its reeeeeally hard to argue against.

Another example where long-term investments have paid off is Flutter's true hot reload that preserves full program state, other frameworks have primitive versions of hot reload which are inevitably going to be replaced with one similar to Dart's when the JS / Java / Swift runtimes implement it properly.

## Performance

Performance is a complicated topic, there are many small things that need to be addressed individually:

1. Shader warmup
	* This was mostly fixed on iOS and is much less noticeable on Android, in my experience building (very pretty) production apps it has never been a big issue. Impeller should fix any remaining issues with warmup.
2. Web
	* Yes the performance on web kinda sucks, I wouldn't use flutter web for most consumer web apps.
	* No, the DOM isn't a solution, the rendering pipeline of Flutter is fundamentally incompatible, LayoutBuilder is impossible in the web for example.
3. Compilation times
	* Compilation times have gotten noticeably worse, particularly after Dart 2.0 and the migration to the CFE, I'm not sure what is going on there, would be really nice if it was faster.
	* I don't have any numbers but anecdotally native Android seems slower at first (creating a new project, syncing gradle, etc.) but incremental compilation is a little faster.
4. Rebuilds
	* A common misconception that beginners have is that StatefulWidget is bad and you should avoid rebuilds at all costs, this is wrong, I could make a whole article on this. A rule of thumb is if something isn't likely to update every frame, don't bother optimizing for rebuild speed.
	* Rebuilding something does not mean it gets re-rendered, RenderObjects only mark themselves for needing layout or paint when the properties actually change, so a redundant rebuild can actually be super cheap.
	* If you observe a slow rebuild causing jank during an animation, try implementing it in something more efficient like a CustomPainter with your Animation passed to `repaint`.
	* Finally, if the build overhead is unavoidable and you just want consistent frame times, there is a package called flutter_smooth which does some clever stuff to preempt rendering.
5. Async
	* I dunno, never experienced performance issues with async.
	* There were some bad benchmarks GetX did that compared the performance of Stream, which may have been a source of confusion in the community.
	* Ten times faster than nothing is still nothing, the overhead of a callback is on the scale of nanoseconds so a relative difference doesn't make a large impact to your program.

## Flutter Web

Flutter web kinda sucks in a lot of aspects, it feels like you are using an emulator, the performance is often poor, it takes a long time to load, accessibility is nonexistent, SEO, no inspect element.

The reasons it sucks is not because Flutter did a bad job but because the web is generally hostile to new frameworks. To run code on the web you are forced to use either JavaScript, which is slow and has inconvenient semantics, or WASM, which is slightly faster but has limited interop. 

There is no comprehensive API for accessibility on web, Flutter has its own accessibility system, the semantics tree, and does its best to translate that to ARIA attributes. To get anything better we will need proactive support from screen readers like JAWS, Vox, and Mac VoiceOver, or wait for the web to standardize something more flexible than ARIA.

These problems are not isolated to Flutter, Qt also struggled with the same problems for years before Flutter Web hit stable, just try out some of the [example apps](https://www.qt.io/qt-examples-for-webassembly).

There is push from multiple directions to make the web more modular and WASM more performant, this will certainly get better in the coming years.

## Macros / JSON

Having to use build_runner to implement JSON serialization is certainly the most annoying thing I deal with day to day, and it is kinda ridiculous how long it's taking to implement alternatives.

Our savior is [macros](https://github.com/dart-lang/language/blob/main/working/macros/feature-specification.md), a language proposal that will replace all of the dirty code generators we rely on today.

Personally I don't think it's enough though, I want to write plugins that directly interact with the AST in the CFE and maybe even add custom tokens to fasta.