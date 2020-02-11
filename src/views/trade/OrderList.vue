<template>

    <div>
        <v-layout row wrap>
            <v-flex xs12 sm6>
                <v-text-field
                        label="搜索"
                        v-model = "search"
                        append-icon="search"
                        @keyup.enter="searchKey()"
                ></v-text-field>
            </v-flex>
            <v-spacer/>
            <v-flex xs3>
                订单状态：
                <v-btn-toggle mandatory v-model.lazy="status">
                    <v-btn>
                        全部
                    </v-btn>
                    <v-btn :value="1">
                        未支付
                    </v-btn>
                    <v-btn :value="2">
                        未发货
                    </v-btn>
                    <v-btn :value="3">
                        已发货
                    </v-btn>
                    <v-btn :value="4">
                        已收货
                    </v-btn>
                    <v-btn :value="5">
                        交易取消
                    </v-btn>
                    <v-btn :value="6">
                        交易结束
                    </v-btn>
                </v-btn-toggle>
            </v-flex>
        </v-layout>
        <v-data-table
                :headers="headers"
                :items="orders"
                :pagination.sync="pagination"
                :total-items="totalOrders"
                :loading="loading"
                class="elevation-1"
        >
            <template v-slot:items="props">
                <td class="text-xs-center">{{ props.item.orderId }}</td>
                <td class="text-xs-center">{{ props.item.bType }}</td>
                <td class="text-xs-center">{{ props.item.username }}</td>
                <td class="text-xs-center"></td>
                <td class="text-xs-center">{{ props.item.price }}</td>
                <td class="text-xs-center">{{ props.item.num }}</td>
                <td class="text-xs-center">{{ props.item.actualFee }}</td>
                <td class="text-xs-center">{{ props.item.createTime }}</td>
                <td class="text-xs-center">{{ props.item.payTime }}</td>
                <td class="text-xs-center">{{ props.item.status }}</td>
                <td class="text-xs-center"></td>
            </template>
        </v-data-table>
    </div>
</template>

<script>
    export default {
        data () {
            return {
                totalOrders: 0,
                orders: [],
                loading: false,
                pagination: {},
                search:'',
                headers: [
                    {text: 'id', align: 'center', sortable: false, value: 'orderId'},
                    { text: '订单类型',  align: 'center',value: 'bType' },
                    { text: '用户',  align: 'center',value: 'username' },
                    { text: '商品',  align: 'center',value: 'name' },
                    { text: '单价',  align: 'center',value: 'username' },
                    { text: '数量',  align: 'center',value: 'num' },
                    { text: '实付',  align: 'center',value: 'actualFee' },
                    { text: '下单时间',  align: 'center',value: 'createTime' },
                    { text: '付款时间',  align: 'center',value: 'payTime' },
                    { text: '状态',  align: 'center',value: 'status' },
                    { text: '操作', align: 'center' }
                ]
            }
        },
        watch:{
            pagination:{
                deep:true,
                handler(){
                    this.loadData();
                }
            }
        },
        mounted () {
            this.loadData();
        },
        methods: {
            //初始化 数据
            init(){
                this.pagination.page = 1;
                this.orders=[];
                this.totalOrders = 0;
            },
            //关键词搜索
            searchKey(){
                this.init();
                this.loadData();
            },
            loadData () {
                //请求服务端
                this.loading = true;
                this.$http.get('/item/order/page',{params:{
                        key:this.search,
                        page:this.pagination.page,
                        rows:this.pagination.rowsPerPage,
                        sortBy:this.pagination.sortBy,
                        desc:this.pagination.descending
                    }}).then(resp =>{
                    this.orders = resp.data.items;
                    this.totalOrders = resp.data.total;
                })
                this.loading = false;
            }
        }
    }
</script>