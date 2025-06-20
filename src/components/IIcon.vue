<template>
  <!--
    根目录规范(必须不能为空)：
    idm-ctrl：控件类型 drag_container：容器，drag_container_inlieblock：行内容器，idm_module：非容器的组件
    id：使用moduleObject.id，如果id不使用这个将会被idm-ctrl-id属性替换
    idm-ctrl-id：组件的id，这个必须不能为空
  -->
  <div
    idm-ctrl="idm_module"
    v-if="componentVisibleStatus"
    :id="moduleObject.id"
    :idm-ctrl-id="moduleObject.id"
  >
    <!--
      组件内部容器
      增加class="drag_container" 必选
      idm-ctrl-id：组件的id，这个必须不能为空
      idm-container-index  组件的内部容器索引，不重复唯一且不变，必选
    -->
    <template v-if="iconData.iconName">
      <i
        :class="`${iconData.familyStr + iconData.iconName}`"
        v-if="propData.iconType == 'iconfont'&&propData.iconMode!='symbol'"
        :style="iconStyleObject"
      ></i>
      <svg
        @click="iconClickHandle"
        class="idm-module-svg-icon"
        :style="iconStyleObject"
        v-else
        aria-hidden="true"
      >
        <use :xlink:href="`#${propData.iconType == 'iconfont'&&propData.iconMode=='symbol'?(iconJsonInfo.css_prefix_text + iconData.iconName):iconData.iconName}`"></use>
      </svg>
    </template>
    <template v-else>请选择图标</template>
    <!-- {{$root.propData}} -->
  </div>
</template>

