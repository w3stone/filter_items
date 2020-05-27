<template>
    <my-radio class="filter_switch" v-model="currentValue" :options="finalOptions"
        :itemid="itemid" :required="required" :titleName="titleName" :titleBefore="titleBefore" :prop="prop"
        :disabled="disabled" :withForm="withForm" @change="updateVal"
        ref="formItem">

        <slot slot="unit" name="unit"></slot>
        <slot slot="description" name="description"></slot>

    </my-radio>
</template>

<script>
    import myRadio from './radio/withForm'

    export default {
        name: "switchs",
        props:{
            withForm: Boolean, //是否包含表单
            itemid: Number,
            required: Boolean, //是否必填
            titleName: String, //标题
            titleBefore: String, //标题前缀
            prop: String,
            /*******************/
            value: {
                required: true
            },
            options: {
                type: Array
            },
            labels: String, //两端文字显示
            disabled: Boolean
        },
        data(){
            return {
                currentValue: this.value,
                defaultOptions: [
                    {title: "否", value: "否"},
                    {title: "是", value: "是"}
                ]
            }
        },
        computed:{
            finalOptions(){
                return this.options || this.defaultOptions;
            }
        },
        methods:{
            updateVal(value){
                //触发事件，并向父级传入新值
                this.$emit('input', value);
                this.$emit('change', value);
            },
            //清空验证(调用子组件方法)
            clearValidate(){
                this.$nextTick(()=>{
                    this.$refs.formItem.clearValidate();
                })
            }
        },
        components:{
            myRadio
        },
        watch:{
            value:{
                handler(newVal, oldVal){
                    //console.log(newVal, oldVal);
                    this.currentValue = newVal;
                }
            }
        }
	}
</script>

<style lang="scss" type="text/css">
    
</style>