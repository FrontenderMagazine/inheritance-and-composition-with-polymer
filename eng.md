14 Jul 2014 
You've probably heard of this new hot thing that will change the way we build
web applications in the future, right? Exactly. I'm talking about
[Web Components][1]. Web Components is a new technology that lets us build our
own components for the web with the same tools the browser vendors use.

Actually, Web Components are a set of four different specifications. These are
**HTML Imports**, **HTML Templates**, **Custom Elements** and **Shadow DOM**. I
will not go into much detail here since that's not the scope of this article. If
you want to learn more about Web Components in general, I recommend checking out
the community project[webcomponents.org][2], which is full of useful resources
and information about the technologies.

With evolving standards come evolving tools, libraries and frameworks. When it
comes to web components, there are basically two frameworks that try to make 
your life easier:[X-Tags][3] by Mozilla and [Polymer][4], a Google project. **
Both use the same polyfills under the hood** to make the four web component
technologies work in all major browsers. However, whereas X-Tags API abstracts 
only the imperative way of building web components, goes Polymer a step further 
and also provides a declarative way.

For example, defining your own custom element with Polymer looks something like
this:

    <span class="nt"><polymer-element</span> <span class="na">name=</span><span class="s">"my-custom-element"</span> <span class="na">noscript</span><span class="nt">></span>
      <span class="nt"><template></span>
        <span class="c"><!-- shadow dom --></span>
        <span class="nt"><div></span>My custom element<span class="nt"></div></span>
      <span class="nt"></template></span>
    <span class="nt"></polymer-element></span>
    

To use the created element, all you have to do is to import it via HTML Imports
:

    <span class="c"><!-- import custom element --></span>
    <span class="nt"><link</span> <span class="na">rel=</span><span class="s">"import"</span> <span class="na">href=</span><span class="s">"path/to/my-custom-element.html"</span><span class="nt">></span>
    
    <span class="nt"><my-custom-element></my-custom-element></span>
    

That's all you need to create your own element with Polymer. Taking a deeper
look at what's happening here, we can actually see all four technologies in 
action. We create a custom element (the actual registration of it on the DOM 
happens behind the scenes in that case. Polymer takes care of it.), we use HTML 
Template to define our custom elements template, which gets later injected into 
its Shadow DOM. And finally, to actually use our element, we import it with HTML
Imports.

There's much more to discover in the world of Polymer. To get a feeling of what
else is possible, I recommend reading their docs[on their website][5].

## Extending existing elements

One of the biggest things that come with web components, is the fact that you
can extend existing elements. And since your own custom elements are also just 
HTML elements, you can extend them as well as native elements. From a surface 
point of view there's no differents between native elements and custom elements.

Let's take a look at how we can extend existing elements with Polymer. To
extend an existing element, Polymer again comes with a nice declarative way via 
HTML. Simply use the`extends` attribute on your elements definition and apply
the name of the element you want to extend as value.

Before we extend an element, let's quickly build an element that is actually
more useful. Here's the definition of a`basic-button` element:

    <span class="nt"><polymer-element</span> <span class="na">name=</span><span class="s">"basic-button"</span> <span class="na">noscript</span><span class="nt">></span>
      <span class="nt"><template></span>
        <span class="nt"><span><content></content></span></span>
      <span class="nt"></template></span>
    <span class="nt"></polymer-element></span>
    

Another addition you see here is the use of the `<content>` element. With
`<content>` elements you can define so called "Insertion Points".
Insertion Points are the places in your Shadow DOM where the contents of your 
actual used element will land. For example using our`basic-button` like this:

    <span class="nt"><basic-button></span>Hello<span class="nt"></basic-button></span>
    

Will internally result with a Shadow DOM like this:

If you're familiar with [AngularJS][6], an insertion point is to Polymer what
transclusion is to Angular, sort of. Insertion points actually go a step further
but I will not go into much detail here.

