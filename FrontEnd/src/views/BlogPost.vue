<!--
 * @Author: Jialin Lu
 * @GitHub: https://github.com/tju2050633
 * @Date: 2023-09-06 19:42:12
 * @FilePath: /SharingGardenProject/FrontEnd/src/views/BlogPost.vue
 * @Description: 
 * 
 * Copyright (c) 2023 by 1640889387@qq.com, All Rights Reserved. 
-->
<template>
  <body class="mainblogpostpage">
    
    <div class="container" style="margin-top: 60px">

      <NavBar />
      <el-row id="mainpart">
        <SideBar />
        
        <el-col :span="13" :offset="2" style="margin-top:25px;">
          <el-card class="blogpostpic">
            <el-row>
              <img class="head-img" src="../assets/BLOGPOST.png" />
            </el-row>
          </el-card>
          <PostBlog style="height:60vh;overflow-y:auto;margin-top:40px"/>
        </el-col>
      </el-row>
    </div>
  </body>
</template>

<script>
export default{
  name:'BlogPost'
}
</script>

<style scoped>
.blogpostpic{
  margin-left: 17vw;
  width:27vw;
  border-radius: 10px;
  border-color: rgb(56, 235, 235);
  border-width: 5px;
}

.head-img{
  margin-left: 6px;
  height: auto;
  width: 24vw;
}
</style>