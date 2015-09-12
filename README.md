#Website Optimization

######Project Background
This is Project #4 for [Front-End Web Developer Nanodegree](https://www.udacity.com/course/front-end-web-developer-nanodegree--nd001). 
The goals were to optimize a given website which has multiple performance issues.

## Project Structure

#####Dev/
Contains development CSS, JS and images. Grunt project also lives in this directory.

#####Prod/
Contains the production ready, minified CSS, JS build from the Dev using Grunt.
 
## Optimization Details

###Index Page

The provided index page had a Google PageSpeed score of 30/100 for Desktop and 28/100 for Mobile. After making changes 
the score increased to 94/100 for both Desktop and Mobile.

The detail of changes are following:

######CSS

Added the [media="print"](http://www.w3.org/TR/CSS21/media.html) attribute to the external CSS link for print styles.
Minified CSS file using Grunt.

######JavaScript

Added the HTML [async](https://developer.mozilla.org/en-US/docs/Games/Techniques/Async_scripts) attribute to all script tags.
Minified JS file using Grunt.


###Sliding Pizzas

The following changes were made to achieve 60 FPS frame rate switch when scrolling.

Optimized the loops and use translateX() function to move around background pizzas.

```js
function updatePositions() {
  frame++;
  window.performance.mark("mark_start_frame");

  var scrollTop = document.body.scrollTop / 1250;
  var items = document.querySelectorAll('.mover');
  for (var i = 0; i < items.length; i++) {
    var phase = Math.sin(scrollTop + (i % 5));
    var left = items[i].basicLeft + 1000 * phase + 'px';
    items[i].style.transform = "translateX("+left+") translateZ(0)";
  }

  // User Timing API to the rescue again. Seriously, it's worth learning.
  // Super easy to create custom metrics.
  window.performance.mark("mark_end_frame");
  window.performance.measure("measure_frame_duration", "mark_start_frame", "mark_end_frame");
  if (frame % 10 === 0) {
    var timesToUpdatePosition = window.performance.getEntriesByName("measure_frame_duration");
    logAverageFrame(timesToUpdatePosition);
  }
}
```
Used `document.createDocumentFragment()` to create additional DOM tree which result better performance in this case. 

Removed height and width styles calculation/assignment from the generated pizza elements and resized the pizza image to 
prevent the browser from having to resize the images.

Reduce amount of sliding pizza element from 200 to 35 which still sufficiently cover the screen with sliding pizzas.

```js
document.addEventListener('DOMContentLoaded', function() {
  var cols = 8;
  var s = 256;
  var movingPizza = document.querySelector("#movingPizzas1");
  var fragment = document.createDocumentFragment();

  for (var i = 0; i < 35; i++) {
    var elem = document.createElement('img');
    elem.className = 'mover';
    elem.src = "images/pizza_small.png";
    elem.basicLeft = (i % cols) * s;
    elem.style.top = (Math.floor(i / cols) * s) + 'px';
    fragment.appendChild(elem);

  }
  movingPizza.appendChild(fragment);
  updatePositions();
});
```