<script>
export default {
  name: "IIcon",
  data() {
    return {
      moduleObject: this._moduleObject||{},
      propData: this._propData?.compositeAttr||this.$root?.propData?.compositeAttr || {},
      //最终显示的图标
      iconData: { iconName: "", familyStr: "" },
      componentVisibleStatus: false,
      isErrorStatus: false,
      iconStyleObject: {},
      functionParam: {
        linkMessageObject: null,
        customFunData: null,
        pageInterfaceData: null,
        dataSourceData: null,
      },
      iconJsonInfo:{}
    };
  },
  props: {
    _moduleObject: Object,
    _propData: Object
  },
  created() {
    this.moduleObject = this._moduleObject|| this.$root.moduleObject;
    this.setIconName();
    this.iconStyleObject.cursor =
      this.propData.clickFunction &&
      this.propData.clickFunction[0] &&
      this.propData.clickFunction[0].name
        ? "pointer"
        : "";
    // console.log(this.moduleObject)
    this.convertAttrToStyleObject();
    this.initComponentStatus();
    this.loadIconFile();
  },
  mounted() {
    //直接使用组件此处的回调必须的
    this._moduleObject&&IDM.callBackComponentMountComplete?.apply(this,[this._moduleObject]);
  },
  destroyed() {},
  methods: {
    // 加载css
    loadIconFile() {
      const that = this;
      if (this.propData.iconfontUrl && this.propData.iconType == "iconfont") {
        let baseUrl =
          this.propData.iconfontUrl +
          (this.propData.iconfontUrl.substring(
            this.propData.iconfontUrl.length - 1,
            this.propData.iconfontUrl.length
          ) == "/"
            ? ""
            : "/");
        IDM.http
          .get(baseUrl + "iconfont.json", {})
          .then((res) => {
            if (!res.data) {
              return;
            }
            
            that.iconJsonInfo = res.data;
            //存在，加载css
            IDM.http
            .importFiles([
              IDM.url.getWebPath(baseUrl + "iconfont."+(that.propData.iconMode=='symbol'?'js':'css'))
            ])
            .then(() => {
              //加载完成
            })
            .catch((err) => {
            });
          })
          .catch(function (error) {
            console.log(error);
          });
        // 自定义
        const isCustom = this.propData.iconfontUrl ? true : false;
        // 取自定义字段 默认 iconfont
        let fontFamily =
          isCustom && this.propData.iconFontFamily ? this.propData.iconFontFamily : "";
        // 取自定义前缀 默认icon-
        let prefix = isCustom && this.propData.iconPrefix ? this.propData.iconPrefix : "";
        this.$set(this.iconData, "familyStr", `${fontFamily} ${prefix}`);
      }
    },
    /**
     * 初始化控件的状态
     */
    initComponentStatus(
      linkMessageObject,
      customFunData,
      pageInterfaceData,
      dataSourceData
    ) {
      if (this.moduleObject.env == undefined || this.moduleObject.env == "develop") {
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
      ["iconColorCustomFunction", "iconSizeCustomFunction"].forEach((funName) => {
        if (this.propData[funName] && this.propData[funName].length) {
          let styleData = IDM.invokeCustomFunctions.apply(this, [
            this.propData[funName],
            {
              linkMessageObject,
              customFunData,
              pageInterfaceData,
              dataSourceData,
            },
          ]);
          if (styleData && styleData.length > 0) {
            switch (funName) {
              case "iconColorCustomFunction":
                this.$set(this.iconStyleObject, "color", styleData[0] + " !important")
                break;
              case "iconSizeCustomFunction":
                this.$set(this.iconStyleObject, "font-size", styleData[0] + " !important")
                if (this.propData.iconType == "select") {
                  this.$set(this.iconStyleObject, "max-width", styleData[0] + " !important");
                  this.$set(this.iconStyleObject, "width", styleData[0] + " !important");
                }
                break;
            }
          }
        }
      });
    },
    setIconName(name) {
      if (name) {
        this.$set(this.iconData, "iconName", name);
        // this.iconName = name;
      } else if (this.propData.iconFontName && this.propData.iconType == "iconfont") {
        this.$set(this.iconData, "iconName", this.propData.iconFontName);
      } else if (
        this.propData.icon &&
        this.propData.icon.length > 0 &&
        this.propData.iconType == "select"
      ) {
        // this.iconName = this.propData.icon[0];
        this.$set(this.iconData, "iconName", this.propData.icon[0]);
      }
    },
    /**
     * 提供父级组件调用的刷新prop数据组件
     */
    propDataWatchHandle(propData) {
      this.propData = propData.compositeAttr || {};
      this.setIconName();
      this.convertAttrToStyleObject();
    },
    /**
     * 把属性转换成样式对象
     */
    convertAttrToStyleObject() {
      var styleObject = {};
      const keyList = ["box", "border", "bgColor"];
      for (const iKey in keyList) {
        const key = keyList[iKey];
        if (this.propData.hasOwnProperty.call(this.propData, key)) {
          const element = this.propData[key];
          if (!element && element !== false && element != 0) {
            continue;
          }
          switch (key) {
            case "box":
              IDM.style.setBoxStyle(styleObject, element);
              break;
            case "bgColor":
              if (element && element.hex8) {
                styleObject["background-color"] =
                  IDM.hex8ToRgbaString(element.hex8) + "  !important";
              }
              break;
            case "border":
              IDM.style.setBorderStyle(styleObject, element);
              break;
          }
        }
      }
      if (!this.propData.bgList?.bgList?.length) {
        IDM.style.setBackgroundStyle(styleObject, this.propData);
      } else if (Object.keys(this.propData.bgList.style).length) {
        Object.assign(styleObject, this.propData.bgList.style);
      }
      window.IDM.setStyleToPageHead(this.moduleObject.id, styleObject);
      //加载图片的样式对象
      this.convertAttrToIconStyleObject();
      this.initData();
    },
    /**
     * 把属性转换成按钮的样式设置(图标)
     */
    convertAttrToIconStyleObject() {
      var styleObject = {};
      const keyList = ["iconColor", "iconSize"];
      for (const iKey in keyList) {
        const key = keyList[iKey];
        if (this.propData.hasOwnProperty.call(this.propData, key)) {
          const element = this.propData[key];
          if (!element && element !== false && element != 0) {
            continue;
          }
          switch (key) {
            case "iconColor":
              if (element && element.hex8) {
                styleObject["color"] = IDM.hex8ToRgbaString(element.hex8) + " !important";
              }
              break;
            case "iconSize":
              styleObject["font-size"] = element + "px";
              break;
          }
        }
      }
      window.IDM.setStyleToPageHead(
        this.moduleObject.id + " .idm-module-svg-icon",
        styleObject
      );
      this.convertThemeListAttrToStyleObject();
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
     * 主题颜色
     */
    convertThemeListAttrToStyleObject() {
      var themeList = this.propData.themeList;
      if (!themeList || !this.propData.openTheme) {
        return;
      }
      const themeNamePrefix =
        IDM.setting &&
        IDM.setting.applications &&
        IDM.setting.applications.themeNamePrefix
          ? IDM.setting.applications.themeNamePrefix
          : "idm-theme-";
      for (var i = 0; i < themeList.length; i++) {
        var item = themeList[i];
        //item.key：为主题样式的key
        //item.mainColor：主要颜色值
        //item.minorColor：次要颜色值
        if (item.key != IDM.theme.getCurrentThemeInfo()) {
          //此处比对是不渲染输出不用的样式，如果页面会刷新就可以把此处放开
          continue;
        }
        let cssObject_color_main = {
          color: item.mainColor ? IDM.hex8ToRgbaString(item.mainColor.hex8) : "",
        };
        const firstPrefix =
          "." +
          themeNamePrefix +
          item.key +
          " #" +
          (this.moduleObject.packageid || "module_demo");

        IDM.setStyleToPageHead(`${firstPrefix}  .idm-module-svg-icon`, {
          ...cssObject_color_main,
        });
      }
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

                that.setIconName(
                  that.getExpressData("resultData", that.propData.dataFiled, res.data)
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
                that.setIconName(
                  that.getExpressData("resultData", that.propData.dataFiled, resData)
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
          if (this.propData.customFunction && this.propData.customFunction.length > 0) {
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
            that.setIconName(resValue);
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
      if (this.propData.customFunction && this.propData.customFunction.length > 0) {
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
     * 内部点击事件
     */
    iconClickHandle() {
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
      console.log("组件收到消息", object);
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
        this.setIconName(
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
      IDM.broadcast && IDM.broadcast.send(object);
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
      //这里使用的是子表，所以要循环匹配所有子表的属性然后再去设置修改默认值
      if (object.key == this.propData.dataName) {
        this.setIconName(
          this.getExpressData(
            this.propData.dataName,
            this.propData.dataFiled,
            object.data
          )
        );
        this.initComponentStatus(null, null, object.data, null);
      }
    },
  },
};
</script>
<style lang="scss" scoped>
.idm-module-svg-icon {
  font-size: 64px;
  max-height: 1em;
  width: 1em;
  fill: currentColor;
  vertical-align: middle;
}
</style>
