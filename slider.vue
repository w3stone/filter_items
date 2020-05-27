<!--滑块控件-->
<template>
    <div class="form_item_slider" :style="{display:!withForm?'inline-block':'block'}">
        <template v-if="withForm">
            <form-item :prop="prop" :titleName="titleName" :titleBefore="titleBefore" :itemid="itemid"
                :rules="[
                    {required:required, message:titleName+'必填', trigger:['blur']}
                ]" ref="formItem">

                <div>
                    <el-slider class="filter_slider" v-model="currentValue"
                        :min="range?minValue:0" :max="range?maxValue:100" :step="step?step:1"
                        :marks="marks"
                        @change="updateVal" :disabled="disabled">
                    </el-slider>

                    <slot name="unit"></slot>
                    <slot name="description"></slot>
                </div>

            </form-item>
        </template>

        <template v-else>
            <el-slider class="filter_slider" v-model="currentValue"
                :min="min?min:0" :max="max?max:undefined" :step="step?step:1"
                :marks="marks"
                @change="updateVal" :disabled="disabled">
            </el-slider>
        </template>
    </div>
</template>

<script>
    import formItem from './formItem';

    export default {
        name: "slider",
        props:{
            withForm: Boolean, //是否包含表单
            itemid: Number,
            required: Boolean, //是否必填
            titleName: String, //标题
            titleBefore: String, //标题前缀
            prop: String,
            /*******************************/
            value: {
                required: true
            },
            width: String, //宽度
            range: String, //滑块范围
            step: Number,
            disabled: Boolean
        },
        data(){
            return {
                currentValue: this.value,
                currentValue_bak: "", //备份当前值
                marks: {
                    0: '0',
                    10: '10',
                    20: '20',
                    30: '30',
                    40: '40',
                    50: '50',
                    60: '60',
                    70: '70',
                    80: '80',
                    90: '90',
                    100: '100'
                }
            }
        },
        computed:{
            rangeStr(){
                return this.range || "";
            },
            minValue(){
                let min = this.rangeStr.split(",")[0];
                return this.str2Value(min);
            },
            maxValue(){
                let max = this.rangeStr.split(",")[1];
                return this.str2Value(max);
            }
        },
        methods:{
            //向父级传值
            updateVal(value){
                value = value || "";
                //if(value===this.currentValue_bak) return false;

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
                    this.currentValue = this.$string2Num(newVal) || 0;
                }
            }
        },
        components:{
            formItem
        }
	}
</script>

<style lang="scss" type="text/css">
    .filter_slider{
        width: 40%;
        .el-slider__marks{
            .el-slider__marks-text{
                margin-top: 0;
            }
        }
    }
</style>