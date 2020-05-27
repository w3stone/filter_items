<template>
    <el-checkbox-group class="filter_radio" v-model="currentValue" 
        @change="updateVal" :disabled="disabled">

        <el-checkbox class="filter_check_item" v-for="(item, index) in currentOptions" :key="index"
            :label="item[finalProps.value]" border>
            
            <template v-if="!picClick">
                {{ item[finalProps.label] }}
            </template>

            <template v-if="picClick">
                <div class="check_pic">
                    <img :src="item[finalProps.label]" />
                </div>
                <p class="pic_title">图片{{index+1}}</p>
            </template>
        </el-checkbox>
    </el-checkbox-group>
</template>

<script>
    export default {
        name: "radios",
        props:{
            value: { required: true },
            options: { 
                required: true
            }, //选项列表
            border: Boolean,
            props: Object, //配置选项
            disabled: Boolean,
            picClick: Boolean, //是否图片点击
        },
        data(){
            return {
                currentValue: [],
                currentOptions: [],
                defaultProps: {
                    label: 'title',
                    value: 'value'
                }
            }
        },
        computed:{
            finalProps(){
                return this.props || this.defaultProps;
            }
        },
        methods:{
            makeValue(value){
                //value = value===""? 0: value; //???
                this.currentValue = (value || value===0)? [value]: [];
            },
            makeOptions(options){
                if(typeof options=='string'){
                    let tempOptions = this.$string2Arr(options);
                    this.currentOptions = tempOptions.map(val=>({'title':val, 'value':val}));

                }else if(typeof this.options=='object'){
                    this.currentOptions = options;
                }else{
                    this.currentOptions = [];
                }
            },
            //向父级传值
            updateVal(value){
                value = value || [];
                let finalValue = value.length>0? value[value.length-1]: "";

                //优先取数组索引为1的值
                // if(value[1] || value[1]===0){
                //     value_bak = value[1];
                // }else if(value[0] || value[0]===0){
                //     value_bak = value[0];
                // }
                
                //触发事件，并向父级传入新值
                this.$emit('input', finalValue);
                this.$emit('change', finalValue);
            }
        },
        watch:{
            value:{
                immediate: true,
                handler(newVal, oldVal){
                    //console.log(newVal, oldVal);
                    this.makeValue(newVal);
                }
            },
            options:{
                immediate: true,
                handler(newVal, oldVal){
                    //console.log(newVal, oldVal);
                    this.makeOptions(newVal);
                }
            }
        }
	}
</script>

<style lang="scss" type="text/css">
    .filter_radio{
        display: inline-block;
        
        .filter_check_item{
            &.is-bordered{
                background-color: #fff;
                margin: 0 !important;
                margin-right: 4px !important;
                margin-bottom: 4px !important;
            }
            &.is-checked{
                background-color: $activeColor;
                border-color: $activeColor;
                .el-checkbox__label{
                    color: #fff;
                }
            }
            .el-checkbox__inner{
                display: none;
            }
        }

        .filter_check_item{
            //图片选项
            .check_pic{
                width: 100px;
                height: 60px;
                border: 1px solid transparent;
                border-radius: 4px;
                overflow: hidden;
                img{
                    width: 100%;
                    height: 100%;
                }
            }
            .pic_title{
                text-align: center;
            }
        }
    }
</style>