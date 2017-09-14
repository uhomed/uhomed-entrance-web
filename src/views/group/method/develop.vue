<template>
    <div>
        <Breadcrumb>
            <Breadcrumb-item>首页</Breadcrumb-item>
            <Breadcrumb-item>分组管理</Breadcrumb-item>
            <Breadcrumb-item>方法列表</Breadcrumb-item>
            <Breadcrumb-item>调试</Breadcrumb-item>
        </Breadcrumb>
        <br/>
        <template>
            <Form :label-width="100">

                <FormItem label="method" >
                    <Input v-model="info.apiMethodCode" disabled class="col-2" ></Input>
                    <Tooltip content="调用接口名" placement="bottom">
                        <Icon type="help"></Icon>
                    </Tooltip>
                </FormItem>

                <FormItem label="version" >
                    <Input v-model="info.apiMethodVersion" disabled class="col-1" ></Input>
                    <Tooltip content="接口版本号" placement="bottom">
                        <Icon type="help"></Icon>
                    </Tooltip>
                </FormItem>

                <FormItem label="format" >
                    <Input disabled value="JSON" class="col-1" ></Input>
                    <Tooltip content="返回值类型" placement="bottom">
                        <Icon type="help"></Icon>
                    </Tooltip>
                </FormItem>

                <FormItem label="mode" >
                    <Input disabled :value="info.mode" class="col-1" ></Input>
                    <Tooltip content="请求方式" placement="bottom">
                        <Icon type="help"></Icon>
                    </Tooltip>
                </FormItem>

                <FormItem label="timestamp" >
                    <Input disabled :value="timestamp" class="col-2" ></Input>
                    <Tooltip content="时间戳" placement="bottom">
                        <Icon type="help"></Icon>
                    </Tooltip>
                </FormItem>

                <FormItem label="sso" >
                    <Input :value="sso" class="col-3" ></Input>
                    <Tooltip content="sso" placement="bottom">
                        <Icon type="help"></Icon>
                    </Tooltip>
                </FormItem>

                <FormItem v-for="param in datas" :key="param.id" :label="param.paramName" >
                    <Input :placeholder="param.paramType" v-model="param.value" class="col-2"></Input>
                    <Tooltip :content="param.paramName + param.paramType" placement="bottom">
                        <Icon type="help"></Icon>
                    </Tooltip>
                </FormItem>

                <FormItem>
                    <Button type="primary" @click="handleSubmit()">调试</Button>
                </FormItem>

                <pre>调用时生成的参数：</pre>

                <pre>
                    {{result}}
                </pre>
            </Form>

        </template>

    </div>
</template>

<script>
import MixSearch from '@/mixins/mix-search';
import { METHOD_PARAM_LIST,METHOD_GATEWAY } from '@/service/gateway';
export default {
    data() {
        return {
            datas:[],
            count:0,
            id: null,
            info : {},
            result : null,
            timestamp: new Date().getTime() * 1000,
            sso:"uhomed-entrance-test",
            formDynamic: {
                items: [
                    {
                        value: ''
                    }
                ]
            }
        }
    },
    mounted() {
        let id = this.$route.query.id;
        if (id) {
            this.id = id;
            this.fatchData();
        }
    },
    methods: {
        
        async handleSubmit(name) {
            let bizParams = {};
            this.datas.forEach(function(element) {
                bizParams[element.paramCode] = element.value;
            }, this);
            let params = {
                method: this.info.apiMethodCode,
                version: this.info.apiMethodVersion,
                format: this.info.format,
                timestamp : this.timestamp,
                bizParams:JSON.stringify(bizParams)
            }
            let response = await METHOD_GATEWAY(params);
            if (!response) { return false; }
            this.result = response;
        },

        handleReset(name) {
            this.$refs[name].resetFields();
        },
        handleAdd() {
            this.formDynamic.items.push({
                value: ''
            });
        },
        handleRemove(index) {
            this.formDynamic.items.splice(index, 1);
        },
        async fatchData() {
            let response = await METHOD_PARAM_LIST({
                methodId: this.id
            });
            if (!response) { return false; };
            let data = response.data;
            this.datas = data;
            this.info = response.info;
        }
    }
}


</script>