---
title: Looping code
slug: Learn/JavaScript/Building_blocks/Looping_code
tags:
  - Article
  - Beginner
  - CodingScripting
  - DO
  - Guide
  - JavaScript
  - Learn
  - Loop
  - break
  - continue
  - for
  - l10n:priority
  - while
---
<div>{{LearnSidebar}}</div>

<div>{{PreviousMenuNext("Learn/JavaScript/Building_blocks/conditionals","Learn/JavaScript/Building_blocks/Functions", "Learn/JavaScript/Building_blocks")}}</div>

<p>Programming languages are very useful for rapidly completing repetitive tasks, from multiple basic calculations to just about any other situation where you've got a lot of similar items of work to complete. Here we'll look at the loop structures available in JavaScript that handle such needs.</p>

<table>
 <tbody>
  <tr>
   <th scope="row">Prerequisites:</th>
   <td>Basic computer literacy, a basic understanding of HTML and CSS, <a href="/en-US/docs/Learn/JavaScript/First_steps">JavaScript first steps</a>.</td>
  </tr>
  <tr>
   <th scope="row">Objective:</th>
   <td>To understand how to use loops in JavaScript.</td>
  </tr>
 </tbody>
</table>

<h2 id="Keep_me_in_the_loop">Keep me in the loop</h2>

<p>Loops, loops, loops. As well as being associated with <a href="https://en.wikipedia.org/wiki/Froot_Loops">popular breakfast cereals</a>, <a href="https://en.wikipedia.org/wiki/Vertical_loop">roller coasters</a>, and <a href="https://en.wikipedia.org/wiki/Loop_(music)">musical production</a>, they are also a critical concept in programming. Programming loops are all to do with doing the same thing over and over again, which is termed <strong>iteration</strong> in programming speak.</p>

<p>Let's consider the case of a farmer who is making sure he has enough food to feed his family for the week. He might use the following loop to achieve this:</p>

<p><br>
 <img alt="" src="loop_js-02-farm.png"></p>

<p>A loop usually has one or more of the following features:</p>

<ul>
 <li> A <strong>counter</strong>, which is initialized with a certain value — this is the starting point of the loop ("Start: I have no food", above).</li>
 <li>A <strong>condition</strong>, which is a true/false test to determine whether the loop continues to run, or stops — usually when the counter reaches a certain value. This is illustrated by "Have I got enough food?" above. Let's say he needs 10 portions of food to feed his family.</li>
 <li>An <strong>iterator</strong>, which generally increments the counter by a small amount on each successive loop until the condition is no longer <code>true</code>. We haven't explicitly illustrated this above, but we could think about the farmer being able to collect say 2 portions of food per hour. After each hour, the amount of food he has collected is incremented by two, and he checks whether he has enough food. If he has reached 10 portions (the point where the condition is no longer true, so the loop exits), he can stop collecting and go home.</li>
</ul>

<p>In {{glossary("pseudocode")}}, this would look something like the following:</p>

<pre>loop(food = 0; foodNeeded = 10) {
  if (food &gt;= foodNeeded) {
    exit loop;
    // We have enough food; let's go home
  } else {
    food += 2; // Spend an hour collecting 2 more food
    // loop will then run again
  }
}</pre>

<p>So the amount of food needed is set at 10, and the amount the farmer currently has is set at 0. In each iteration of the loop, we check whether the amount of food the farmer has is larger or equal to the amount he needs. If so, we can exit the loop. If not, the farmer spends an hour collecting two portions of food and the loop runs again.</p>

<h3 id="Why_bother">Why bother?</h3>

<p>At this point, you probably understand the high-level concepts behind loops, but you are probably thinking "OK, great, but how does this help me write better JavaScript code?" As we said earlier, <strong>loops are all to do with doing the same thing over and over again</strong>, which is great for <strong>rapidly completing repetitive tasks</strong>.</p>

<p>Often, the code will be slightly different on each successive iteration of the loop, which means that you can complete a whole load of tasks that are similar but slightly different; if you've got a lot of different calculations to do, you want to do each different one, not the same one over and over again!</p>

<h4>Looping code example</h4>

<p>Let's look at an example to illustrate why loops are such a good thing. Let's say we wanted to draw 100 random circles on a {{htmlelement("canvas")}} element (press the <em>Update</em> button to run the example again and again to see different random sets):</p>

