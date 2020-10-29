+++
title = "awesome-tab flycheck 引起 modeline 闪烁修复"
author = ["lee"]
date = 2020-07-04
draft = false
+++

问题描述:  
<https://github.com/manateelazycat/awesome-tab/issues/85>  

-   重现代码

<!--listend-->

```emacs-lisp
(setq package-enable-at-startup nil)
(setq package-archives
      '(("org"          . "http://orgmode.org/elpa/")
        ("gnu" . "http://elpa.gnu.org/packages/")
        ("melpa"        . "https://melpa.org/packages/")
        ("melpa-stable" . "http://stable.melpa.org/packages/")))

(package-initialize)

;; Bootstrap `use-package'
(unless (package-installed-p 'use-package)
  (package-refresh-contents)
  (package-install 'use-package))
(require 'use-package)

(use-package awesome-tab
  :load-path "~/.emacs.d/.local/straight/repos/awesome-tab"
  :config
  (awesome-tab-mode))


(use-package doom-modeline
  :ensure t
  :config
  (doom-modeline-mode 1))

(use-package flycheck
  :ensure t
  :init
  (setq flycheck-emacs-lisp-load-path 'inherit)
  (setq flycheck-check-syntax-automatically '(save mode-enabled idle-buffer-switch))
  (setq flycheck-buffer-switch-check-intermediate-buffers t)
  (setq flycheck-display-errors-delay 0.25)
  :config
  (global-flycheck-mode))
```

在flycheck里面启用了 idle-buffer-switch,flycheck会添加buffer-list-update-hook,  
这个hook 和 with-temp-buffer 配合不好的话，会引起无限递归  

<https://www.gnu.org/software/emacs/manual/html%5Fnode/elisp/Buffer-List.html#Buffer-List>  
Functions run by this hook should avoid calling select-window with a nil norecord argument or with-temp-buffer since either may lead to infinite recursion  

解决方法：  

```emacs-lisp
(let (buffer-list-update-hook)
  (with-temp-buffer
    (let ((truncate-partial-width-windows nil)
          (inhibit-modification-hooks t)
          deactivate-mark ;; Prevent deactivation of the mark!
          start)
      (setq truncate-lines nil
            buffer-undo-list t)
      (setq start (point))
      (while (and (cdr elts) ;; Always show the selected tab!
                  (progn
                    (delete-region start (point-max))
                    (goto-char (point-max))
                    (apply 'insert elts)
                    (goto-char (point-min))
                    (> (vertical-motion 1) 0)))
        (awesome-tab-scroll tabset 1)
        (setq elts (cdr elts))))))
```