Alright, now let's extend our `basic-button` element. We want to have a button
element that always has an icon, so our new element will be called`icon-button`
`extends` attribute to extend our existing `basic-button` element:

    <span class="nt"><polymer-element</span> <span class="na">name=</span><span class="s">"icon-button"</span> <span class="na">extends=</span><span class="s">"basic-button"</span><span class="nt">></span>
      <span class="nt"><template></span>
        <span class="nt"><span></span>
          <span class="nt"><i</span> <span class="na">class=</span><span class="s">"icon"</span><span class="nt">></i><shadow></shadow></span>
        <span class="nt"></span></span>
      <span class="nt"></template></span>
      <span class="nt"><script></span>
        <span class="nx">Polymer</span><span class="p">(</span><span class="s1">'icon-button'</span><span class="p">);</span>
      <span class="nt"></script></span>
    <span class="nt"></polymer-element></span>
    

There are a few new things in this snippet, so let's take a deeper look. First
, there's this`<shadow>` element. `<shadow>` is very powerful
because it lets extend the Shadow DOM of your element with the contents of the 
parent elements Shadow DOM. We also use the`Polymer` constructor here to
explicitly register our custom element (we'll see in a minute why
).

Using our extended element stays straight forward:

    <span class="nt"><icon-button></span>Hello<span class="nt"></icon-button></span>
    

Depending on your use case, you might want to use the `is=""` syntax to extend
an existing element in your application code. So instead of using your extended 
element as tag, you would rather do something like this:

    <span class="nt"><basic-button</span> <span class="na">is=</span><span class="s">"icon-button"</span><span class="nt">></basic-button></span>
    

Again, this depends on your use case. In this example it probably doesn't make
much sense to use this syntax.

If there's additional functionality you want to add, for example a `ready`
callback handler, you can define it directly on the constructor call. To make 
sure that the parent element callback handler is called too, you can call
`this.super()` which does exactly that:

    <span class="nx">Polymer</span><span class="p">(</span><span class="s1">'icon-button'</span><span class="p">,</span> <span class="p">{</span>
      <span class="nx">ready</span><span class="o">:</span> <span class="kd">function</span> <span class="p">()</span> <span class="p">{</span>
        <span class="c1">// gets called once component is ready</span>
        <span class="nx">console</span><span class="p">.</span><span class="nx">log</span><span class="p">(</span><span class="s1">'icon-button ready'</span><span class="p">);</span>
    
        <span class="c1">// call parent `ready` handler</span>
        <span class="k">this</span><span class="p">.</span><span class="kr">super</span><span class="p">();</span>
      <span class="p">}</span>
    <span class="p">});</span>
    

You can also override existing methods and access data-bound properties.
Curious how? Find out more[here][7].

## Extending multiple elements

Now that we know how to extend an element with Polymer, you might wonder how we
can extend multiple elements. For example let's say have a web component that 
extends existing elements in a way that it makes them draggable. We could use it
by applying it to existing elements like this:

    <span class="c"><!-- draggable img --></span>
    <span class="nt"><img</span> <span class="na">is=</span><span class="s">"draggable"</span><span class="nt">></span>
    
    <span class="c"><!-- draggable something --></span>
    <span class="nt"><div</span> <span class="na">is=</span><span class="s">"draggable"</span><span class="nt">></div></span>
    

So how can we extend multiple elements? You think there has to be a way to say
something like`extends="foo bar"` right? Unfortunately, the answer is **no**.
Or to make it more clear:

**It's not possible to extend multiple elements**

You won't find anything in the official docs and you can also read about it in
this[StackOverflow question][8]. However, there are some ways to get *some*
result when it comes to extending multiple elements.

## Composition over inheritance

Basically it's always better to prefer composition over inheritance at a first
glance, to stay flexible. So instead of explicitly declare which element extends
which other element to reuse functionality, it's better to define new elements 
that**make use** of existing elements. 

We could take our `icon-button` element and break it down into three smaller
elements using composition. For example we could have a`basic-icon` element, a
`basic-button` element and a `icon-button` element, that does **not**
explicitly extend`basic-button`, but uses it in it's Shadow DOM. Then we could
also define a`icon-link` element, that uses `basic-icon` and another 
`basic-link` element (or whatever comes to your mind).