<pre class="brush: html hidden">&lt;!DOCTYPE html&gt;
&lt;html&gt;
  &lt;head&gt;
    &lt;meta charset="utf-8"&gt;
    &lt;title&gt;Random canvas circles&lt;/title&gt;
    &lt;style&gt;
      html {
        width: 100%;
        height: inherit;
        background: #ddd;
      }

      canvas {
        display: block;
      }

      body {
        margin: 0;
      }

      button {
        position: absolute;
        top: 5px;
        left: 5px;
      }
    &lt;/style&gt;
  &lt;/head&gt;
  &lt;body&gt;

  &lt;button&gt;Update&lt;/button&gt;

  &lt;canvas&gt;&lt;/canvas&gt;

    &lt;script&gt;
    const btn = document.querySelector('button');
    const canvas = document.querySelector('canvas');
    const ctx = canvas.getContext('2d');

    let WIDTH = document.documentElement.clientWidth;
    let HEIGHT = document.documentElement.clientHeight;

    canvas.width = WIDTH;
    canvas.height = HEIGHT;

    function random(number) {
      return Math.floor(Math.random()*number);
    }

    function draw() {
      ctx.clearRect(0,0,WIDTH,HEIGHT);
      for (let i = 0; i &lt; 100; i++) {
        ctx.beginPath();
        ctx.fillStyle = 'rgba(255,0,0,0.5)';
        ctx.arc(random(WIDTH), random(HEIGHT), random(50), 0, 2 * Math.PI);
        ctx.fill();
      }
    }

    btn.addEventListener('click',draw);

    &lt;/script&gt;

  &lt;/body&gt;
&lt;/html&gt;</pre>

<p>{{ EmbedLiveSample('Looping_code_example', '100%', 400) }}</p>

<h4>With and without a loop</h4>

<p>You don't have to understand all the code for now, but let's look at the part of the code that actually draws the 100 circles:</p>

<pre class="brush: js">for (let i = 0; i &lt; 100; i++) {
  ctx.beginPath();
  ctx.fillStyle = 'rgba(255,0,0,0.5)';
  ctx.arc(random(WIDTH), random(HEIGHT), random(50), 0, 2 * Math.PI);
  ctx.fill();
}</pre>

<ul>
 <li><code>random(x)</code>, defined earlier in the code, returns a whole number between <code>0</code> and <code>x-1</code>.</li>
 <li><code>WIDTH</code> and <code>HEIGHT</code> are the width and height of the inner browser window. </li>
</ul>

<p>You should get the basic idea — we are using a loop to run 100 iterations of this code, each one of which draws a circle in a random position on the page. The amount of code needed would be the same whether we were drawing 100 circles, 1000, or 10,000. Only one number has to change.</p>

<p>If we weren't using a loop here, we'd have to repeat the following code for every circle we wanted to draw:</p>

<pre class="brush: js">ctx.beginPath();
ctx.fillStyle = 'rgba(255,0,0,0.5)';
ctx.arc(random(WIDTH), random(HEIGHT), random(50), 0, 2 * Math.PI);
ctx.fill();</pre>

<p>This would get very boring and difficult to maintain very quickly. Loops really are the best.</p>

<h2 id="The_standard_for_loop">The standard for loop</h2>

<p>Let's start exploring some specific loop constructs. The first, which you'll use most of the time, is the <a href="/en-US/docs/Web/JavaScript/Reference/Statements/for">for</a> loop. This has the following syntax:</p>

<pre>for (initializer; condition; final-expression) {
  // code to run
}</pre>

<p>Here we have:</p>

<ol>
 <li>The keyword <code>for</code>, followed by some parentheses.</li>
 <li>Inside the parentheses we have three items, separated by semi-colons:
  <ol>
   <li>An <strong>initializer</strong> — this is usually a variable set to a number, which is incremented to count the number of times the loop has run. It is also sometimes referred to as a <strong>counter variable</strong>.</li>
   <li>A <strong>condition</strong> — as mentioned before, this defines when the loop should stop looping. This is generally an expression featuring a comparison operator, a test to see if the exit condition has been met.</li>
   <li>A <strong>final-expression</strong> — this is always evaluated (or run) each time the loop has gone through a full iteration. It usually serves to increment (or in some cases decrement) the counter variable, to bring it closer to the point where the condition is no longer <code>true</code>.</li>
  </ol>
 </li>
 <li>Some curly braces that contain a block of code — this code will be run each time the loop iterates.</li>
</ol>

<h3>Listing my cats</h3>

<p>Let's look at a real example so we can visualize what these do more clearly.</p>

<pre class="brush: html hidden">
  &lt;p&gt;&lt;/p&gt;
</pre>

<pre class="brush: js">const cats = ['Bill', 'Jeff', 'Pete', 'Biggles', 'Jasmin'];
let info = 'My cats are called ';
const para = document.querySelector('p');

for (let i = 0; i &lt; cats.length; i++) {
  info += cats[i] + ', ';
}

para.textContent = info;</pre>

<p>This gives us the following output:</p>

<p>{{ EmbedLiveSample('Listing_my_cats', '100%', 80) }}</p>

<div class="note">
<p><strong>Note:</strong> You can find this <a href="https://github.com/mdn/learning-area/blob/master/javascript/building-blocks/loops/basic-for.html">example code on GitHub</a> too (also <a href="https://mdn.github.io/learning-area/javascript/building-blocks/loops/basic-for.html">see it running live</a>).</p>
</div>

<p>This shows a loop is used to iterate over the items in an array and do something with each of them — a very common pattern in JavaScript. Here:</p>

