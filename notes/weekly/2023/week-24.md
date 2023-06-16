---
share: true
---

# Week 24 - 2023

## Done

* AI meetup thing
* Added `uninstall-puro` command to Puro
* Fixed bugs with ancient Flutter versions in Puro
* Fixed "Flutter SDK is not available" bug in Puro
* Released Puro 1.3.1

## TODO

- Translate abstract tree to Agda (remove Range on TopLevelModuleName?)
- JS interpreter?
- Figure out plan for basic editor

## Iota

Monaco is a good text editor but doesn't have much WYSIWYG

Alternatives

* Milkdown (https://github.com/Milkdown/milkdown)
	* Supports markdown, mermaid, and TeX
* Toast UI (https://ui.toast.com/tui-editor)
	* Supports markdown, charts, UML, tables, colors, TeX
	* Feels a little clunkier
	  * Nvm, perfectly fine with some css
* CKEditor: https://ckeditor.com/ckeditor-5/
	* GPLv2 / Commercial (rip)
	* Nicest formatting out of the box so far

Holy #\*@& web development is horrible

* Use AI to generate thumbnails for documents?
* Delta shell for editing documents?

### Bijection

```
0            | []
1            | 0
10           | 1
10x          | 2 + x
10xx         | 4 + x
11           | 8
110          | 9
110x         | 10 + x
110xx        | 12 + x
110xxx       | 16 + x
110xxxx      | 24 + x
111          | 40
1110         | 41
1110x        | 42 + x
1110xx       | 44 + x
1110xxx      | 48 + x
1110xxxx     | 56 + x
1110xxxxx    | 72 + x
1110xxxxxx   | 104 + x
1110xxxxxxx  | 168 + x
1110xxxxxxxx | 296 + x

0x         | 0 + x
10xx       | 2 + x
110xxxx    | 10 + x
```