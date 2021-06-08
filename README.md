[![Build Status](https://travis-ci.com/jcs090218/company-fuzzy.svg?branch=master)](https://travis-ci.com/jcs090218/company-fuzzy)
[![MELPA](https://melpa.org/packages/company-fuzzy-badge.svg)](https://melpa.org/#/company-fuzzy)
[![MELPA Stable](https://stable.melpa.org/packages/company-fuzzy-badge.svg)](https://stable.melpa.org/#/company-fuzzy)
[![License: GPL v3](https://img.shields.io/badge/License-GPL%20v3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)

# company-fuzzy
> Fuzzy matching for `company-mode'.

<p align="center">
  <img src="./etc/demo.gif"/>
</p>

Pure `elisp` fuzzy completion for `company-mode`. This plugin search through
all the buffer local `company-backends` and fuzzy search all candidates.

## Features

* *Work across all backends* - Any backend that gives list of string should work.
* *Only uses native `elisp` code* - I personally don't prefer any external
program unless is necessary.
* *Combined all backends to one backend* - Opposite to [company-try-hard](https://github.com/Wilfred/company-try-hard),
hence all possible candidates will be shown in the auto-complete menu.

## Differences from other alternatives

* [company-ycmd](https://github.com/abingham/emacs-ycmd)
  * Uses [ycmd](https://github.com/Valloric/ycmd) as backend to provide functionalities.
  * Quite hard to config properly.
* [company-flx](https://github.com/PythonNut/company-flx)
  * Uses library [flx](https://github.com/lewang/flx).
  * Only works with `elisp-mode` currently.

## Usage

You can enable it globally by adding this line to your config
```el
(global-company-fuzzy-mode 1)
```
Or you can just enable it in any specific buffer/mode you want.
```el
(company-fuzzy-mode 1)
```

Make sure you call either of these functions after all
`company-backends` are set and config properly. Because
this plugin will replace all backends to this minor mode
specific backend (basically take all backends away, so
this mode could combine all sources and do the fuzzy work).

### Sorting/Scoring backend

There are multiple sorting algorithms for auto-completion. You can choose your
own backend by customize `company-fuzzy-sorting-backend` variable like this.

```el
(setq company-fuzzy-sorting-backend 'alphabetic)
```

Currently supports these values,

* *`none`* - Gives you the raw result.
* *`alphabetic`* - Sort in the alphabetic order. (VSCode)
* *`flx`* - Use library [flx](https://github.com/lewang/flx) as matching engine. (Sublime Text)
* *`flex`* - Use library [flex](https://github.com/jcs-elpa/flex) as matching engine.
* *`liquidmetal`* - Use library [liquidmetal](https://github.com/jcs-elpa/liquidmetal) similar to [Quicksilver](https://qsapp.com/) algorithm.

Or implements your sorting algorithm yourself? Assgin the function to
`company-fuzzy-sorting-function` variable like this.

```el
(setq company-fuzzy-sorting-function (lambda (candidates)
                                       (message "%s" candidates)
                                       candidates))  ; Don't forget to return the candidaites!
```

### Prefix On Top

If you wish the prefix matchs on top of all other selection, customize
this variable to `t` like the line below.

```el
(setq company-fuzzy-prefix-on-top t)
```

*P.S.
If you set `company-fuzzy-sorting-backend` to `'flx` then
you probably don't need this to be on because the `flx` scoring engine
already take care of that!*

### For annotation

You can toggle `company-fuzzy-show-annotation` for showing annotation or not.

```el
(setq company-fuzzy-show-annotation t)
```

You can also customize annotation using `format` variable.

* `company-fuzzy-annotation-format` => ` <%s>`

## Details

Since [company](https://github.com/company-mode/company-mode)
granted most control to users, every company backend developer
has different method of implementing company backend. It is hard
to manage all backends to one by varies of rules.

### History

Some backends doesn't allow me to get the list of candidates by passing the
possible prefix; hence I have created this type of special scenario. If you
encountered a backend that sometimes does fuzzy, but sometimes does not;
try add the backend to `company-fuzzy-history-backends` like the following
code snippet. `'company-yasnippet` is one of the backend that does not
allow me to do that.

```el
(add-to-list 'company-fuzzy-history-backends 'company-yasnippet)
```

## Recommended Settings

There are something that `company` design it weirdly, in order to make this
plugin work smoothly I would recommend these `company`'s variables to be set.

```el
(use-package company
  :init
  (setq company-require-match nil            ; Don't require match, so you can still move your cursor as expected.
        company-tooltip-align-annotations t  ; Align annotation to the right side.
        company-eclim-auto-save nil          ; Stop eclim auto save.
        company-dabbrev-downcase nil)        ; No downcase when completion.
  :config
  ;; Enable downcase only when completing the completion.
  (defun jcs--company-complete-selection--advice-around (fn)
    "Advice execute around `company-complete-selection' command."
    (let ((company-dabbrev-downcase t))
      (call-interactively fn)))
  (advice-add 'company-complete-selection :around #'jcs--company-complete-selection--advice-around))
```

*P.S.
For the full configuration you can check out my configuration
[here](https://github.com/jcs090218/jcs-emacs-init/blob/master/.emacs.jcs/jcs-plugin.el).*

## Contribution

If you would like to contribute to this project, you may either
clone and make pull requests to this repository. Or you can
clone the project and establish your own branch of this tool.
Any methods are welcome!
