<template>
    <div class="grid-content">
        <div class='head-top'>
            <p>部门列表</p>
        </div>
        <div class='list'>
            <el-tree :data="list" :props="defaultProps" :default-expand-all='true' :expand-on-click-node='false' @node-click='pick'></el-tree>
        </div>
    </div>
</template>
<script>
import { mapState } from 'vuex'
export default {
    props:['type'],
    data(){
        return {
            defaultProps: {
                children: 'children',
                label: 'info'
            }
        }
    },
    methods:{
        pick(data,node,vuecomponent){
            let dom_current=vuecomponent.$el.firstChild.lastChild;
            let classname=dom_current.getAttribute('class');
            let don_on=document.getElementsByClassName('on');
            don_on.length?don_on[0].setAttribute('class','el-tree-node__label'):void(0);
            dom_current.setAttribute('class','el-tree-node__label on');
            if(this.type=='rolemanage'){
                this.$root.$emit('getrole',data.id);
            }
            else if(this.type=='usermanage'){
                this.$root.$emit('getemploy',data.id);
            }
            
        }
    },
    watch:{
        list(value){
            // 默认读取第一个选项下的列表数据
            if(this.type=='rolemanage'){
                this.$root.$emit('getrole',value[0].id);
            }
            else if(this.type=='usermanage'){
                this.$root.$emit('getemploy',value[0].id);
            }
        }
    },
    updated(){
        //默认选中第一个节点，修改样式
        document.querySelector('.el-tree-node').querySelector('.el-tree-node__content').querySelector('.el-tree-node__label').setAttribute('class','el-tree-node__label on');
    },
    computed: {
        ...mapState({
            list: state => state.deplist.deplisttree
        })
    },
}
</script>
<style scoped>
.grid-content{
    height: 88%;
    background-color:#fff;
}
.head-top{
    height:90px;
	color:#8b8b8b;
	line-height:90px;
	padding-left:5%;
    font-size: 16px;
}
.list{
    margin-left: 5%;
    margin-right: 5%;
    height:80%;
    padding-top: 20px;
    margin-top: -20px;
    border-bottom: 1px solid #ebeef5;
    border-top: 1px solid #ebeef5;
}
</style>
<style>
.el-tree{
    height:95%;
    overflow:auto;
}
.el-tree-node__expand-icon{
    color:#27a1f2;
    font-size:14px;
}
.el-tree-node__expand-icon.expanded{
    transform: rotate(0deg)
}
.el-tree-node__expand-icon:before{
    content:"\E602"
    
}
.el-tree-node__expand-icon.expanded:before{
    content:"\E63C"
}
.list{
    margin-left: 5%;
    height:80%;
}
.list .el-tree-node__label{
    font-size:14px;
    color:#8b8b8b;
}
.el-tree-node__label{
    line-height:10px;
    padding:5px;
    border-radius:5px;
}
.el-tree-node__label.on{
    background-color: #27a1f2;
    color:#fff;
}

</style>
