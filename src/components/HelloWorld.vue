<template>
  <div class="markdown" :style="{width:this.width,height:this.height}">
    <!--功能按钮区-->
    <ul class="markdown-toolbars">
      <li title="粗体">
        <span @click="Strong" class="iconfont icon-bold"></span>
      </li>
      <li title="斜体">
        <span @click="Italic" class="iconfont icon-italic"></span>
      </li>
      <li title="删除线">
        <span @click="Overline" class="iconfont icon-overline"></span>
      </li>
      <li title="下划线">
        <span @click="Underline" class="iconfont icon-under-line"></span>
      </li>
      <li title="标题1">
        <span @click="Title(1)" style="font-size:16px;">h1</span>
      </li>
      <li title="标题2">
        <span @click="Title(2)" style="font-size:16px;">h2</span>
      </li>
      <li title="标题3">
        <span @click="Title(3)" style="font-size:16px;">h3</span>
      </li>
      <li title="标题4">
        <span @click="Title(4)" style="font-size:16px;">h4</span>
      </li>
      <li title="标题5">
        <span @click="Title(5)" style="font-size:16px;">h5</span>
      </li>
      <li title="标题6">
        <span @click="Title(6)" style="font-size:16px;">h6</span>
      </li>
      <li title="分割线">
        <span @click="Line" class="iconfont icon-horizontal"></span>
      </li>
      <li title="引用">
        <span @click="Quote" class="iconfont icon-quote"></span>
      </li>l
      <li title="无序列表">
        <span @click="Ul" class="iconfont icon-ul"></span>
      </li>
      <li title="有序列表">
        <span @click="Ol" class="iconfont icon-ol"></span>
      </li>
      <li title="代码块">
        <span @click="Code" class="iconfont icon-code"></span>
      </li>
      <li title="链接">
        <span @click="Link" class="iconfont icon-link"></span>
      </li>
      <li title="图片">
        <span @click="Image" class="iconfont icon-img"></span>
      </li>
    </ul>
    <!--主要内容区-->
    <div class="main_content">
      <!--编辑区-->
      <div class="markdown_content">
        <ul
          class="index"
          ref="index"
          :style="{
              height: textareaHeight ? `${textareaHeight}px` : '100%'
          }"
        >
            <li v-for="index in indexLenth" :key="index">
                {{ index }}
            </li>
        </ul>
        <textarea v-model="markdownString" class="textarea_content" ref="edit" @keyup.enter="enter"></textarea>
      </div>
      <!--解析区-->
      <div class="html_content">
        <p v-html="htmlString"></p>
      </div>
    </div>
  </div>
</template>

