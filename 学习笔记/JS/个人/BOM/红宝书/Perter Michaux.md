- [peter.michaux.ca](http://peter.michaux.ca/)
-  

- [contents](http://peter.michaux.ca/)
-  

- [archives](http://peter.michaux.ca/archives)
-  

- [index](http://peter.michaux.ca/index)
-  

- [search](http://peter.michaux.ca/search)
-  

- [![RSS](http://peter.michaux.ca/imgs/feed.png)](http://peter.michaux.ca/feed/atom.xml)
-  

- [![Twitter](http://peter.michaux.ca/imgs/twitter.png)](http://twitter.com/petermichaux)
-  

- [![GitHub](images/Perter%20Michaux/github.png)](https://github.com/petermichaux)

# Feature Detection: State of the Art Browser Scripting

Published February 16, 2008 in [Feature Detection](http://peter.michaux.ca/index#Feature Detection), [JavaScript](http://peter.michaux.ca/index#JavaScript)

The goal of feature detection in browser scripting with JavaScript is straight forward. Before our code uses an object or object reference we want to know that our use of it will execute without error and behavior as we are expecting. If we don't know these things before we use the object or object reference then we don't want to use it because using it would risk a broken page and ultimately an unsatisfactory user experience.

The idea of feature detection has been around for many years. If you are writing a script for the general web, where anyone with a web browser can visit your page then you will likely need to spend a decent amount of time thinking about how the feature detection works and only add progressive enhancements to your page when you know those enhancements will work. Good feature detection is one of the fundamental indicators of a quality browser script...but developers still aren't using it! Look in all the mainstream JavaScript libraries. They sniff `navigator.userAgent` even when far better tests are well known.

This article compiles all of the various feature detection techniques I have thought about, encountered and learned from others. There are many. The ideas are not that complex. Sometimes determining the way to make the right test is complex but the ideas about testing procedures are not.

## Native and Host Objects

The distinction between native and host objects is important when discussing feature detection.

Native ECMAScript objects include `Object`, `Function`, `String`, and the global object. These are described in detail in the ECMAScript specification.

Host objects are non-native objects provided by the host environment and the ECMAScript specification is very permissive about how host objects can behave. This makes it possible to build a compliant ECMAScript implementation for many different applications: web browser or otherwise. Unfortunately this permissiveness makes it difficult to use feature detection even in a compliant ECMAScript implementation. Even more unfortunate is that not all web browsers are ECMAScript compliant so the job of the application programmer is harder still.

## Feature Testing a Native Object

Suppose the variable `arr` holds a reference to a native `Array` object and we want to execute `arr.pop()`. Before we let that code execute we want to be sure we've satisfied our goal that code will execute without error and as expected. This section looks at a few ways we could make our test.

### related execution inference

The first option is to just say, "Well, the ECMAScript is actually executing, `Array.prototype.pop` is part of the ECMAScript standard and has been around in the main browsers for a while, so I'm just going to assume the code will work." This is related execution inference. The logic is that we know the code is executing, we know the code is ECMAScript which is supposed to have `Array.prototype.pop` and so we will infer that the feature exists and works.

This inference is not necessarily a bad decision if we are calling `Array.prototype.join` because it was in the first implementations of JavaScript and JScript that supported `Array` and it has been in the ECMAScript standard since the first edition. It is not so easy to say that the related execution inference for `Array.prototype.pop` is a good idea. This is because the `pop` property was not available in browsers as recent as Internet Explorer 5 and perhaps some *modern* cellphones or other embedded devices. It is clearly a very bad idea to use related execution inference for the JavaScript 1.6 `Array.prototype.filter` property that will almost certainly be part of the ECMAScript 4th ed standard. The `filter` property is not widely supported and won't be for a long time. You may think Internet Explorer 5 is old and talking about support for `pop` is ridiculous. It isn't which features we talk about as these issues reoccur with new features as the language evolves.

Related execution inference does allow for false positives where we attempt to use a feature that does not exist. False positives when the script is out in the world being used are a complete failure of feature testing. There are no false negatives with this type of inference.

### specific existence inference

Another option is to test that at least a property called `"pop"` exists before we call it. We can write the following.

```
if (arr.pop) {
  arr.pop();
}
```

This type of test is specific existence inference because we are checking that, for the specific object we will call, at least the feature exists before inferring the feature will actually work.

Wrapping the test directly around the call to `pop` may be too late to make the test. We may have already made some operation with irreversible side-effects that we should not have made if this code will not execute successfully. This is a very common situation in browser scripting and we need to test before we end up with such a problem. Also, testing each time we use `pop` may be too computationally expensive with no real benefit. Testing just once is likely sufficient.

Specific existence inference does allow for false positives and negatives. With a false positive, the runtime error we risk seeing here is "arr.pop is not a function" in some non-compliant host.

### exemplar existence inference

Instead of testing for the `pop` property on the actual object `arr`, we can test for `pop` on a different `Array` instance which will serve as our exemplar. This is called exemplar existence inference because if the exemplar has the `pop` property we will infer that the code `arr.pop()` will run without error and as expected. Because we can create an exemplar `Array` at any time, we can test for `pop` before we go down some execution path that is irreversible.

Thanks to the prototypical nature of JavaScript, in this particular case, we don't need to create an exemplar `Array` instance. The `Array.prototype` object can serve as our exemplar.

```
if (Array.prototype.pop) {
  // irreversible code
  // ...
  arr.pop();
  // ...
}
```

Exemplar existence inference does allow for false positives and negatives and is a little less sure than specific existence inference. The tradeoff of better performance and easier development may be well justified.

### exemplar existence and `typeof` inference

We can make our tests more specific. Since we are calling the `pop` property we could do even better by checking that it is callable before we call it. The ECMAScript spec says the `typeof` operator will return `"function"` for a callable native object. So we can be more specific in our test and write.

```
if (typeof Array.prototype.pop == 'function') {
  //...
  arr.pop();
  //...
}
```

By being more specific with our test we stop non-compliant hosts, which return the wrong result for the `typeof` operation, from running the `pop` function. This could be a false negative. The browser may very well be capable of running `pop` but have a bug in the `typeof` implementation. It seems strange that by making a test more specific we might be making it *too* specific and induce false negatives. The reason for this is we are using `typeof` to test if a function can be called successfully. These may be completely unrelated operations in the guts of the ECMAScript implementation.

In practice, the more specific we make the tests the more likely the code will only run when it can run properly with a likely minor tradeoff that we may prevent a capable, non-compliant browser from running the code. Depending on the feature this may be a good or bad trade-off and this is likely where debates will rage about what is enough and what isn't. Deciding how much more restrictive to go beyond just existence inference requires a deep personal evaluation of your risk tolerance for false positives.

### exemplar use inference

The best way to know if `arr.pop()` will run successfully is to actually run it and see if it worked as expected after. There is a big problem with doing exactly that. The `pop` function has a side effect of modifying `arr` and so if the execution fails to work as expected, it may leave `arr` in some mangled state and we cannot fix the problems we have caused.

We can however try using `pop` on an exemplar `Array` instance. If the use works on the exemplar then we can infer it will work on any `Array` instance. This is why it is called exemplar use inference and it seems like a very small stretch of inference.

There are many ways we can perform this feature test with varying levels of paranoia. If we cannot use try-catch syntax because we need to support certain *modern* cellphones, *for example,* and we can accept the false negatives of the `typeof` inference for non-compliant browsers then we can write

```
var supportsPop = (function() {
  var a = [1,2];
  if (typeof a.pop == 'function') {
    var b = a.pop();
    return b == 2;
  }
  return false;
})();

// ...

if (supportsPop) {
  //...
  arr.pop();
  //...
}
```

If we are able to use a try-catch, we can avoid the possible false negatives in a buggy browser with `typeof` inference by writing...

```
var supportsPop = (function() {
  var a = [1,2];
  try {
    var b = a.pop();
    return b == 2;
  }
  catch (e) {
    return false;
  }
})();

if (supportsPop) {
  //...
  arr.pop();
  //...
}
```

**Exemplar use inference is the strongest practical feature test** because it doesn't need to run repeatedly but it really uses the feature and checks the feature works. It is a good tradeoff. I think more programmers should be using this type of inference. Perhaps not for `pop` but a good example is checking that necessary CSS is supported before manipulating the DOM for a widget. If you want to know that a feature works, why not just try it out?

## Feature Testing a Host Object

Welcome, the can of worms is now open.

You cannot trust host objects. They haven't earned it.

```
// Internet Explorer 7

var xhr = new ActiveXObject('Microsoft.XMLHTTP');
if (xhr.open) {}               // Error
typeof xhr.open                // 'unknown'
xhr.open('GET', '/foo', true); // works

  
// Safari 3

var el = document.getElementById('foo');
typeof el.childNodes // 'function'

typeof document.all  // 'undefined'
alert(document.all)  // [object HTMLCollection]
```

Just the above examples alone make feature testing host objects a complete mess. We don't give up, however, we do the best we can.

### unrelated execution inference

Unrelated execution inference is when we infer a host feature is available and will work because the ECMAScript is executing. Simply put we shouldn't use this type of inference...ever!

Just because ECMAScript is executing that doesn't indicate any particular host object exists because we don't know what host is executing the code. The code doesn't know where it's executing and we are not there with it to figure out the type of host. The code just tries to access objects. If an object isn't there the code likely errors. We need to test for all host features.

The two contentious objects that programmers may say don't need feature testing are the the `document` and `window` objects. They will say that if the code is executing then it is executing in a web browser because that is the only place they ever intended the code to execute. That may be their intention but for the sake of just two tests, they are throwing away the opportunity to make the code both much more robust, and runnable in a different host where they can use some parts of their code. They are just two little tests and make the difference from testing most host objects to finishing the job and testing all host objects. They are worth it.

### exemplar existence inference

When Internet Explorer implements a callable object as an ActiveX object, it is not possible to make a simple type converting test for the object's existance.

```
var xhr = new ActiveXObject('Microsoft.XMLHTTP');
if (xhr.open) {}               // Error
```

The error will be along the lines of "Object does not support this property or method" but actually it does. It is only possible to speculate why Internet Explorer's ActiveX objects behave this way but the ECMAScript spec seems to allow this in the first paragraph of section 8.6.2.

Internet Explorer is free to change it's implementation at any time so that more host objects are ActiveX objects. That means that even when a simple type converting test for a callable property works now, it may not work in the future. I've been told that Microsoft has actually made such a change but I haven't seen such a case working first hand.

We also don't really know which objects are currently implemented as ActiveX objects and so we can't use these simple type converting tests to check for the existence of a property. Thank you Microsoft.

### `typeof` inference for callable host objects

It seems that every time the ActiveX type converting test errors occurs, the following test works.

```
if (typeof xhr.open == 'unknown') {}
```

Most browsers will have a `typeof` result `'function'`.

As a third complication for callable host objects, non-ActiveX callable host objects in Internet Explorer have a `typeof` result `'object'` and we don't really know which objects are ActiveX or not.

What to do? The good news is that all the browsers are returning something for `typeof` and not throwing errors. We can use a `typeof` inference test for callable objects. How we design that test goes back to how worried we are about false positives and false negatives.

According to the ECMAScript spec, a callable host object can have a `typeof` result string that is anything. An empty string is ok. Even `'undefined'` is possible which is very unfortunate because that is what is also returned when an object doesn't exist. So in theory we cannot make a perfect test based on `typeof` alone. Fortunately the only host object that I've seen that has a `typeof` result `'undefined'` is Safari and `document.all`. (The Safari developers must not want their browser detected as Internet Explorer in the old `if (document.all)` browser sniffs from 1999 and even today unfortunately. Fair enough.)

The following test seems to be a pretty good one that works today.

```
// version 1
function isHostMethod(object, property) {
  var t = typeof object[property];  
  return t=='function' ||
         (!!(t=='object' && object[property])) ||
         t=='unknown';
}
```

You would use this function with a call like `isHostMethod(el, 'appendChild')` if you already know that `el` is a DOM object and you have the expectation that it should have a callable property with the name `'appendChild'`.

The reason for the `&& object[property]` is because in ECMAScript version 3, a `null` object has `typeof` result `'object'`. This is considered a bug and will change in ECMAScript version 4 so the `typeof` result is `'null'`.

Because a callable host object can legitimately have any `typeof` result, the above code could produce false negatives. That can be considered a bonus because it gives you time to examine the host in question and determine if you want to make this test more relaxed to allow that particular host to pass this test.

If you want to make a ECMAScript compliant test, are not worried about false positives and can use try-catch, then you could use the following.

```
// version 2
function isHostMethod(object, property) {
  var t = typeof object[property];
  if (t != 'undefined') {
    return true;
  }
  try {
    // Even if the typeof result is 'undefined',
    // check to see if the property exists by
    // type conversion.
    //
    // This test would error for ActiveX but
    // those test will have returned above 
    // because typeof result will have been 
    // 'unknown'.
    if (object[property]) {
      return true;
    }
  }
  catch (e) {}
}
```

If you want to go really risky you can simply write

```
// version 3
function isHostMethod(object, property) {
  return typeof object[property] == 'unknown' ||
         // Would error for ActiveX but
         // will have returned already.
         !!(object[property]);
}
```

There is quite a bit of concern about the false positives these last two versions could allow. I don't know of any cases where that would occur in actual browsers. The first version is simply more conservative and makes it so you have to manually open up the passing of the test to enable browsers that have new `typeof` results.

### `typeof` inference for host collections

So you want to examine the `childNodes` of a DOM Element. You probably just want to iterate over them and have no intention of calling the `childNodes` property. That makes sense. It is callable in some browsers and browsers return different `typeof` results.

```
// Firefox 2

var el = document.getElementById('foo');
typeof el.foo; // 'object'
  
// Safari 3

var el = document.getElementById('foo');
typeof el.foo; // 'function'
```

The following test seems to work across all the browsers today. It may produce false negatives but you can make it more permissive as you learn about new browsers' behaviors.

```
function isHostCollection(object, property) {
  var t = typeof object[property];  
  return (!!(t=='object' && object[property])) ||
         t=='function';
}
```

Note that versions 2 and 3 of `isHostMethod` would also work if you are not concerned about false positives. I have not heard about any real browser cases where there would be a false positive.

### existence inference for host collections

Host collections are a different situation than callable host objects. That is because a host collection constantly need to be accessed without calling them, for example, `el.childNodes`. A simple type converting test should work and would be ECMAScript compliant where the object could have any `typeof` result.

```
function isHostCollection(object, property) {
  return !!(object[property]);
}
```

This may give false positives in a buggy browser.

### `typeof` inference for non-callable, non-collection, host objects

The remainder of host objects are currently known, at least to me, to have `typeof` result `'object'`.

```
function isHostObject(object, property) {
  return !!(typeof(object[property]) == 'object' && object[property]);
}
```

### exemplar use inference for host features

This type of testing is a very good idea for host features. If you want to know how the box model works in a particular browser, add a dummy element to the page and move it around. Is the element positioned as you expect? If not then don't enable your widget that depends on this behavior. This also tests that CSS is even enabled at all. I have written a followup article showing this technique: [Cross-Browser Widgets](http://peter.michaux.ca/article/7217).

## A good set of host object testing functions

The following is a conservative set of functions you can use to test host objects. These reduce the chance of false positives. You can make them more permissive if you learn about a browser with a different `typeof` result that should be allowed to pass the test.

```
function isHostMethod(object, property) {
  var t = typeof object[property];  
  return t=='function' ||
         (!!(t=='object' && object[property])) ||
         t=='unknown';
}

function isHostCollection(object, property) {
  var t = typeof object[property];  
  return (!!(t=='object' && object[property])) ||
         t=='function';
}

function isHostObject(object, property) {
  return !!(typeof(object[property]) == 'object' && object[property]);
}
```

## Many Ways to Infer

Below is a summary of ways you might test that a feature is available and will work. It is not exhaustive. Some are good tests and some are bad.

### related execution inference

If the code is executing then infer that a language features exist and works. This seems to be acceptable for old language features (e.g. String.prototype.substring which was in JavaScript 1.0, JScript 1.0 and ECMAScript v1). For such an old feature, the tradeoff of code bulk and poorer performance for more robust inference tests is considered unjustified. These tests are so easy to write it is worth it to test middle-aged language features.

### unrelated execution inference

If the code is executing then infer that a host features exist and works. For example, many programmers assume that if JavaScript is executing then the necessary CSS support is also available. This is extremely poor quality feature testing.

### language version inference

The script type attributes with its version number will start to play a role in the future when ES4 is released. This happened a long time ago with the script language attribute and JavaScript versioning. That was before my time.

### syntax inference

Using syntax when it is not required where it is used, so that a particular browser will syntax error. For example, using `===` where `==` is sufficient just because somewhere else in the script the browser needs to support `document.getElementById` and IE4 doesn't have this feature. This is likely to produce false positives.

You can look at this the other way around. If a script is running and has new-ish syntax then infer other new-ish features are available and work.

### `navigator.userAgent` inference

Since browsers can and do lie in navigator.userAgent looking at substrings of this property is pointless. Very likely to give false positives. This is extremely poor quality feature testing.

### unrelated existence inference

If one object property exists, then infer that a different object property will work. Checking for the existence of `document.all` or `ActiveXObject` and then assuming the IE event model is available. Very likely to give false positives.

### related existence inference

Checking for `element.addEventListener` and then infering `event.preventDefault` is available and works. This example gives a false positive in Safari in version 1 and early version 2 releases where there is a problem with `preventDefault` when the event listener is added with `addEventListener`. Less likely to give false positives than unrelated existence inference but this is still sniffing.

### exemplar existence inference

Check that a property exists on an exemplary object. If it exists then infer that it will work on any similar object. This is the most common form of feature testing for language features. It should be sufficient if no one is messing with your native prototypes or objects between the time of the test and the use of the property.

### specific existence inference

This checks for the existence of a property on a particular object but doesn't assume another similar object will also have that property. If the feature exists on a particular object then assume it works on that object.

### exemplar `typeof` inference

If the `typeof` value of an object is one of the known or allowed values then infer the feature will work. This is white-listing.

### specific `typeof` inference

Similar to exemplar `typeof` testing but on the specific object being used. This is useful on the singletons like the `window`, `document`, and `external` objects.

### exemplar non-bad value inference

Check that a host object property that exists is not in a particular set of known bad values, then infer the feature will work. This is black listing. For example, if a host object property is `null` it is defined and exists but that value may be no good to you.

### specific non-bad value inference

Similar to example non-bad-value inference but on the actual object being used.

### exemplar object, exemplar use inference

Check that a property exists on an exemplar object and then try using it. If it works then infer it will work again on similar objects. Unlikely to give a false positive.

An [example](http://www.jibbering.com/faq/faq_notes/not_browser_detect.html#bdReplace) of exemplar object, exemplar use inference for String.prototype.replace.

### specific object, exemplar use inference

Similar to exemplar object, exemplar use inference but once on the actual object being used.

### specific object, specific use *testing*

Check that a property exists on a specific object, use it, and check that it worked as expected after each use. If this is done repeatedly then this is computationally expensive and likely not practical in many situations. If the feature executes but produces a poor result there is no guarantee you can reverse the operation. The reversal may fail.

## Summary

Feature testing is not easy and there is no one right answer. From a practical stand point, the more strict your tests are the more likely your code runs only when it will run successfully.

There are some obvious wrong answers like `navigator.UserAgent` inference, unrelated execution inference and unrelated object inference. Check the library you are using. Does it use any of these techniques? I have seen many uses of these techniques in mainstream libraries even where there is a well known, better test available. The mainstream libraries cannot consider claiming to be state of the art until they remove these bad practices at the very least.

The above discussion focuses on tests against a single object to infer if that object or similar ones work. Sometimes it may be necessary to use multiple objects together to make an inference about one object. An [example](http://www.jibbering.com/faq/faq_notes/not_browser_detect.html#bdScroll) for scroll reporting. Even when using multiple host objects in a test the three `isHost*` functions will be useful.

Feature detection is not easy, but as professionals, we should use the best tests we can.

### Acknowledgments

I would like to thank the contributors to Usenet's [comp.lang.javascript](http://groups.google.com/group/comp.lang.javascript/topics) for discussions over the past couple years about feature detection and particularly to David Mark for the long discussions over the past three months. David's code has stood the scrutiny of the group and are virtually unchanged in the three `isHost*` functions recommended in this article. I think all JavaScript and browser scripting programmers would benefit from participating on the comp.lang.javascript newsgroup. It isn't always friendly if you are looking for a pat on the back for bad code but the quality of most programmer's code will improve from the advice. Mine certainly has.

[A great article on feature detection.](http://www.jibbering.com/faq/faq_notes/not_browser_detect.html) The fact this article has needed so little revision since it was written years ago, is testament to the robustness that is good feature detection. I believe [Richard Cornford](http://www.litotes.demon.co.uk/) wrote the majority of the article.

[A definition of progressive enhancement](http://developer.yahoo.com/yui/articles/gbs/index.html#progressive-enhancement) that I like. I don't agree with everything on the page but I do like that definition.

### Updates

[detecting event support](http://thinkweb2.com/projects/prototype/detecting-event-support-without-browser-sniffing/) and [CSS support](http://thinkweb2.com/projects/prototype/feature-testing-css-properties/).

## Comments

Have something to write? [Comment on this article.](http://peter.michaux.ca/cgi-bin/comment-form.pl)

David Mark February 16, 2008

Excellent article. Hopefully the browser scripting community will take notice. History has shown that the majority ignore the debates and discussions in comp.lang.javascript.

Thanks for the acknowledgment.

Lennie February 17, 2008

"Good feature detection is one of the fundamental indicators of a quality browser script...but developers still aren't using it!"

It's not the features I'm worried about, it's the bugs in the implementation of those features.

That's why developers don't check for features.

I know, it's sad. But that's the reason why.

[Peter Michaux](http://peter.michaux.ca/) February 17, 2008

Lennie,

That is why part of feature detection is trying the feature to see if it works. The exemplar object, exemplar use inference I discuss is exactly for this purpose.

[Max](http://webbugtrack.blogspot.com/2007/08/bug-152-getelementbyid-returns.html) February 19, 2008

I'm with Lennie. In concept, you are dead on! but in reality, there are so many issues with this that it just isn't worth it.

Case in point. I want to know if a browser supports document.getElementById()

Well to be honest, I don't care about browsers that don't even have an implementation of this because, well, that was before Y2K even.

Anyway, if I run any test in IE on the above method, it will tell me it is defined, that it is a function that it takes 1 parameter, that it returns an element/null... but it won't tell me that the implementation is "Completely And Utterly Broken"

see here, and here:

- http://webbugtrack.blogspot.com/2007/08/bug-152-getelementbyid-returns.html
- http://webbugtrack.blogspot.com/2007/09/bug-154-getelementbyid-is-not-case.html

Thus I have to use browser sniffing, to determine if this is a known, affected version of IE, and apply hacks as necs.

In fact in IE, you can pretty much pick ***ANY\*** DOM Property/Method you want, and there WILL be a bug with it.

I'm not silly enough to sniff for "document.all" and assume IE, because we all know what that means... but running one or two quick tests to accurately determine the browser, saves me countless hours later on.

When I'm about to set the "cellpadding" and "cellspacing" on a table... I KNOW that I need to pass special code to IE.

In fact, for generic methods (either on the document or the window) I define a separate set (in a separate file) for each browser (that doesn't follow the specs).

Now when I call document.getElementById( id ) it doesn't matter what the browsers implementation is, because if it is IE (or an old version of Opera), I let the code sort out the bugs for me.

Max.

[Peter Michaux](http://peter.michaux.ca/) February 19, 2008

Max,

The `document.getElementsById` problems shouldn't happen because you can simply make sure the page's HTML doesn't have any `name`/`id` clashes or incorrect case; however, you can workaround the problem if need be. When `document.getElementsById` returns a DOM element you can check that the `id` attribute of that returned DOM element is what it should have been. If it is not, then you can iterate over the elements in `document.all` examining each one's `id` attribute. The code to do this is only a few lines.

The cellpadding and cellspacing issues can similarly be either designed out or tested. issue can be dealt with by adding a dummy table to the document and examining it's height and width, etc. I just posted a new article that shows building a tabbed pane in detail. I do something like this to ensure the panes really do show and hide.

It takes a mind shift to get into feature detection. First it is necessary to give up the idea that sniffs exist. Then set about solving the problem in a much more robust way. I hope you will give it a try.

[Bertrand Le Roy](http://weblogs.asp.net/bleroy) February 21, 2008

"There are some obvious wrong answers like navigator.UserAgent inference"

While this is true most of the times, and as others have pointed out, in practice, working around browser bugs and quirks without that technique is impractical at best. In principle you could detect the presence of those bugs through an example but if that requires manipulating the DOM significantly, it's an absolute no-go. Another area where nothing else works is /undetectable/ quirks and bugs. For example, there are bugs in url fragment handling that we use for back button management that are positively undetectable in practice and that require some form of user agent sniffing.

Never say never.

[Peter Michaux](http://peter.michaux.ca/) February 21, 2008

Bertrand,

The manipulation of the DOM will not usually be very extensive. How much DOM manipulation testing to use is a judgment call so you are comfortable inferring the page will work. If a feature bug is undetectable then that I believe that feature should be wrapped with a workaround or shouldn't be used at all on a page for the general web. The general web environment is too unpredictable to take such risks.

[Bertrand Le Roy](http://weblogs.asp.net/bleroy) February 21, 2008

We don't have a choice here, we can't afford not to take that kind of calculated risk.

[Peter Michaux](http://peter.michaux.ca/) February 21, 2008

Bertrand,

My goal for this article and my follow-up article [Cross-Browser Widgets](http://peter.michaux.ca/article/7217) is to present a choice.

[Bertrand Le Roy](http://weblogs.asp.net/bleroy) February 21, 2008

Oh sure, and they're great articles, it's just that dismissing user agent could be done with maybe some more pragmatism. Cheers.

[Ray Cromwell](http://timepedia.org/) February 22, 2008

The problem I see with so much feature detection code (besides the fact that there are side effects of many features which aren't testable) is that it will bloat the size code base, increase startup time, and potentially impact running time.

Thus, you trade potential future breakage on new browsers which may break sniffing code, by saddling everyone else with bloat in the present. Given that there is a strong correlation between app responsiveness and user retention, I'm not sure it's a tradeoff I'd be willing to make.

[Peter Michaux](http://peter.michaux.ca/) February 22, 2008

Ray,

Yes the code is bigger with feature detection but without feature detection the page is broken in anything but the current versions of a few browsers. Testing isn't just protection against future browser but protection against all browsers: past, current and future. I choose a working page over a broken page.

[Ray Cromwell](http://timepedia.org/) February 22, 2008

I think there are competing value systems here. On the one hand, the view that developer resources are finite, and one should put regrettably limited resources into doing the greatest good for the greatest number, which is where the majority of the browsing audience lives. Then there is the purist view, that one should do things 'correctly' (assuming there is an objective measurement of such), because running on Netscape 4.0 will make a difference to someone, somewhere.

The problem with the purist view is that code correctness that guarantees that the app will run doesn't guarantee it will run well enough to be usable. Game developers face this all the time. Just because your game adheres to OpenGL or DirectX, codes defensively to check extensions (OGL) or capabilities (DX) and falls back gracefully, doesn't mean, in the end result, that the game will run at a playable framerate.

So, while making sure that a tabbed panel still works on paleolithic browsers, you end up adding significant bloat to JS size and page startup. This may not be all that noticeable on a P4 with a DSL connection, but if one is worried about ancient browsers, shouldn't one also be worried about offending the legion of 56k dialup users out there, those Win95 users on 1Ghz or less PCs? By adding even 5k extra of compressed Javascript, page load time goes up by 1 second, and who knows how much slower JS execution is on these old browsers. The Javascript interpreters of yesteryear certainly weren't speed demons or memory efficient.

Thus, with finite development resources, I think I'd rather set a minimum requirement to use my app based on my targeted audience. I'd rather treat Netscape 4/IE4 users as having no JS at all, than put all that extra effort in. The return on investment doesn't seem that great to me.

Still, from a purist point of view, it feels cool and elegant to have code that gracefully degrades back to 1997. I just don't think it's practical.

[Peter Michaux](http://peter.michaux.ca/) February 22, 2008

Ray,

I appreciate the comments and they are certainly worth consideration.

Yes Netscape 4.0 on a Windows 3 machine is very old but there are cell phone around now that have very poor support for DHTML. We are also entering a new phase of browser evolution. The types of problems I've discussed that were critical in about 2001 are going to come back as being critical in 2009 with a whole new set of features. It isn't strictly a paleolithic problem.

About bloat. After running my tabbed pane and library code through the YUI compressor it is 6 442 bytes. The YUI tabbed pane and dependencies total 48 982 bytes! There are several ways to attack bloat and I think we need better ways to design libraries so we send only the minimal code necessary to the browser.

Feature testing should not affect runtime performance in a repeated way. The startup might be longer for the feature testing version but by focusing on small code in the first place we have room for the feature testing. I have little doubt that 6 442 bytes will start up faster than 48 982 bytes.

In terms of "practical", all of this feature testing business is not as onerous on the browser as it first appears. Yes adding more code means more download and startup time compared to the same code with feature testing removed, but in practice it is not significantly different. When we are developing for the general web, we are building simple widgets like tabbed panes, tree menus, draggable windows and draggable maps. We are not building video games. The results are significantly different because we don't have to frequently update browser sniffing techniques. Written working code should be touched as little as possible because regression testing is expensive. That is practical.

[Stephen DeGrace](http://www.infiniterecursion.ca/) September 10, 2009

I like the way you've broken down the idea of feature detection. This is a good article.

I don't agree with writing JavaScript exhaustively to work on the maximum number of platforms, though. This just removes the pressure on the developers of platforms to ensure that their JavaScript support will work with content their users want to access. They would be able to go on their merry way creating a greater and greater hell for JavaScript application developers, secure in the knowledge that these developers will simply twist themselves into ever more complicated knots.

Scripts should be written for only the majority of cases, taking into account the target audience, and taking a "reasonable" effort only to accommodate broken or deficient platforms. People who insist on using IE5, for example, should be punished by not being able to access the content they want. By deliberately ignoring IE5, developers are doing a huge public service by forcing the holdouts to upgrade if they want their content.

Simply writing for comparatively recent versions of Firefox, IE, Safari and Opera is really more than sufficient in terms of making efforts at interoperability. Perhaps mobile devices can be considered if this is an important audience for a particular application.

We have to put up with IE because the majority of users are on it and so its quirks have to be indulged. Firefox and Safari support are important for a significant subset of any audience, and W3C standards should always be supported because it is the moral thing to do and in an ideal world would be the best way to ensure interoperability. But at some point you simply have to shift the burden to the user to use a reasonable means of accessing the content they want.

How much can we be expected to coddle people?

[Peter Michaux](http://peter.michaux.ca/) September 10, 2009

Stephen,

I agree that a developer of most sites does not have to go into contortions to achieve the same behavior on IE5 as will be experienced by a user on a modern browser. That does not mean IE5 is ignored. It means feature test for the necessary features of a modern browser and then IE5 will *naturally* go down the degradation path. So users of IE5 will have a good, static user experience. The modern-browser user will have a good, dynamic user experience. As you point out, business decisions like which user groups are targeted will guide all decisions about browser support. There is no one-size-fits-all rule.

Asen Bozhilov January 8, 2010

Hello Peter. Excellent article.

I don't like any of feature tests related with `pop` in article.

```
if (arr.pop) {
  arr.pop();
}
```

ToBoolean(arr.pop) doesn't make a sense for arr.pop[[Call]], and can be error-prone in some case.

```
if (Array.prototype.pop) {
  arr.pop();
}
```

In so dynamic language like ECMAScript that can be harmful in three case:

- Shadow `pop` property in Prototype Chain.
- Cross window/frame scripting in browser environment.
- Object referred from `pop` doesn't have internal [[Call]] method.

For my is enough:

```
if (typeof obj.pop == 'function')
{
  obj.pop();
}
```

I don't need to check behavior of `pop` method. Expected behavior is in documentation. That test for my is more stable because:

- Resolve properties `obj` and `pop` from Scope and Prototype chains in the same way, in test and before call expression.
- Test `object' referred from `pop` for internal [[Call]] method, before i use that object as a part from `Call Expression`.

If i want to make a feature test for `pop` only once, i will be use different strategy.

```
if (typeof Array.prototype.pop != 'function')
{
  Array.prototype.pop = function() {
    //do pop
  };
}
arr.pop();
```

Of course i know disadvantages of that approach, with inserted property which doesn't have {DontEnum} attribute in Prototype chain. But here concern is other. With my feature test and augmentation of built-in `object' referred from `Array.prototype' property. Whenever i have native object which internal [[Prototype]] refer Array.prototype i can use directly `pop` property of that `object' as a part of `Call Expression`.

Of course here stay the problem with shadow property and cross window/frame scripting and i can use next lines:

```
if (arr.pop === Array.prototype.pop)
{
  arr.pop();
}
```

But that is useless and it is only overhead. Especially when `arr` is object created from my.

Regards and Happy New Year 2010 ;~)

Have something to write? [Comment on this article.](http://peter.michaux.ca/cgi-bin/comment-form.pl)

© 2006-2018 Peter Michaux