# QuillEditor

基于[Vue-Quill-Editor](https://github.com/surmon-china/vue-quill-editor) 的富文本编辑器，根据图片上传接口，封装了上传图片到服务器的方法；

## Usage

```
npm install vue-quill-editor -S
```

```html
<QuillEditorWithUpload
  class="quill-editor-with-upload"
  :uploadAction="uploadAction"
  :imgUrl="uploadToken.url"
  @imageBtnClick="geToken"
  @imageChange="dealUploadFile"
  @contentChange="contentChange"
/>
```

```js
import { geTokenApi } from "@/api/index.js"; //获取上传token的接口
import QuillEditorWithUpload from "./QuillEditorWithUpload";
export default {
  components: { QuillEditorWithUpload },
  data() {
    return {
      uploadAction: "//gz-pro.oss-cn-shenzhen.aliyuncs.com",
      uploadToken: {
        accessid: "",
        policy: "",
        signature: "",
        key: "",
        url: ""
      }
    };
  },
  methods: {
    //获取token
    async geToken() {
      try {
        let res = await geTokenApi();
        if (!res.ok) {
          throw new Error(res);
        }
        this.uploadToken = res.result;
      } catch (error) {
        console.error(error.message);
      }
    },

    //为上传文件添加token
    dealUploadFile(formData) {
      //上传图片
      let uploadToken = this.uploadToken;
      let file = formData.get("file");
      formData.delete("file");
      formData.append("OSSAccessKeyId", uploadToken.accessid);
      formData.append("policy", uploadToken.policy);
      formData.append("Signature", uploadToken.signature);
      formData.append("key", uploadToken.key);
      formData.append("file", file);
    },

    // 输出内容
    contentChange(content) {
      console.log(content);
    }
  }
};
```

```css
.quill-editor-with-upload {
  width: 800px;
}
```

## Attributes

| 参数         | 类型   | 必填 | 可选 | 默认值                                  | 说明               |
| ------------ | ------ | ---- | ---- | --------------------------------------- | ------------------ |
| uploadAction | String | true | -    | '//gz-pro.oss-cn-shenzhen.aliyuncs.com' | 图片上传地址       |
| imgUrl       | String | true | -    | ''                                      | 上传图片的访问地址 |

## Events

| 事件名        | 回调参数 | 说明                                                                                    |
| ------------- | -------- | --------------------------------------------------------------------------------------- |
| imageBtnClick | -        | 插入图片按键被点击时触发                                                                |
| imageChange   | formData | 用户选择了图片时触发;返回参数 formData 的第一个字段名为'file',值为用户选择的文件 Blob， |
| contentChange | content  | 富文本内容改变时触发                                                                    |

## 备注

- 因为上传到 oss 有不会返回成功信息，所以在`quill-image-extend-module.js`文件有有个 FIXME