To get an idea how far you can actually break down things, just take a look at
[Polymer Paper Elements][9]. Another project you should keep an eye on is the
[Basic Web Components][10] project. This project even defines its own 
[principles][11] that are heavily based on composability.

## Reuse functionality with mixins

However, even if we're able to compose new elements with existing elements,
there are cases where we really just want to reuse existing functionality. Just 
think about the`draggable` example. Having the functionality of being draggable
is not really about composing a new element out of existing elements. It's 
rather about sharing functionality across multiple elements.

Can we do that with Polymer? Yes we can. Polymer supports mixins. With mixins
we're able to extends existing components with shared functionality without 
explicitly extending other elements. All we have to do is to isolate our shared 
functionality and mix it into our components constructor using`Polymer.mixin()`

For example let's say we have a mixin object that provides functionality that
should be shared across multiple components:

    <span class="kd">var</span> <span class="nx">sharedMixin</span> <span class="o">=</span> <span class="p">{</span>
      <span class="c1">// define shared functions and properties here</span>
    <span class="p">};</span>
    

Plain old JavaScript. Now, if we want to reuse the provided functionality, all
we have to do is to extend the constructor of our component using
`Polymer.mixin()` like so:

    <span class="nx">Polymer</span><span class="p">(</span><span class="s1">'my-component'</span><span class="p">,</span> <span class="nx">Polymer</span><span class="p">.</span><span class="nx">mixin</span><span class="p">({</span>
      <span class="c1">// component logic</span>
    <span class="p">},</span> <span class="nx">sharedMixin</span><span class="p">);</span>
    

## Sharing mixins across multiple imports

With the power of Web Components it's super easy to make shared mixins
available across multiple imports. Even if the term "Web Components" sounds 
super special, it's still just HTML, CSS and JavaScript. Same tools. Same rules.

So, if we want to make a mixin available globally across multiple imports, all
we have to do is to put our shared mixin into it's own document that does 
nothing but defining our mixin on the global namespace. Our`shared.html` could
look something like this:

    <span class="nt"><script></span>
      <span class="nb">window</span><span class="p">.</span><span class="nx">sharedMixin</span> <span class="o">=</span> <span class="p">{</span>
        <span class="c1">// logic goes here</span>
      <span class="p">};</span>
    <span class="nt"></script></span>
    

And thank HTML Imports, we can just import it into our own component as we
would do with other full-fledged web components:

    <span class="nt"><link</span> <span class="na">rel=</span><span class="s">"import"</span> <span class="na">href=</span><span class="s">"path/to/shared.html"</span><span class="nt">></span>
    
    <span class="nt"><element</span> <span class="na">name=</span><span class="s">"foo-element"</span><span class="nt">></span>
      <span class="nt"><script></span>
        <span class="nx">Polymer</span><span class="p">(</span><span class="s1">'foo-element'</span><span class="p">,</span> <span class="nx">Polymer</span><span class="p">.</span><span class="nx">mixin</span><span class="p">({</span>
          <span class="c1">// foo-element logic</span>
        <span class="p">},</span> <span class="nx">sharedMixin</span><span class="p">);</span>
      <span class="nt"></script></span>
    <span class="nt"></element></span>
    

(Since I struggled especially with that part, I posted another 
[StackOverflow question][12] here.)

I hope this article helped you understanding inheritance and composition with
Polymer.

 [1]: http://webcomponents.org/
 [2]: http://webcomponents.org
 [3]: http://www.x-tags.org/
 [4]: http://polymer-project.org
 [5]: http://www.polymer-project.org/docs/polymer/polymer.html
 [6]: http://angularjs.org

 [7]: http://www.polymer-project.org/docs/polymer/polymer.html#extending-other-elements

 [8]: http://stackoverflow.com/questions/24157576/how-to-extend-multiple-elements-with-polymer
 [9]: https://github.com/Polymer?query=paper
 [10]: https://github.com/basic-web-components/

 [11]: https://github.com/basic-web-components/components-dev/wiki/Ten-Principles-for-Great-General-Purpose-Web-Components

 [12]: http://stackoverflow.com/questions/24180622/is-it-possible-to-share-mixins-across-web-components-and-imports-in-polymer