<script>
import "../assets/font/iconfont.css";
import marked from 'marked'
import hljs from 'highlight.js'
export default {
  name: 'HelloWorld',
  props: {
    width: {
      type:String,
      default:'100%'
    },
    height: {
      type:String,
      default:'600px'
    }
  },
  data(){
    return {
      markdownString:'',
      htmlString:'',
      indexLenth:0
    }
  },
  updated(){
    this.indexLenth = this.markdownString.split('\n').length;
  },
  methods: {
    Strong(){
      const point = this.getCursortPosition();
      this.changeSelectedText("**","**")
      this.setCaretPosition(point + 2);
    },
    Italic(){
      const point = this.getCursortPosition();
      this.changeSelectedText("***","***")
      this.setCaretPosition(point + 3);
    },
    Overline(){
      const point = this.getCursortPosition();
      this.changeSelectedText("~~","~~")
      this.setCaretPosition(point + 2);
    },
    Underline(){
      const point = this.getCursortPosition();
      this.changeSelectedText("<u>","</u>")
      this.setCaretPosition(point + 3);
    },
    Title(level){
      const levelObj ={
        1:"# ",
        2:"## ",
        3:"### ",
        4:"#### ",
        5:"##### ",
        6:"###### "
      }
      const point = this.getCursortPosition();
      this.changeSelectedText(levelObj[level],"")
      this.setCaretPosition(point + level + 1);
    },
    Line(){
      const point = this.getCursortPosition();
      this.changeSelectedText("\n----\n","")
      this.setCaretPosition(point + 6);
    },
    Quote(){
      const point = this.getCursortPosition();
      this.changeSelectedText("\n> ","")
      this.setCaretPosition(point + 3);
    },
    Ul(){
      const point = this.getCursortPosition();
      this.changeSelectedText("- ","")
      this.setCaretPosition(point + 2);
    },
    Ol(){
      const point = this.getCursortPosition();
      this.changeSelectedText("1. ","")
      this.setCaretPosition(point + 3);
    },
    Code(){
      const point = this.getCursortPosition();
      this.changeSelectedText("\n```\n","\n```")
      this.setCaretPosition(point + 5);
    },
    Link(){
      const point = this.getCursortPosition();
      this.changeSelectedText("\n[link](href)","")
      this.setCaretPosition(point + 11);
    },
    Image(){
      const point = this.getCursortPosition();
      this.changeSelectedText("\n![image](imgUrl)","")
      this.setCaretPosition(point + 16);
    },
    changeSelectedText(startString,endString){
      let editContent = this.$refs.edit
      if(window.getSelection){
        if(editContent.selectionStart != undefined && editContent.selectionEnd != undefined){
          let editContentOne = editContent.value.substring(0,editContent.selectionStart)
          let editContentTwo = editContent.value.substring(editContent.selectionStart,editContent.selectionEnd)
          let editContentThree = editContent.value.substring(editContent.selectionEnd)
          let editContentRes = editContentOne + startString + editContentTwo + endString + editContentThree
          editContent.value = editContentRes
          this.markdownString = editContent.value
          console.log(this.markdownString)
        }
      }
    },
    getCursortPosition() {
            // 获取光标位置
            const textDom = this.$refs.edit;
            let cursorPos = 0;
            if (document.selection) {
                textDom.focus();
                let selectRange = document.selection.createRange();
                selectRange.moveStart('character', -this.currentValue.length);
                cursorPos = selectRange.text.length;
            } else if (
                textDom.selectionStart ||
                parseInt(textDom.selectionStart, 0) === 0
            ) {
                cursorPos = textDom.selectionStart;
            }
            return cursorPos;
        },
      setCaretPosition(position) {
            // 设置光标位置
            const textDom = this.$refs.edit;
            if (textDom.setSelectionRange) {
                textDom.focus();
                textDom.setSelectionRange(position, position);
            } else if (textDom.createTextRange) {
                let range = textDom.createTextRange();
                range.collapse(true);
                range.moveEnd('character', position);
                range.moveStart('character', position);
                range.select();
            }
        },
  },
  watch: {

      //监听markString变化
      markdownString: function (value) {
        marked.setOptions({
          renderer: new marked.Renderer(),
          gfm: true,
          tables: true,
          breaks: true,
          pedantic: false,
          sanitize: false,
          smartLists: true,
          smartypants: false,
          highlight: function (code) {
        return hljs.highlightAuto(code).value;
    }
        })

        this.htmlString = marked(value)
        console.log(this.htmlString)
      },

      //监听htmlString并对其高亮
      htmlString: function () {
        this.$nextTick(() => {
          const codes = document.querySelectorAll(".html_content pre code");

          // elem 是一个 object
          codes.forEach(elem => {
            elem.innerHTML = "<ul><li>" + elem.innerHTML.replace(/\n(?=.)/g, "\n</li><li>") + "\n</li></ul>"
            hljs.highlightBlock(elem);
          });
        });
      }
    }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
* {
        margin: 0;
        padding: 0;
        box-sizing: border-box;
    }
.markdown {
    overflow: hidden;
    position: relative;
    margin: 0;
    padding: 0;
    box-sizing: border-box;
    display: flex;
    justify-content: space-between;
    align-items: center;
    flex-direction: column;
    border: 1px solid black;
}
.markdown-toolbars {
  width: 100%;
  display: flex;
  align-items: center;
  list-style: none;
  background: #fff;
  color: #6a6f7b;
  height: 40px;
  cursor: pointer;
  padding-left: 4px;
  border-bottom: 1px solid black;
}
.markdown-toolbars li {
  position: relative;
  cursor: pointer;
}
.markdown-toolbars li span {
  font-size: 18px;
  color: #999;
  cursor: pointer;
  display: block;
  width: 30px;
  height: 30px;
  border-radius: 3px;
  line-height: 30px;
  text-align: center;
}
/* .markdown-toolbars li span:after {
  display: block;
  content: "wode";
  position: absolute;
  z-index: 999999999999;
  top: 32px;
  left: 20px;
  background: #000;
  color: #fff;
  white-space: nowrap;
  font-size: 12px;
  line-height: 28px;
  padding: 0 6px;
  transition: all 0.3s 0.1s;
  transform: scale(0);
  opacity: 0;
  transform-origin: top;
  border-radius: 2px;
}
.markdown-toolbars li span:hover{
  background-color: #D3D3D3;
  color: cornflowerblue;
} */
.markdown-toolbars li span:hover::after{
  transform: scale(1);
  opacity: 1;
}
.main_content{
  display: flex;
  width: 100%;
  height: 100%;
}
.markdown_content{
  display: flex;
  width: 50%;
  height: 100%;
  /* background-color: black; */
}
.html_content{
  display: flex;
  width: 50%;
  height: 100%;
}
.textarea_content{
  flex: 1;
  height: 100%;
  padding: 12px;
  overflow: auto;
  resize: none;
  outline: none;
  border: none;
  background-color: black;
  color: #fff;
  font-size: 14px;
  line-height: 24px;
}
.index {
    background: #272727;
    min-height: 100%;
    width: 36px;
    line-height: 24px;
    padding: 12px 0;
  }
.index li {
    background: #272727;
    color: #ccc;
    font-size: 14px;
    text-align: center;
    font-family: Consolas;
}
</style>