<ol>
 <li>The counter variable (sometimes known as an initializer or an iteration variable), <code>i</code>, starts at <code>0</code> (<code>let i = 0</code>).</li>
 <li>The loop has been told to run until <code>i</code> is no longer smaller than the length of the <code>cats</code> array. This is important — the condition is the condition under which the loop will still run. So in this case, while <code>i &lt; cats.length</code> is still true, the loop will still run.</li>
 <li>Inside the loop, we concatenate the current loop item (<code>cats[i]</code> , which is <code>cats[whatever i is at the time]</code>) along with a comma and space, onto the end of the <code>info</code> variable. So:
  <ol>
   <li>During the first run, <code>i = 0</code>, therefore <code>cats[0] + ', '</code> (which is equal to <code>Bill, </code>) will be concatenated onto <code>info</code>.</li>
   <li>During the second run, <code>i = 1</code>, so <code>cats[1] + ', '</code> (which is equal to <code>Jeff, </code>) will be concatenated onto <code>info</code>.</li>
   <li>And so on. After each time the loop has run, 1 will be added to <code>i</code> (<code>i++</code>), then the process will start again.</li>
  </ol>
 </li>
 <li>When <code>i</code> becomes equal to <code>cats.length</code> (in this case, 5), the loop will stop, and the browser will move on to the next bit of code below the loop.</li>
</ol>

<div class="note">
<p><strong>Note:</strong> We have made the condition <code>i &lt; cats.length</code>, not <code>i &lt;= cats.length</code>, because computers count from 0, not 1 — we are starting <code>i</code> at <code>0</code>, and going up to <code>i = 4</code> (the index of the last array item). <code>cats.length</code> returns 5, as there are 5 items in the array, but we don't want to get up to <code>i = 5</code>, as that would return <code>undefined</code> for the last item (there is no array item with an index of 5). So, therefore, we want to go up to 1 less than <code>cats.length</code> (<code>i &lt;</code>), not the same as <code>cats.length</code> (<code>i &lt;=</code>).</p>
</div>

<div class="note">
<p><strong>Note:</strong> A common mistake with conditions is making them use "equal to" (<code>===</code>) rather than say "less than or equal to" (<code>&lt;=</code>). If we wanted to run our loop up to <code>i = 5</code>, the exit condition would need to be <code>i &lt;= cats.length</code>. If we set it to <code>i === cats.length</code>, the loop would not run at all because <code>i</code> is not equal to <code>5</code> on the first loop iteration, so it would stop immediately.</p>
</div>

<h3>Handling the last cat</h3>

<p>One small problem we are left with is that the final output sentence isn't very well-formed:</p>

<blockquote>
<p>My cats are called Bill, Jeff, Pete, Biggles, Jasmin,</p>
</blockquote>

<p>Ideally, we want to change the concatenation on the final loop iteration so that we haven't got a comma on the end of the sentence. Well, no problem — we can quite happily insert a conditional inside our for loop to handle this special case:</p>

<pre class="brush: html hidden">
  &lt;p&gt;&lt;/p&gt;
</pre>

<pre class="brush: js">
const cats = ['Bill', 'Jeff', 'Pete', 'Biggles', 'Jasmin'];
let info = 'My cats are called ';
const para = document.querySelector('p');

for (let i = 0; i &lt; cats.length; i++) {
  if (i === cats.length - 1) {
    info += 'and ' + cats[i] + '.';
  } else {
    info += cats[i] + ', ';
  }
}

para.textContent = info;
</pre>

<p>{{ EmbedLiveSample('Handling_the_last_cat', '100%', 80) }}</p>

<div class="note">
<p><strong>Note:</strong> You can find this <a href="https://github.com/mdn/learning-area/blob/master/javascript/building-blocks/loops/basic-for-improved.html">example code on GitHub</a> too (also <a href="https://mdn.github.io/learning-area/javascript/building-blocks/loops/basic-for-improved.html">see it running live</a>).</p>
</div>

<div class="warning">
<p><strong>Warning:</strong> With for — as with all loops — you must make sure that the initializer is incremented or, depending on the case, decremented, so that it eventually reaches the point where the condition is not true. If not, the loop will go on forever, and either the browser will force it to stop, or it will crash. This is called an <strong>infinite loop</strong>.</p>
</div>

<h2 id="Exiting_loops_with_break">Exiting loops with break</h2>

<p>If you want to exit a loop before all the iterations have been completed, you can use the <a href="/en-US/docs/Web/JavaScript/Reference/Statements/break">break</a> statement. We already met this in the previous article when we looked at <a href="/en-US/docs/Learn/JavaScript/Building_blocks/conditionals#switch_statements">switch statements</a> — when a case is met in a switch statement that matches the input expression, the <code>break</code> statement immediately exits the switch statement and moves on to the code after it.</p>

<p>It's the same with loops — a <code>break</code> statement will immediately exit the loop and make the browser move on to any code that follows it.</p>

<p>Say we wanted to search through an array of contacts and telephone numbers and return just the number we wanted to find? First, some simple HTML — a text {{htmlelement("input")}} allowing us to enter a name to search for, a {{htmlelement("button")}} element to submit a search, and a {{htmlelement("p")}} element to display the results in:</p>

<pre class="brush: html">&lt;label for="search"&gt;Search by contact name: &lt;/label&gt;
&lt;input id="search" type="text"&gt;
&lt;button&gt;Search&lt;/button&gt;

