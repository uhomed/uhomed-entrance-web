<template>
    <div>
        <Breadcrumb>
            <Breadcrumb-item>首页</Breadcrumb-item>
            <Breadcrumb-item>分组列表</Breadcrumb-item>
        </Breadcrumb>

        <!-- 搜索 -->
        <Form class="space" inline :show-message="false"  label-position="top">
            <Form-item label="分组code">
                <Input placeholder="模糊搜索" v-model="params.likeCode"></Input>
            </Form-item>
            <Form-item label="名称">
                <Input placeholder="模糊搜索" v-model="params.linkName"></Input>
            </Form-item>
            <!-- 
            <Form-item label="选择日期" style="width:250px;">
                <Row>
                    <Col span="11">
                        <Form-item prop="date">
                            <Date-picker type="date" placeholder="开始日期"></Date-picker>
                        </Form-item>
                    </Col>
                    <Col span="2" style="text-align: center">-</Col>
                    <Col span="11">
                        <Form-item prop="time">
                            <Date-picker type="date" placeholder="结束日期"></Date-picker>
                        </Form-item>
                    </Col>
                </Row>
            </Form-item>
            -->

            <Form-item label="操作">
                <Button type="primary" @click="handleSearch">搜索</Button>
                <!-- <Button type="primary">新增</Button> -->
                
                <router-link :to="{path:'/group/addGroup'}"><Button type="primary" :to="{path:'/method/addParam'}">新增</Button></router-link>
            </Form-item>
        </Form>

        <!-- 表格 -->
        <Table border :columns="columns" :data="datas"></Table>
        <!-- 分页 -->
        <Page class="space" 
            @on-change="handlePage" 
            :total="count"
            show-elevator 
            show-total
        ></Page>

    </div>
</template>

<script>
import MixSearch from '@/mixins/mix-search';
import { GET_GROUP_LIST,DELETE_GROUP } from '@/service/gateway';
export default{
    data(){
        return{
            columns:[
                {
                    type: 'index',
                    width: 60
                },
                {
                    title: '分组code',
                    render(h, params){
                        let Div = {
                            template: `
                                <Tooltip>
                                    <div slot="content" style="white-space: normal;">
                                        ${params.row.code}
                                    </div>
                                    <router-link :to="{path:'/group/method',query:{code:'${params.row.code}'}}">${params.row.code}</router-link>
                                </Tooltip>
                            `
                        }
                        return h(Div);
                    }
                },
                {
                    title:'名称',
                    key: 'name'
                },
                {
                    title: '方法数量',
                    width: 100,
                    key: 'methodCount'

                },
                // {
                //     title: '状态',
                //     width: 120,
                //     render(h, params){
                //         let html = null;
                //         const row = params.row;
                //         const color = params.row.open === 'Y' ? 'green' : 'red';
                //         const text = params.row.open === 'Y' ? 'online' : 'offline' ;

                //         return h('Tag', {
                //             props: {
                //                 type: 'dot',
                //                 color: color
                //             }
                //         }, text);
                //         return h(html);
                //     }
                // },
                // {
                //     title: '调用类型',
                //     width: 100,
                //     key: 'mode'
                // },
                {
                    title: '操作',
                    width: 280,
                    render:(h, params)=>{
                        let that = this;
                        let html = `<div><Button type="error" @click="delGroup('${params.row.code}')">删除</Button></div>`;

                        let Div = {
                            template: html,
                            methods:{
                                delGroup(code){
                                    that.delGroup(code);
                                }
                            }
                        }
                        
                        return h(Div);
                    }
                }
            ],
            datas: [],
            count: 0,

            // 查询条件
            params:{
                pageSize: 10,
                currPage: 1
            }
        }
    },
    beforeMount(){
        this.facth();
    },
    methods:{
        async facth(){
            let params = this.params;
            let response = await GET_GROUP_LIST(params);
            if( !response ){return false;}
            this.datas = response.data;
            this.count = response.count;
        },
        

        delGroup(code){
            this.$Modal.confirm({
                title: '确认',
                content: '<p>确定删除该方法吗？</p>',
                onOk: async () => {
                    let params = {
                        code: code
                    };
                    let response = await DELETE_GROUP(params);
                    if( !response ){return false;}
                    if(response.success){
                        this.$Message.success(response.message);
                    }else {
                        this.$Message.error(response.message);
                    }
                    this.facth();
                }
            });
        },
        handleSearch(){
            this.query = Object.assign({}, this.params, {currPage: 1});
        },
        handlePage(number){
            this.query = {currPage: number-0};
        }
    },
    watch:{
        $route(){
            this.facth();
        }
    },
    mixins:[MixSearch]
}
</script>