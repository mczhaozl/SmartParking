<template>
  <a-drawer
    :title="title"
    :width="width"
    placement="right"
    :closable="false"
    @close="close"
    :visible="visible">
  
    <a-spin :spinning="confirmLoading">
      <a-form :form="form">

        <a-form-item label="会议名称" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="['name']" placeholder="请输入会议名称"></a-input>
        </a-form-item>
        <a-form-item label="会议主题" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="['theme']" placeholder="请输入会议主题"></a-input>
        </a-form-item>
        <a-form-item label="会议开始时间" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="['startTime']" placeholder="请输入会议开始时间"></a-input>
        </a-form-item>
        <a-form-item label="会议结束时间" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="['endTime']" placeholder="请输入会议结束时间"></a-input>
        </a-form-item>
        <a-form-item label="会议室" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-dict-select-tag type="list" v-decorator="['roomName']" :trigger-change="true" dictCode="" placeholder="请选择会议室"/>
        </a-form-item>
        <a-form-item label="发起人" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="['sponsor']" placeholder="请输入发起人"></a-input>
        </a-form-item>
        <a-form-item label="审批人" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="['approve']" placeholder="请输入审批人"></a-input>
        </a-form-item>
        <a-form-item label="状态" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="['status']" placeholder="请输入状态"></a-input>
        </a-form-item>
        <a-form-item label="备注" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="['remark']" placeholder="请输入备注"></a-input>
        </a-form-item>
        
      </a-form>
    </a-spin>
    <a-button type="primary" @click="handleOk">确定</a-button>
    <a-button type="primary" @click="handleCancel">取消</a-button>
  </a-drawer>
</template>

<script>

  import { httpAction } from '@/api/manage'
  import pick from 'lodash.pick'
  import { validateDuplicateValue } from '@/utils/util'
  import JDictSelectTag from "@/components/dict/JDictSelectTag"
  
  export default {
    name: "JsMeetingModal",
    components: { 
      JDictSelectTag,
    },
    data () {
      return {
        form: this.$form.createForm(this),
        title:"操作",
        width:800,
        visible: false,
        model: {},
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
          add: "/org.jeecg.modules.meeting/jsMeeting/add",
          edit: "/org.jeecg.modules.meeting/jsMeeting/edit",
        }
      }
    },
    created () {
    },
    methods: {
      add () {
        this.edit({});
      },
      edit (record) {
        this.form.resetFields();
        this.model = Object.assign({}, record);
        this.visible = true;
        this.$nextTick(() => {
          this.form.setFieldsValue(pick(this.model,'name','theme','startTime','endTime','roomName','sponsor','approve','status','remark'))
        })
      },
      close () {
        this.$emit('close');
        this.visible = false;
      },
      handleOk () {
        const that = this;
        // 触发表单验证
        this.form.validateFields((err, values) => {
          if (!err) {
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
            let formData = Object.assign(this.model, values);
            console.log("表单提交数据",formData)
            httpAction(httpurl,formData,method).then((res)=>{
              if(res.success){
                that.$message.success(res.message);
                that.$emit('ok');
              }else{
                that.$message.warning(res.message);
              }
            }).finally(() => {
              that.confirmLoading = false;
              that.close();
            })
          }
         
        })
      },
      handleCancel () {
        this.close()
      },
      popupCallback(row){
        this.form.setFieldsValue(pick(row,'name','theme','startTime','endTime','roomName','sponsor','approve','status','remark'))
      }
      
    }
  }
</script>

<style lang="less" scoped>
/** Button按钮间距 */
  .ant-btn {
    margin-left: 30px;
    margin-bottom: 30px;
    float: right;
  }
</style>