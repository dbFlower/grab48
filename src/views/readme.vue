<style>
.c-readme {
  padding-left: 2rem;
  margin: 2rem 0;
}
.c-readme p span {
  margin-right: 1rem;
}
</style>

<template>
  <div>
    <h1># 关于&说明</h1>
    <cDivider/>
    <div class="c-readme">
      <!-- <img src="static/img/a.jpg" style="width: 80px"> -->
      <h3>
        本程序仅供测试使用！
      </h3>
      <p>
        <span>当前版本: v{{ sharedState.version }}</span>
        <span>如有疑问请Telegram: <a href="https://t.me/kennen0">kennen0</a></span>
      </p>
      <p>
        <span>
          GitHub项目:
          <a target="_blank" href="http://github.com/kennen0/grab48">grab48</a>
        </span>
      </p>
      <cDivider/>
    </div>
    <cDivider/>
  </div>
</template>
<script>
import cDivider from "@/components/cDivider";
export default {
  name: "readme",
  data() {
    return {
      sharedState: this.GLOBAL,
    };
  },
  methods: {
  },
  mounted() {
  },
  components: { cDivider }
};
</script>