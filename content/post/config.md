+++
title = "Emacs config file"
author = ["Rodrigo Dorantes-Gilardi"]
date = 2019-09-26T00:00:00-05:00
draft = false
toc = true
+++

I recently went for a full migration to an `org` file, and things be said, my config file looks way
more tidy. I recommend to make the change to have a more **literal programming** kind of `init` file.

This file is of course continuously being updated, you can see the updated version [here](https://github.com/rodogi/emacs/blob/master/config.org).


## Personal info {#personal-info}

```emacs-lisp
(setq user-full-name "Rodrigo Dorantes Gilardi"
		user-mail-address "rodgdor@gmail.com"
		calendar-latitude 19.4326
		calendar-longitude -99.13
		calendar-location-name "Mexico City")
```


## `use-package` {#use-package}

This configuration is inspired by Harry Schwartz
[config file](https://github.com/hrs/dotfiles/blob/master/emacs/.emacs.d/configuration.org). So let's first start by setting `use-package`.

`use-package` is already installed in `init.el` file, but we want to make sure that all packages
added for configuration are installed, for this we use `use-package-ensure`.

```emacs-lisp
(require 'use-package-ensure)
(setq use-package-always-ensure t)
```

Always compile packages, and use the newest version available.

```emacs-lisp
(use-package auto-compile
	:config (auto-compile-on-load-mode))

(setq load-prefer-newer t)
```


## Sensible defaults {#sensible-defaults}

Some sensible, rational defaults that help when writing/reading code.

```emacs-lisp
;; Set line numbers and relative mode
(global-display-line-numbers-mode)
;; Ask y/n instead of yes/no
(fset 'yes-or-no-p 'y-or-n-p)
;; When you double-click on a file in the Mac Finder open it as a
;; buffer in the existing Emacs frame, rather than creating a new
;; frame just for that file.
(setq ns-pop-up-frames nil)
```

More sensible indentation (2 instead of 8 spaces).

```emacs-lisp
(setq-default tab-width 2)
```

Remove the annoying bell sound from Emacs.

```emacs-lisp
(setq ring-bell-function 'ignore)
```

Get proper line breaks, 100 for text and 80 for programming.

```emacs-lisp
(dolist (hook '(text-mode-hook latex-mode-hook tex-mode-hook))
	(add-hook hook (lambda () (set-fill-column 100))))
(dolist (hook '(python-mode-hook prog-mode-hook list-mode-hook))
	(add-hook hook (lambda () (set-fill-column 80))))
```

Wrap paragraphs automatically

```emacs-lisp
(add-hook 'text-mode-hook 'auto-fill-mode)
(add-hook 'org-mode-hook 'auto-fill-mode)
```

Deal with pop-up windows better with [popwin](https://github.com/m2ym/popwin-el).

```emacs-lisp
(use-package popwin
	:config
	(popwin-mode 1)
	;; set all buffers to appear to the right
	(push '(help-mode :position right :width 0.45) popwin:special-display-config))
```

Count the number of occurrences of a word when searched.

```emacs-lisp
(use-package anzu
	:config
	(global-anzu-mode +1)
	(global-set-key [remap query-replace] 'anzu-query-replace)
	(global-set-key [remap query-replace-regexp] 'anzu-query-replace-regexp))
```

Set the number of the column

```emacs-lisp
(setq column-number-mode 1)
```

Stop the cursor from blinking.

```emacs-lisp
(blink-cursor-mode -1)
```

Disable auto backup files.

```emacs-lisp
(setq-default backup-inhibited t)
```


## Evil {#evil}

Load evil.

```emacs-lisp
(use-package evil
	:config
	(evil-mode 1))
```

Enable vim-surround everywhere.

```emacs-lisp
(use-package evil-surround
	:config
	(global-evil-surround-mode 1))
```

Use up or down-stream search of visually selected object.

```emacs-lisp
(use-package evil-escape
:init
(evil-escape-mode)
:config
(setq-default evil-escape-key-sequence "jk"))
```

```emacs-lisp
(use-package evil-visualstar
		:config
		(global-evil-visualstar-mode)
		(setq evil-visualstar/persistent nil))
```


## UI {#ui}

Remove the scroll bar and the menu.

```emacs-lisp
(tool-bar-mode 0)
(menu-bar-mode 0)
(scroll-bar-mode 0)
(set-window-scroll-bars (minibuffer-window) nil nil)
```

Use full-path-name of file in window

```emacs-lisp
(setq frame-title-format
			'((:eval (if (buffer-file-name)
									 (abbreviate-file-name (buffer-file-name))
								 "%b"))))
```

Use zenburn theme and set font and mode bar.

```emacs-lisp
(use-package zenburn-theme
	:config
	(load-theme 'zenburn t)
	(let ((line (face-attribute 'mode-line :underline)))
		(set-face-attribute 'mode-line          nil :overline   line)
		(set-face-attribute 'mode-line-inactive nil :overline   line)
		(set-face-attribute 'mode-line-inactive nil :underline  line)
		(set-face-attribute 'mode-line          nil :box        nil)
		(set-face-attribute 'mode-line-inactive nil :box        nil)
		(set-face-attribute 'mode-line-inactive nil :background "#f9f2d9")))
(set-frame-font "IBM Plex Mono-14" nil t)

;; Use moody for the mode bar
(use-package moody
	:config
	(setq x-underline-at-descent-line t)
	(moody-replace-mode-line-buffer-identification)
	(moody-replace-vc-mode))
```

Hide minor modes from bar.

```emacs-lisp
(use-package minions
	:config
	(setq minions-mode-line-lighter ""
				minions-mode-line-delimiters '("" . ""))
	(minions-mode 1))
```

Highlight the current line.

```emacs-lisp
(global-hl-line-mode)
```

Highlight uncommited changes.

```emacs-lisp
(use-package diff-hl
	:config
	(add-hook 'prog-mode-hook 'turn-on-diff-hl-mode))
```

Change the specs for the cursor.

```emacs-lisp
(setq evil-insert-state-cursor '((bar . 2) "yellow")
			evil-normal-state-cursor '(box "yellow"))
```


## Spelling {#spelling}

```emacs-lisp
(dolist (hook '(org-mode-hook latex-mode-hook tex-mode-hook git-commit-mode-hook))
	(add-hook hook (lambda () (flyspell-mode 1))))
;;; Setting english to be the spelling language
(setq ispell-program-name "/usr/local/bin/aspell")
(setq ispell-dictionary "english")
```


## Python {#python}


### Basics {#basics}

We start by making the symbol \`\_\` as part of the word.

```emacs-lisp
(add-hook 'python-mode-hook #'(lambda () (modify-syntax-entry ?_ "w")))
```


### Elpy {#elpy}

Let's start with installing `elpy`. Also, let's add jedi as the

```emacs-lisp
(use-package elpy
		:init
		(elpy-enable)
		:bind ("M-." . elpy-goto-definition)

		:config
		;; The path to python3
		(setq exec-path (append exec-path '("/usr/local/bin")))
		(setenv "PATH" (concat (getenv "PATH") ":/usr/local/bin"))
		(setq elpy-rpc-python-command "/usr/local/bin/python3"))
```

Then a little bit of configuration for the interpreter.

```emacs-lisp
(use-package python
		:mode ("\\.py\\'" . python-mode)
		:interpreter ("python" . python-mode)
		:config
		(setq python-shell-interpreter "/usr/local/bin/jupyter"
				python-shell-interpreter-args "console --simple-prompt"
				python-shell-prompt-detect-failure-warning nil)
add-to-list 'python-shell-completion-native-disabled-interpreters
						"jupyter"))
```

Use `jedi` for autocompletion. If used for the first time, we need to run the command
`M-x jedi:install-server`.

```emacs-lisp
(use-package jedi
	:config
	(add-hook 'python-mode-hook 'jedi:setup)
	(setq jedi:complete-on-dot t))
```

Use flycheck for syntax checking.

```emacs-lisp
(use-package flycheck
	:config
	(add-hook 'elpy-mode-hook 'flycheck-mode))
```

Format according to pep-8 on save.

```emacs-lisp
(use-package py-autopep8
	:config
	(add-hook 'elpy-mode-hook 'py-autopep8-enable-on-save))
```


## Org {#org}


### General {#general}

First things first

```emacs-lisp
(use-package org)

;; The following is to fix a bug to be able to expand
;; '<s' to code block.
(when (version<= "9.2" (org-version))
		(require 'org-tempo))
```


### Display {#display}

Use bullets instead of stars.

```emacs-lisp
(use-package org-bullets
	:init
	(add-hook 'org-mode-hook 'org-bullets-mode))
```

Let's add an arrow pointing down if there is some content. Also, let's use syntax highlighting
inside of code blocks.

```emacs-lisp
(setq org-ellipsis "⤵")
(setq org-src-tab-acts-natively t)
```


### Agenda {#agenda}

Add key-binding `\C-ca` to invoke agenda. Also, add a directory for agenda files inside Dropbox.

```emacs-lisp
(global-set-key "\C-ca" 'org-agenda)
(setq org-agenda-files '("~/Dropbox/org/"))
```

TODO headers can have more states (additionally to `TODO` and `DONE`), also let's add a file to
archive all the TODO trees completed.

```emacs-lisp
;; Workflow of TODO keywords
(setq org-todo-keywords
			'((sequence "TODO(t)" "|" "DONE(d!)" "CANCELED(c@/!)")))
;; archive in a datetree
(setq org-archive-location "~/org/archive.org::datetree/")
;; Close TODOs with a timestamp
(setq org-log-done 'time)
```


### Capture {#capture}

Emacs allows to capture ideas "on the fly" using a template, that way, you don't need to stop
working on something else when the idea pops-up. We, first are going to create a keybinding for that
function and then we will create the templates.

```emacs-lisp
(global-set-key "\C-cc" 'org-capture)
(setq org-capture-templates
	'(("b" "Blog idea"
				 entry
				 (file "~/Dropbox/notes/blog_ideas.org")
				 "* %?\n")))
```


### Export {#export}


#### Classes {#classes}

Allow export to different classes, including beamer, markdown, hugo, latex...

```emacs-lisp
(require 'ox-beamer)
(use-package ox-hugo
	:after ox)
;; Pretty html
(use-package ox-twbs)
```


#### Code blocks {#code-blocks}

Allow to evaluate code in `python`, `C`, `bash`, and `elisp`.

```emacs-lisp
(org-babel-do-load-languages
 'org-babel-load-languages '((C . t)
					 (python . t)
					 (emacs-lisp . t)
					 (shell . t)))
```

Allow TAB to function normally inside code blocks.

```emacs-lisp
(setq org-src-tab-acts-natively t)
```


#### Latex {#latex}

Set the process of compilation of a latex document.

```emacs-lisp
(setq org-latex-pdf-process
			'("pdflatex -shell-escape -interaction nonstopmode -output-directory %o %f"
		"bibtex %b"
		"pdflatex -shell-escape -interaction nonstopmode -output-directory %o %f"
		"pdflatex -shell-escape -interaction nonstopmode -output-directory %o %f"))
```

Set sections used in latex section when exported.

```emacs-lisp
(add-to-list 'org-latex-classes
						 '("article"
							 "\\documentclass{article}"
							 ("\\section{%s}" . "\\section*{%s}")
							 ("\\subsection{%s}" . "\\subsection*{%s}")
							 ("\\subsubsection{%s}" . "\\subsubsection*{%s}")
							 ("\\paragraph{%s}" . "\\paragraph*{%s}")
							 ("\\subparagraph{%s}" . "\\subparagraph*{%s}")))
```

Include minted package in latex exports.

```emacs-lisp
;;; Include minted package in all LaTeX reports
(add-to-list 'org-latex-packages-alist '("" "minted"))
(setq org-latex-listing 'minted)
```

Use the incredible `org-ref` package.

```emacs-lisp
(use-package org-ref)
```


## Magit {#magit}

Magit is a great package where I prefer its keybinding to `C-x g` rather than `C-x m`.

```emacs-lisp
(use-package magit
	:config
	(global-set-key (kbd "C-x g") 'magit-status))
```


## Helm {#helm}

Helm is great for file exploration.

```emacs-lisp
(use-package helm
	:config
	(helm-mode 1)
	(global-set-key (kbd "C-x C-f") 'helm-find-files))
```

Let's bind \`C-f C-a\` to `helm-apropos`

```emacs-lisp
(global-set-key (kbd "C-x C-a") 'helm-apropos)
```

Let's look for commands with helm.

```emacs-lisp
(global-set-key (kbd "M-x") 'helm-M-x)
```

Set helm to fuzzy matching.

```emacs-lisp
(setq helm-M-x-fuzzy-match t)
```

Show the kill-ring with helm

```emacs-lisp
(global-set-key (kbd "M-y") 'helm-show-kill-ring)
```

Get a better buffer to change buffers. Also, enable fuzzy matching in there.

```emacs-lisp
	(global-set-key (kbd "C-x b") 'helm-mini)
(global-set-key (kbd "C-x C-b") 'helm-mini)
	(setq helm-buffers-fuzzy-matching t
				helm-recentf-fuzzy-match    t)
```

Let's set semantic-mode to be able to use \`helm-semantic\` and then bind it to 'C-x C-m'.
Bind heml-imenu to \`C-x C-m\`.

```emacs-lisp
(use-package semantic
:config
(semantic-mode 1))
(global-set-key (kbd "C-x C-m") 'helm-semantic-or-imenu)
```

Now let's make it fuzzy matchers.

```emacs-lisp
(setq helm-semantic-fuzzy-match t
			helm-imenu-fuzzy-match    t)
```

Use heml-occur with 'C-x C-o'.

```emacs-lisp
(global-set-key (kbd "C-x C-o") 'helm-occur)
```


## Dired {#dired}

Switch the default \`ls\` to have the parameters \`lhva\`.

-   `l`: long format
-   `h`: human readable size
-   `S`: sort files by size
-   `a`: all files (including dotfiles)
-   `r`: reverse the sort

<!--listend-->

```emacs-lisp
(setq dired-listing-switches "-AlShr")

```


## Projectile {#projectile}

Projectile is a package to work on projects defined by a directory. Let's first install it.

```emacs-lisp
(use-package projectile
	:config
	(projectile-mode +1)
	(define-key projectile-mode-map (kbd "s-p") 'projectile-command-map)
	(define-key projectile-mode-map (kbd "C-c p") 'projectile-command-map))

```
