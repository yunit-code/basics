<template>
  <!--
    根目录规范(必须不能为空)：
    idm-ctrl：控件类型 drag_container：容器，drag_container_inlieblock：行内容器，idm_module：非容器的组件
    id：使用moduleObject.id，如果id不使用这个将会被idm-ctrl-id属性替换
    idm-ctrl-id：组件的id，这个必须不能为空
  -->
  <div
    idm-ctrl="idm_module"
    :id="moduleObject.id"
    :idm-ctrl-id="moduleObject.id"
    v-if="componentVisibleStatus"
  >
    <!--
      组件内部容器
      增加class="drag_container" 必选
      idm-ctrl-id：组件的id，这个必须不能为空
      idm-container-index  组件的内部容器索引，不重复唯一且不变，必选
    -->
    <a
      @click="linkClickHandle"
      class="idm-module-alink"
      :class="{ ellipsis: propData.ellipsis }"
      href="javascript:;"
      :title="linkData.linkText"
      >{{ linkData.linkText }}</a
    >
  </div>
</template>

<script>
export default {
  name: "ILink",
  data() {
    return {
      errorMessage: "",
      thisValue: "",
      moduleObject: {},
      propData: this.$root.propData.compositeAttr || {},
      componentVisibleStatus: false,
      isErrorStatus: false,
      //最终显示的文字
      linkData: { linkText: "" },
      functionParam: {
        linkMessageObject: null,
        customFunData: null,
        pageInterfaceData: null,
        dataSourceData: null,
      },
    };
  },
  props: {},
  created() {
    this.moduleObject = this.$root.moduleObject;
    this.setLinkText();
    // console.log(this.moduleObject)
    // this.propData = testAttr;
    this.convertAttrToStyleObject();
    this.initComponentStatus();
  },
  mounted() {},
  destroyed() {},
  methods: {
    /**
     * 初始化控件的状态
     */
    initComponentStatus(
      linkMessageObject,
      customFunData,
      pageInterfaceData,
      dataSourceData
    ) {
      if (
        this.moduleObject.env == undefined ||
        this.moduleObject.env == "develop"
      ) {
        this.componentVisibleStatus = true;
        return;
      }
      this.functionParam.linkMessageObject = linkMessageObject;
      this.functionParam.customFunData = customFunData;
      this.functionParam.pageInterfaceData = pageInterfaceData;
      this.functionParam.dataSourceData = dataSourceData;
      switch (this.propData.defaultStatus) {
        case "default":
          this.componentVisibleStatus = true;
          break;
        case "hidden":
          this.componentVisibleStatus = false;
          break;
        case "custom":
          if (
            this.propData.statusCustomFunction &&
            this.propData.statusCustomFunction.length
          ) {
            let result = IDM.invokeCustomFunctions.apply(this, [
              this.propData.statusCustomFunction,
              {
                linkMessageObject,
                customFunData,
                pageInterfaceData,
                dataSourceData,
              },
            ]);
            this.componentVisibleStatus =
              result && result.length > 0 && result[0] === true;
          } else if (this.propData.statusExpression) {
            this.componentVisibleStatus = IDM.getExpressData(
              this.propData.statusExpression,
              {
                linkMessageObject,
                customFunData,
                pageInterfaceData,
                dataSourceData,
              }
            );
          }
          break;
      }
    },
    setLinkText(name) {
      if (name) {
        this.$set(this.linkData, "linkText", name);
      } else if (this.propData.content) {
        this.$set(this.linkData, "linkText", this.propData.content);
      }
    },
    /**
     * 把属性转换成超链接的样式设置(active)
     */
    convertAttrToLinkActiveStyle() {
      var styleObject = {};
      const keyList = ["borderActive", "fontActive"];
      for (const iKey in keyList) {
        const key = keyList[iKey];
        if (this.propData.hasOwnProperty.call(this.propData, key)) {
          const element = this.propData[key];
          if (!element && element !== false && element != 0) {
            continue;
          }
          switch (key) {
            case "borderActive":
              IDM.style.setBorderStyle(styleObject, element);
              break;
            case "fontActive":
              IDM.style.setFontStyle(styleObject, element);
              if (element.fontSize && element.fontSizeUnit) {
                window.IDM.setStyleToPageHead(
                  this.moduleObject.id + " button:active .button-svg-icon",
                  {
                    "font-size": element.fontSize + element.fontSizeUnit,
                    "max-height": element.fontSize + element.fontSizeUnit,
                    width: element.fontSize + element.fontSizeUnit,
                  }
                );
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
      window.IDM.setStyleToPageHead(
        this.moduleObject.id + " a:active",
        styleObject
      );
    },
    /**
     * 把属性转换成超链接的样式设置(focus)
     */
    convertAttrToLinkFocusStyle() {
      var styleObject = {};
      const keyList = ["borderFocus", "fontFocus"];
      for (const iKey in keyList) {
        const key = keyList[iKey];
        if (this.propData.hasOwnProperty.call(this.propData, key)) {
          const element = this.propData[key];
          if (!element && element !== false && element != 0) {
            continue;
          }
          switch (key) {
            case "borderFocus":
              IDM.style.setBorderStyle(styleObject, element);
              break;
            case "fontFocus":
              IDM.style.setFontStyle(styleObject, element);
              if (element.fontSize && element.fontSizeUnit) {
                window.IDM.setStyleToPageHead(
                  this.moduleObject.id + " button:focus .button-svg-icon",
                  {
                    "font-size": element.fontSize + element.fontSizeUnit,
                    "max-height": element.fontSize + element.fontSizeUnit,
                    width: element.fontSize + element.fontSizeUnit,
                  }
                );
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
      window.IDM.setStyleToPageHead(
        this.moduleObject.id + " a:focus",
        styleObject
      );
    },
    /**
     * 把属性转换成超链接的样式设置(hover)
     */
    convertAttrToLinkHoverStyle() {
      var styleObject = {};
      const keyList = ["borderHover", "fontHover"];
      for (const iKey in keyList) {
        const key = keyList[iKey];
        if (this.propData.hasOwnProperty.call(this.propData, key)) {
          const element = this.propData[key];
          if (!element && element !== false && element != 0) {
            continue;
          }
          switch (key) {
            case "borderHover":
              IDM.style.setBorderStyle(styleObject, element);
              break;
            case "fontHover":
              IDM.style.setFontStyle(styleObject, element);
              if (element.fontSize && element.fontSizeUnit) {
                window.IDM.setStyleToPageHead(
                  this.moduleObject.id + " button:hover .button-svg-icon",
                  {
                    "font-size": element.fontSize + element.fontSizeUnit,
                    "max-height": element.fontSize + element.fontSizeUnit,
                    width: element.fontSize + element.fontSizeUnit,
                  }
                );
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
      window.IDM.setStyleToPageHead(
        this.moduleObject.id + " a:hover",
        styleObject
      );
    },
    /**
     * 把属性转换成超链接的样式设置(默认)
     */
    convertAttrToLinkDefaultStyle() {
      var styleObject = {};
      const keyList = ["borderDefault", "fontDefault"];
      for (const iKey in keyList) {
        const key = keyList[iKey];
        if (this.propData.hasOwnProperty.call(this.propData, key)) {
          const element = this.propData[key];
          if (!element && element !== false && element != 0) {
            continue;
          }
          switch (key) {
            case "borderDefault":
              IDM.style.setBorderStyle(styleObject, element);
              break;
            case "fontDefault":
              IDM.style.setFontStyle(styleObject, element);
              if (element.fontSize && element.fontSizeUnit) {
                window.IDM.setStyleToPageHead(
                  this.moduleObject.id + " button .button-svg-icon",
                  {
                    "font-size": element.fontSize + element.fontSizeUnit,
                    "max-height": element.fontSize + element.fontSizeUnit,
                    width: element.fontSize + element.fontSizeUnit,
                  }
                );
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
      window.IDM.setStyleToPageHead(this.moduleObject.id + " a", styleObject);
    },
    /**
     * 把属性转换成超链接的样式设置(基础)
     */
    convertAttrToLinkBaseStyle() {
      var styleObject = {};
      const keyList = ["width", "height", "box"];
      for (const iKey in keyList) {
        const key = keyList[iKey];
        if (this.propData.hasOwnProperty.call(this.propData, key)) {
          const element = this.propData[key];
          if (!element && element !== false && element != 0) {
            continue;
          }
          switch (key) {
            case "width":
            case "height":
              if (element != "auto") {
                styleObject[key] = element;
              }
              break;
            case "box":
              IDM.style.setBoxStyle(styleObject, element);
              break;
          }
        }
      }
      window.IDM.setStyleToPageHead(this.moduleObject.id + " a", styleObject);
    },
    /**
     * 提供父级组件调用的刷新prop数据组件
     */
    propDataWatchHandle(propData) {
      this.propData = propData.compositeAttr || {};
      this.setLinkText();
      this.convertAttrToStyleObject();
    },
    /**
     * 把属性转换成样式对象
     */
    convertAttrToStyleObject() {
      //默认值
      this.convertAttrToLinkBaseStyle();
      //样式设置下面四种状态:
      if (this.propData.linkType == "custom") {
        //默认
        this.convertAttrToLinkDefaultStyle();
        //hover
        this.convertAttrToLinkHoverStyle();
        //focus
        this.convertAttrToLinkFocusStyle();
        //active
        this.convertAttrToLinkActiveStyle();
      }
      this.initData();
    },
    /**
     * 加载动态数据
     */
    initData() {
      let that = this;
      //所有地址的url参数转换
      var params = that.commonParam();
      switch (this.propData.dataSourceType) {
        case "customInterface":
          this.propData.customInterfaceUrl &&
            window.IDM.http
              .get(this.propData.customInterfaceUrl, params)
              .then((res) => {
                //res.data

                that.setLinkText(
                  that.getExpressData(
                    "resultData",
                    that.propData.dataFiled,
                    res.data
                  )
                );
                that.initComponentStatus(null, null, null, res.data);
                // that.propData.fontContent = ;
              })
              .catch(function (error) {});
          break;

        case "datasource":
          if (
            this.propData.dataSourceSelectData &&
            this.propData.dataSourceSelectData.length
          ) {
            IDM.datasource.request(
              this.propData.dataSourceSelectData[0].id,
              {
                moduleObject: this.moduleObject,
                param: this.commonParam(),
              },
              function (resData) {
                //这里是请求成功的返回结果
                that.setLinkText(
                  that.getExpressData(
                    "resultData",
                    that.propData.dataFiled,
                    resData
                  )
                );

                that.initComponentStatus(null, null, null, resData);
              },
              function (error) {
                //这里是请求失败的返回结果
                that.isErrorStatus = true;
              }
            );
          }
          break;
        case "pageCommonInterface":
          //使用通用接口直接跳过，在setContextValue执行
          break;
        case "customFunction":
          if (
            this.propData.customFunction &&
            this.propData.customFunction.length > 0
          ) {
            var resValue = "";
            try {
              resValue =
                window[this.propData.customFunction[0].name] &&
                window[this.propData.customFunction[0].name].call(this, {
                  ...params,
                  ...this.propData.customFunction[0].param,
                  moduleObject: this.moduleObject,
                });
            } catch (error) {}
            that.setLinkText(resValue);
            that.initComponentStatus(null, resValue, null, null);
          }
          break;
      }
    },
    /**
     * 通用的获取表达式匹配后的结果
     */
    getExpressData(dataName, dataFiled, resultData) {
      //给defaultValue设置dataFiled的值
      var _defaultVal = undefined;
      if (dataFiled) {
        _defaultVal = window.IDM.getExpressData(dataFiled, resultData);
      }
      //对结果进行再次函数自定义
      if (
        this.propData.customFunction &&
        this.propData.customFunction.length > 0
      ) {
        var params = this.commonParam();
        var resValue = "";
        try {
          resValue =
            window[this.propData.customFunction[0].name] &&
            window[this.propData.customFunction[0].name].call(this, {
              ...params,
              ...this.propData.customFunction[0].param,
              moduleObject: this.moduleObject,
              expressData: _defaultVal,
              interfaceData: resultData,
            });
        } catch (error) {}
        _defaultVal = resValue;
      }

      return _defaultVal;
    },
    /**
     * 通用的url参数对象
     * 所有地址的url参数转换
     */
    commonParam() {
      let urlObject = IDM.url.queryObject();
      var params = {
        pageId:
          window.IDM.broadcast && window.IDM.broadcast.pageModule
            ? window.IDM.broadcast.pageModule.id
            : "",
        urlData: JSON.stringify(urlObject),
      };
      return params;
    },
    /**
     * 内部点击事件
     */
    linkClickHandle(e) {
      let that = this;
      if (this.moduleObject.env == "develop") {
        //开发模式下不执行此事件
        return;
      }
      //获取所有的URL参数、页面ID（pageId）、以及所有组件的返回值（用范围值去调用IDM提供的方法取出所有的组件值）
      let urlObject = window.IDM.url.queryObject(),
        pageId =
          window.IDM.broadcast && window.IDM.broadcast.pageModule
            ? window.IDM.broadcast.pageModule.id
            : "";
      if (this.propData.linkUrl) {
        if (this.propData.openNewPage) {
          window.open(window.IDM.url.getWebPath(this.propData.linkUrl));
        } else {
          window.location.href = window.IDM.url.getWebPath(
            this.propData.linkUrl
          );
        }
      }
      //自定义函数
      /**
       * [
       * {name:"",param:{}}
       * ]
       */
      var clickFunction = this.propData.clickFunction;
      clickFunction &&
        clickFunction.forEach((item) => {
          window[item.name] &&
            window[item.name].call(this, {
              urlData: urlObject,
              pageId,
              customParam: item.param,
              _this: this,
              ...this.functionParam,
            });
        });
    },
    showThisModuleHandle() {
      this.componentVisibleStatus = true;
    },
    hideThisModuleHandle() {
      this.componentVisibleStatus = false;
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
    receiveBroadcastMessage(object) {
      if (object && object.type == "linkageShowModule") {
        this.showThisModuleHandle();
      } else if (object && object.type == "linkageHideModule") {
        this.hideThisModuleHandle();
      } else if (
        object &&
        object.type == "linkageResult" &&
        this.propData.dataSourceType === "receiveMessage" &&
        (!this.propData.receiveMessageKey ||
          (this.propData.receiveMessageKey &&
            this.propData.receiveMessageKey.length == 0) ||
          (this.propData.receiveMessageKey &&
            IDM.type(this.propData.receiveMessageKey) == "array" &&
            this.propData.receiveMessageKey.indexOf(object.messageKey) > -1) ||
          (IDM.type(this.propData.receiveMessageKey) == "string" &&
            this.propData.receiveMessageKey == object.messageKey))
      ) {
        //结果格式化
        this.setLinkText(
          this.getExpressData("", this.propData.dataFiled, object.message)
        );
        this.initComponentStatus(object, null, null, null);
      }
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
    sendBroadcastMessage(object) {
      window.IDM.broadcast && window.IDM.broadcast.send(object);
    },
    /**
     * 交互功能：设置组件的上下文内容值
     * @param {
     *  type:"定义的类型，已知类型：pageCommonInterface（页面统一接口返回值）、"
     *  key:"数据key标识，页面每个接口设置的数据集名称，方便识别获取自己需要的数据"
     *  data:"数据集，内容为：字符串 or 数组 or 对象"
     * }
     */
    setContextValue(object) {
      if (object.type != "pageCommonInterface") {
        return;
      }
      if (object.key == this.propData.dataName) {
        this.setLinkText(
          this.getExpressData(
            this.propData.dataName,
            this.propData.dataFiled,
            object.data
          )
        );
        this.initComponentStatus(null, null, object.data, null);
      }
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
    getContextValue() {},
    getStyle(key) {
      let styles = {};
      switch (key) {
        case "root":
          break;
        default:
          break;
      }
      return styles;
    },
  },
};
</script>
<style lang="scss" scoped>
.idm-module-alink {
  color: #1890ff;
  text-decoration: none;
  background-color: transparent;
  outline: none;
  cursor: pointer;
  transition: color 0.3s;
  &:hover {
    color: #40a9ff;
  }

  &:active {
    color: #096dd9;
  }

  &:active,
  a:hover {
    text-decoration: none;
    outline: 0;
  }

  &[disabled] {
    color: rgba(0, 0, 0, 0.25);
    cursor: not-allowed;
    pointer-events: none;
  }
  &.ellipsis {
    display: inline-block;
    white-space: nowrap;
    max-width: 100%;
    overflow: hidden;
    text-overflow: ellipsis;
  }
}
</style>