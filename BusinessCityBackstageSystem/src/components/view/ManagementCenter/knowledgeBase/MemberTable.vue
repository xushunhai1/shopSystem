<template>
    <el-table
    :data="datalist"
    @selection-change='showextra'
    @cell-click='showMemberInfo'
    :default-sort = "{prop: 'date', order: 'descending'}"
    v-loading="this.listLoading"
    :stripe='true'
    style="width: 100%"
    height='500'>
    <el-table-column
    fixed 
    type="index"
    label="N"
    :index="indexMethod">
    </el-table-column>
        <el-table-column
        fixed
        style='display: none;'
        type="selection"
        width="55" >
        </el-table-column>
        <el-table-column class='borderRight' fixed prop="problemTitle" label="问题"  height='100'>
        </el-table-column>

        <el-table-column
        prop="knowledgeSortName"
        label="分类"
        >
        </el-table-column>
        <el-table-column
        prop="knowledgePoint"
        label="知识点">
        </el-table-column>
        <el-table-column
        prop="mobile"
        label="操作">
         <template slot-scope="scope">
            <el-button type="text" size="small"  @click='handleWindow(scope.row)' >编辑</el-button>
            <el-button type="text" size="small"  @click='handleClick(scope.row)' >删除</el-button>
        </template>
        </el-table-column>
    </el-table>
</template>
<script>
/ eslint-disable /
//@row-click="showMemberInfo()"
export default {
    prop:['listLoading'],
    data(){
        return {
            datalist:[],
            showLeft:0,
            pageIndex:1,
            data:{
                problemTitle:null,
                knowledgeSortId:null,
                knowledgePointId:null,
            }
        }
    },
    created:function(){
        this.$root.$on('pageIndex',(data) => {
            this.pageIndex = data.value
            this.getDate(this.pageIndex)
        })
        this.getDate(1)
        this.$root.$on('getDatezdy',(data)=>{
             this.getDate( data)
        })
        this.$root.$on('dataListBox',(data)=>{
            this.datalist = data
        })
         this.$root.$on('search',(datas)=>{
            //  let data={
            //      problemTitle:datas.knowledge.problemTitle===''?null:datas.knowledge.problemTitle,
            //      knowledgeSortId:datas.knowledge.knowledgeSortId===''?null:datas.knowledge.knowledgeSortId,
            //      knowledgePointId:datas.knowledge.knowledgePointId===''?null:datas.knowledge.knowledgePointId
            //  };
             this.data.problemTitle=datas.knowledge.problemTitle===''?null:datas.knowledge.problemTitle;
             this.data.knowledgeSortId=datas.knowledge.knowledgeSortId===''?null:datas.knowledge.knowledgeSortId;
             this.data.knowledgePointId=datas.knowledge.knowledgePointId===''?null:datas.knowledge.knowledgePointId
             this.getDate(1);
         })
        
    },
    methods:{
      getDate(pageIndex) {
            this.listLoading =  true;
            let url = '/api/public/knowledge/library/find?pageNo='+pageIndex+'&pageSize=10';
            this.$http({
                url: url,
                method: 'POST',
                // 请求体重发送的数据
                headers: { 'Content-Type': 'application/json' },
                data:this.data,
            })
            .then(response => {
                this.listLoading =  false;
                this.datalist=(response.data.info.list);
                console.log(this.datalist)
                this.$root.$emit('pages',response.data.info.pages)
                this.$root.$emit('total',response.data.info.total)
          })
          .catch(error=>{
              console.log(error);
              //         alert('网络错误，不能访问');
          })
        },
        handleWindow(row){
           this.showWindow([row])
       },
       showWindow(id) {
            this.$root.$emit("showWindow",id);
        },
        handleClick(row) {
            this.delBox(row)
       },
       delBox(id){
            this.$root.$emit("delBox",[id])
        },
        showMemberInfo(row,column,cell,event){//  点击显示侧滑
            //console.log(row,column,cell,event)
            //  let classNum = cell.className.split('n_')[1] //  获取单元格的类名
            let labelValue = column.label
            if(labelValue == 'ID'){
                this.showLeft = 16
                this.$root.$emit('infoCoverShow',this.showLeft)
                this.$root.$emit('searchPersonnelInfo',row.id)
            }
        },      
        showextra(val){
             let show=false;
             let editcan=true;
             this.multipleSelection = val
            if(this.multipleSelection.length>0){
                show=true;
            }
            if(this.multipleSelection.length>1){
                editcan=false;
            }
             this.$root.$emit('showlttip',{show,editcan,num:this.multipleSelection.length,datas:this.multipleSelection});
        },
        indexMethod(index) {
            return index + 1
        },
    },
    beforeDestroy(){
        this.$root.$off('search');
        this.$root.$off('pageIndex');
        this.$root.$off('getDatezdy');
        this.$root.$off('dataListBox');

    }

}
</script>
