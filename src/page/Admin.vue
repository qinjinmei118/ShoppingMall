<template>
  <section class="admin-layout-container">
    <div class="layout">

      <Layout>
        <Sider ref="sidebar" class="sidebar" hide-trigger collapsible width="230" :collapsed-width="78" v-model.trim="isCollapsed">
          <div class="logo" >
            <div class="xfind-line" v-if="!isCollapsed">
              <div class="line-h"></div>
            </div>
            <div v-if="!isCollapsed" class="logo-saiqu">
              <Avatar icon="ios-person" size="large"/>
              <span class="user-name" v-popover:popover-user>{{username}}</span>
              <el-popover
                placement="bottom-start"
                width="330"
                trigger="hover"
                ref="popover-user"
              >
               <admin-panel></admin-panel>
              </el-popover>
            </div>
            <Avatar icon="ios-person" size="large" v-else/>
          </div>
          <Menu
            ref="side_menu"
            :active-name="activeMenuName"
            :open-names="openMenuName"
            theme="light"
            width="230px"
            :class="menuitemClasses"
            @on-select="choosedMenu"
            v-if="!isCollapsed">
            <template v-for="(menu,menu_index) in menus">
              <Submenu :name="menu.name" v-if="menu.children" :key="menu_index">
                <template slot="title">
                  <Icon :size="20" :type="menu.icon"></Icon>
                  <span>{{menu.title}}</span>
                </template>
                <MenuItem :name="child.name" v-for="(child ,child_index) in menu.children" :key="child_index">
                  <Icon :size="20" :type="child.icon"></Icon>
                  <span>{{child.title}}</span>
                </MenuItem>
              </Submenu>
              <MenuItem :name="menu.name" v-if="!menu.children && menu.showInMenus" :key="menu_index">
                <Icon :size="20" :type="menu.icon" :key="menu_index"></Icon>
                <span>{{menu.title}}</span>
              </MenuItem>
            </template>
          </Menu>

          <div class="dropdown-wrap"  v-if="isCollapsed">
            <div class="dw-content">
              <template v-for="(menu,menu_index) in menus">
                <Dropdown transfer placement="right-start" v-if="menu.children" @on-click="dropdownClick" :key="menu_index">
                  <Button class="dd-btn" type="text">
                    <Icon :size="25" :type="menu.icon"></Icon>
                  </Button>
                  <DropdownMenu class="dd-menu" slot="list">
                    <template v-for="(child, i) in menu.children">
                      <DropdownItem :name="child.name" :key="i">
                        <div class="ddi-wapper">
                          <Icon :size="16" :type="child.icon"></Icon>
                          <span class="ddi-text">
                              {{ child.title }}
                          </span>
                        </div>
                      </DropdownItem>
                    </template>
                  </DropdownMenu>
                </Dropdown>
                <Dropdown transfer v-if="!menu.children && menu.showInMenus" placement="right-start" @on-click="dropdownClick" :key="menu_index">
                  <Button class="dd-btn" type="text">
                    <Icon :size="25" :type="menu.icon"></Icon>
                  </Button>
                  <DropdownMenu class="dd-menu" slot="list">
                    <DropdownItem :name="menu.name">
                      <div class="ddi-wapper">
                        <Icon :size="16" :type="menu.icon"></Icon>
                        <span class="ddi-text">
                          {{ menu.title }}
                      </span>
                      </div>
                    </DropdownItem>
                  </DropdownMenu>
                </Dropdown>
              </template>
            </div>
          </div>
        </Sider>
        <Layout>
          <Header class="layout-header-bar">
            <div class="header-wapper">
              <div class="header-left">
                <Icon @click.native="collapsedSider" :class="rotateIcon" type="md-menu" size="28"></Icon>
                <span class="header-title">电子商城后台管理系统</span>
              </div>

              <div class="header-right">
                <Button type="text" icon="md-exit" class="btn-blue" size="large" @click="quit">退出系统</Button>
              </div>
            </div>
          </Header>
          <Content class="main-content">

            <Layout class="main-layout-con">

              <div class="tags-nav-wapper">

                <div class="tags-nav">

                  <div class="btn-con left-btn">

                    <Button type="text" @click="handleScroll(240)">

                      <Icon :size="18" type="ios-arrow-back" />

                    </Button>

                  </div>

                  <div ref="scrollOuter" class="scroll-outer" @DOMMouseScroll="handlescroll" @mousewheel="handlescroll">

                    <div ref="scrollBody" class="scroll-body" :style="{left: tagBodyLeft + 'px'}">

                      <transition-group name="taglist-moving-animation">

                        <Tag type="dot"

                             v-for="tab in tags"

                             :closable="tab.closable"

                             :color="tab.choosed? 'primary':'#e9eaec'"

                             :name="tab.name"

                             @click.native="clickTag(tab)"

                             @on-close="closeTag"

                             :key="tab.name">

                          {{tab.title}}

                        </Tag>

                      </transition-group>

                    </div>

                  </div>

                  <div class="btn-con right-btn">

                    <Button type="text" @click="handleScroll(-240)">

                      <Icon :size="18" type="ios-arrow-forward" />

                    </Button>

                  </div>

                </div>

              </div>

              <Content class="content-wrapper">


                <!--保存组件状态到内存，避免重新渲染-->

                <keep-alive>

                  <router-view/>

                </keep-alive>

              </Content>

            </Layout>

          </Content>

        </Layout>

      </Layout>

    </div>

  </section>

