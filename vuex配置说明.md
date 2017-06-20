#### 导语：
> 近期因为新的项目需求，不得不增加vuex的使用，vuex由于之前没有配置过，刚开始配置的时候各种报错，经过两天的研究终于搞清楚了一点来龙去脉，如有欠缺或不足，欢迎补充。至于vuex是什么等等的官方说明就不讲了，直接从项目入手
## vuex的配置以及用法
我的项目是用vue-cil官方的手脚架搭的壳，在这个基础上配了一下路由，引了一些组件，已经设定了公共的组件（header,footer）
### npm install vuex --save
第一步就是安装好vuex并添加到依赖表（packge.json）
###  项目增加vuex
* 如果只是小项目并且子组件通信并不多，那么可以直接在main.js中定义好vuex


        //main.js
        import vuex from 'vuex'
        Vue.use(vuex);
        var store = new vuex.Store({//store对象
            state:{
                show:false
            }
        })
* 不过大多数情况下，我们还是建一个文件夹更好，因为目录结构更加清晰。

src目录下创建一个放置vuex文件的目录，取名相关为vuex或者stores都可以，目录首先包含index.js,在main.js中引入

    //stores/index.js
    import stroe from './stores';
    vue实例中加入vuex
    new Vue({
        el: '#app',
        router,
        store,
        template: '<App/>',
        components: { App }
    })
到这里就没main.js什么事情了，可以放一边了。
###  state
接下来stroes下index需要引入vuex并且进行配置代码如下

    //main.js
    import	Vue from 'vue'
    import Vuex from 'vuex'
    Vue.use(Vuex);
    export default new Vuex.Store({
        state:{
            //这里可以写上子组件可以共享的参数和值
            name:'userName from state'
        }
    })
完事了，到这一步vuex也勉强能用了，.vue的模块中调用共享这里面的参数都可以用this.$store.state+变量名，就可以进行通信了。
###  modules
对于不同的组件来说如果全部公用state里的数据，可能会比较乱后期不好维护，最好是每一大块为一个单独模块抽离出来，每个抽离出来的文件的书写格式必须为：

    //module1.js
    export default {
        state:{
            name:'userName from state'
        }
    }

在store下的index.js中进行相应的修改

    //stores/index.js
    import	Vue from 'vue'
    import Vuex from 'vuex'
    import modules1 from './modules'
    Vue.use(Vuex);
    export default new Vuex.Store({
        /*state:{
            //这里可以写上子组件可以共享的参数和值
            name:'userName from state'
        }*/
        modules:{
            modules1:modules1
        }
    })  
这个时候.vue模块要调用vuex具体模块中的参数就需要更改为：this.$store.state+模块名+变量名；
###  mutations
在组件中调用vuex的state中的状态的时候每次只能是单个去调用，mutations解决了批量的操作这些状态。相当于vue实例中的methods，里面放置方法

    export default {
        state:{//state
            name:'userName from state',
            show:false
        },
        mutations:{
            changeName(state){//这里的state对应着上面这个state
                state.name = 'change state name';
                //你还可以在这里执行其他的操作改变state
                state.show = true;
            }
        }
    }
在组件中调用到mutations里的方法需要用$store.commit(argument) ,参数为字符串且为mutations中的方法名称。
这里需要注意的是:
mutations 中的方法是不分组件的 , 假如你在 modules1.js 文件中的mutations中定义了
changeName 方法 , 在其他文件中的一个 changeName 方法 , 那么
$store.commit('changeName') 会执行所有的 changeName 方法。
mutations里的操作必须是同步的。你一定好奇 , 如果在 mutations 里执行异步操作会发生什么事情 , 实际上并不会发生什么奇怪的事情 , 只是官方推荐 , 不要在 mutationss 里执行异步操作而已。
###  actions
多个 state 的操作 , 使用 mutations 会来触发会比较好维护 , 那么需要执行多个 mutations 就需要用 actions 了

    //stores/index.js
    export default {
        state:{//state
            name:'userName from state',
            show:false
        },
        modules:{
            modules1:modules1
        },
        mutations:{
            changeName(state){//这里的state对应着上面这个state
                state.name = 'change state name';
                //你还可以在这里执行其他的操作改变state
                state.show = true;
            }
        },
        actions:{
            getMutationsFn(context){
                //这里的context和我们使用的$store拥有相同的对象和方法
                //组件中使用this.$store.dispatch('getMutationsFn')来触发actions里面的方法
                //context.commit(argument)中的参数为字符串且为mutations中的方法名
                context.commit('changeUserName');
                //你还可以在这里触发其他的mutations方法
                //context.commit('showNames');
            },
        }
    }
使用 $store.dispatch('getMutationsFn') 来触发 action 中的 getMutationsFn 方法。
###  getter
getters 和 vue 中的 computed 类似 , 都是用来计算 state 然后生成新的数据 ( 状态 ) 的。新建getter.js

    //stores/index.js
    import getter from './getter.js'
    export default {
        state:{//state
            name:'userName from state',
            show:false
        },
        modules:{
            modules1:modules1
        },
        mutations:{
            changeName(state){//这里的state对应着上面这个state
                state.name = 'change state name';
                //你还可以在这里执行其他的操作改变state
                state.show = true;
            }
        },
        actions:{
            getMutationsFn(context){
                //这里的context和我们使用的$store拥有相同的对象和方法
                //组件中使用this.$store.dispatch('getMutationsFn')来触发actions里面的方法
                //context.commit(argument)中的参数为字符串且为mutations中的方法名
                context.commit('changeUserName');
                //你还可以在这里触发其他的mutations方法
                //context.commit('showNames');
            },
        },
        getter
    }

getter.js

        //getters能够取得state里面的属性值，即使在getters里更改，也无法更改state里面的对应属性值
        //类似于组件中的computed
        export const test = state =>{
            return !state.show;
        }
###  mapState、mapGetters、mapActions
很多时候 , $store.state.dialog.show 、$store.dispatch('switch_dialog') 这种写法又长又臭 , 很不方便 , 我们没使用 vuex 的时候 , 获取一个状态只需要 this.show , 执行一个方法只需要 this.switch_dialog 就行了 , 使用 vuex 使写法变复杂了 ?

使用 mapState、mapGetters、mapActions 就不会这么复杂了。
以 mapState 为例 :
1. 在组件中引入需要的模块，比如mapState，那么就import {mapState} from 'vuex';
2. 语法：

         computed:{
        //这里的三点叫做 : 扩展运算符
        ...mapState([
        modules1Name:state=>state.modules1.user_name
        ]),
        }
组件访问只需要this.modules1Name 就可以代替 this.$store.state.modules1.user_name了

3. mapGetters、mapActions 和 mapState 类似 , mapGetters 一般也写在 computed 中 , mapActions 一般写在 methods 中。


