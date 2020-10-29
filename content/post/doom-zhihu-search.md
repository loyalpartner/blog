+++
title = "Doom 添加知乎搜索引擎"
author = ["lee"]
date = 2020-07-04
tags = ["doom", "emacs"]
draft = false
+++

使用 `SPC s o` 快捷键可以选择搜索引擎,然后用这个搜索引擎搜索这个关键词，  
其中 Google,DuckDuckGo 可以提供搜索建议,在使用知乎的过程中，搜索关键词  
也提供搜索建议，于是查了下知乎搜索建议的 api  

> <https://www.zhihu.com/api/v4/search/suggest?q>=  

下面我们就把知乎的搜索建议添加到 doom-emacs 里面.  

```emacs-lisp
(add-to-list  counsel-search-engines-alist
              (zhihu "https://www.zhihu.com/api/v4/search/suggest"
                     "https://www.zhihu.com/search?type=content&q="
                     counsel--search-request-data-zhihu))

(add-to-list '+lookup-provider-url-alist
             '("zhihu" +lookup--online-backend-zhihu "https://www.zhihu.com/search?type=content&q=%25s"))

(defun counsel--search-request-data-zhihu (data)
  (mapcar (lambda (elt)
            (alist-get 'query elt))
          (alist-get 'suggest data)))

;;;###autoload
(defun +lookup--online-backend-zhihu (query)
  "Search google, starting with QUERY, with live autocompletion."
  (cond ((fboundp 'counsel-search)
         (let ((ivy-initial-inputs-alist `((t . ,query)))
               (counsel-search-engine 'zhihu))
           (call-interactively #'counsel-search)
           t))))
```
