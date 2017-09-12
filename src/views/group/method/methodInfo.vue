<template>
    <div>
        <Breadcrumb>
            <Breadcrumb-item>首页</Breadcrumb-item>
            <Breadcrumb-item>接口</Breadcrumb-item>
        </Breadcrumb>
        <br/>
        <Form ref="formValidate" :model="formValidate" :rules="ruleValidate" :label-width="100">
            <FormItem label="方法code" prop="apiMethodCode">
                <Input v-model="formValidate.apiMethodCode" placeholder="xkhstar" class="col-2"></Input>
            </FormItem>
            <FormItem label="方法名" prop="apiMethodName">
                <Input v-model="formValidate.apiMethodName" placeholder="登录接口" class="col-3"></Input>
            </FormItem>
            <FormItem label="版本号" prop="apiMethodVersion">
                <Input v-model="formValidate.apiMethodVersion" placeholder="接口版本号" class="col-2"></Input>
            </FormItem>
            <FormItem label="接口类型" prop="type">
                <Select v-model="formValidate.type" placeholder="请选择" class="col-1">
                    <Option value="DUBBO">DUBBO</Option>
                    <Option value="HTTP">HTTP</Option>
                    <Option value="HTTPS">HTTPS</Option>
                </Select>
            </FormItem>
            <FormItem label="接口状态" prop="status">
                <Select v-model="formValidate.status" placeholder="请选择" class="col-1">
                    <Option value="Y">online</Option>
                    <Option value="N">offline</Option>
                </Select>
            </FormItem>
            <FormItem label="sso校验" prop="verifiSso">
                <Select v-model="formValidate.verifiSso" placeholder="请选择" class="col-1">
                    <Option value="Y">是</Option>
                    <Option value="N">否</Option>
                </Select>
            </FormItem>
            <FormItem label="调用方式" prop="mode">
                <Select v-model="formValidate.mode" placeholder="请选择" class="col-1">
                    <Option value="GET">GET</Option>
                    <Option value="POST">POST</Option>
                </Select>
            </FormItem>
            <FormItem label="方法描述" prop="methodDesc">
                <Input v-model="formValidate.methodDesc" type="textarea" :autosize="{minRows: 2,maxRows: 5}" placeholder="请输入..." class="col-4"></Input>
            </FormItem>
            <div v-if="formValidate.type == 'DUBBO'">
                <FormItem label="classPath" prop="classPath">
                    <Input v-model="formValidate.classPath" placeholder="com.xkhstar.xxx.xxx" class="col-5"></Input>
                </FormItem>
                <FormItem label="methodName" prop="methodName">
                    <Input v-model="formValidate.methodName" placeholder="test" class="col-2"></Input>
                </FormItem>
            </div>
            <div v-else-if="formValidate.type == 'HTTP'">
                http
            </div>
            <div v-else-if="formValidate.type == 'HTTPS'">
                https
            </div>
            
            <FormItem>
                <Button type="primary" @click="handleSubmit('formValidate')">保存</Button>
                <Button type="ghost" @click="handleReset('formValidate')" style="margin-left: 8px">重置</Button>
            </FormItem>
        </Form>
        
    </div>
</template>

<script>
    import MixSearch from '@/mixins/mix-search';
    import { CREATE_METHOD_DUBBO,METHOD_INFO,UPDATE_METHOD_DUBBO } from '@/service/gateway';
    export default {
        data () {
            return {
                id: null,
                formValidate: {
                    apiMethodCode: null,
                    apiMethodName: null,
                    apiMethodVersion: null,
                    status:null,
                    verifiSso:null,
                    mode:null,
                    methodDesc:null,
                    classPath:null,
                    methodName:null,
                    type : null,
                    groupCode: this.$route.query.groupCode
                },
                ruleValidate: {
                    apiMethodCode: [
                        { required: true, message: '方法code不能为空', trigger: 'blur' }
                    ],
                    apiMethodName: [
                        { required: true, message: '方法名不能为空', trigger: 'blur' }
                    ],
                    apiMethodVersion: [
                        { required: true, message: '版本号不能为空', trigger: 'blur' }
                    ],
                    status: [
                        { required: true, message: '接口状态不能为空', trigger: 'blur' }
                    ],
                    verifiSso: [
                        { required: true, message: 'sso校验不能为空', trigger: 'blur' }
                    ],
                    mode: [
                        { required: true, message: '请求方式不能为空', trigger: 'blur' }
                    ],
                    methodDesc: [
                        { required: true, message: '方法描述不能为空', trigger: 'blur' }
                    ],
                    type: [
                        { required: true, message: '接口类型不能为空', trigger: 'blur' }
                    ],
                    classPath: [
                        { required: true, message: 'classPath不能为空', trigger: 'blur' }
                    ],
                    methodName: [
                        { required: true, message: 'methodName不能为空', trigger: 'blur' }
                    ]
                },
                formDynamic: {
                    items: [
                        {
                            value: ''
                        }
                    ]
                }
            }
        },
        mounted(){
            let id = this.$route.query.id;
            if( id ){
                this.id = id;
                this.fatchData();
            }
        },
        methods: {
             handleSubmit (name) {
                this.$refs[name].validate(async (valid) => {
                    if (valid) {
                        let params = {
                            apiMethodCode: this.formValidate.apiMethodCode,
                            apiMethodName: this.formValidate.apiMethodName,
                            apiMethodVersion: this.formValidate.apiMethodVersion,
                            status: this.formValidate.status,
                            verifiSso: this.formValidate.verifiSso,
                            mode: this.formValidate.mode,
                            methodDesc: this.formValidate.methodDesc,
                            classPath: this.formValidate.classPath,
                            methodName: this.formValidate.methodName,
                            groupCode : this.formValidate.groupCode
                        };
                        let response = null;
                        if(this.$route.path == '/group/method/updateMethod'){
                            params.id = this.$route.query.id;
                            response = await UPDATE_METHOD_DUBBO(params);
                        }else {
                            response = await CREATE_METHOD_DUBBO(params);
                        }
                        
                        if( !response ){return false;}

                        if(response.success){
                            this.$Message.success(response.message);
                            this.$router.go(-1);
                        }else {
                            this.$Message.error(response.message);
                        }
                        
                    } else {
                        this.$Message.error('请根据错误提示修改！');
                    }
                })
            },
            
            handleReset (name) {
                this.$refs[name].resetFields();
            },
            handleAdd () {
                this.formDynamic.items.push({
                    value: ''
                });
            },
            handleRemove (index) {
                this.formDynamic.items.splice(index, 1);
            },
            async fatchData(){
                let response = await METHOD_INFO({
                    id: this.id
                });
                if( !response ){ return false; };
                let data = response.data;
                Object.assign(this.formValidate, data);

                if(data.type == 'DUBBO'){
                    this.formValidate.classPath = response.dubbo.classPath;
                    this.formValidate.methodName = response.dubbo.methodName;
                }
            }
        }
    }


</script>