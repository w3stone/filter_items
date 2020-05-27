<!--select封装-->
<template>
    <my-select ref="formItem" class="form_item_hospital" v-model="currentValue" :options="hospitalOptions"
        :itemid="itemid" :required="required" :titleName="titleName" :titleBefore="titleBefore" :prop="prop"
        :disabled="disabled || method!='add'" :withForm="withForm" 
        @change="updateVal">

        <slot slot="unit" name="unit"></slot>
        <slot slot="description" name="description"></slot>
    </my-select>

</template>

<script>
    import formItem from '../formItem';
    import mySelect from '../select/withForm';

    export default {
        name: "special_doctors",
        props:{
            withForm: Boolean,
            itemid: Number,
            required: Boolean, //是否必填
            titleName: String, //标题
            titleBefore: String, //标题前缀
            prop: String,
            /***********************/
            value: {
                required: true
            },
            disabled: Boolean, //禁用状态
            placeholder: String,
            notClearable: Boolean //是否可清除
        },
        data(){
            return {
                currentValue: "",
                hospitalOptions: []
            }
        },
        computed:{
            method(){ //新增or编辑/查看
                return this.$route.path=="/patient"? "add": "other";
            }
        },
        mounted(){
            this.getHosList();
        },
        methods:{
            //获取管辖的医院列表
            getHosList(){ 
                this.$dataPost("/hospital/list", (data)=>{
                    data = data || [];
                    this.hospitalOptions = data.map(o => ({"title":o.hosname, "value":o.id.toString()}) );
                    if(this.hospitalOptions.length==1){ //如果单医院，自动赋值
                        this.currentValue = this.hospitalOptions[0].value;
                    }
                });
            },
            //向父级传值
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
        watch:{
            value:{
                immediate: true,
                handler(newVal, oldVal){
                    //console.log(newVal, oldVal);
                    this.currentValue = newVal || "";
                }
            }
        },
        components:{
            formItem, mySelect
        }
	}
</script>

<style lang="scss" type="text/css">
    
</style>