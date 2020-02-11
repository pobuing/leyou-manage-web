<template>
    <div>
        <v-layout row wrap>
            <v-flex  xs6>
                 <v-btn round color="primary" dark>新增品牌</v-btn>
            </v-flex>
            <v-flex  xs6>
                 <v-text-field label="请输入关键字搜索" v-model="keyword" append-icon="search"></v-text-field>
            </v-flex>
        </v-layout>
        <v-data-table
                :headers="headers"
                :items="brandList"
                :pagination.sync="pagination"
                :total-items="totalBrands"
                :loading="loading"
                class="elevation-1"
        >
            <template v-slot:items="props">
                <td  class="text-xs-center">{{ props.item.id }}</td>
                <td class="text-xs-center">{{ props.item.name }}</td>
                <td class="text-xs-center"> <img v-bind:src="props.item.image"></td>
                <td class="text-xs-center">{{ props.item.letter }}</td>
            </template>
        </v-data-table>
    </div>
</template>

<script>
    export default {
        data () {
            return {
                keyword:'',
                totalBrands: 100,
                brandList: [],
                loading: true,
                pagination: {},
                headers: [
                    {
                        text: '编号',
                        align: 'center',
                        sortable: false,
                        value: 'id'
                    },
                    { text: '名称',align: 'center', value: 'name' },
                    { text: 'logo图片', align: 'center',value: 'image' },
                    { text: '首字母', align: 'center',value: 'letter' }
                ]
            }
        },
        watch: {
            pagination: {
                handler () {
                    this.getBrandListFromServer();
                },
                deep: true
            },
            keyword(){
                this.getBrandListFromServer();
            }
        },
        mounted () {
            this.getBrandListFromServer();
        },
        methods: {
            getBrandListFromServer () {
                this.loading = true  //显示进度条
                    // const { sortBy, descending, page, rowsPerPage } = this.pagination
                // GET /brand/page?key=&page=1&rows=5&sortBy=id&desc=false
                ///item/brand/page?key=&page=1&rows=5&sortBy=id&desc=false
                this.$http.get("/item/brand/page",{
                    params: {
                        key: this.keyword,
                        page: this.pagination.page,
                        rows:  this.pagination.rowsPerPage,
                        sortBy:  this.pagination.sortBy,
                        desc:  this.pagination.descending
                    }
                }).then(resp=>{
                    this.loading = false  //隐藏进度条
                    this.brandList = resp.data.items;
                    this.totalBrands = resp.data.total;
                })


               /* setTimeout(() => {
                    this.loading = false  //关闭进度条
                    this.brandList= [
                        {id: 2032, name: "OPPO", image: "1.jpg", letter: "O"},
                        {id: 2033, name: "飞利浦", image: "2.jpg", letter: "F"},
                        {id: 2034, name: "华为", image: "3.jpg", letter: "H"},
                        {id: 2036, name: "酷派", image: "4.jpg", letter: "K"},
                        {id: 2037, name: "魅族", image: "5.jpg", letter: "M"}
                    ];
                }, 1000)*/
            }
        }
    }
</script>