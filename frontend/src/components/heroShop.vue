<template>
  <div class="character-list">
    <div>用户名：{{ username }}</div>
    <div>余额：{{ balance }} 元</div>
    <el-row :gutter="20">
      <el-col v-for="character in characters" :key="character.id" :span="6">
        <el-card :body-style="{ padding: '20px' }">
          <h3>{{ character.name }}</h3>
          <p>价格: {{ character.price }} 元</p>
          <el-button type="primary" @click="buyCharacter(character)"
            >购买</el-button
          >
        </el-card>
      </el-col>
    </el-row>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "heroShop",
  data() {
    return {
      username: "", // 添加用户名
      balance: 2000, // 添加余额
      characters: [
        { id: 1, name: "角色一", price: 100 },
        { id: 2, name: "角色二", price: 200 },
        { id: 3, name: "角色三", price: 300 },
        { id: 4, name: "角色四", price: 400 },
      ],
      // character:[]
    };
  },
  mounted() {
    this.fetchUserData(); // 获取用户信息
    this.fetchCharacters(); // 获取角色数据
  },
  methods: {
    async fetchUserData() {
      // 获取用户信息
      try {
        const response = await axios.get("/api/getUserInfo"); // 调整为你的用户信息接口路径
        this.username = response.data.username; // 获取用户名
        this.balance = response.data.balance; // 获取余额
      } catch (error) {
        console.error("获取用户数据失败:", error);
        this.$message.error("获取用户数据失败");
      }
    },
    async fetchCharacters() {
      // 获取角色数据
      try {
        const response = await axios.get("/api/getHeroInfo"); // 调整为你的角色接口路径
        this.characters = response.data; // 接口返回的是角色数据数组
      } catch (error) {
        console.error("获取角色数据失败:", error);
        this.$message.error("获取角色数据失败");
      }
    },
    async buyCharacter(character) {
      // 检查余额是否足够
      if (this.balance < character.price) {
        this.$message.error("余额不足，无法购买此角色");
        return;
      }

      // 扣除价格
      this.balance -= character.price;
      console.log(`购买角色 ID: ${character.id}`);

      try {
        // 向后端发送请求，更新用户的余额
        await axios.post("/api/buyCharacter", {
          // 调整为你的角色接口路径
          userId: this.username, // 根据用户名来标识用户
          characterId: character.id,
          price: character.price,
        });
        this.$message.success(`成功购买角色 ID: ${character.id}`);
      } catch (error) {
        console.error("购买角色失败:", error);
        this.$message.error("购买角色失败，请稍后重试");
      }
    },
  },
};
</script>

<style scoped>
.character-list {
  padding: 20px;
}
</style>