&lt;p&gt;&lt;/p&gt;</pre>

<p>Now on to the JavaScript:</p>

<pre class="brush: js">
const contacts = ['Chris:2232322', 'Sarah:3453456', 'Bill:7654322', 'Mary:9998769', 'Dianne:9384975'];
const para = document.querySelector('p');
const input = document.querySelector('input');
const btn = document.querySelector('button');

btn.addEventListener('click', function() {
  let searchName = input.value.toLowerCase();
  input.value = '';
  input.focus();
  for (let i = 0; i &lt; contacts.length; i++) {
    let splitContact = contacts[i].split(':');
    if (splitContact[0].toLowerCase() === searchName) {
      para.textContent = splitContact[0] + '\'s number is ' + splitContact[1] + '.';
      break;
    } else if (i === contacts.length-1)
      para.textContent = 'Contact not found.';
  }
});</pre>

<p>{{ EmbedLiveSample('Exiting_loops_with_break', '100%', 100) }}</p>

<ol>
 <li>First of all, we have some variable definitions — we have an array of contact information, with each item being a string containing a name and phone number separated by a colon.</li>
 <li>Next, we attach an event listener to the button (<code>btn</code>) so that when it is pressed some code is run to perform the search and return the results.</li>
 <li>We store the value entered into the text input in a variable called <code>searchName</code>, before then emptying the text input and focusing it again, ready for the next search. Note that we also run the <code><a href="/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/toLowerCase">toLowerCase()</a></code> method on the string, so that searches will be case-insensitive.</li>
 <li>Now on to the interesting part, the for loop:
  <ol>
   <li>We start the counter at <code>0</code>, run the loop until the counter is no longer less than <code>contacts.length</code>, and increment <code>i</code> by 1 after each iteration of the loop.</li>
   <li>Inside the loop, we first split the current contact (<code>contacts[i]</code>) at the colon character, and store the resulting two values in an array called <code>splitContact</code>.</li>
   <li>We then use a conditional statement to test whether <code>splitContact[0]</code> (the contact's name, again lower-cased with <code><a href="/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/toLowerCase">toLowerCase()</a></code>) is equal to the inputted <code>searchName</code>. If it is, we enter a string into the paragraph to report what the contact's number is, and use <code>break</code> to end the loop.</li>
  </ol>
 </li>
 <li>
  <p>After <code>(contacts.length-1)</code> iterations, if the contact name does not match the entered search the paragraph text is set to "Contact not found.", and the loop continues looping until the condition is no longer true.</p>
 </li>
</ol>

<div class="note">
<p><strong>Note:</strong> You can view the <a href="https://github.com/mdn/learning-area/blob/master/javascript/building-blocks/loops/contact-search.html">full source code on GitHub</a> too (also <a href="https://mdn.github.io/learning-area/javascript/building-blocks/loops/contact-search.html">see it running live</a>).</p>
</div>

<h2 id="Skipping_iterations_with_continue">Skipping iterations with continue</h2>

<p>The <a href="/en-US/docs/Web/JavaScript/Reference/Statements/continue">continue</a> statement works in a similar manner to <code>break</code>, but instead of breaking out of the loop entirely, it skips to the next iteration of the loop. Let's look at another example that takes a number as an input, and returns only the numbers that are squares of integers (whole numbers).</p>

<p>The HTML is basically the same as the last example — a simple text input, and a paragraph for output.</p>

<pre class="brush: html">
&lt;label for="number"&gt;Enter number: &lt;/label&gt;
&lt;input id="number" type="text"&gt;
&lt;button&gt;Generate integer squares&lt;/button&gt;

&lt;p&gt;Output: &lt;/p&gt;
</pre>

<p>The JavaScript is mostly the same too, although the loop itself is a bit different:</p>

<pre class="brush: js">
const para = document.querySelector('p');
const input = document.querySelector('input');
const btn = document.querySelector('button');

btn.addEventListener('click', function() {
  para.textContent = 'Output: ';
  let num = input.value;
  input.value = '';
  input.focus();
  for (let i = 1; i &lt;= num; i++) {
    let sqRoot = Math.sqrt(i);
    if (Math.floor(sqRoot) !== sqRoot) {
      continue;
    }
    para.textContent += i + ' ';
  }
});</pre>

<p>Here's the output:</p>

<p>{{ EmbedLiveSample('Skipping_iterations_with_continue', '100%', 100) }}</p>

<ol>
 <li>In this case, the input should be a number (<code>num</code>). The <code>for</code> loop is given a counter starting at 1 (as we are not interested in 0 in this case), an exit condition that says the loop will stop when the counter becomes bigger than the input <code>num</code>, and an iterator that adds 1 to the counter each time.</li>
 <li>Inside the loop, we find the square root of each number using <a href="/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/sqrt">Math.sqrt(i)</a>, then check whether the square root is an integer by testing whether it is the same as itself when it has been rounded down to the nearest integer (this is what <a href="/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/floor">Math.floor()</a> does to the number it is passed).</li>
 <li>If the square root and the rounded down square root do not equal one another (<code>!==</code>), it means that the square root is not an integer, so we are not interested in it. In such a case, we use the <code>continue</code> statement to skip on to the next loop iteration without recording the number anywhere.</li>
 <li>If the square root is an integer, we skip past the <code>if</code> block entirely, so the <code>continue</code> statement is not executed; instead, we concatenate the current <code>i</code> value plus a space on to the end of the paragraph content.</li>
</ol>

<div class="note">
<p><strong>Note:</strong> You can view the <a href="https://github.com/mdn/learning-area/blob/master/javascript/building-blocks/loops/integer-squares.html">full source code on GitHub</a> too (also <a href="https://mdn.github.io/learning-area/javascript/building-blocks/loops/integer-squares.html">see it running live</a>).</p>
</div>

<h2 id="while_and_do_..._while">while and do ... while</h2>

<p><code>for</code> is not the only type of loop available in JavaScript. There are actually many others and, while you don't need to understand all of these now, it is worth having a look at the structure of a couple of others so that you can recognize the same features at work in a slightly different way.</p>

<p>First, let's have a look at the <a href="/en-US/docs/Web/JavaScript/Reference/Statements/while">while</a> loop. This loop's syntax looks like so:</p>

<pre>initializer
while (condition) {
  // code to run

  final-expression
}</pre>

<p>This works in a very similar way to the <code>for</code> loop, except that the initializer variable is set before the loop, and the final-expression is included inside the loop after the code to run, rather than these two items being included inside the parentheses. The condition is included inside the parentheses, which are preceded by the <code>while</code> keyword rather than <code>for</code>.</p>

<p>The same three items are still present, and they are still defined in the same order as they are in the for loop. This is because you must have an initializer defined before you can check whether or not the condition is true. The final-expression is then run after the code inside the loop has run (an iteration has been completed), which will only happen if the condition is still true.</p>

<p>Let's have a look again at our cats list example, but rewritten to use a while loop:</p>

<pre class="brush: js">let i = 0;

while (i &lt; cats.length) {
  if (i === cats.length - 1) {
    info += 'and ' + cats[i] + '.';
  } else {
    info += cats[i] + ', ';
  }

  i++;
}</pre>

<div class="note">
<p><strong>Note:</strong> This still works just the same as expected — have a look at it <a href="https://mdn.github.io/learning-area/javascript/building-blocks/loops/while.html">running live on GitHub</a> (also view the <a href="https://github.com/mdn/learning-area/blob/master/javascript/building-blocks/loops/while.html">full source code</a>).</p>
</div>

<p>The <a href="/en-US/docs/Web/JavaScript/Reference/Statements/do...while">do...while</a> loop is very similar, but provides a variation on the while structure:</p>

<pre>initializer
do {
  // code to run

  final-expression
} while (condition)</pre>

<p>In this case, the initializer again comes first, before the loop starts. The keyword directly precedes the curly braces containing the code to run and the final expression.</p>

<p>The differentiator here is that the condition comes after everything else, wrapped in parentheses and preceded by a <code>while</code> keyword. In a <code>do...while</code> loop, the code inside the curly braces is always run once before the check is made to see if it should be executed again (in while and for, the check comes first, so the code might never be executed).</p>

<p>Let's rewrite our cat listing example again to use a <code>do...while</code> loop:</p>

<pre class="brush: js">let i = 0;

do {
  if (i === cats.length - 1) {
    info += 'and ' + cats[i] + '.';
  } else {
    info += cats[i] + ', ';
  }

  i++;
} while (i &lt; cats.length);</pre>

<div class="note">
<p><strong>Note:</strong> Again, this works just the same as expected — have a look at it <a href="https://mdn.github.io/learning-area/javascript/building-blocks/loops/do-while.html">running live on GitHub</a> (also view the <a href="https://github.com/mdn/learning-area/blob/master/javascript/building-blocks/loops/do-while.html">full source code</a>).</p>
</div>

<div class="warning">
<p><strong>Warning:</strong> With while and do...while — as with all loops — you must make sure that the initializer is incremented or, depending on the case, decremented, so the condition eventually becomes false. If not, the loop will go on forever, and either the browser will force it to stop, or it will crash. This is called an <strong>infinite loop</strong>.</p>
</div>

<h2 id="Active_learning_Launch_countdown">Active learning: Launch countdown</h2>

<p>In this exercise, we want you to print out a simple launch countdown to the output box, from 10 down to Blastoff. Specifically, we want you to:</p>

<ul>
 <li>Loop from 10 down to 0. We've provided you with an initializer — <code>let i = 10;</code>.</li>
 <li>For each iteration, create a new paragraph and append it to the output <code>&lt;div&gt;</code>, which we've selected using <code>const output = document.querySelector('.output');</code>. In comments, we've provided you with three code lines that need to be used somewhere inside the loop:
  <ul>
   <li><code>const para = document.createElement('p');</code> — creates a new paragraph.</li>
   <li><code>output.appendChild(para);</code> — appends the paragraph to the output <code>&lt;div&gt;</code>.</li>
   <li><code>para.textContent =</code> — makes the text inside the paragraph equal to whatever you put on the right-hand side, after the equals sign.</li>
  </ul>
 </li>
 <li>Different iteration numbers require different text to be put in the paragraph for that iteration (you'll need a conditional statement and multiple <code>para.textContent =</code> lines):
  <ul>
   <li>If the number is 10, print "Countdown 10" to the paragraph.</li>
   <li>If the number is 0, print "Blast off!" to the paragraph.</li>
   <li>For any other number, print just the number to the paragraph.</li>
  </ul>
 </li>
 <li>Remember to include an iterator! However, in this example we are counting down after each iteration, not up, so you <strong>don't</strong> want <code>i++</code> — how do you iterate downwards?</li>
</ul>

<div class="note">
<p><strong>Note:</strong> If you start typing the loop (for example (while(i&gt;=0)), the browser might stuck because you have not yet entered the end condition. So be careful with this. You can start writing your code in a comment to deal with this issue and remove the comment after you finish.</p>
</div>

<p>If you make a mistake, you can always reset the example with the "Reset" button. If you get really stuck, press "Show solution" to see a solution.</p>

<pre class="brush: html hidden">&lt;h2&gt;Live output&lt;/h2&gt;
&lt;div class="output" style="height: 410px;overflow: auto;"&gt;

&lt;/div&gt;

&lt;h2&gt;Editable code&lt;/h2&gt;
&lt;p class="a11y-label"&gt;Press Esc to move focus away from the code area (Tab inserts a tab character).&lt;/p&gt;
&lt;textarea id="code" class="playable-code" style="height: 300px;width: 95%"&gt;
let output = document.querySelector('.output');
output.innerHTML = '';

// let i = 10;

// const para = document.createElement('p');
// para.textContent = ;
// output.appendChild(para);
&lt;/textarea&gt;

&lt;div class="playable-buttons"&gt;
  &lt;input id="reset" type="button" value="Reset"&gt;
  &lt;input id="solution" type="button" value="Show solution"&gt;
&lt;/div&gt;
</pre>

<pre class="brush: css">html {
  font-family: sans-serif;
}

h2 {
  font-size: 16px;
}

.a11y-label {
  margin: 0;
  text-align: right;
  font-size: 0.7rem;
  width: 98%;
}

body {
  margin: 10px;
  background: #f5f9fa;
}</pre>

<pre class="brush: js hidden">const textarea = document.getElementById('code');
const reset = document.getElementById('reset');
const solution = document.getElementById('solution');
let code = textarea.value;
let userEntry = textarea.value;

function updateCode() {
  eval(textarea.value);
}

reset.addEventListener('click', function() {
  textarea.value = code;
  userEntry = textarea.value;
  solutionEntry = jsSolution;
  solution.value = 'Show solution';
  updateCode();
});

solution.addEventListener('click', function() {
  if(solution.value === 'Show solution') {
    textarea.value = solutionEntry;
    solution.value = 'Hide solution';
  } else {
    textarea.value = userEntry;
    solution.value = 'Show solution';
  }
  updateCode();
});

let jsSolution = 'const output = document.querySelector(\'.output\');\noutput.innerHTML = \'\';\n\nlet i = 10;\n\nwhile(i &gt;= 0) {\n let para = document.createElement(\'p\');\n if(i === 10) {\n para.textContent = \'Countdown \' + i;\n } else if(i === 0) {\n  para.textContent = \'Blast off!\';\n } else {\n para.textContent = i;\n }\n\n output.appendChild(para);\n\n i--;\n}';
let solutionEntry = jsSolution;

textarea.addEventListener('input', updateCode);
window.addEventListener('load', updateCode);

// stop tab key tabbing out of textarea and
// make it write a tab at the caret position instead

textarea.onkeydown = function(e){
  if (e.keyCode === 9) {
    e.preventDefault();
    insertAtCaret('\t');
  }

  if (e.keyCode === 27) {
    textarea.blur();
  }
};

function insertAtCaret(text) {
  const scrollPos = textarea.scrollTop;
  let caretPos = textarea.selectionStart;
  const front = (textarea.value).substring(0, caretPos);
  const back = (textarea.value).substring(textarea.selectionEnd, textarea.value.length);

  textarea.value = front + text + back;
  caretPos = caretPos + text.length;
  textarea.selectionStart = caretPos;
  textarea.selectionEnd = caretPos;
  textarea.focus();
  textarea.scrollTop = scrollPos;
}

// Update the saved userCode every time the user updates the text area code

textarea.onkeyup = function(){
  // We only want to save the state when the user code is being shown,
  // not the solution, so that solution is not saved over the user code
  if(solution.value === 'Show solution') {
    userEntry = textarea.value;
  } else {
    solutionEntry = textarea.value;
  }

  updateCode();
};</pre>

<p>{{ EmbedLiveSample('Active_learning_Launch_countdown', '100%', 900) }}</p>

<h2 id="Active_learning_Filling_in_a_guest_list">Active learning: Filling in a guest list</h2>

<p>In this exercise, we want you to take a list of names stored in an array and put them into a guest list. But it's not quite that easy — we don't want to let Phil and Lola in because they are greedy and rude, and always eat all the food! We have two lists, one for guests to admit, and one for guests to refuse.</p>

<p>Specifically, we want you to:</p>

<ul>
 <li>Write a loop that will iterate from 0 to the length of the <code>people</code> array. You'll need to start with an initializer of  <code>let i = 0;</code>, but what condition do you need?</li>
 <li>During each loop iteration, check if the current array item is equal to "Phil" or "Lola" using a conditional statement:
  <ul>
   <li>If it is, concatenate the array item to the end of the <code>refused</code> paragraph's <code>textContent</code>, followed by a comma and a space.</li>
   <li>If it isn't, concatenate the array item to the end of the <code>admitted</code> paragraph's <code>textContent</code>, followed by a comma and a space.</li>
  </ul>
 </li>
</ul>

<p>We've already provided you with:</p>

<ul>
 <li><code>let i = 0;</code> — Your initializer.</li>
 <li><code>refused.textContent +=</code> — the beginnings of a line that will concatenate something on to the end of <code>refused.textContent</code>.</li>
 <li><code>admitted.textContent +=</code> — the beginnings of a line that will concatenate something on to the end of <code>admitted.textContent</code>.</li>
</ul>

<p>Extra bonus question — after completing the above tasks successfully, you will be left with two lists of names, separated by commas, but they will be untidy — there will be a comma at the end of each one. Can you work out how to write lines that slice the last comma off in each case, and add a full stop to the end? Have a look at the <a href="/en-US/docs/Learn/JavaScript/First_steps/Useful_string_methods">Useful string methods</a> article for help.</p>

<p>If you make a mistake, you can always reset the example with the "Reset" button. If you get really stuck, press "Show solution" to see a solution.</p>

<pre class="brush: html hidden">&lt;h2&gt;Live output&lt;/h2&gt;
&lt;div class="output" style="height: 100px;overflow: auto;"&gt;
  &lt;p class="admitted"&gt;Admit: &lt;/p&gt;
  &lt;p class="refused"&gt;Refuse: &lt;/p&gt;
&lt;/div&gt;

&lt;h2&gt;Editable code&lt;/h2&gt;
&lt;p class="a11y-label"&gt;Press Esc to move focus away from the code area (Tab inserts a tab character).&lt;/p&gt;
&lt;textarea id="code" class="playable-code" style="height: 400px;width: 95%"&gt;
const people = ['Chris', 'Anne', 'Colin', 'Terri', 'Phil', 'Lola', 'Sam', 'Kay', 'Bruce'];

const admitted = document.querySelector('.admitted');
const refused = document.querySelector('.refused');
admitted.textContent = 'Admit: ';
refused.textContent = 'Refuse: '

// let i = 0;

// refused.textContent += ;
// admitted.textContent += ;

&lt;/textarea&gt;

&lt;div class="playable-buttons"&gt;
  &lt;input id="reset" type="button" value="Reset"&gt;
  &lt;input id="solution" type="button" value="Show solution"&gt;
&lt;/div&gt;
</pre>

<pre class="brush: css hidden">html {
  font-family: sans-serif;
}

h2 {
  font-size: 16px;
}

.a11y-label {
  margin: 0;
  text-align: right;
  font-size: 0.7rem;
  width: 98%;
}

body {
  margin: 10px;
  background: #f5f9fa;
}</pre>

<pre class="brush: js hidden">const textarea = document.getElementById('code');
const reset = document.getElementById('reset');
const solution = document.getElementById('solution');
let code = textarea.value;
let userEntry = textarea.value;

function updateCode() {
  eval(textarea.value);
}

reset.addEventListener('click', function() {
  textarea.value = code;
  userEntry = textarea.value;
  solutionEntry = jsSolution;
  solution.value = 'Show solution';
  updateCode();
});

solution.addEventListener('click', function() {
  if(solution.value === 'Show solution') {
    textarea.value = solutionEntry;
    solution.value = 'Hide solution';
  } else {
    textarea.value = userEntry;
    solution.value = 'Show solution';
  }
  updateCode();
});

const jsSolution = 'const people = [\'Chris\', \'Anne\', \'Colin\', \'Terri\', \'Phil\', \'Lola\', \'Sam\', \'Kay\', \'Bruce\'];\n\nconst admitted = document.querySelector(\'.admitted\');\nconst refused = document.querySelector(\'.refused\');\n\nadmitted.textContent = \'Admit: \';\nrefused.textContent = \'Refuse: \'\nlet i = 0;\n\ndo {\n if(people[i] === \'Phil\' || people[i] === \'Lola\') {\n refused.textContent += people[i] + \', \';\n } else {\n admitted.textContent += people[i] + \', \';\n }\n i++;\n} while(i &lt; people.length);\n\nrefused.textContent = refused.textContent.slice(0,refused.textContent.length-2) + \'.\';\nadmitted.textContent = admitted.textContent.slice(0,admitted.textContent.length-2) + \'.\';';
let solutionEntry = jsSolution;

textarea.addEventListener('input', updateCode);
window.addEventListener('load', updateCode);

// stop tab key tabbing out of textarea and
// make it write a tab at the caret position instead

textarea.onkeydown = function(e){
  if (e.keyCode === 9) {
    e.preventDefault();
    insertAtCaret('\t');
  }

  if (e.keyCode === 27) {
    textarea.blur();
  }
};

function insertAtCaret(text) {
  const scrollPos = textarea.scrollTop;
  let caretPos = textarea.selectionStart;
  const front = (textarea.value).substring(0, caretPos);
  const back = (textarea.value).substring(textarea.selectionEnd, textarea.value.length);

  textarea.value = front + text + back;
  caretPos = caretPos + text.length;
  textarea.selectionStart = caretPos;
  textarea.selectionEnd = caretPos;
  textarea.focus();
  textarea.scrollTop = scrollPos;
}

// Update the saved userCode every time the user updates the text area code

textarea.onkeyup = function(){
  // We only want to save the state when the user code is being shown,
  // not the solution, so that solution is not saved over the user code
  if(solution.value === 'Show solution') {
    userEntry = textarea.value;
  } else {
    solutionEntry = textarea.value;
  }

  updateCode();
};</pre>

<p>{{ EmbedLiveSample('Active_learning_Filling_in_a_guest_list', '100%', 680) }}</p>

<h2 id="Which_loop_type_should_you_use">Which loop type should you use?</h2>

<p>For basic uses, <code>for</code>, <code>while</code>, and <code>do...while</code> loops are largely interchangeable. They can all be used to solve the same problems, and which one you use will largely depend on your personal preference — which one you find easiest to remember or most intuitive. Let's have a look at them again.</p>

<p>First <code>for</code>:</p>

<pre>for (initializer; condition; final-expression) {
  // code to run
}</pre>

<p><code>while</code>:</p>

<pre>initializer
while (condition) {
  // code to run

  final-expression
}</pre>

<p>and finally <code>do...while</code>:</p>

<pre>initializer
do {
  // code to run

  final-expression
} while (condition)</pre>

<p>We would recommend <code>for</code>, at least to begin with, as it is probably the easiest for remembering everything — the initializer, condition, and final-expression all have to go neatly into the parentheses, so it is easy to see where they are and check that you aren't missing them.</p>

<div class="note">
<p><strong>Note:</strong> There are other loop types/features too, which are useful in advanced/specialized situations and beyond the scope of this article. If you want to go further with your loop learning, read our advanced <a href="/en-US/docs/Web/JavaScript/Guide/Loops_and_iteration">Loops and iteration guide</a>.</p>
</div>

<h2 id="Test_your_skills!">Test your skills!</h2>

<p>You've reached the end of this article, but can you remember the most important information? You can find some further tests to verify that you've retained this information before you move on — see <a href="/en-US/docs/Learn/JavaScript/Building_blocks/Test_your_skills:_Loops">Test your skills: Loops</a>.</p>

<h2 id="Conclusion">Conclusion</h2>

<p>This article has revealed to you the basic concepts behind, and different options available when looping code in JavaScript. You should now be clear on why loops are a good mechanism for dealing with repetitive code and are raring to use them in your own examples!</p>

<p>If there is anything you didn't understand, feel free to read through the article again, or <a href="/en-US/docs/Learn#contact_us">contact us</a> to ask for help.</p>

<h2 id="See_also">See also</h2>

<ul>
 <li><a href="/en-US/docs/Web/JavaScript/Guide/Loops_and_iteration">Loops and iteration in detail</a></li>
 <li><a href="/en-US/docs/Web/JavaScript/Reference/Statements/for">for statement reference</a></li>
 <li><a href="/en-US/docs/Web/JavaScript/Reference/Statements/while">while</a> and <a href="/en-US/docs/Web/JavaScript/Reference/Statements/do...while">do...while</a> references</li>
 <li><a href="/en-US/docs/Web/JavaScript/Reference/Statements/break">break</a> and <a href="/en-US/docs/Web/JavaScript/Reference/Statements/continue">continue</a> references</li>
 <li>
  <p><a href="https://www.impressivewebs.com/javascript-for-loop/">What’s the Best Way to Write a JavaScript For Loop?</a> — some advanced loop best practices</p>
 </li>
</ul>

<p>{{PreviousMenuNext("Learn/JavaScript/Building_blocks/conditionals","Learn/JavaScript/Building_blocks/Functions", "Learn/JavaScript/Building_blocks")}}</p>

<h2 id="In_this_module">In this module</h2>

<ul>
 <li><a href="/en-US/docs/Learn/JavaScript/Building_blocks/conditionals">Making decisions in your code — conditionals</a></li>
 <li><strong>Looping code</strong></li>
 <li><a href="/en-US/docs/Learn/JavaScript/Building_blocks/Functions">Functions — reusable blocks of code</a></li>
 <li><a href="/en-US/docs/Learn/JavaScript/Building_blocks/Build_your_own_function">Build your own function</a></li>
 <li><a href="/en-US/docs/Learn/JavaScript/Building_blocks/Return_values">Function return values</a></li>
 <li><a href="/en-US/docs/Learn/JavaScript/Building_blocks/Events">Introduction to events</a></li>
 <li><a href="/en-US/docs/Learn/JavaScript/Building_blocks/Image_gallery">Image gallery</a></li>
</ul>