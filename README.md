# Writing and refactoring for clean CSS

This note is meant as a guide for writing clean CSS, as well as refactoring bad CSS as you touch code. The problem is that people don't want to touch an old CSS style and break something. So bad styles stick around for a long time. We need to put in the extra effort of getting rid of those bad styles.


### General Guidelines
Our highest priorities are:
 + Semantics (readability)
 + Modularity (reusability, flexibility)
 + Efficiency (performance)
    - Keep performance in mind, but don't worry too much about it. The main advantage of using performant selectors is that it increases readability and modularity. For example, `.container *` is terrible not just because of performance, but also because it hurts flexibility and and requires future styles to use hacky overrides.
    - Steve Souders examined the performance impact of CSS selectors and determined that the delta between the best case and the worst case was 50ms. In other words, consider selector performance but don’t waste too much time on it.
      + http://www.stevesouders.com/blog/2009/03/10/performance-impact-of-css-selectors/
    - Nicole Sullivan's comment on the above article: "Micro-optimization of selectors is going a bit off track in a performance analysis of CSS. The focus should be on scalable CSS. There are two ways to measure 0(n) in CSS. What happens as you add more pages and modules to the site? Then you measure both file size, and HTTP requests. The way you write selectors has a huge impact on both of these that is much more significant than the time it takes the browser to process a reasonable number of selectors (even deeply nested)."

### Readability
+ Be specific with class names.
  - use `.profile-loading-indicator` instead of `.loading`. This is more reusable, less ambiguous, and easier to find in searches.
+ Namespace classnames. 
  - use `.menu-wide` instead of `.menu.wide`. This is easier to reuse and find in searches as well, and harder to create hacky combinations and overrides.
+ Use prefixes to group things together.
  - e.g. `.sidebar-foo`,  `.sidebar-bar`
+ Keep documenting. Whenever you add a new class or group of classes, leave comments to clarify what they're for, examples of where they're being used, etc. You want people to be able to read your CSS and understand your intentions, just as if you were writing in a programming language.

### Modularity
+ Avoid `!important`. People add it to override styles that are too specific. The correct solution is to avoid too many levels of nesting in the first place, so you never have to use `!important`. If you see too much nesting, this is a good place to refactor. If you have to use it, document your use of `!important`.
+ Write "object-oriented CSS". Whenever you add a rule, think of ways to make it modular and reusable.
  - Why "objected-oriented"? Because the same principles that apply to OO programming also apply here.
    + `button strong span .callout` is like an inheritance tree that's too deep.
    + It could have just been `button .callout` -- this way you can move `.callout` around more easily in the future and it's not tightly coupled with the DOM tree.
  - Separation of container from content: Children elements should not depend on parent element for styles. If the child needs an override, extend it with a new classname, e.g. `.alert-error,` instead of, e.g. `.followers .alert`.
   ```
  <div class="followers">
    <div class="alert alert-error"></div>
  </div>
    ```

### Performance
+ CSS rules parse right to left. As each element is drawn on the page, the browser checks if the CSS rule should apply to it. E.g. for `div ul li`, any time it draws an `li`, it checks if it's in a `ul`, then it checks if the `ul` is in a `div`.
+ Try and use a max of two selectors to target your element -- the fewer rules required to check for a given element, the faster style resolution will be.
+ Don't over-qualify selectors. E.g. you can just use `.content` instead of `div.content`, or `.social-link` instead of `.social .social-link`.
```
  <ul class="social">
    <li><a class="social-link" href="">Facebook</a></li>
  </ul>
```
+ Avoid universal rules/selectors: example `.container *`.
+ Avoid tag selectors, e.g. `.followers span`. They are neither performant, nor flexible for future scenarios where you might not want all spans to have this style. Use classnames instead. This also makes it more semantic and readable.

### Selector Efficiency
Below is the order of efficiency for selectors. Id's are the most efficient and pseudo classes and pseudo elements are the least efficient.

Even though id's are more efficient than classes, the difference is extremely small, so opt for classes when you can. Id's have to be unique, while classes are more flexible and reusable.

 1.	id (#myid)
 2.	class (.myclass)
 3.	tag (div, h1, p)
 4.	adjacent sibling (h1 + p)
 5.	child (ul > li)
 6.	descendent (li a)
 7.	universal (*)
 8.	attribute (a[rel="external"])
 9.	pseudo-class and pseudo element (a:hover, li:first)

### Refactoring
+ See if a style belongs in a different file.
+ Look for duplicated or unused CSS.
+ If you see something bad, don't be afraid to fix it. This is how bad styles manage to stick around for years. Ack for it, see where it's being used, and clean it up. Most of the time, it will be trivial. I sometimes find whole files that aren't being used any more.
