<template>
  <a-spin :spinning="confirmLoading">
    <j-form-container :disabled="formDisabled">
      <a-form-model ref="form" :model="model" :rules="validatorRules" slot="detail">
        <a-row>
          <a-col :span="24">
            <a-form-model-item label="活动主题" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="activityThemeId">
              <a-select v-model="model.activityThemeId">

                  <a-select-option v-for="(v, k) in activityThemeList"  :value="v.id">{{v.activityName}}</a-select-option>

              </a-select>
            </a-form-model-item>

          </a-col>

          <a-col :span="24">
            <a-form-model-item label="联系方式" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="phone">
              <a-input v-model="model.phone" placeholder="请输入联系方式"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="简介" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="briefIntroduction">
              <a-textarea v-model="model.briefIntroduction" rows="4" placeholder="请输入简介" />
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="地址" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="address">
              <a-input v-model="model.address" placeholder="请输入地址"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="姓名" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="name">
              <a-input v-model="model.name" placeholder="请输入姓名"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="备注" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="comment">
              <a-input v-model="model.comment" placeholder="请输入备注"  ></a-input>
            </a-form-model-item>
          </a-col>
        </a-row>
        <a-col :span="24">
          <a-form-item label="图片" style="margin-left: 120px">
            <j-image-upload v-model="model.fileList" @change="selectChange" :isMultiple="true"></j-image-upload>
          </a-form-item>
        </a-col>
      </a-form-model>
    </j-form-container>
  </a-spin>
</template>

<script>

  import { httpAction, getAction } from '@/api/manage'
  import { validateDuplicateValue } from '@/utils/util'


  export default {
    name: 'VoteActivityContentForm',
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
          add: "/vote/voteActivityContent/add",
          edit: "/vote/voteActivityContent/edit",
          queryById: "/vote/voteActivityContent/queryById"
        },
        activityTheme:{},
        isAddOrEdit:'',
        activityThemeList:[]
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
      this.getActivityTheme();
      this.getActivityThemeList();
    },
    methods: {
    selectChange(){
      console.log("this.model",this.fileList)
    },
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
      getActivityTheme(){
        let that = this;
        let httpurl = '';
        httpurl+='/vote/voteActivityTheme/getList';
        getAction(httpurl,'').then((res)=>{
          if(res.success){
            that.activityTheme = res.result[0];
            console.log("that.activityThemeList",that.activityTheme)
          }else{
            that.$message.warning(res.message);
          }
        }).finally(() => {
          that.confirmLoading = false;
        })
      },
      getActivityThemeList(){
              let that = this;
              let httpurl = '';
              httpurl+='/vote/voteActivityTheme/getVoteActivityThemeList';
              getAction(httpurl,'').then((res)=>{
                if(res.success){
                  that.activityThemeList = res.result;
                }else{
                  that.$message.warning(res.message);
                }
              }).finally(() => {
                that.confirmLoading = false;
              })
            }
    }
  }
</script>