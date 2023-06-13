<template>
  <!--
    根目录规范(必须不能为空)：
    idm-ctrl：控件类型 drag_container：容器，drag_container_inlieblock：行内容器，idm_module：非容器的组件
    id：使用moduleObject.id，如果id不使用这个将会被idm-ctrl-id属性替换
    idm-ctrl-id：组件的id，这个必须不能为空
  -->
  <div idm-ctrl="idm_module" :id="moduleObject.id" :idm-ctrl-id="moduleObject.id">
    <!--
      组件内部容器
      增加class="drag_container" 必选
      idm-ctrl-id：组件的id，这个必须不能为空
      idm-container-index  组件的内部容器索引，不重复唯一且不变，必选
    -->
    <a @click="linkClickHandle" class="idm-module-alink" :class="{'ellipsis':propData.ellipsis}" href="javascript:;" v-if="propData.defaultStatus!='hidden'" :title="propData.content">{{propData.content}}</a>
  </div>
</template>

<script>
export default {
  name: 'ILink',
  data(){
    return {
      errorMessage:"",
      thisValue:"",
      moduleObject:{},
      propData:this.$root.propData.compositeAttr||{}
    }
  },
  props: {
  },
  created() {
    this.moduleObject = this.$root.moduleObject
    // console.log(this.moduleObject)
    // this.propData = testAttr;
    this.convertAttrToStyleObject();
  },
  mounted() {
  },
  destroyed() {},
  methods:{
    /**
     * 把属性转换成超链接的样式设置(active)
     */
    convertAttrToLinkActiveStyle(){
      var styleObject = {};
      const keyList=["borderActive","fontActive"];
      for (const iKey in keyList) {
        const key = keyList[iKey];
        if (this.propData.hasOwnProperty.call(this.propData, key)) {
          const element = this.propData[key];
          if(!element&&element!==false&&element!=0){
            continue;
          }
          switch (key) {
            case "borderActive":
              IDM.style.setBorderStyle(styleObject, element)
              break;
            case "fontActive":
              IDM.style.setFontStyle(styleObject, element)
              if(element.fontSize&&element.fontSizeUnit){
                window.IDM.setStyleToPageHead(this.moduleObject.id+" button:active .button-svg-icon",{
                  "font-size":element.fontSize+element.fontSizeUnit,
                  "max-height":element.fontSize+element.fontSizeUnit,
                  "width":element.fontSize+element.fontSizeUnit,
                });
              }
              break;
          }
        }
      }
      IDM.style.setBackgroundStyle(styleObject, this.propData, {
        bgSize: "bgSizeActive",
        bgSizeWidth: "bgSizeWidthActive",
        bgSizeHeight: "bgSizeHeightActive",
        positionX: "positionXActive",
        positionY: "positionYActive",
        bgColor: "bgColorActive",
        bgImgUrl: "bgImgUrlActive",
        bgRepeat: "bgRepeatActive",
        bgAttachment: "bgAttachmentActive",
      });
      window.IDM.setStyleToPageHead(this.moduleObject.id+" a:active",styleObject);
    },
    /**
     * 把属性转换成超链接的样式设置(focus)
     */
    convertAttrToLinkFocusStyle(){
      var styleObject = {};
      const keyList=["borderFocus","fontFocus"];
      for (const iKey in keyList) {
        const key = keyList[iKey];
        if (this.propData.hasOwnProperty.call(this.propData, key)) {
          const element = this.propData[key];
          if(!element&&element!==false&&element!=0){
            continue;
          }
          switch (key) {
            case "borderFocus":
              IDM.style.setBorderStyle(styleObject, element)
              break;
            case "fontFocus":
              IDM.style.setFontStyle(styleObject, element)
              if(element.fontSize&&element.fontSizeUnit){
                window.IDM.setStyleToPageHead(this.moduleObject.id+" button:focus .button-svg-icon",{
                  "font-size":element.fontSize+element.fontSizeUnit,
                  "max-height":element.fontSize+element.fontSizeUnit,
                  "width":element.fontSize+element.fontSizeUnit,
                });
              }
              break;
          }
        }
      }
      IDM.style.setBackgroundStyle(styleObject, this.propData, {
        bgSize: "bgSizeFocus",
        bgSizeWidth: "bgSizeWidthFocus",
        bgSizeHeight: "bgSizeHeightFocus",
        positionX: "positionXFocus",
        positionY: "positionYFocus",
        bgColor: "bgColorFocus",
        bgImgUrl: "bgImgUrlFocus",
        bgRepeat: "bgRepeatFocus",
        bgAttachment: "bgAttachmentFocus",
      });
      window.IDM.setStyleToPageHead(this.moduleObject.id+" a:focus",styleObject);
    },
    /**
     * 把属性转换成超链接的样式设置(hover)
     */
    convertAttrToLinkHoverStyle(){
      var styleObject = {};
      const keyList=["borderHover","fontHover"];
      for (const iKey in keyList) {
        const key = keyList[iKey];
        if (this.propData.hasOwnProperty.call(this.propData, key)) {
          const element = this.propData[key];
          if(!element&&element!==false&&element!=0){
            continue;
          }
          switch (key) {
            case "borderHover":
              IDM.style.setBorderStyle(styleObject, element)
              break;
            case "fontHover":
              IDM.style.setFontStyle(styleObject, element)
              if(element.fontSize&&element.fontSizeUnit){
                window.IDM.setStyleToPageHead(this.moduleObject.id+" button:hover .button-svg-icon",{
                  "font-size":element.fontSize+element.fontSizeUnit,
                  "max-height":element.fontSize+element.fontSizeUnit,
                  "width":element.fontSize+element.fontSizeUnit,
                });
              }
              break;
          }
        }
      }
      IDM.style.setBackgroundStyle(styleObject, this.propData, {
        bgSize: "bgSizeHover",
        bgSizeWidth: "bgSizeWidthHover",
        bgSizeHeight: "bgSizeHeightHover",
        positionX: "positionXHover",
        positionY: "positionYHover",
        bgColor: "bgColorHover",
        bgImgUrl: "bgImgUrlHover",
        bgRepeat: "bgRepeatHover",
        bgAttachment: "bgAttachmentHover",
      });
      window.IDM.setStyleToPageHead(this.moduleObject.id+" a:hover",styleObject);
    },
    /**
     * 把属性转换成超链接的样式设置(默认)
     */
    convertAttrToLinkDefaultStyle(){
      var styleObject = {};
      const keyList=["borderDefault","fontDefault"];
      for (const iKey in keyList) {
        const key = keyList[iKey];
        if (this.propData.hasOwnProperty.call(this.propData, key)) {
          const element = this.propData[key];
          if(!element&&element!==false&&element!=0){
            continue;
          }
          switch (key) {
            case "borderDefault":
              IDM.style.setBorderStyle(styleObject, element)
              break;
            case "fontDefault":
              IDM.style.setFontStyle(styleObject, element)
              if(element.fontSize&&element.fontSizeUnit){
                window.IDM.setStyleToPageHead(this.moduleObject.id+" button .button-svg-icon",{
                  "font-size":element.fontSize+element.fontSizeUnit,
                  "max-height":element.fontSize+element.fontSizeUnit,
                  "width":element.fontSize+element.fontSizeUnit,
                });
              }
              break;
          }
        }
      }
      IDM.style.setBackgroundStyle(styleObject, this.propData, {
        bgSize: "bgSizeDefault",
        bgSizeWidth: "bgSizeWidthDefault",
        bgSizeHeight: "bgSizeHeightDefault",
        positionX: "positionXDefault",
        positionY: "positionYDefault",
        bgColor: "bgColorDefault",
        bgImgUrl: "bgImgUrlDefault",
        bgRepeat: "bgRepeatDefault",
        bgAttachment: "bgAttachmentDefault",
      });
      window.IDM.setStyleToPageHead(this.moduleObject.id+" a",styleObject);
    },
    /**
     * 把属性转换成超链接的样式设置(基础)
     */
    convertAttrToLinkBaseStyle(){
      var styleObject = {};
      const keyList=["width","height","box"];
      for (const iKey in keyList) {
        const key = keyList[iKey];
        if (this.propData.hasOwnProperty.call(this.propData, key)) {
          const element = this.propData[key];
          if(!element&&element!==false&&element!=0){
            continue;
          }
          switch (key) {
            case "width":
            case "height":
              if(element!="auto"){
                styleObject[key]=element;
              }
              break;
            case "box":
              IDM.style.setBoxStyle(styleObject, element)
              break;
          }
        }
      }
      window.IDM.setStyleToPageHead(this.moduleObject.id+" a",styleObject);
    },
    /**
     * 提供父级组件调用的刷新prop数据组件
     */
    propDataWatchHandle(propData){
      this.propData = propData.compositeAttr||{};
      this.convertAttrToStyleObject();
    },
    /**
     * 把属性转换成样式对象
     */
    convertAttrToStyleObject(){
      //默认值
      this.convertAttrToLinkBaseStyle();
      //样式设置下面四种状态:
      if(this.propData.linkType=="custom"){
        //默认
        this.convertAttrToLinkDefaultStyle();
        //hover
        this.convertAttrToLinkHoverStyle();
        //focus
        this.convertAttrToLinkFocusStyle();
        //active
        this.convertAttrToLinkActiveStyle();
      }
      
    },
    /**
     * 内部点击事件
     */
    linkClickHandle(e){
      let that = this;
      if(this.moduleObject.env=="develop"){
        //开发模式下不执行此事件
        return;
      }
      //获取所有的URL参数、页面ID（pageId）、以及所有组件的返回值（用范围值去调用IDM提供的方法取出所有的组件值）
      let urlObject = window.IDM.url.queryObject(),
      pageId = window.IDM.broadcast&&window.IDM.broadcast.pageModule?window.IDM.broadcast.pageModule.id:"";
      if(this.propData.linkUrl){
        if(this.propData.openNewPage){
          window.open(window.IDM.url.getWebPath(this.propData.linkUrl));
        }else{
          window.location.href=window.IDM.url.getWebPath(this.propData.linkUrl);
        }
      }
      //自定义函数
      /**
       * [
       * {name:"",param:{}}
       * ]
       */
      var clickFunction = this.propData.clickFunction;
      clickFunction&&clickFunction.forEach(item=>{
        window[item.name]&&window[item.name].call(this,{
          urlData:urlObject,
          pageId,
          customParam:item.param,
          _this:this
        });
      })
    },
    /**
     * 组件通信：接收消息的方法
     * @param {
     *  type:"发送消息的时候定义的类型，这里可以自己用来要具体做什么，统一规定的type：linkageResult（组件联动传结果值）、linkageDemand（组件联动传需求值）、linkageReload（联动组件重新加载）
     * 、linkageOpenDialog（打开弹窗）、linkageCloseDialog（关闭弹窗）、linkageShowModule（显示组件）、linkageHideModule（隐藏组件）、linkageResetDefaultValue（重置默认值）"
     *  message:{发送的时候传输的消息对象数据}
     *  messageKey:"消息数据的key值，代表数据类型是什么，常用于表单交互上，比如通过这个key判断是什么数据"
     *  isAcross:如果为true则代表发送来源是其他页面的组件，默认为false
     * } object 
     */
    receiveBroadcastMessage(object){
      console.log("组件收到消息",object)
    },
    /**
     * 组件通信：发送消息的方法
     * @param {
     *  type:"自己定义的，统一规定的type：linkageResult（组件联动传结果值）、linkageDemand（组件联动传需求值）、linkageReload（联动组件重新加载）
     * 、linkageOpenDialog（打开弹窗）、linkageCloseDialog（关闭弹窗）、linkageShowModule（显示组件）、linkageHideModule（隐藏组件）、linkageResetDefaultValue（重置默认值）",
     *  message:{实际的消息对象},
     *  rangeModule:"为空发送给全部，根据配置的属性中设定的值（值的内容是组件的packageid数组），不取子表的，比如直接 this.$root.propData.compositeAttr["attrKey"]（注意attrKey是属性中定义的bindKey）,这里的格式为：['1','2']"",
     *  className:"指定的组件类型，比如只给待办组件发送，然后再去过滤上面的值"
     *  globalSend:如果为true则全站发送消息，注意全站rangeModule是无效的，只有className才有效，默认为false
     * } object 
     */
    sendBroadcastMessage(object){
        window.IDM.broadcast&&window.IDM.broadcast.send(object);
    },
    /**
     * 交互功能：设置组件的上下文内容值
     * @param {
     *  type:"定义的类型，已知类型：pageCommonInterface（页面统一接口返回值）、"
     *  key:"数据key标识，页面每个接口设置的数据集名称，方便识别获取自己需要的数据"
     *  data:"数据集，内容为：字符串 or 数组 or 对象"
     * }
     */
    setContextValue(object){
      console.log(object)
    },
    /**
     * 交互功能：获取需要返回的值，返回格式如下
     * @return {
     *    type:"success",
     *    message:"type为失败的时候原因",
     *    key:"定义的key标识，一般组件定义了一个属性，然后获取指定属性填写的值，这样返回后就能识别对应的字段或者元数据",
     *    data:{要返回的值，内容为：字符串 or 数组 or 对象},
     * }
     */
    getContextValue(){
    },
    getStyle(key){
        let styles = {};
        switch (key) {
            case "root":
            break;
            default:
            break;
        }
        return styles;
    }
  }
}
</script>
<style lang="scss" scoped>
.idm-module-alink{
    color: #1890ff;
    text-decoration: none;
    background-color: transparent;
    outline: none;
    cursor: pointer;
    transition: color .3s;
    &:hover {
        color: #40a9ff
    }

    &:active {
        color: #096dd9
    }

    &:active,a:hover {
        text-decoration: none;
        outline: 0
    }

    &[disabled] {
        color: rgba(0,0,0,.25);
        cursor: not-allowed;
        pointer-events: none
    }
    &.ellipsis{
      display: inline-block;
      white-space: nowrap;
      max-width: 100%;
      overflow: hidden;
      text-overflow: ellipsis;
    }
}
</style>