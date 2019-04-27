---
layout:      post
title:       "Survive Vim with these 5 simple tricks"
date:        2017-02-28 12:00:00
update-date: 2017-02-28 12:00:00
author:      "Alexandre"
comments:    True

---

<p>This article was origininally published on <a href="https://blog.theodo.fr/2017/02/survive-vim-with-these-5-simple-tricks/" target="_blank">Theodo's blog</a></p>

<hr/>

<p>Sometimes, you have to ssh on a server to fix some configuration files or to test a feature that you cannot test locally. You don't have your favourite text editor with all your configuration here.</p>

<p>Most of the time, you have to rely on <code>vim</code> or <code>nano</code> in this situation.</p>

<p>Your coworkers told you that <code>nano</code> is for newbies and you want to gain some street credibility, so you use <code>vim</code>.</p>

<img src="https://d24ju8re1w4x9e.cloudfront.net/original/1X/16a2e29f6692dbc3c304e68faefb056b9c188f29.png" width="1000" height="563" class="aligncenter" />
<p style="text-align: center;"><em>Your beard is not long enough for emacs. </br> You wouldn't be here anyway</em></p>

<p>Here is a guide to help you solve the annoying problems that you may face with vim.</p>
<h2 id="whenyouforgettosudo">When you forget to sudo</h2>
<p>Your server has a configuration issue, and you have to fix the configuration file, so you go <code>vim &lt;path-to-your-config-file&gt;</code>.</p>
<p>You make a bunch of changes, <kbd>Esc</kbd><kbd>:</kbd><kbd>x</kbd> to save the file and exit and then:</p>
<pre><code>E505: "&lt;your-config-file&gt;" is read-only (add ! to override)</code></pre>
<p>You forgot about the <code>sudo</code> and now you think you have to drop your changes, and open vim again with <code>sudo</code> this time.</p>
<script type="text/javascript" src="https://asciinema.org/a/38ctyad3l20rutip6woihjila.js" id="asciicast-38ctyad3l20rutip6woihjila" async></script>
<p>Fortunately, there is a solution: <code>:w !sudo tee %</code></p>
<p>Let's analyse this command:</p>
<ul>
<li id="w"><p><code>:w</code> doesn't write in your file in this case. If you are editing <code>file1</code> with vim and type <code>:w file2</code>, you will create a new file named <code>file2</code> with your buffer and <code>file1</code> will be left untouched.
Here, you write to the "file" <code>!sudo tee %</code>.</p></li>
<li id="sudo"><p><code>!sudo</code>: The bang <code>!</code> lets you execute a command as if you were in a shell, in this case <code>sudo</code> to get superusers rights.</p></li>
<li id="tee"><p><code>tee</code> sounds like the letter <strong>T</strong> for a reason: it acts like a T-shaped pipe. It takes a filename in argument, and redirects the output to the file you gave it and to the standard output.</p></li>
<li id=""><p><code>%</code> refers to the current file in vim.</p></li>
<p>You now have the full equation: <code>:w</code> writes the buffer into <code>tee</code> (with superuser rights) that redirects the buffer into the current file (and to the standard output, but that's not useful here)</p>
</ul>
<script type="text/javascript" src="https://asciinema.org/a/ea3ta93mty7hra7crszwxuy36.js" id="asciicast-ea3ta93mty7hra7crszwxuy36" async></script>

<h2 id="pastemode">Pastemode</h2>
<p>What's more frustrating than pasting a snippet of code and having your formatting all messed up, especially in languages like Python where indentation is part of your code?</p>
<p>Example:</p>
<script type="text/javascript" src="https://asciinema.org/a/cun58gnx8kav436if1nfni9d2.js" id="asciicast-cun58gnx8kav436if1nfni9d2" async></script>
<p>You can switch to paste mode which allows you to paste text as is.</p>
<p>Simply type <code>:set paste</code> in normal mode and then you're good to go (do not forget to switch to insert mode before pasting). Look carefully, everything happens on the last line of the video.</p>
<script type="text/javascript" src="https://asciinema.org/a/d7snwhgrkks6jynoy9oyi67qm.js" id="asciicast-d7snwhgrkks6jynoy9oyi67qm" async></script>
<p>You might wonder: why not stay in paste mode all the time? Well it changes some of the configurations of vim like smarttabs and smartindent.</p>
<p>However, if you have a lot of copy / pasting to do, you can use <code>set pastetoggle=&lt;F2&gt;</code> to set a toggle. Pressing <kbd>F2</kbd> will switch paste mode on and off. You can replace <kbd>F2</kbd> with any key you like. More info with <code>:help paste</code>.</p>
<h2 id="searchingfortext">Searching for text</h2>
<p>Chances are that you will look for something in the file you are trying to edit.</p>
<p>In normal mode simply type <code>/&lt;your-search&gt;</code>.
It will highlight text that matches and you can press <kbd>n</kbd> to go to the next or <kbd>N</kbd> to go to the previous occurrence.</p>
<p>Notice how the text is left highlighted even after you entered insert mode? You can stop the highlighting by typing <code>:noh</code> as in "no highlight".</p>
<script type="text/javascript" src="https://asciinema.org/a/7posku5aqhpi9mhrw2fjvtkko.js" id="asciicast-7posku5aqhpi9mhrw2fjvtkko" async></script>
<h2 id="blockcomment">Block comment</h2>
<p>Sometimes, you have to test your file with a part of the code removed and the quickest way to do this without losing it is to comment it.</p>
<p>To comment multiple lines at once, simply put the cursor on the first column by pressing <kbd>0</kbd> at the top (or bottom) of the block you want to comment and press <kbd>Ctrl</kbd> + <kbd>v</kbd></p>
<p>You are now in visual block mode where you can select a bloc of text, in our case a column. Go down or up and select the lines you want to comment. Press <kbd>Shift</kbd> + <kbd>I</kbd> and insert your programming language comment symbol. It will only print on the highest row selected. Then press <kbd>Esc</kbd> <kbd>Esc</kbd> and boom, your lines are commented!</p>
<p>Press <kbd>u</kbd> to cancel the change, or simply bloc select it again and press <kbd>x</kbd>.</p>
<script type="text/javascript" src="https://asciinema.org/a/c5i80983fzy510lqrut2u6p2x.js" id="asciicast-c5i80983fzy510lqrut2u6p2x" async></script>
<h2 id="indent">Indent</h2>
<p>Press <kbd>v</kbd> to select text and then press <kbd> < </kbd> or <kbd> > </kbd> to indent in either direction. You can combine it with navigation actions. For example <kbd> > </kbd> <kbd> G </kbd> will indent all lines from the cursor to the end of the file.</p>
<script type="text/javascript" src="https://asciinema.org/a/5hh25pyrj1i4hfcb6p4g19r31.js" id="asciicast-5hh25pyrj1i4hfcb6p4g19r31" async></script>
<p>However, it will indent your file of <code>shiftwidth</code> spaces. By default this value is 8 and that is quite big. You can use <code>:set shiftwidth=&lt;your-value&gt;</code> to adapt it to the indent style of your file.</p>
<h2 id="bonusquicknavigation">Bonus: quick navigation</h2>
<ul>
<li><kbd>$</kbd> goes to end of line</li>
<li><kbd>0</kbd> goes to the beginning of the line (column 1) while <kbd>^</kbd> goes to the first non blank character</li>
<li><kbd>g</kbd><kbd>g</kbd> goes to the top of the file while <kbd>G</kbd> goes to the bottom</li>
<li><kbd>%</kbd> goes to the matching parenthesis/bracket</li>
<li><kbd>Ctrl</kbd> + <kbd>F</kbd> goes one page down and <kbd>Ctrl</kbd> + <kbd>B</kbd> goes one page up (think forward and backward to remember it)</li>
<li><kbd>u</kbd> cancels your last action and <kbd>Ctrl</kbd> + <kbd>r</kbd> reapplies it</li>
</ul>
<p>I hope this article made Vim less painful.</p>
<p>You can share your tips in the comments.</p>
