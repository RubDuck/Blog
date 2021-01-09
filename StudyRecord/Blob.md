```
function createASTElement (tag, attrs, parent) {
  return {
    type: 1,
    tag,
    attrsList: attrs,
    parent,
    children: []
  }
}
// parseHTML

parseHTML(template, {
  start(tag, attrs, unary) {
    // 开始标签解析，触发函数
  },
  end() {
    // 结束标签解析，触发函数
  },
  chars(text) {
    // 解析文本时，触发函数
  },
  comment(text) {
    // 注释节点，触发函数
  }
})

// 文本节点
parseHTML(template, {
  chars(text) {
    let element = { type: 3, text }
  }
})

// 注释节点
parseHTML(template, {
  comment(text) {
    let element = { type: 3, text, isComment: true }
  }
})
```

#### blob

* 文件下载

  ```
  通过URL.createObjectURL(blob) 生成Blob URL, 赋值给a.download属性
  ```

* 图片显示

  ```
  通过URL.createObjectURL(blob)生成Blob URL, 赋值给img.src属性
  ```

* 资源分段上传

  ```
  blob.slice进行分割
  ```

* 本地读取文件

  ```
  FileReader的API将Blob或File转化为ArrayBuffer/Data URL 类型
  ```

  

