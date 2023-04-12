<template>
    <div class="main-border">
        <el-menu class="el-menu-demo" default-active="1" mode="horizontal" @select="getAllPics">
            <el-menu-item index="4">轮播图列表</el-menu-item>
            <div v-show="this.mode == 4" class="addAdminButton">
                <el-button size="mini" type="success" @click="adminRegVisible = true">添加轮播图</el-button>
                <el-dialog
                    :visible.sync="adminRegVisible"
                    title="添加轮播图"
                    width="25%"
                >
                    <el-upload
                        :limit="1"
                        :on-exceed="handleExceed"
                        :on-preview="fileHandlePreview"
                        :on-remove="fileHandleRemove"
                        :on-success="fileHandleSuccess"
                        :show-file-list="showFileList"
                        accept="image/*"
                        action="http://localhost:8080/file/"
                        drag
                        multiple>
                        <i class="el-icon-upload"></i>
                        <div class="el-upload__text">将图片拖到此处，或<em>点击上传</em></div>
                    </el-upload>
                    <span slot="footer" class="dialog-footer">
                        <el-button type="primary" @click="addPic">添加</el-button>
                    </span>
                </el-dialog>
            </div>
        </el-menu>
        <el-table  v-show="this.mode == 4"
                   :data='pictureList'
                   stripe
                   style="width: 100%;color: #5a5c61;">
            <el-table-column
                label='图片'>
                <template slot-scope="scope">
                    <el-image
                        :preview-src-list="JSON.parse(scope.row.pictureList).pictureList"
                        :src="JSON.parse(scope.row.pictureList).pictureList[0]"
                        style="width: 100px; height: 100px">
                    </el-image>
                </template>
            </el-table-column>
            <el-table-column
                label="图片路径"
                show-overflow-tooltip
                width="200">
                <template slot-scope='scope'>
                    {{JSON.parse(scope.row.pictureList).pictureList[0]}}
                </template>
            </el-table-column>
            <el-table-column
                label="创建时间"
                prop='createTime'
            >
            </el-table-column>
            <el-table-column
                label='操作'>
                <template slot-scope="scope">
                    <el-button round type="danger" @click="deletePic(scope.row.id)">删除</el-button>
                </template>
            </el-table-column>

        </el-table>
        <div class="block">
<!--            <el-pagination-->
<!--                :current-page.sync="nowPage"-->
<!--                :page-size="7"-->
<!--                :total="total"-->
<!--                background-->
<!--                layout="prev, pager, next,jumper"-->
<!--                @current-change="handleCurrentChange">-->
<!--            </el-pagination>-->
        </div>
    </div>
</template>

<script>
export default {
    name: 'carouselMapList',
    data(){
        return {
            mode:4,
            nowPage: 1,
            total: 63,
            adminRegVisible: false,
            pictureList: [],
            adminName:'',
            imgList:[],
            showFileList:true,


        }
    },
    created() {
        this.getAllPics();
    },
    methods: {
        handleCurrentChange(val) {
            if(this.mode == 4){
                this.getAllPics();
            }
        },
        getAllPics() {
            this.$api.findAllCarouselMap().then(res => {
                if (res.status_code == 1){
                    this.pictureList = res.data
                } else {
                    this.$message.error(res.msg)
                }
            }).catch(e => {
                console.log(e);
            })
        },
        addPic() {
            this.$api.addCarouselMap({pictureList: this.imgList}).then(res => {
                if(res.status_code==1){
                    this.getAllPics();
                }else {
                    this.$message.error(res.msg)
                }
            }).catch(e => {
                console.log(e);
                this.$message.error("添加失败，账号重复或网络异常")
            });
            this.adminRegVisible = false
        },
        deletePic(value) {
            this.$api.deleteCarouselMap({id: value}).then(res => {
                if (res.status_code == 1) {
                    this.$message.success("删除成功！")
                }else {
                    this.$message.error(res.msg)
                }
            }).catch(e => {
                console.log(e);
                this.$message.error("删除失败，网络异常！")
            })
        },
        fileHandleRemove(file, fileList) {
            console.log(file, fileList);
            for(let i=0;i<this.imgList.length;i++){
                if(this.imgList[i]===file.response.data){
                    this.imgList.splice(i,1);
                }
            }
        },
        fileHandlePreview(file) {
            console.log(file);
            this.dialogImageUrl=file.response.data;
            this.imgDialogVisible=true;
        },
        fileHandleSuccess(response, file, fileList){
            console.log("file:",response,file,fileList);
            this.imgList.push(response.data);
        },
        handleExceed(files, fileList) {
            this.$message.warning(`限制1张图片，本次选择了 ${files.length} 张图，共选择了 ${files.length + fileList.length} 张图`);
        },
    }
};
</script>

<style scoped>
.main-border{
    background-color: #FFF;
    padding: 10px 30px;
    box-shadow: 0 1px 15px -6px rgba(0,0,0,.5);
    border-radius: 5px;
}
.block {
    display: flex;
    justify-content:center;
    padding-top: 15px;
    padding-bottom: 10px;
    width: 100%;
}
.addAdminButton{
    display:flex;
    justify-content: flex-end;
    align-items: center;
    height: 60px;
    outline: none;
}
</style>