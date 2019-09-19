+++
title = "Folding python code in Emacs"
author = ["Rodrigo Dorantes"]
date = 2018-03-19T00:00:00-06:00
draft = false
+++

## Purpose {#purpose}

-   I use python in my every-day work and I'm starting to use (and
    love) Emacs.
-   I use `vimish-folding` package to fold code in Emacs. It allows you
    to define your own foldings and remembers them for the next time you
    open the file.
-   Comming from **vim** I miss having my functions and classes folded out-of-the-box.
-   Unfortunately you don't have that in `vimish-folding`, but it's
    easy to implemented a function to do just that: Fold every code-block in
    your buffer.


## The Function {#the-function}

```emacs-lisp
;; Fold code-blocks in python
(defun fold-python-blocks ()
  "Fold all code blocks in python"
  (interactive)
  (forward-word) ; start with the second word
  (setq p (point))
  (while (forward-word)
    (backward-word)
    (setq col (current-column))
    (forward-word)
    (if (= col 0)
	(progn
	  (setq p1 (car (bounds-of-thing-at-point 'word)))
	  (vimish-fold p p1)
	  (setq p p1)
	  (goto-char p)
	  (forward-word))))
  (vimish-fold p (buffer-size))
  (goto-char 1))
```

<div class="src-block-caption">
  <span class="src-block-number">Code Snippet 1</span>:
  Add the following code to your <code>init.el</code> file to fold code blocks in python.
</div>

You can use the previous function by typing `M-x
  fold-python-blocks`. This will fold each code block in your file,
including code out of functions and classes, for instance, after a
sentence of the style:
\#+BEGIN\_SRC python
if <span class="underline"><span class="underline">name</span></span> == "<span class="underline"><span class="underline">main</span></span>":
\#+END\_SR
