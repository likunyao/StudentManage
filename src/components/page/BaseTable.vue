<template>
    <div>
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item>
                    <i class="el-icon-lx-cascades"></i> 学生信息
                </el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="container">
            <div class="handle-box">
                <el-button type="primary" icon="el-icon-search" @click="handleSearch">搜索</el-button>
                <el-input v-model="query.name" placeholder="用户名" class="handle-input mr10"></el-input>

            </div>

            <el-table
                    :data="tableData1"
                    class="studentTable"
                    >
                <el-table-column v-for="items in tableDataType"
                             :prop="items.nameProp"
                             :label="items.nameLable"
                              width="300">
                </el-table-column>
            </el-table>

            <!--<div class="pagination">
                <el-pagination
                    background
                    layout="total, prev, pager, next"
                    :current-page="query.pageIndex"
                    :page-size="query.pageSize"
                    :total="pageTotal"
                    @current-change="handlePageChange"
                ></el-pagination>
            </div>-->
        </div>

        <!-- 编辑弹出框 -->
        <el-dialog title="编辑" :visible.sync="editVisible" width="30%">
            <el-form ref="form" :model="form" label-width="70px">
                <el-form-item label="用户名">
                    <el-input v-model="form.name"></el-input>
                </el-form-item>
                <el-form-item label="地址">
                    <el-input v-model="form.address"></el-input>
                </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button @click="editVisible = false">取 消</el-button>
                <el-button type="primary" @click="saveEdit">确 定</el-button>
            </span>
        </el-dialog>
    </div>
</template>

<script>
import { fetchData } from '../../api/index';
export default {
    name: 'basetable',
    data() {
        return {
            tableDataType: [{
                nameLable:'序号',
                nameProp:'id'
            }, {
                nameLable: '姓名',
                nameProp: 'name'
            }, {
                nameLable: '学号',
                nameProp: 'num'

            }, {
                nameLable: '年级',
                nameProp: 'grade'
            },{
                nameLable:'今日签到' ,
                nameProp:'check'
            }
            ],
            tableData1: [{
                id: 1 ,
                name: '王小虎',
                num: '101',
                grade: '研一',
                check:'7:14'

            }, {
                id: 2 ,
                name: '王小虎',
                num: '101',
                grade: '研一',
                check:'7:14'
            }, {
                id: 3 ,
                name: '王小虎',
                num: '101',
                grade: '研一',
                check:'7:14'
            }, {
                id: 4 ,
                name: '王小虎',
                num: '101',
                grade: '研一',
                check:'7:14'
            }],

            query: {
                address: '',
                name: '',
                pageIndex: 1,
                pageSize: 10
            },
            tableData: [],
            multipleSelection: [],
            delList: [],
            editVisible: false,
            pageTotal: 0,
            form: {},
            idx: -1,
            id: -1
        };
    },
    created() {
        this.getData();
    },
    methods: {
        // 获取 easy-mock 的模拟数据
        getData() {
            fetchData(this.query).then(res => {//读取数据 res即为返回
                console.log(res);
                this.tableData = res.list;
                this.pageTotal = res.pageTotal || 50;
            });
        },
        // 触发搜索按钮
        handleSearch() {
            this.$set(this.query, 'pageIndex', 1);
            this.getData();
        },
        // 删除操作
        handleDelete(index, row) {
            // 二次确认删除
            this.$confirm('确定要删除吗？', '提示', {
                type: 'warning'
            })
                .then(() => {
                    this.$message.success('删除成功');
                    this.tableData.splice(index, 1);
                })
                .catch(() => {});
        },
        // 多选操作
        handleSelectionChange(val) {
            this.multipleSelection = val;//val为所选事务集合
        },
        delAllSelection() {
            const length = this.multipleSelection.length;
            let str = '';
            this.delList = this.delList.concat(this.multipleSelection);
            for (let i = 0; i < length; i++) {
                str += this.multipleSelection[i].name + ' ';
            }
            this.$message.error(`删除了${str}`);
            this.multipleSelection = [];
        },
        // 编辑操作
        handleEdit(index, row) {
            this.idx = index;
            this.form = row;
            this.editVisible = true;
        },
        // 保存编辑
        saveEdit() {
            this.editVisible = false;
            this.$message.success(`修改第 ${this.idx + 1} 行成功`);
            this.$set(this.tableData, this.idx, this.form);
        },
        // 分页导航
        handlePageChange(val) {
            this.$set(this.query, 'pageIndex', val);
            this.getData();
        }
    }
};
</script>

<style scoped>
    .studentTable{
        width: 1500px;
        font-size: 14px;
        margin-left: 70px;
        margin-right: 30px;
        margin-top: 30px;
    }
.handle-box {
    margin-bottom: 20px;

    text-align: left;
}

.handle-select {
    width: 120px;
}

.handle-input {
    width: 300px;
    display: inline-block;
    margin-left: 5px;
}
.table {
    width: 100%;
    font-size: 14px;
}
.red {
    color: #ff0000;
}
.mr10 {
    margin-right: 10px;
}
.table-td-thumb {
    display: block;
    margin: auto;
    width: 40px;
    height: 40px;
}
</style>
