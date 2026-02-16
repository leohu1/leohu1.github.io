---
title: Vue2 Userscript 注入
tags: 
 - Userscript
 - Vue
date: 
---

## Vue2注入
### 注入获得Vue
{% codeblock lang:js %}
let formaldefine=unsafeWindow.Object.defineProperty
let vuepro=null
unsafeWindow.Object.defineProperty=function (...args){
    if(args[1]==='$data'){
        //得到Vue原型，开始干_init
        //恢复defined
        vuepro=args[0]
        unsafeWindow.Object.defineProperty=formaldefine
        let init=vuepro._init
        vuepro._init=function(options){
            ...
            init.call(vue,options);
            ...
        }
    }
    return formaldefine.call(unsafeWindow.Object,...args)
}
{% endcodeblock %}
### 注入位点（第25行）
{% codeblock vue/src/core/instance/index.ts lang:js mark:25 %}
export function stateMixin(Vue: typeof Component) {
    // flow somehow has problems with directly declared definition object
    // when using Object.defineProperty, so we have to procedurally build up
    // the object here.
    const dataDef: any = {}
    dataDef.get = function () {
        return this._data
    }
    const propsDef: any = {}
    propsDef.get = function () {
        return this._props
    }
    if (__DEV__) {
        dataDef.set = function () {
            warn(
                'Avoid replacing instance root $data. ' +
                'Use nested data properties instead.',
                this
            )
        }
        propsDef.set = function () {
            warn(`$props is readonly.`, this)
        }
    }
    Object.defineProperty(Vue.prototype, '$data', dataDef)
    Object.defineProperty(Vue.prototype, '$props', propsDef)
    
    Vue.prototype.$set = set
    Vue.prototype.$delete = del
    
    Vue.prototype.$watch = function (
        expOrFn: string | (() => any),
        cb: any,
        options?: Record<string, any>
    ): Function {
        const vm: Component = this
        if (isPlainObject(cb)) {
            return createWatcher(vm, expOrFn, cb, options)
        }
        options = options || {}
        options.user = true
        const watcher = new Watcher(vm, expOrFn, cb, options)
        if (options.immediate) {
            const info = `callback for immediate watcher "${watcher.expression}"`
            pushTarget()
            invokeWithErrorHandling(cb, vm, [watcher.value], vm, info)
            popTarget()
        }
        return function unwatchFn() {
            watcher.teardown()
        }
    }
}
{% endcodeblock %}

## 注入router，在上文空中填入
{% codeblock lang:js %}
if (options && options.router ){
    // 注入路由
    options.router.afterHooks.push(function(){
        var r = options.router;
        return () => {
            console.log("路由发生改变", r.history.current.name);
        };
    }());
}
{% endcodeblock %}

## 注入事件，在上文空中填入
{% codeblock lang:js %}
if (options && options.router ){
    // 注入事件
    if (vue.$options["beforeMount"]===undefined){
        vue.$options["beforeMount"] = []
    }
    vue.$options["beforeMount"].push(function(){
    })
}
{% endcodeblock %}

### 生命周期
1. 创建数据代理、数据检测 
    beforeCreate()
    created（）
2. 挂载
   beforeMount()
   mounted()
3. 更新
   beforeUpdate（）
   Updated（）
4. 销毁
   beforeDestroy()
   destroyed()
5. activated 和 deactivated
   activated（）
   deactivated（）
6. errorCaptured