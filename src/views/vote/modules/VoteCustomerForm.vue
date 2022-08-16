<template>
  <a-spin :spinning="confirmLoading">
    <j-form-container :disabled="formDisabled">
      <a-form-model ref="form" :model="model" :rules="validatorRules" slot="detail">
        <a-row>
          <a-col :span="24">
            <a-form-model-item label="真实姓名" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="realName">
              <a-input v-model="model.realName" placeholder="请输入真实姓名"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="昵称" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="nickName">
              <a-input v-model="model.nickName" placeholder="请输入昵称"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="电话" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="phone">
              <a-input v-model="model.phone" placeholder="请输入电话"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="微信号" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="wxNumber">
              <a-input v-model="model.wxNumber" placeholder="请输入微信号"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="age" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="age">
              <a-input-number v-model="model.age" placeholder="请输入age" style="width: 100%" />
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="sex" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="sex">
              <a-input v-model="model.sex" placeholder="请输入sex"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="0是启用1是禁用" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="isStart">
              <a-input v-model="model.isStart" placeholder="请输入0是启用1是禁用"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="0是逻辑未删除 1是逻辑删除" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="isDeleted">
              <a-input v-model="model.isDeleted" placeholder="请输入0是逻辑未删除 1是逻辑删除"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="openId" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="openId">
              <a-input v-model="model.openId" placeholder="请输入openId"  ></a-input>
            </a-form-model-item>
          </a-col>
        </a-row>
      </a-form-model>
    </j-form-container>
  </a-spin>
</template>

<script>

  import { httpAction, getAction } from '@/api/manage'
  import { validateDuplicateValue } from '@/utils/util'

  export default {
    name: 'VoteCustomerForm',
    components: {
    },
    props: {
      //表单禁用
      disabled: {
        type: Boolean,
        default: false,
        required: false
      }
    },
    data () {
      return {
        model:{
         },
        labelCol: {
          xs: { span: 24 },
          sm: { span: 5 },
        },
        wrapperCol: {
          xs: { span: 24 },
          sm: { span: 16 },
        },
        confirmLoading: false,
        validatorRules: {
        },
        url: {
          add: "/vote/voteCustomer/add",
          edit: "/vote/voteCustomer/edit",
          queryById: "/vote/voteCustomer/queryById"
        }
      }
    },
    computed: {
      formDisabled(){
        return this.disabled
      },
    },
    created () {
       //备份model原始值
      this.modelDefault = JSON.parse(JSON.stringify(this.model));
    },
    methods: {
      add () {
        this.edit(this.modelDefault);
      },
      edit (record) {
        this.model = Object.assign({}, record);
        this.visible = true;
      },
      submitForm () {
        const that = this;
        // 触发表单验证
        this.$refs.form.validate(valid => {
          if (valid) {
            that.confirmLoading = true;
            let httpurl = '';
            let method = '';
            if(!this.model.id){
              httpurl+=this.url.add;
              method = 'post';
            }else{
              httpurl+=this.url.edit;
               method = 'put';
            }
            httpAction(httpurl,this.model,method).then((res)=>{
              if(res.success){
                that.$message.success(res.message);
                that.$emit('ok');
              }else{
                that.$message.warning(res.message);
              }
            }).finally(() => {
              that.confirmLoading = false;
            })
          }
         
        })
      },
    }
  }
</script>