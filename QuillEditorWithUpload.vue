<template>
  <section class="quill-editor-with-upload">
    <quillEditor
      v-model="content"
      ref="quillEditor"
      :options="editorOption"
      @blur="onEditorBlur($event)"
      @focus="onEditorFocus($event)"
      @ready="onEditorReady($event)"
    ></quillEditor>
  </section>
</template>

<script>
import { quillEditor } from 'vue-quill-editor';
import 'quill/dist/quill.core.css';
import 'quill/dist/quill.snow.css';
import 'quill/dist/quill.bubble.css';

import { Quill } from 'vue-quill-editor';
import {
  container,
  ImageExtend,
  QuillWatch
} from './quill-image-extend-module';
Quill.register('modules/ImageExtend', ImageExtend);

export default {
  components: {
    quillEditor
  },
  props: {
    uploadAction: {
      type: String,
      default: '//gz-pro.oss-cn-shenzhen.aliyuncs.com',
      required: true
    },
    imgUrl: {
      type: String,
      default: '',
      required: true
    }
  },
  data() {
    return {
      content: '',
      editorOption: {
        theme: 'snow',
        modules: {
          ImageExtend: {
            loading: true,
            name: 'file',
            action: this.uploadAction,
            response: function() {
              return this.imgUrl;
            }.bind(this),
            end: () => {},
            error: () => {},
            success: () => {},
            change: this.change.bind(this)
          },
          toolbar: {
            container: container,
            handlers: {
              image: function() {
                QuillWatch.emit(this.editor.id);
                this.$emit('imageBtnClick');
              }.bind(this)
            }
          }
        }
      }
    };
  },
  methods: {
    change(xhr, formData) {
      this.$emit('imageChange', formData);
    },
    onEditorBlur(quill) {
      console.log('editor blur!', quill);
    },
    onEditorFocus(quill) {
      console.log('editor focus!', quill);
    },
    onEditorReady(quill) {
      console.log('editor ready!', quill);
    }
  },
  computed: {
    editor() {
      return this.$refs.quillEditor.quill;
    }
  },
  watch: {
    content(value) {
      return this.$emit('contentChange', value);
    }
  },
  created() {},
  mounted() {}
};
</script>

<style lang="scss" scoped>
.quill-editor {
  width: 100%;
}
/deep/ .ql-container {
  height:150px; //输入内容区域的尺寸
}
</style>
