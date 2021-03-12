### 一、制作一个简单的编辑器会有几种方式？
1. 文本框 input
2. 文本域 textarea
3. 使用属性contenteditable,让一个div可编辑。

### 二、简单的编辑器涉及到复制、粘贴的问题

复制、粘贴的东西来自于剪切板Clipboard。

[Clipboard API](https://developer.mozilla.org/zh-CN/docs/Web/API/Clipboard_API)提供了响应剪贴板命令（剪切、复制和粘贴）与异步读写系统剪贴板的能力。从权限 Permissions API 获取权限之后，才能访问剪贴板内容；如果用户没有授予权限，则不允许读取或更改剪贴板内容。


https://w3c.github.io/clipboard-apis/

ckeditor读取剪切板的类-DataTransfer
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

