# antd-auto  自动化CMS系统框架(文档创建中)

项目介绍，本项目主要是为了解决前端对于各类CMS系统开发的效率提升，不在收到CMS系统开发的包括组件，逻辑，展现等重复工作量。

1. 实现前端只需提供数据便可以实现各种数据展现，包括table，simple objest，以及各类图形展现（引入echarts）
2. 提供数据操作接口，前端只需关心数据的接受与传递，便可以实现数据的增删改查，而无需再关心CMS内部的实现。暂时实现 table数据的增删改查，simple object数据的修改等
3. 在以上全自动话的基础上，可以实现自定义组装页面，目前功能包括，图片上传组件（需要正对自己项目调整接口），富文本编辑器（Tinymce），复制功能，antd UI 组件等。


* 基于 dva 脚手架
* 基于 antd 2.0 UI组件
* 优化组织结构，优化代码，倾向于使用组件的 Stateless Functional Component 

## Start
npm start 开始本地调试项目 开启

## Build
npm Build 开始构建压缩

[ES6 react 实践的技术图](https://github.com/dvajs/dva-knowledgemap)

[dva 完成一个中型应用](https://github.com/dvajs/dva-docs/blob/master/v1/zh-cn/tutorial/01-%E6%A6%82%E8%A6%81.md)

[系统引用的UI组件文档 Ant Design of React](https://ant.design/docs/react/introduce)


## 整站配置文件详解

**src/config.js** 
需配置整体的后台系统数据，以及自定义的配置数据

    
    // header 管理系统头部配置(必要属性)
    //     title    String  管理系统显示的标题
    //     icon     String  管理系统显示的icon 可在 [icon](https://ant.design/components/icon/)
    //     style    Object  自定义设置头部样式
        
    // sider  管理后台侧栏配置(必要属性)
    //     menu         Array object   sider列表 实现单层or多层级展现
    //          title   展现的title
    //          key     sider中对应的选项（若为菜单主项，则在openKeys中使用选择是否打开，
    //                  若为功能项，则对应 selectedKey，以及 main 中 components 值）
    //          icon    展现的icon选项（可选）
    //          items   功能栏目列表（可选 可设置多层结构）

    //     openKeys     Array   默认展开的sider主导航项目（对应menu项目主栏目中的key）
    //     selectedKey  String  默认打开的目标功能页面key（对应menu项目功能栏目中的key）
    //     style        Object  自定义样式  
        
    // main  功能区域配置
    //     components   Object  配置sider对应功能区域组件
    //         FeatureKey     Object  对应sider menu 中的功能key对应功能组件
    //             title        String  功能区域标题显示名称
    //             component    require 加载对应功能区域的feature模块
                
    //     style        Object  配置样式  
    
    // header 示例 
    header: {
        title: "测试配置管理后台",
        icon: "appstore",
        style: {
            padding: "15px 15px 15px 25px",
            borderBottom: "1px solid #E9E9E9",
            backgroundColor: "#F5F5F5"
        }
    }
    
    // sider 边栏导航配置示例 
    sider: {
        // 层级列表
        menu: [
            {   
                // 多级
                title: "导航1", // 主导航名称
                key: "subTitle1",
                icon: "setting",
                items: [
                    {title: "选项1", key: "Feature1"},
                    {title: "选项2", key: "Feature2"},
                    {title: "选项3", key: "Feature3"},
                    {   
                        title: "导航3",
                        key: "subTitle3",
                        icon: "",
                        items: [
                            {title: "选项6", key: "Feature6"},
                            {title: "选项7", key: "Feature7"},
                            {title: "选项8", key: "Feature8"}
                        ]
                    }
                ]
            },{
                // 单级
                title: "选项5",
                key: "Feature5"
            }
        ],
        // 默认打开的导航项目
        openKeys:['subTitle1'],

        // 默认功能页
        selectedKey: "Feature1",

        // 自定义样式
        style: {
            backgroundColor: "#F5F5F5"
        }
    }

    // main 示例 
    main: {
        components: {
            // key 值对应 sider item中功能选项的 key
            "Feature1": { 
                // 功能区域标题显示名称
                title: 'table 数据展示',
                // require 加载对应功能区域的feature模块
                component: require('./components/feature/Feature1')
            },
            "Feature2": {
                title: 'simple对象 数据展示',
                component: require('./components/feature/Feature2')
            }
        },
        style: {
            backgroundColor: "#F5F5F5"
        } 
    }


**src/components/feature/Feature.js** 
配置单独功能页面的配置文件

* table数据，单例数据，图标型数据的展现
* 查询、创建、更新、删除表单自动化处理
* 富文本编辑器功能使用
* 独立的图片上传组件使用
* 表单元素自定义使用

    // **必要数据**
    // type 对于数据展现形式, 目前有 tableList graphList simpleObject 三种类型
    // initData 初始化展现的数据,参数 callback 用于接受获取的数据

    // **table类型 展现数据**
    // columns

    // **table类型 创建数据**
    // 当数据类型为table 并且含有创建新数据的需求时
    // CType    Array   展现的填写表单的数据字段。类型含有示例中的形式
    // Create

    {   
        // 对于数据展现形式 目前有 tableList graphList simpleObject 三种类型
        
        type: 'tableList', // tableList graphList simpleObject complexObject 

        // 初始化展现的数据，使用callback 回传列表数据
        // 需要手动添加唯一id key
        // callback 组件数据的回调函数(接受列表数据参数)

        initData: function(callback){
            // 同步或者异步获取原始数据
            // 数据需要确认唯一的 key（react 形式）
            // 若是 table 类型 则每一条数据均需要唯一的 key
            
            data.key = data.id;
            callback(data);
        },

        // table 列表展现配置
        // {
        //      title       table显示表题
        //      dataIndex   显示数据中的key
        //      type        展现形式 （string image link）
        //      render      自定义展现形式 参数 （当前数据，当前对象数据）
        //      sort        是否需要排序功能
        //      width       自定义该列宽度 否则等分
        // }
        // 
        columns: [
            {
                title: 'DOCID',     // table header 文案
                dataIndex: 'docid', // 数据对象内的属性，也做react vdom 的key
                type: 'string',     // table 内显示的类型
                sort: true,         // 是否需要排序
                width:200
            }, {
                title: '标题',
                dataIndex: 'title',
                type: 'string'
            }, {
                title: '链接',
                dataIndex: 'link',
                type: 'link',
                render: (text) => ( <span>
                                        <a href={text}>链接</a>
                                    </span>),
                width: 50
            },{
                title: '日期',
                dataIndex: 'date',
                type: 'string',
                width: 150
            },{
                title: '图片',
                dataIndex: 'img',
                type: 'image'
            },{
                title: '操作',
                type: 'operate',    // 操作的类型必须为 operate
                width: 120,
                btns: [{
                    text: '更新',
                    type: 'update'
                },{
                    text: '删除',
                    type: 'delete'
                }, {
                    text: '展示',
                    callback: function(item){
                        console.log(item)
                    }
                }] // 可选
            }
        ],
        
        // 功能模块需要时 添加 CRUD 4方法
        Create: function(data, callback){
            let dataI = Immutable.fromJS({
                type: 'entry_list'
            }).merge({id: data.key});
            
            // ... 操作删除请求
            console.log(dataI.toJS());
            
            // 模拟请求删除成功的回调
            setTimeout(function(){
                callback();
            }, 1000)
        },
        Delete: function(data, callback){
            let dataI = Immutable.fromJS({
                type: 'entry_list'
            }).merge({id: data.key});
            
            // ... 操作删除请求
            console.log(dataI.toJS());
            
            // 模拟请求删除成功的回调
            setTimeout(function(){
                callback();
            }, 1000)
               
        },
        Update:function(data, callback){
            console.log(data);
        },
        Retrieve: function(data, callback){
            Reqwest({
                url: XXXXX,
                type: 'jsonp',
                success: function (data) {
                    // 获取数组数据
                    let lists = data.array;
                
                    // 必须要向数据中 添加唯一的 key
                    lists.forEach(function(ele) {
                        ele.key = ele.id;
                    });

                    // 回传数据
                    callback(lists);
                }
            });
        },

        // 更新项目所需的字段
        UType:[
            {
                name: 'docid',
                label: '唯一标识',
                type: 'string',
                placeholder: '请输入标示名称'
            },{
                name: 'title',
                label: '标题',
                type: 'string',
                placeholder: '请输入标示名称'
            },{
                name: 'link',
                label: '链接',
                type: 'string'
            },{
                name: 'date',
                label: '日期',
                type: 'date'
            },{
                name: 'img',
                label: '图片',
                type: 'imageUpload'
            }

        ],

        // 更新项目所需的字段
        CType:[
            {
                name: 'docid',
                label: '唯一标识',
                type: 'string',
                placeholder: '请输入标示名称'
            },{
                name: 'title',
                label: '标题',
                type: 'string',
                placeholder: '请输入标示名称'
            },{
                name: 'link',
                label: '链接',
                type: 'string'
            },{
                name: 'date',
                label: '日期',
                type: 'date'
            },{
                name: 'img',
                label: '图片',
                type: 'imageUpload'
            }

        ],
        
        // simpleObject 类型下按钮
        operate:[
            {
                text: '确认修改',
                type: 'update',
                style: {
                    'marginRight': '30px'
                }
            }, {
                text: '展示数据',
                callback: function(item){
                    console.log(item)
                }
            }
        ],

    }

bibibibi~ 改版中！！！！！！！！！！！！！！！！