</template>

<script>
import AdminPanel from "../components/AdminPanel";
    export default {
      components:{AdminPanel},
        data(){

            return{

                isCollapsed: false,
                title:'首页',
                activeMenuName:'admin',
                openMenuName:[],
                tagBodyLeft: 0,
                rightOffset: 40,
                outerPadding: 4,
                contextMenuLeft: 0,
                contextMenuTop: 0,
                visible: false,
                menus:[
                    {

                        title:'首页',

                        num:1,

                        name:'admin',

                        icon:'ios-home',

                        href:'/AdminPage',

                        closable:false,

                        showInTags:true,

                        showInMenus:true,

                        choosed:true

                    },

                    {

                        title:'用户管理',

                        name:'members-agents',

                        icon:'ios-people',

                        children:[

                            {

                                title:'管理用户信息',

                                name:'userManage',

                                href:'/showUser',

                                closable:true,

                                showInTags:false,

                                showInMenus:true,

                                choosed:false

                            },

                        ]

                    },

                    {

                        title:'商品管理',

                        name:'system-manage',

                        icon:'ios-cog',

                        children:[

                            {

                                title:'商品编辑',

                                name:'backwater-setting',

                                href:'/EditGoods',

                                closable:true,

                                showInTags:false,

                                showInMenus:true,

                                choosed:false

                            },

                            {

                                title:'添加商品',

                                name:'commissionSetting',

                                href:'/AddGoods',

                                closable:true,

                                showInTags:false,

                                showInMenus:true,

                                choosed:false

                            },




                         ]

                    },

                    {

                        title:'订单管理',

                        name:'accounting-manage',

                        icon:'ios-cash',

                        children:[

                            {

                                title:'查看订单',

                                name:'companyPayment',

                                href:'/showOrder',

                                closable:true,

                                showInTags:false,

                                showInMenus:true,

                                choosed:false

                            },


                            {

                                title:'交易记录查询',

                                name:'transactionRecord',

                                href:'/home',

                                closable:true,

                                showInTags:false,

                                showInMenus:true,

                                choosed:false

                            },

                        ]

                    },

                    {

                        title:'活动计划',

                        name:'report-manage',

                        icon:'ios-stats',

                        children:[

                            {

                                title:'统计报表',

                                name:'about22',

                                href:'/home',

                                closable:true,

                                showInTags:false,

                                showInMenus:true,

                                choosed:false

                            },

                            {

                                title:'投注记录查询',

                                name:'about23',

                                href:'/home',

                                closable:true,

                                showInTags:false,

                                showInMenus:true,

                                choosed:false

                            },

                            {

                                title:'娱乐城转账记录',

                                name:'about24',

                                href:'/home',

                                closable:true,

                                showInTags:false,

                                showInMenus:true,

                                choosed:false

                            },

                            {

                                title:'总存取款汇出',

                                name:'about25',

                                href:'/home',

                                closable:true,

                                showInTags:false,

                                showInMenus:true,

                                choosed:false

                            },

                        ]

                    },

                    {

                        title:'前端管理设置',

                        name:'frontend-setting',

                        icon:'logo-html5',

                        children:[

                            {

                                title:'Banner设置',

                                name:'about26',

                                href:'/home',

                                closable:true,

                                showInTags:false,

                                showInMenus:true,

                                choosed:false

                            },

                            {

                                title:'顶部Icon设置',

                                name:'about27',

                                href:'/home',

                                closable:true,

                                showInTags:false,

                                showInMenus:true,

                                choosed:false

                            },

                            {

                                title:'底部Icon设置',

                                name:'about28',

                                href:'/home',

                                closable:true,

                                showInTags:false,

                                showInMenus:true,

                                choosed:false

                            }

                        ]

                    },



                    {

                        title:'活动中心',

                        name:'activity-center',

                        icon:'ios-cafe',

                        children:[

                            {

                                title:'活动设置',

                                name:'about29',

                                href:'/home',

                                closable:true,

                                showInTags:false,

                                showInMenus:true,

                                choosed:false

                            },

                            {

                                title:'活动模板设置',

                                name:'about30',

                                href:'/home',

                                closable:true,

                                showInTags:false,

                                showInMenus:true,

                                choosed:false

                            },

                        ]

                    },

                    {

                        title:'公告管理',

                        name:'notice-manage',

                        icon:'md-volume-mute',

                        children:[

                            {

                                title:'系统公告',

                                name:'system-notice',

                                href:'/home',

                                closable:true,

                                showInTags:false,

                                showInMenus:true,

                                choosed:false

                            }

                        ]

                    },

                ]

                // ------------------------------  菜单操作结束  --------------------------------

            }

        },

        computed: {

            // 筛选menus中选中的menu
            username(){
                return sessionStorage.getItem('userName')
            },

            tags(){

                let tags = [];

                // 将menus中showInTags=true的标签放到tags数组中

                this.menus.forEach(menu=>{

                    if(menu.showInTags){

                        tags.push(menu);

                    }else if(menu.children){

                        menu.children.forEach(child=>{

                            if(child.showInTags){

                                tags.push(child)

                            }

                        })

                    }

                });

                //标签数组排序，从小到到

                tags.sort((a,b)=>{

                    return (a.num - b.num)

                })

                return tags;

            },

            rotateIcon () {

                return [

                    'menu-icon',

                    this.isCollapsed ? 'rotate-icon' : ''

                ];

            },

            menuitemClasses () {

                return [

                    'menu-item',

                    this.isCollapsed ? 'collapsed-menu' : ''

                ]

            }

        },

        // ------------------------------  菜单操作开始  --------------------------------

        //刷新页面之后保存并选中最后一次菜单和标签

        beforeRouteEnter (to, from, next) {

            next(vm => {

                // 通过 `vm` 访问组件实例

                let activeMenuName = localStorage.activeMenuName;

                vm.activeMenuName = activeMenuName;



                let tags_last_num = vm.tags[vm.tags.length - 1].num;



                if(activeMenuName && activeMenuName.length != 0){

                    vm.menus.forEach(_menu=>{

                        if(activeMenuName == _menu.name){

                            _menu.choosed = true;

                            _menu.showInTags = true;

                            _menu.num = tags_last_num + 1;

                        }

                        else if(_menu.children){

                            _menu.children.forEach(child=>{

                                if(activeMenuName == child.name){

                                    child.choosed = true;

                                    child.showInTags = true;

                                    child.num = tags_last_num + 1;

                                    vm.openMenuName = [_menu.name];

                                }

                            })

                        }

                        else{

                            // 排除首页

                            if(_menu.name != 'admin'){

                                _menu.choosed = false;

                                _menu.showInTags = false;

                            }else{

                                _menu.choosed = false;

                            }

                        }

                    })

                }

                vm.$nextTick(()=>{

                    // console.log(vm.$refs.side_menu, 22)

                    // vm.$refs.side_menu.updateOpened();

                    // vm.$refs.side_menu.updateActiveName();

                });

            })

        },

        // ------------------------------  菜单操作结束  --------------------------------

        methods: {

            /*tags 滚动事件 */

            handlescroll (e) {

                var type = e.type

                let delta = 0

                if (type === 'DOMMouseScroll' || type === 'mousewheel') {

                    delta = (e.wheelDelta) ? e.wheelDelta : -(e.detail || 0) * 40

                }

                this.handleScroll(delta)

            },

            /*tags 滑动事件 */

            handleScroll (offset) {

                const outerWidth = this.$refs.scrollOuter.offsetWidth

                const bodyWidth = this.$refs.scrollBody.offsetWidth

                if (offset > 0) {

                    this.tagBodyLeft = Math.min(0, this.tagBodyLeft + offset)

                } else {

                    if (outerWidth < bodyWidth) {

                        if (this.tagBodyLeft < -(bodyWidth - outerWidth)) {

                            this.tagBodyLeft = this.tagBodyLeft

                        } else {

                            this.tagBodyLeft = Math.max(this.tagBodyLeft + offset, outerWidth - bodyWidth)

                        }

                    } else {

                        this.tagBodyLeft = 0

                    }

                }

            },

            quit(){
                sessionStorage.removeItem('userName');
                this.$router.push('/Login')

            },

            clickNotice(){

                this.choosedMenu('notice');

            },

            collapsedSider() {

                this.$refs.sidebar.toggleCollapse();

            },

            // ------------------------------  菜单操作开始  --------------------------------

            closeTag(event, name){

                // 判断该标签是否是选中状态

                // 如果是那么就要设置标签数组中最后一个标签成选中状态

                // 如果否那么就直接删除就好

                let is_choosed = false;

                this.menus.forEach((menu)=>{

                    if(menu.name == name){

                        is_choosed = menu.choosed;

                        menu.showInTags = false;

                    }else if(menu.children){

                        menu.children.forEach(child=>{

                            if(child.name == name){

                                is_choosed = child.choosed;

                                child.showInTags = false;

                            }

                        })

                    }

                })

                // 关闭标签并选中tags中最后一个标签高亮

                if(is_choosed){

                    let last_tag = this.tags[this.tags.length-1];

                    last_tag.choosed = true;

                    this.$router.push(last_tag.href);

                    this.activeMenuName = last_tag.name;

                    localStorage.activeMenuName = this.activeMenuName;

                }

            },

            clickTag(tag){

                this.tags.forEach(_tag=>{

                    if(_tag.name == tag.name){

                        _tag.choosed=true;

                    }else{

                        _tag.choosed= false;

                    }

                })

                // 设置菜单选中name

                this.activeMenuName = tag.name;

                localStorage.activeMenuName = this.activeMenuName;

                // 刷新菜单

                this.$nextTick(()=>{

                    if(this.$refs.side_menu){

                        this.$refs.side_menu.updateActiveName()

                    }

                });

                //点击tab跳转

                this.$router.push(`${tag.href}`);

            },

            choosedMenu(name){

                // 获取标签数组最后一个元素的num

                let tags_last_num = this.tags[this.tags.length - 1].num;

                // 设置选中菜单name

                this.activeMenuName = name;

                localStorage.activeMenuName = this.activeMenuName;



                //根据name查找对应的菜单对象

                let menu = null;

                this.menus.forEach(_menu=>{

                    if(_menu.name == name){

                        // 只有不在tags数组中的元素才能设置num

                        if(!_menu.showInTags){

                            _menu.num = tags_last_num + 1;

                        }

                        menu = _menu;

                        _menu.showInTags = true;

                        _menu.choosed = true;



                    }

                    else if(_menu.children){

                        _menu.children.forEach(child=>{

                            if(child.name == name){

                                // 只有不在tags数组中的元素才能设置num

                                if(!_menu.showInTags){

                                    child.num = tags_last_num + 1;

                                }

                                menu = child;

                                child.showInTags = true;

                                child.choosed = true;



                            }else{

                                child.choosed = false;

                            }

                        })

                    }

                    else {

                        _menu.choosed = false;

                    }

                })

                this.$router.push(`${menu.href}`);

            },

            dropdownClick(name){

                this.choosedMenu(name);

            }

            // ------------------------------  菜单操作结束  --------------------------------

        }

    }

</script>
<!--<style scoped="scss">
  @import "../../src/styles/index.scss";
</style>-->

