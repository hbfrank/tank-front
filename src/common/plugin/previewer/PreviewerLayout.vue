<template>
  <div></div>
</template>

<script>
  import PdfPanel from "./panels/PdfPanel"
  import TextPanel from "./panels/TextPanel"
  import OfficePanel from "./panels/OfficePanel"
  import AudioPanel from "./panels/AudioPanel"
  import VideoPanel from "./panels/VideoPanel"
  import {humanFileSize} from "../../filter/str";


  let CLASS_NAME = " previewer-mode"
  export default {
    data() {
      return {

      }
    },
    computed: {},
    props: {},
    watch: {},
    methods: {

      bodyAddClass() {
        document.body.className += CLASS_NAME
      },
      bodyRemoveClass() {
        let bodyClassName = document.body.className
        let position = bodyClassName.indexOf(CLASS_NAME);

        if (position !== -1) {
          document.body.className = bodyClassName.substr(0, position) + bodyClassName.substr(position + bodyClassName.length)
        }
      },

      preview(name, url, size, vNode) {
        let that = this;

        that.bodyAddClass()

        //需要给vue一点点时间去挂载这个vNode
        setTimeout(function () {
          if (vNode.componentInstance && vNode.componentInstance.show) {
            vNode.componentInstance.show()
          }
        }, 100)


        that.$msgbox({
          title: name + "(" + humanFileSize(size) + ")",
          message: vNode,
          center: true,
          showCancelButton: false,
          showConfirmButton: false,
          confirmButtonText: '确定',
          beforeClose: (action, instance, done) => {

            that.bodyRemoveClass()

            //如果有定义close函数，那么去调用。主要是音频和视频需要停止
            if (vNode.componentInstance && vNode.componentInstance.close) {
              vNode.componentInstance.close()
            }

            done();
          }
        }).then(action => {
        }).catch((e) => {
          //关闭了对话框
          that.bodyRemoveClass()
        });
      },
      previewPdf(name, url, size) {

        let that = this
        let vNode = this.$createElement(PdfPanel, {
          props: {
            name: name,
            url: url
          }
        });

        this.preview(name, url, size, vNode)
      },
      previewText(name, url, size) {

        let that = this
        let vNode = this.$createElement(TextPanel, {
          props: {
            name: name,
            url: url
          }
        });

        this.preview(name, url, size, vNode)
      },
      previewOffice(name, url, size) {

        let that = this
        let vNode = this.$createElement(OfficePanel, {
          props: {
            name: name,
            url: url
          }
        });

        this.preview(name, url, size, vNode)
      },
      previewAudio(name, url, size) {

        let that = this

        let vNode = this.$createElement(AudioPanel, {
          props: {
            name: name,
            url: url
          }
        });

        this.preview(name, url, size, vNode)
      },
      previewVideo(name, url, size) {

        let that = this

        let vNode = this.$createElement(VideoPanel, {
          props: {
            name: name,
            url: url
          }
        });

        this.preview(name, url, size, vNode)
      }

    },
    mounted() {


    }
  }
</script>
<style lang="less" rel="stylesheet/less">
  @import "../../../assets/css/global/variables";

  //弹出框出现时会在body中添加这个类
  .previewer-mode {

    .el-message-box {
      width: 95%;
      height: 95%;

      .el-message-box__content {
        height: 95%;

        .el-message-box__message {
          height: 100%;
        }
      }

    }

  }
</style>

