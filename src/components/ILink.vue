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
      if(this.propData.bgSizeActive&&this.propData.bgSizeActive=="custom"){
        styleObject["background-size"]=(this.propData.bgSizeWidthActive?this.propData.bgSizeWidthActive.inputVal+this.propData.bgSizeWidthActive.selectVal:"auto")+" "+(this.propData.bgSizeHeightActive?this.propData.bgSizeHeightActive.inputVal+this.propData.bgSizeHeightActive.selectVal:"auto")
      }else if(this.propData.bgSizeActive){
        styleObject["background-size"]=this.propData.bgSizeActive;
      }
      if(this.propData.positionXActive&&this.propData.positionXActive.inputVal){
        styleObject["background-position-x"]=this.propData.positionXActive.inputVal+this.propData.positionXActive.selectVal;
      }
      if(this.propData.positionYActive&&this.propData.positionYActive.inputVal){
        styleObject["background-position-y"]=this.propData.positionYActive.inputVal+this.propData.positionYActive.selectVal;
      }
      for (const key in this.propData) {
        if (this.propData.hasOwnProperty.call(this.propData, key)) {
          const element = this.propData[key];
          if(!element&&element!==false&&element!=0){
            continue;
          }
          switch (key) {
            case "bgColorActive":
              if(element&&element.hex8){
                styleObject["background-color"]=element.hex8;
              }
              break;
            case "bgImgUrlActive":
              styleObject["background-image"]=`url(${window.IDM.url.getWebPath(element)})`;
              break;
            case "positionXActive":
              //背景横向偏移
              
              break;
            case "positionYActive":
              //背景纵向偏移
              
              break;
            case "bgRepeatActive":
              //平铺模式
                styleObject["background-repeat"]=element;
              break;
            case "bgAttachmentActive":
              //背景模式
                styleObject["background-attachment"]=element;
              break;
            case "borderActive":
              if(element.border.top.width>0){
                styleObject["border-top-width"]=element.border.top.width+element.border.top.widthUnit;
                styleObject["border-top-style"]=element.border.top.style;
                if(element.border.top.colors.hex8){
                  styleObject["border-top-color"]=element.border.top.colors.hex8;
                }
              }
              if(element.border.right.width>0){
                styleObject["border-right-width"]=element.border.right.width+element.border.right.widthUnit;
                styleObject["border-right-style"]=element.border.right.style;
                if(element.border.right.colors.hex8){
                  styleObject["border-right-color"]=element.border.right.colors.hex8;
                }
              }
              if(element.border.bottom.width>0){
                styleObject["border-bottom-width"]=element.border.bottom.width+element.border.bottom.widthUnit;
                styleObject["border-bottom-style"]=element.border.bottom.style;
                if(element.border.bottom.colors.hex8){
                  styleObject["border-bottom-color"]=element.border.bottom.colors.hex8;
                }
              }
              if(element.border.left.width>0){
                styleObject["border-left-width"]=element.border.left.width+element.border.left.widthUnit;
                styleObject["border-left-style"]=element.border.left.style;
                if(element.border.left.colors.hex8){
                  styleObject["border-left-color"]=element.border.left.colors.hex8;
                }
              }
              
              styleObject["border-top-left-radius"]=element.radius.leftTop.radius+element.radius.leftTop.radiusUnit;
              styleObject["border-top-right-radius"]=element.radius.rightTop.radius+element.radius.rightTop.radiusUnit;
              styleObject["border-bottom-left-radius"]=element.radius.leftBottom.radius+element.radius.leftBottom.radiusUnit;
              styleObject["border-bottom-right-radius"]=element.radius.rightBottom.radius+element.radius.rightBottom.radiusUnit;
              break;
            case "fontActive":
              styleObject["font-family"]=element.fontFamily;
              if(element.fontColors.hex8){
                styleObject["color"]=element.fontColors.hex8;
              }
              styleObject["font-weight"]=element.fontWeight&&element.fontWeight.split(" ")[0];
              styleObject["font-style"]=element.fontStyle;
              styleObject["font-size"]=element.fontSize+element.fontSizeUnit;
              styleObject["line-height"]=element.fontLineHeight+(element.fontLineHeightUnit=="-"?"":element.fontLineHeightUnit);
              styleObject["text-align"]=element.fontTextAlign;
              styleObject["text-decoration"]=element.fontDecoration;
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
      window.IDM.setStyleToPageHead(this.moduleObject.id+" a:active",styleObject);
    },
    /**
     * 把属性转换成超链接的样式设置(focus)
     */
    convertAttrToLinkFocusStyle(){
      var styleObject = {};
      if(this.propData.bgSizeFocus&&this.propData.bgSizeFocus=="custom"){
        styleObject["background-size"]=(this.propData.bgSizeWidthFocus?this.propData.bgSizeWidthFocus.inputVal+this.propData.bgSizeWidthFocus.selectVal:"auto")+" "+(this.propData.bgSizeHeightFocus?this.propData.bgSizeHeightFocus.inputVal+this.propData.bgSizeHeightFocus.selectVal:"auto")
      }else if(this.propData.bgSizeFocus){
        styleObject["background-size"]=this.propData.bgSizeFocus;
      }
      if(this.propData.positionXFocus&&this.propData.positionXFocus.inputVal){
        styleObject["background-position-x"]=this.propData.positionXFocus.inputVal+this.propData.positionXFocus.selectVal;
      }
      if(this.propData.positionYFocus&&this.propData.positionYFocus.inputVal){
        styleObject["background-position-y"]=this.propData.positionYFocus.inputVal+this.propData.positionYFocus.selectVal;
      }
      for (const key in this.propData) {
        if (this.propData.hasOwnProperty.call(this.propData, key)) {
          const element = this.propData[key];
          if(!element&&element!==false&&element!=0){
            continue;
          }
          switch (key) {
            case "bgColorFocus":
              if(element&&element.hex8){
                styleObject["background-color"]=element.hex8;
              }
              break;
            case "bgImgUrlFocus":
              styleObject["background-image"]=`url(${window.IDM.url.getWebPath(element)})`;
              break;
            case "positionXFocus":
              //背景横向偏移
              
              break;
            case "positionYFocus":
              //背景纵向偏移
              
              break;
            case "bgRepeatFocus":
              //平铺模式
                styleObject["background-repeat"]=element;
              break;
            case "bgAttachmentFocus":
              //背景模式
                styleObject["background-attachment"]=element;
              break;
            case "borderFocus":
              if(element.border.top.width>0){
                styleObject["border-top-width"]=element.border.top.width+element.border.top.widthUnit;
                styleObject["border-top-style"]=element.border.top.style;
                if(element.border.top.colors.hex8){
                  styleObject["border-top-color"]=element.border.top.colors.hex8;
                }
              }
              if(element.border.right.width>0){
                styleObject["border-right-width"]=element.border.right.width+element.border.right.widthUnit;
                styleObject["border-right-style"]=element.border.right.style;
                if(element.border.right.colors.hex8){
                  styleObject["border-right-color"]=element.border.right.colors.hex8;
                }
              }
              if(element.border.bottom.width>0){
                styleObject["border-bottom-width"]=element.border.bottom.width+element.border.bottom.widthUnit;
                styleObject["border-bottom-style"]=element.border.bottom.style;
                if(element.border.bottom.colors.hex8){
                  styleObject["border-bottom-color"]=element.border.bottom.colors.hex8;
                }
              }
              if(element.border.left.width>0){
                styleObject["border-left-width"]=element.border.left.width+element.border.left.widthUnit;
                styleObject["border-left-style"]=element.border.left.style;
                if(element.border.left.colors.hex8){
                  styleObject["border-left-color"]=element.border.left.colors.hex8;
                }
              }
              
              styleObject["border-top-left-radius"]=element.radius.leftTop.radius+element.radius.leftTop.radiusUnit;
              styleObject["border-top-right-radius"]=element.radius.rightTop.radius+element.radius.rightTop.radiusUnit;
              styleObject["border-bottom-left-radius"]=element.radius.leftBottom.radius+element.radius.leftBottom.radiusUnit;
              styleObject["border-bottom-right-radius"]=element.radius.rightBottom.radius+element.radius.rightBottom.radiusUnit;
              break;
            case "fontFocus":
              styleObject["font-family"]=element.fontFamily;
              if(element.fontColors.hex8){
                styleObject["color"]=element.fontColors.hex8;
              }
              styleObject["font-weight"]=element.fontWeight&&element.fontWeight.split(" ")[0];
              styleObject["font-style"]=element.fontStyle;
              styleObject["font-size"]=element.fontSize+element.fontSizeUnit;
              styleObject["line-height"]=element.fontLineHeight+(element.fontLineHeightUnit=="-"?"":element.fontLineHeightUnit);
              styleObject["text-align"]=element.fontTextAlign;
              styleObject["text-decoration"]=element.fontDecoration;
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
      window.IDM.setStyleToPageHead(this.moduleObject.id+" a:focus",styleObject);
    },
    /**
     * 把属性转换成超链接的样式设置(hover)
     */
    convertAttrToLinkHoverStyle(){
      var styleObject = {};
      if(this.propData.bgSizeHover&&this.propData.bgSizeHover=="custom"){
        styleObject["background-size"]=(this.propData.bgSizeWidthHover?this.propData.bgSizeWidthHover.inputVal+this.propData.bgSizeWidthHover.selectVal:"auto")+" "+(this.propData.bgSizeHeightHover?this.propData.bgSizeHeightHover.inputVal+this.propData.bgSizeHeightHover.selectVal:"auto")
      }else if(this.propData.bgSizeHover){
        styleObject["background-size"]=this.propData.bgSizeHover;
      }
      if(this.propData.positionXHover&&this.propData.positionXHover.inputVal){
        styleObject["background-position-x"]=this.propData.positionXHover.inputVal+this.propData.positionXHover.selectVal;
      }
      if(this.propData.positionYHover&&this.propData.positionYHover.inputVal){
        styleObject["background-position-y"]=this.propData.positionYHover.inputVal+this.propData.positionYHover.selectVal;
      }
      for (const key in this.propData) {
        if (this.propData.hasOwnProperty.call(this.propData, key)) {
          const element = this.propData[key];
          if(!element&&element!==false&&element!=0){
            continue;
          }
          switch (key) {
            case "bgColorHover":
              if(element&&element.hex8){
                styleObject["background-color"]=element.hex8;
              }
              break;
            case "bgImgUrlHover":
              styleObject["background-image"]=`url(${window.IDM.url.getWebPath(element)})`;
              break;
            case "positionXHover":
              //背景横向偏移
              
              break;
            case "positionYHover":
              //背景纵向偏移
              
              break;
            case "bgRepeatHover":
              //平铺模式
                styleObject["background-repeat"]=element;
              break;
            case "bgAttachmentHover":
              //背景模式
                styleObject["background-attachment"]=element;
              break;
            case "borderHover":
              if(element.border.top.width>0){
                styleObject["border-top-width"]=element.border.top.width+element.border.top.widthUnit;
                styleObject["border-top-style"]=element.border.top.style;
                if(element.border.top.colors.hex8){
                  styleObject["border-top-color"]=element.border.top.colors.hex8;
                }
              }
              if(element.border.right.width>0){
                styleObject["border-right-width"]=element.border.right.width+element.border.right.widthUnit;
                styleObject["border-right-style"]=element.border.right.style;
                if(element.border.right.colors.hex8){
                  styleObject["border-right-color"]=element.border.right.colors.hex8;
                }
              }
              if(element.border.bottom.width>0){
                styleObject["border-bottom-width"]=element.border.bottom.width+element.border.bottom.widthUnit;
                styleObject["border-bottom-style"]=element.border.bottom.style;
                if(element.border.bottom.colors.hex8){
                  styleObject["border-bottom-color"]=element.border.bottom.colors.hex8;
                }
              }
              if(element.border.left.width>0){
                styleObject["border-left-width"]=element.border.left.width+element.border.left.widthUnit;
                styleObject["border-left-style"]=element.border.left.style;
                if(element.border.left.colors.hex8){
                  styleObject["border-left-color"]=element.border.left.colors.hex8;
                }
              }
              
              styleObject["border-top-left-radius"]=element.radius.leftTop.radius+element.radius.leftTop.radiusUnit;
              styleObject["border-top-right-radius"]=element.radius.rightTop.radius+element.radius.rightTop.radiusUnit;
              styleObject["border-bottom-left-radius"]=element.radius.leftBottom.radius+element.radius.leftBottom.radiusUnit;
              styleObject["border-bottom-right-radius"]=element.radius.rightBottom.radius+element.radius.rightBottom.radiusUnit;
              break;
            case "fontHover":
              styleObject["font-family"]=element.fontFamily;
              if(element.fontColors.hex8){
                styleObject["color"]=element.fontColors.hex8;
              }
              styleObject["font-weight"]=element.fontWeight&&element.fontWeight.split(" ")[0];
              styleObject["font-style"]=element.fontStyle;
              styleObject["font-size"]=element.fontSize+element.fontSizeUnit;
              styleObject["line-height"]=element.fontLineHeight+(element.fontLineHeightUnit=="-"?"":element.fontLineHeightUnit);
              styleObject["text-align"]=element.fontTextAlign;
              styleObject["text-decoration"]=element.fontDecoration;
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
      window.IDM.setStyleToPageHead(this.moduleObject.id+" a:hover",styleObject);
    },
    /**
     * 把属性转换成超链接的样式设置(默认)
     */
    convertAttrToLinkDefaultStyle(){
      var styleObject = {};
      if(this.propData.bgSizeDefault&&this.propData.bgSizeDefault=="custom"){
        styleObject["background-size"]=(this.propData.bgSizeWidthDefault?this.propData.bgSizeWidthDefault.inputVal+this.propData.bgSizeWidthDefault.selectVal:"auto")+" "+(this.propData.bgSizeHeightDefault?this.propData.bgSizeHeightDefault.inputVal+this.propData.bgSizeHeightDefault.selectVal:"auto")
      }else if(this.propData.bgSizeDefault){
        styleObject["background-size"]=this.propData.bgSizeDefault;
      }
      if(this.propData.positionXDefault&&this.propData.positionXDefault.inputVal){
        styleObject["background-position-x"]=this.propData.positionXDefault.inputVal+this.propData.positionXDefault.selectVal;
      }
      if(this.propData.positionYDefault&&this.propData.positionYDefault.inputVal){
        styleObject["background-position-y"]=this.propData.positionYDefault.inputVal+this.propData.positionYDefault.selectVal;
      }
      for (const key in this.propData) {
        if (this.propData.hasOwnProperty.call(this.propData, key)) {
          const element = this.propData[key];
          if(!element&&element!==false&&element!=0){
            continue;
          }
          switch (key) {
            case "bgColorDefault":
              if(element&&element.hex8){
                styleObject["background-color"]=element.hex8;
              }
              break;
            case "bgImgUrlDefault":
              styleObject["background-image"]=`url(${window.IDM.url.getWebPath(element)})`;
              break;
            case "positionXDefault":
              //背景横向偏移
              
              break;
            case "positionYDefault":
              //背景纵向偏移
              
              break;
            case "bgRepeatDefault":
              //平铺模式
                styleObject["background-repeat"]=element;
              break;
            case "bgAttachmentDefault":
              //背景模式
                styleObject["background-attachment"]=element;
              break;
            case "borderDefault":
              if(element.border.top.width>0){
                styleObject["border-top-width"]=element.border.top.width+element.border.top.widthUnit;
                styleObject["border-top-style"]=element.border.top.style;
                if(element.border.top.colors.hex8){
                  styleObject["border-top-color"]=element.border.top.colors.hex8;
                }
              }
              if(element.border.right.width>0){
                styleObject["border-right-width"]=element.border.right.width+element.border.right.widthUnit;
                styleObject["border-right-style"]=element.border.right.style;
                if(element.border.right.colors.hex8){
                  styleObject["border-right-color"]=element.border.right.colors.hex8;
                }
              }
              if(element.border.bottom.width>0){
                styleObject["border-bottom-width"]=element.border.bottom.width+element.border.bottom.widthUnit;
                styleObject["border-bottom-style"]=element.border.bottom.style;
                if(element.border.bottom.colors.hex8){
                  styleObject["border-bottom-color"]=element.border.bottom.colors.hex8;
                }
              }
              if(element.border.left.width>0){
                styleObject["border-left-width"]=element.border.left.width+element.border.left.widthUnit;
                styleObject["border-left-style"]=element.border.left.style;
                if(element.border.left.colors.hex8){
                  styleObject["border-left-color"]=element.border.left.colors.hex8;
                }
              }
              
              styleObject["border-top-left-radius"]=element.radius.leftTop.radius+element.radius.leftTop.radiusUnit;
              styleObject["border-top-right-radius"]=element.radius.rightTop.radius+element.radius.rightTop.radiusUnit;
              styleObject["border-bottom-left-radius"]=element.radius.leftBottom.radius+element.radius.leftBottom.radiusUnit;
              styleObject["border-bottom-right-radius"]=element.radius.rightBottom.radius+element.radius.rightBottom.radiusUnit;
              break;
            case "fontDefault":
              styleObject["font-family"]=element.fontFamily;
              if(element.fontColors.hex8){
                styleObject["color"]=element.fontColors.hex8;
              }
              styleObject["font-weight"]=element.fontWeight&&element.fontWeight.split(" ")[0];
              styleObject["font-style"]=element.fontStyle;
              styleObject["font-size"]=element.fontSize+element.fontSizeUnit;
              styleObject["line-height"]=element.fontLineHeight+(element.fontLineHeightUnit=="-"?"":element.fontLineHeightUnit);
              styleObject["text-align"]=element.fontTextAlign;
              styleObject["text-decoration"]=element.fontDecoration;
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
      window.IDM.setStyleToPageHead(this.moduleObject.id+" a",styleObject);
    },
    /**
     * 把属性转换成超链接的样式设置(基础)
     */
    convertAttrToLinkBaseStyle(){
      var styleObject = {};
      for (const key in this.propData) {
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
              if(element.marginTopVal){
                styleObject["margin-top"]=`${element.marginTopVal}`;
              }
              if(element.marginRightVal){
                styleObject["margin-right"]=`${element.marginRightVal}`;
              }
              if(element.marginBottomVal){
                styleObject["margin-bottom"]=`${element.marginBottomVal}`;
              }
              if(element.marginLeftVal){
                styleObject["margin-left"]=`${element.marginLeftVal}`;
              }
              if(element.paddingTopVal){
                styleObject["padding-top"]=`${element.paddingTopVal}`;
              }
              if(element.paddingRightVal){
                styleObject["padding-right"]=`${element.paddingRightVal}`;
              }
              if(element.paddingBottomVal){
                styleObject["padding-bottom"]=`${element.paddingBottomVal}`;
              }
              if(element.paddingLeftVal){
                styleObject["padding-left"]=`${element.paddingLeftVal}`;
              }
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
      clickFunction.forEach(item=>{
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