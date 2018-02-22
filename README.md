# Style composition (with SASS)

In our current standard web development, CSS is still the No.1 choices in styling. This means we inherit styling from element (tag) and attributes (class,id) by default. Thus, coding in styling, we are facing the same [banana and gorilla](https://www.johndcook.com/blog/2011/07/19/you-wanted-banana/) problem in OO programing. This page is a quick guide of how to improve code quality in styling with [Composition over inheritance](https://youtu.be/wfMtDGfHWpA) (in SASS)

Composition over inheritance can improve styling code quality in following sections

## Gain flexibility and robustness
Styling is always based on its root because of CSS inheritance, and this feature covers some deeper issues in scaling up process

Here is a sample code of inheritance styling

**CSS**
```
p {
	font-size: 12px;
	line-height: 1.5
}

.heading {
	color: blue
}
```
**HTML**
```
<p class="heading"> this is heading style </p>
```
In this inheritance styling, the element `.heading` has inherited `font-size: 12px; line-height: 1.5` from `p` with its own `color: blue`. This would be fine for initial development, but could become an issue when we need to update the HTML. 

Let's say we have a SEO requirement: update tag to `h2` (instead of `p`) in HTML. Based on the code above, the style will break (missing font style), since the `.heading` is relying on `p` tag to provide font style.

To solve this problem, we could use style composition
```
@mixin p {
	font-size: 12px;
	line-height: 1.5
}

p {
	@include p;
}

.heading {
	@include p;
	color: blue
}

```
Since the `.heading` has all required style, when html `p` tag change to `h2`, nothing will change.

In the composition code, styling code is relatively more independent from HTML, as a flexible and robust styling should be. This flexibility and robustness of coding could dramatically reduce code reconstruction in the process of scaling up, since styling sections are independent from others. 

## Improve code readability 
Let's reorganize the initial inheritance code, and make it more realistic as below

**file-a.scss**
```
p {
	font-size: 12px;
	line-height: 1.5
}
```
**file-b.scss**
```
.heading {
	color: blue
}
```
When we are maintaining `file-b.scss`, since we could not see `p` styling, we may NOT be able to aware the base style from `p`. This visibility issue could create technical debts (like unnecessary duplications), if we were having a tight deadline; and it is also harder to be noticed in reviewing process like pull request. 

In the contrary, our composition code could presenting all the styling code at one place.

**bass.scss**
```
@mixin p {
	font-size: 12px;
	line-height: 1.5
}
```
**file-a.scss**
```
p {
	@include p;
}
```
**file-b.scss**
```
.heading {
	@include p;
	color: blue
}

```
With `@include p`, this composition styling code is more readable and is self-documented.

In our next section, we will improve the code a little more with a realistic change request

## Reduce side effects in styling
Now we just received a new design update, the default `p` tag will need extra spacing, while `.heading` should be untouched. Since our code is not fully following the composition style, we will need to update code as below

**file-a.scss**
```
p {
	@include p;
	margin-top: 8px; // the actual design update
}
```
**file-b.scss**
```
.heading {
	@include p;
	margin-top: 0; // the reset design update
	color: blue
}

```
**HTML**
```
<p class="heading"> this is heading style </p>
```
Noticing that `margin-top: 0;` is NOT the styling that is required by `.heading`, but it is needed to cover the side effects from `p`. That is because `.heading` still inherited unnecessary styling from `p` via HTML.

Here is our better composition code to rescue

**file-a.scss**
```
p:not([class]) {
	@include p;
	margin-top: 8px; // the actual design update
}
```
**file-b.scss**
```
.heading {
	@include p;
	color: blue
}

```
As we added `:not([class])` for `p`, the `margin-top: 8px;` only relevant to `p` styling with no class. Thus there will be no styling from `p` when we have a class. Now `.heading` has full control of the styling.

---------
## Summary
The main idea of using style composition is try to limit the inheritance between styling sections. That could be between HTML and CSS (as wed demonstrated), or CSS and CSS

Compare to inheritance, style composition is:
 - more flexible and robust
 - more readable code
 - less likely to have side effects

style composition is still consistent (as inheritance), as long as we are using sharing styling code like `@include p`.

All of above made style composition a better candidate for scalable structure

## Extra 
I would like to address one thing before we close the topic: 

that is **Choosing third party library/plugin/framework wisely**: 

Composition over inheritance is main reason many experienced developers are NOT using styling frameworks (like bootstrap) as the base of their scalable project. Because with OOCSS framework as the base, we will have to start our project with override framework (the design will never be the same as bootstrap style) in inheritance styling. Following with other change requests, the styling of project will be extremely difficult to maintain very soon.

NOTE: I am not saying we shall hand code everything. For instance, the bootstrap OOCSS part is not suitable for scaling, but its HTML structure is (for things like accessability). It is the same standard for integrating Javascript library/plugins, as long as the library/plugin/framework is suitable the composition styling, we could integrate them with our scalable project

## The end
I hope this page could give you some ideas of how to improve styling code with composition. Thank you for your time and happy styling
