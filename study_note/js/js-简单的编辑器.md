### 一、制作一个简单的编辑器会有几种方式？
1. 文本框 input
2. 文本域 textarea
3. 使用属性contenteditable,让一个div可编辑。

### 二、简单的编辑器涉及到复制、粘贴的问题

复制、粘贴的东西来自于剪切板[Clipboard](https://w3c.github.io/clipboard-apis/)。

[Clipboard API](https://developer.mozilla.org/zh-CN/docs/Web/API/Clipboard_API)提供了响应剪贴板命令（剪切、复制和粘贴）与异步读写系统剪贴板的能力。从权限 Permissions API 获取权限之后，才能访问剪贴板内容；如果用户没有授予权限，则不允许读取或更改剪贴板内容。

#### 2.1如何进行简单的粘贴呢？

粘贴分为本文和图片的粘贴，我们一般都可以直接使用[onpaste](https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLElement/onpaste)事件去做监听

此处还需要了解一下关于选区的windowApi-[Window.getSelection](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/getSelection)

``` html
    <div class="simple-editor" id="sEditor" contenteditable="true" ></div>
```
```javascript
            editor = document.querySelector('#sEditor')
            // 监听paste对象--剪切板中处理图片与文本
            editor.onpaste = (event)=> {
                const datatransfer = new DataTransfer(event.clipboardData)
                const txt = datatransfer.getData('text/plain')
                const file = datatransfer.files.length ? datatransfer.files[0] : null
                if (txt || file) {
                    // 处理图片文件
                    if (file) {
                        if (/image/.test(file.type)) {
                            const reader = new FileReader() // 实例化文件读取对象
                            reader.readAsDataURL(file) // 将文件读取为 DataURL,也就是base64编码
                            reader.onload = (ev) => {
                                const imageUrl = ev.target.result // 获得文件读取成功后的DataURL,也就是base64编码
                                insertImg(imageUrl)
                            }
                        }
                    }
                    if (txt) {
                        // 处理文本对象
                        insertContent(txt)
                    }
                }
                event.preventDefault()//阻止粘贴，使用insertContent方法统一处理粘贴内容
                return false
            }

// 设置图片
function insertImg(imageUrl) {
    const imgTag = `<img data-type='img' style="max-height:100px;max-width:100px" src=${imageUrl} />`
    insertContent(imgTag)
}

// 在光标位置插入内容--收藏文章内容 https://blog.csdn.net/mafan121/article/details/79280529
function insertContent(content) {
    if (!content) {
        // 如果插入的内容为空则返回
        return
    }
    let sel = null
    if (document.selection) {
        // IE9以下
        sel = document.selection
        sel.createRange().pasteHTML(content)
    } else {
        sel = window.getSelection()
        if (sel.rangeCount > 0) {
            const range = sel.getRangeAt(0) // 获取选择范围
            range.deleteContents() // 删除选中的内容
            const el = document.createElement('div') // 创建一个空的div外壳
            el.innerHTML = content // 设置div内容为我们想要插入的内容。
            const frag = document.createDocumentFragment() // 创建一个空白的文档片段，便于之后插入dom树

            const node = el.firstChild
            const lastNode = frag.appendChild(node)
            range.insertNode(frag) // 设置选择范围的内容为插入的内容
            const contentRange = range.cloneRange() // 克隆选区
            contentRange.setStartAfter(lastNode) // 设置光标位置为插入内容的末尾
            contentRange.collapse(true) // 移动光标位置到末尾
            sel.removeAllRanges() // 移出所有选区
            sel.addRange(contentRange) // 添加修改后的选区
        }
    }
}
```
其中粘贴板的内容可以用 [Clipboard.read](https://developer.mozilla.org/zh-CN/docs/Web/API/Clipboard/read)方法去读取。
但这里，我直接抠出了ckeditor的DataTransfer进行粘贴板内容的读取。
ckeditor读取剪切板内容的类-DataTransfer

```javascript
/**
 * @license Copyright (c) 2003-2020, CKSource - Frederico Knabben. All rights reserved.
 * For licensing, see LICENSE.md or https://ckeditor.com/legal/ckeditor-oss-license
 */

/**
 * @module clipboard/datatransfer
 */

/**
 * Facade over the native [`DataTransfer`](https://developer.mozilla.org/en-US/docs/Web/API/DataTransfer) object.
 */
export default class DataTransfer {
    constructor(nativeDataTransfer) {
        /**
         * The array of files created from the native `DataTransfer#files` or `DataTransfer#items`.
         *
         * @readonly
         * @member {Array.<File>} #files
         */
        this.files = getFiles(nativeDataTransfer)

        /**
         * The native DataTransfer object.
         *
         * @private
         * @member {DataTransfer} #_native
         */
        this._native = nativeDataTransfer
    }

    /**
     * Returns an array of available native content types.
     *
     * @returns {Array.<String>}
     */
    get types() {
        return this._native.types
    }

    /**
     * Gets data from the data transfer by its mime type.
     *
     * dataTransfer.getData( 'text/plain' );
     *
     * @param {String} type The mime type. E.g. `text/html` or `text/plain`.
     * @returns {String}
     */
    getData(type) {
        return this._native.getData(type)
    }

    /**
     * Sets data in the data transfer.
     *
     * @param {String} type The mime type. E.g. `text/html` or `text/plain`.
     * @param {String} data
     */
    setData(type, data) {
        this._native.setData(type, data)
    }
}

function getFiles(nativeDataTransfer) {
    // DataTransfer.files and items are Array-like and might not have an iterable interface.
    const files = nativeDataTransfer.files ? Array.from(nativeDataTransfer.files) : []
    const items = nativeDataTransfer.items ? Array.from(nativeDataTransfer.items) : []

    if (files.length) {
        return files
    }
    // Chrome have empty DataTransfer.files, but let get files through the items interface.
    return items
        .filter(item => item.kind === 'file')
        .map(item => item.getAsFile())
}

```
### 三、编辑器中需要@后跟提示语的--获取光标在页面上的像素位置
#### 3.1 获取textarea和input光标的像素位置，即光标的offsetLeft与offsetTop

此处为摘录收藏内容了。

原文地址：https://www.cnblogs.com/kingwolfofsky/archive/2011/07/05/2098446.html

```javascript
var kingwolfofsky = {
    /**
    * 获取输入光标在页面中的坐标
    * @param        {HTMLElement}    输入框元素        
    * @return        {Object}        返回left和top,bottom
    */
    getInputPositon: function (elem) {
        if (document.selection) {   //IE Support
            elem.focus();
            var Sel = document.selection.createRange();
            return {
                left: Sel.boundingLeft,
                top: Sel.boundingTop,
                bottom: Sel.boundingTop + Sel.boundingHeight
            };
        } else {
            var that = this;
            var cloneDiv = '{$clone_div}', cloneLeft = '{$cloneLeft}', cloneFocus = '{$cloneFocus}', cloneRight = '{$cloneRight}';
            var none = '<span style="white-space:pre-wrap;"> </span>';
            var div = elem[cloneDiv] || document.createElement('div'), focus = elem[cloneFocus] || document.createElement('span');
            var text = elem[cloneLeft] || document.createElement('span');
            var offset = that._offset(elem), index = this._getFocus(elem), focusOffset = { left: 0, top: 0 };

            if (!elem[cloneDiv]) {
                elem[cloneDiv] = div, elem[cloneFocus] = focus;
                elem[cloneLeft] = text;
                div.appendChild(text);
                div.appendChild(focus);
                document.body.appendChild(div);
                focus.innerHTML = '|';
                focus.style.cssText = 'display:inline-block;width:0px;overflow:hidden;z-index:-100;word-wrap:break-word;word-break:break-all;';
                div.className = this._cloneStyle(elem);
                div.style.cssText = 'visibility:hidden;display:inline-block;position:absolute;z-index:-100;word-wrap:break-word;word-break:break-all;overflow:hidden;';
            };
            div.style.left = this._offset(elem).left + "px";
            div.style.top = this._offset(elem).top + "px";
            var strTmp = elem.value.substring(0, index).replace(/</g, '<').replace(/>/g, '>').replace(/\n/g, '<br/>').replace(/\s/g, none);
            text.innerHTML = strTmp;

            focus.style.display = 'inline-block';
            try { focusOffset = this._offset(focus); } catch (e) { };
            focus.style.display = 'none';
            return {
                left: focusOffset.left,
                top: focusOffset.top,
                bottom: focusOffset.bottom
            };
        }
    },

    // 克隆元素样式并返回类
    _cloneStyle: function (elem, cache) {
        if (!cache && elem['${cloneName}']) return elem['${cloneName}'];
        var className, name, rstyle = /^(number|string)$/;
        var rname = /^(content|outline|outlineWidth)$/; //Opera: content; IE8:outline && outlineWidth
        var cssText = [], sStyle = elem.style;

        for (name in sStyle) {
            if (!rname.test(name)) {
                val = this._getStyle(elem, name);
                if (val !== '' && rstyle.test(typeof val)) { // Firefox 4
                    name = name.replace(/([A-Z])/g, "-$1").toLowerCase();
                    cssText.push(name);
                    cssText.push(':');
                    cssText.push(val);
                    cssText.push(';');
                };
            };
        };
        cssText = cssText.join('');
        elem['${cloneName}'] = className = 'clone' + (new Date).getTime();
        this._addHeadStyle('.' + className + '{' + cssText + '}');
        return className;
    },

    // 向页头插入样式
    _addHeadStyle: function (content) {
        var style = this._style[document];
        if (!style) {
            style = this._style[document] = document.createElement('style');
            document.getElementsByTagName('head')[0].appendChild(style);
        };
        style.styleSheet && (style.styleSheet.cssText += content) || style.appendChild(document.createTextNode(content));
    },
    _style: {},

    // 获取最终样式
    _getStyle: 'getComputedStyle' in window ? function (elem, name) {
        return getComputedStyle(elem, null)[name];
    } : function (elem, name) {
        return elem.currentStyle[name];
    },

    // 获取光标在文本框的位置
    _getFocus: function (elem) {
        var index = 0;
        if (document.selection) {// IE Support
            elem.focus();
            var Sel = document.selection.createRange();
            if (elem.nodeName === 'TEXTAREA') {//textarea
                var Sel2 = Sel.duplicate();
                Sel2.moveToElementText(elem);
                var index = -1;
                while (Sel2.inRange(Sel)) {
                    Sel2.moveStart('character');
                    index++;
                };
            }
            else if (elem.nodeName === 'INPUT') {// input
                Sel.moveStart('character', -elem.value.length);
                index = Sel.text.length;
            }
        }
        else if (elem.selectionStart || elem.selectionStart == '0') { // Firefox support
            index = elem.selectionStart;
        }
        return (index);
    },

    // 获取元素在页面中位置
    _offset: function (elem) {
        var box = elem.getBoundingClientRect(), doc = elem.ownerDocument, body = doc.body, docElem = doc.documentElement;
        var clientTop = docElem.clientTop || body.clientTop || 0, clientLeft = docElem.clientLeft || body.clientLeft || 0;
        var top = box.top + (self.pageYOffset || docElem.scrollTop) - clientTop, left = box.left + (self.pageXOffset || docElem.scrollLeft) - clientLeft;
        return {
            left: left,
            top: top,
            right: left + box.width,
            bottom: top + box.height
        };
    }
};

function getPosition(ctrl) {
    var p = kingwolfofsky.getInputPositon(ctrl);
    document.getElementById('show').style.left = p.left + "px";
    document.getElementById('show').style.top = p.bottom + "px";
}
```
```html
<!DOCTYPE html>
<html>
<head>
    <title>InputPostion</title>
    <script type="text/javascript" src="kingwolfofsky.js""></script>
    <script type="text/javascript">
        function show(elem) {
            var p = kingwolfofsky.getInputPositon(elem);
            var s = document.getElementById('show');
            s.style.top = p.bottom+'px';
            s.style.left = p.left + 'px';
            s.style.display = 'inherit';
        }
    </script>
</head>
<body>
    <textarea id="text" onkeyup="show(this)" style="width: 340px; height: 210px;"></textarea>    
    <br />
    <input type="text" onkeyup="show(this)" style="width: 340px;" />
    <div id="show" style="width: 34px; height: 13px; background: #eee; position: absolute;border:1px solid grey;font-size:13px; display:none;">Tips</div>
</body>
</html>
```
#### 3.2 使用contenteditable="true"的div，如何获取其位置呢？

首先，先了解一下contenteditable="true"的div。可编辑的div，再结合它的表现来看，就是在div里添加的dom节点，文本就是text节点，想插入图片也是一个图片dom节点进行插入。

主要的思路-[把获取光标位置转化为获取一个元素的位置就好](https://www.cnblogs.com/idche/archive/2010/10/30/1865085.html)

1.利用keydown事件进行监听
2.监听到特殊字符，手动插入一个span元素
3.获取span元素的位置即光标的当前的位置

```javascript
   keydown(event) {
            const keyCode = event.keyCode || event.which || event.charCode;
            const { ctrlKey, altKey, shiftKey } = event;
            //组合键的监听
           if (shiftKey && (keyCode === 50 || keyCode === 229)) {
                //监听到特殊字符@
                let spanChild = document.querySelector("#sflag");
                let spanHtml = "";
                if (spanChild) {
                    //假如有先移除
                    spanChild.parentNode.removeChild(spanChild);
                    spanHtml = '@<span id="sflag">@</span>';//有此flag则肯定前一个字符是@
                } else {
                    spanHtml = '<span id="sflag">@</span>';
                }
                //插入到contenteditable="true"的div中
                insertContent(spanHtml)
                //获取其位置
                let spanFlag = document.querySelector("#sflag");
                if (spanFlag) {
                    let position = spanFlag.getClientRects()[0];
                    console.log(position)
                }
                event.preventDefault(); // 阻止浏览器输入@
                return false;
            }
        },
```

摘要的文章链接：

https://www.cnblogs.com/kingwolfofsky/archive/2011/07/05/2098446.html

https://www.cnblogs.com/idche/archive/2010/10/30/1865085.html

https://blog.csdn.net/mafan121/article/details/79280529