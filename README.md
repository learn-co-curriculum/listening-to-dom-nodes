# Listening to Nodes

## Objectives

1. Add an event listener to a DOM node
2. Trigger event listeners on DOM nodes
3. Explain the difference between bubbling and capturing events

If you are using the Learn IDE available in your browser, you will automatically clone down the files you need when you click 'Open IDE', but in order to view index.html, you will need to use httpserver to serve the HTML page temporarily. In the terminal, type httpserver and press enter. You will see that Your server is running at ... followed by a string of numbers and dots. This string is a temporary IP address that is hosting your index.html file. Copy this string of numbers, open a new tab and past the string in to the URL bar.

## Say what?

We've seen that we can easily manipulate nodes in the DOM, and even create and remove elements at will. But how do we interact with nodes on the page?

Well, we listen for them.

## `addEventListener()`

Adding an event listener to a DOM node is easy — we just call `addEventListener()` on the node. `addEventListener()` takes two arguments: the name of the event, and a function to handle the event.

Let's start by adding a listener for `click` events to the `main#main` element in `index.html`. Once you've opened `index.html` in the browser, enter the following in the browser's JS console:

```js
const main = document.getElementById('main')

main.addEventListener('click', function(event) {
  alert('I was clicked!')
})
```

Now if you click on the `main` element (you can click its text, "My ID is 'main'!"), you should see an alert: `'I was clicked!'. What's going on here?

The first argument, `'click'` is, as we've said, the name of the event we're listening for. Click events probably make up a majority of events listened to, but other common events are `change`, `'keydown'`, `'keyup'`, `'load'`, `'mouseover'`, `'mouseout'` — the list goes on. You can find a reasonably comprehensive list on [MDN](https://developer.mozilla.org/en-US/docs/Web/Events).

The second argument is a function that accepts the event as its argument. The event has a number of useful properties on it — keypress, keydown, and keyup events, for example, will have a `which` property that tells us which key was pressed. Let's add an event listener to the `input` element to get a feel for this.

```js
const input = document.querySelector('input')

input.addEventListener('keydown', function(e) {
  console.log(e.which)
})
```

You'll notice that, for example, pressing "enter" prints `13` in console; pressing "a" prints `65`; etc.

## Preventing the default behavior

Refresh the page. We've got a vendetta against the letter "g" (71), so we're going to prevent the input from receiving "g"s. Enter the following in your console:

```js
const input = document.querySelector('input')

input.addEventListener('keydown', function(e) {
  if (e.which === 71) {
    return e.preventDefault()
  }
})
```

Now try to enter "g" in the input — no can do!

Every DOM `event` comes with a `preventDefault` property — `preventDefault` is a function that, when called, will prevent the, well, default event from taking place. It provides us an opportunity to intercept and tweak user interactions (usually in more helpful ways than preventing them from typing "g").

Another, related event property is called `stopPropagation`. Like `preventDefault`, `stopPropagation` is a function that, when called, interrupts the event's normal behavior. In this case, it stops the event from triggering other nodes in the DOM that might be listening for the same event.

Wait. Do we mean one action can trigger multiple events?

Yep.

## Bubbling and Capturing

DOM events propagate by bubbling (starting at the target node and moving up the DOM tree to the root) and capturing (starting from the target node's parent elements and propagating down the tree until it reaches the target) — by default, events nowadays all bubble. We can verify this behavior by attaching listeners to those nested `div`s in `index.html`. Enter the following in your console:

```js
let divs = document.querySelectorAll('div')

function bubble(e) {
  // remember all of those fancy DOM node properties?
  // we're making use of them to get the number
  // in each div here!

  // if `this` is a bit confusing, don't worry —
  // for now, know that it refers to the div that
  // is triggering the current event handler.
  console.log(this.firstChild.nodeValue.trim() + ' bubbled')
}

for (let i = 0; i < divs.length; i++) {
  divs[i].addEventListener('click', bubble)
}
```

Now click on the `div` containing "5". You should see

```js
5 bubbled
4 bubbled
3 bubbled
2 bubbled
1 bubbled
```

What's going on? Well, the event starts at `div` 5, and then it bubbles up to the topmost node. Along the way, it triggers any other nodes that are listening for the event -- in this case, `'click'`.

Try clicking on a node that's not so deeply nested -- you should still see the event bubble up, starting at the node that you clicked and hitting every node up the tree until it reaches the top.

What about capturing? In order to capture, we need to set the third argument to `addEventListener` to `true`. Let's try it out.

```js
divs = document.querySelectorAll('div')

function capture(e) {
  console.log(this.firstChild.nodeValue.trim() + ' captured')
}

for (let i = 0; i < divs.length; i++) {
  // set the third argument to `true`!
  divs[i].addEventListener('click', capture, true)
}
```

Now click on `div` 5. You should see

```js
1 captured
2 captured
3 captured
4 captured
5 bubbled
5 captured
4 bubbled
3 bubbled
2 bubbled
1 bubbled
```

Now, the event propagates from the top of the page towards the target node, triggering event handlers as appropriate along the way.

Notice that the target node is the _last node to capture the event_, whereas it's the _first node to bubble the event up_. This is the most important takeaway.

**NOTE**: Don't worry if bubbling and capturing seems a bit esoteric. The different event behaviors are the results of the browser wars of the 90s, but most of the time it's safe just to stick to the default (which, for the record, is bubbling). You can read more about bubbling and capture on [StackOverflow](http://stackoverflow.com/questions/4616694/what-is-event-bubbling-and-capturing) and [QuirksMode](http://www.quirksmode.org/js/events_order.html)

## `stopPropagation()`

Now that you've learned a bit about the dangers and behavior of bubbling and capturing, you understand how events propagate through the DOM. Much of the time, since we're listening for very specific events, this doesn't matter: our events can propagate up or down, and they'll only trigger the event handler(s) that we want them to trigger. But sometimes, as with these `div`s, we have a fairly generic event that we want to constrain to its target. That's where `stopPropagation` comes in.

Let's rewrite the bubbling example to stop propagation so that only one event is triggered (be sure to reload the page before entering this code!):

```js
const divs = document.querySelectorAll('div')

function bubble(e) {
  // stop! that! propagation!
  e.stopPropagation()

  console.log(this.firstChild.nodeValue.trim() + ' bubbled')
}

for (let i = 0; i < divs.length; i++) {
  divs[i].addEventListener('click', bubble)
}
```

Now try clicking on any node — you should only see one log statement!

## Review

We covered a lot in this lesson. Feel free to edit `index.html`, to write code directly in the document (just put it between `<script></script>` tags), and to play around with different events. It's important to practice so you can get the hang of it!

You should now understand how to add an event listener, how different event triggers work, and how to intercept user interactions with `e.preventDefault()` and `e.stopPropagation().`


## Resources

- [MDN - Web Events](https://developer.mozilla.org/en-US/docs/Web/Events)
- [StackOverflow - Bubbling and Capturing](http://stackoverflow.com/questions/4616694/what-is-event-bubbling-and-capturing)
- [QuirksMode - Event order](http://www.quirksmode.org/js/events_order.html)

<p class='util--hide'>View <a href='https://learn.co/lessons/listening-to-dom-nodes'>Listening To Nodes</a> on Learn.co and start learning to code for free.</p>
