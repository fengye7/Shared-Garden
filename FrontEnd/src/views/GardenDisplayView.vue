<template>
  <body class="maingardendis">
    <div class="container" style="margin-top: 60px">
      <!-- 导航栏 -->
      <NavBar />

      <el-row id="mainpart">
        <!-- 侧边栏 -->
        <SideBar />
        <!-- 主体 -->
        <el-col :span="13">
          <el-container>

            <!-- 标签栏 -->
            <el-header>
              <el-menu
                :default-active="activeTab"
                mode="horizontal"
                @select="handleMenuSelect"
                class="menuheader"
              >
                <el-menu-item index="hot" class="infohead">热门</el-menu-item>
                <el-menu-item index="new" class="infohead">最新</el-menu-item>
                <el-menu-item index="mine" class="infohead">我的</el-menu-item>
              </el-menu>
            </el-header>

            <!-- 图片展示区域 -->
            <el-main>
              <!-- hot -->
              <div v-if="activeTab === 'hot'">
                <el-card
                  v-for="(image, index) in hotGarden"
                  :key="index"
                  @click="this.$router.push({name: 'garden', params: { garden_id: image.garden_id }})"
                  class="garden-card"
                >
                  <GardenBlock :image="image" />
                </el-card>
              </div>

              <!-- new -->
              <div v-else-if="activeTab === 'new'">
                <el-card
                  v-for="(image, index) in newGarden"
                  :key="index"
                  @click="this.$router.push({name: 'garden', params: { garden_id: image.garden_id }})"
                  class="garden-card"
                >
                  <GardenBlock :image="image" />
                </el-card>
              </div>

              <!-- mine -->
              <div v-else-if="activeTab === 'mine'">
                <el-card
                  v-for="(image, index) in myGarden"
                  :key="index"
                  @click="this.$router.push({name: 'garden', params: { garden_id: image.garden_id }})"
                  class="garden-card"
                >
                  <GardenBlock :image="image" />
                </el-card>
              </div>

            </el-main>

          </el-container>
        </el-col>

        <!-- 花园排行榜 -->
        <el-col :span="8">
          <el-container>
            <br />
            <i class="el-icon-star-on"></i>
            <el-header class="infohead">花园排行榜</el-header>
            <el-main>
              <el-row :gutter="20">
                <el-col
                  :span="9"
                  v-for="(item, index) in displayedImageList"
                  :key="index"
                >
                  <el-card
                    class="garden-rank"
                    @click="this.$router.push({name: 'garden', params: { garden_id: item.garden_id }})"
                  >
                    <!-- 图片展示区域 -->
                    <img :src="item.imageUrl" alt="花园" style="width: 100%" />
                    <!-- 图片描述 -->
                    <div style="text-align: center" class="garden-info">
                      {{ item.description }}
                    </div>
                  </el-card>
                </el-col>
              </el-row>
              <br />
              <el-button
                @click="toggleShowMore"
                style="margin-top: 10px"
                class="showMorebutton"
                >{{ showMore ? "收起" : "显示更多" }}</el-button
              >
            </el-main>
          </el-container>
        </el-col>

      </el-row>
    </div>
  </body>
</template>

<style scoped>
@import url("../css/gardenDisplay.css");
</style>
 
<script>
import { getHotGarden, getNewGarden, getMyGarden, getGardenList } from '@/api/gardenDisplayAPI';
import { getUserNameById } from '@/api/accountApi';
import { useStore } from 'vuex';
export default {
  el: "#mainpart",
  data() {
    return {
      userId: useStore().state.user.id,
      activeTab: "hot", // 默认选中热门板块
      showMore: false, // 控制是否显示更多图片
      displayedImageList: [], // 实际显示的图片列表
      maxDisplayCount: 4, // 默认显示的图片数量
      imageList: [ ],
      hotGarden: [ ],
      newGarden: [ ],
      myGarden: [ ],
    };
  },
  methods: {

    // init garden

    async initGardenData() {
      this.initHotGarden();
      this.initNewGarden();
      this.initMyGarden();
    },
    async initHotGarden() {
      for (let i = 0; i < 3; i++) {
        const hotGardenData = await getHotGarden();
        const username = await getUserNameById(hotGardenData.ownerId);
        const hotGarden = {
          garden_id: hotGardenData.gardenId,
          imageUrl: hotGardenData.pictures,
          username: username,
          gardenname: hotGardenData.name,
          hot: hotGardenData.stars,
        };
        this.hotGarden.push(hotGarden);
      }
    },
    async initNewGarden() {
      for (let i = 0; i < 3; i++) {
        const newGardenData = await getNewGarden();
        const username = await getUserNameById(newGardenData.ownerId);
        const newGarden = {
          garden_id: newGardenData.gardenId,
          imageUrl: newGardenData.pictures,
          username: username,
          gardenname: newGardenData.name,
          hot: newGardenData.stars,
        };
        this.newGarden.push(newGarden);
      }
    },
    async initMyGarden() {
      for (let i = 0; i < 1; i++) {
        const myGardenData = await getMyGarden();
        const username = await getUserNameById(myGardenData.ownerId);
        const myGarden = {
          garden_id: myGardenData.gardenId,
          imageUrl: myGardenData.pictures,
          username: username,
          gardenname: myGardenData.name,
          hot: myGardenData.stars,
        };
        this.myGarden.push(myGarden);
      }
    },

    // init images (right side bar)

    async initDisplayedImages() {
      const gardenList = await getGardenList();
      for (let i = 0; i < gardenList.length; i++)
      {
        this.imageList.push({
          garden_id: gardenList[i].gardenId,
          imageUrl: gardenList[i].pictures,
          description: "TOP" + (i + 1) + " " + gardenList[i].name,
        });
      }
      this.displayedImageList = this.imageList.slice(0, this.maxDisplayCount);
    },

    handleMenuSelect(index) {
      this.activeTab = index;
    },
    toggleShowMore() {
      this.showMore = !this.showMore;
      this.updateDisplayedImages();
    },
    updateDisplayedImages() {
      if (this.showMore) {
        this.displayedImageList = this.imageList; // 显示全部图片
      } else {
        this.displayedImageList = this.imageList.slice(0, this.maxDisplayCount); // 仅显示前maxDisplayCount张图片
      }
    },

  },
  async created() {
    this.initGardenData();
    this.initDisplayedImages();
    this.updateDisplayedImages(); // 初始化时根据showMore状态设置图片数量
  },

  watch:{
    activeTab(oldValue,newValue){
      console.log(`activeTab 变化！ ${oldValue} -> ${newValue}`);

      if (this.userId != '')
      {
        if (newValue == "hot")
        {
          //互动模块
          this.hotImages=getHotGarden(this.userId)
          console.log('开始获取用户信息！')
          //无本地需要配置的参数
        }
        else if (newValue == "new") {
          //博客模块
          this.newImages=getNewGarden(this.userId)
          console.log('开始获取用户信息！')
          //无本地需要配置的参数
        }
      }

    }
  }

};
</script>