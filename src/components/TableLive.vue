/* 成员直播表格 */
/*
  感谢 @dbFlower(https://github.com/dbFlower) 的贡献
 */
<style scoped>
img {
  max-width: 20px;
  max-height: 20px;
}
table td {
  text-overflow: ellipsis;
  white-space: nowrap;
  overflow: hidden;
  line-height: 1rem;
  padding-top: 10px;
  padding-bottom: 10px;
}
.c-cwidth {
  max-width: 10rem;
}
</style>
<style>
tr.c-table-live td,
tr.c-table-live td a {
  color: var(--teamcolor);
}
div.c-is-col td.c-switch-width {
  max-width: 10rem;
}
</style>


<template>
  <div class="mdui-table-fluid mdui-shadow-0">
    <div class="c-table" :class="{'c-is-col': isCol}">
      <table class="mdui-table mdui-table-hoverable">
        <thead>
          <tr>
            <th>成员</th>
            <th>直播标题</th>
            <th>类型</th>
            <th>开播时间</th>
            <th>封面</th>
            <th>在线观看</th>
            <th>视频源地址</th>
            <th v-if="!isLive">弹幕源地址</th>
          </tr>
        </thead>
        <tbody>
          <tr
            v-for="row in list"
            :key="row.liveId"
            :style="{'--teamcolor': GLOBAL.renderColor(row.userInfo.userId)}"
            class="c-table-live"
          >
            <td>{{ GLOBAL.memberId2name(row.userInfo.userId) }}</td>
            <!-- 标题 -->
            <td class="c-switch-width">{{ row.title }}</td>
            <!-- 类型 -->
            <td>
              {{ row.liveType == 1 ? "视频" : "电台" }}
              <!-- 在线播放 暂不可用 -->
              <span v-if="isLive&&false">
                <span v-if="row.liveone&&row.liveone.roomId">
                  <a href @click.prevent="livePlay(row)">
                    <i v-if="row.liveType == 1" class="mdui-icon material-icons">live_tv</i>
                    <i v-else class="mdui-icon material-icons">radio</i>
                  </a>
                </span>
              </span>
            </td>
            <td>{{ new Date(parseInt(row.ctime)).Format("yyyy-MM-dd HH:mm:ss") }}</td>

            <!-- 配图 -->
            <td class="c-switch-width">
              <img v-lazy="GLOBAL.getPicPath(row.coverPath)">
              <span v-if="row.liveone">
                <span v-if="row.liveone.carousels">
                  <img
                    v-for="(v,i) in row.liveone.carousels.carousels"
                    v-lazy="GLOBAL.getPicPath(v)"
                    :key="i"
                  >
                </span>
              </span>
            </td>
            <td class="c-switch-width">
              <a
                :href="GLOBAL.memberLiveUrl + row.liveId"
                target="_blank"
              >{{GLOBAL.memberLiveUrl + row.liveId}}</a>
            </td>
            <td class="c-switch-width">
              <!-- 被更正时，可能错误的原地址 -->
              <div v-if="row.liveone">
                <el-tooltip v-if="renderStreamPath(row)!=row.liveone.playStreamPath" effect="light">
                  <i class="el-icon-info"/>
                  <span slot="content" style="white-space: normal;">
                    可能错误的原地址:
                    <a
                      :href="row.liveone.playStreamPath"
                      target="_blank"
                    >{{row.liveone.playStreamPath}}</a>
                  </span>
                </el-tooltip>
                <a :href="renderStreamPath(row)" target="_blank">{{renderStreamPath(row)}}</a>
              </div>
            </td>
            <td v-if="!isLive" class="c-switch-width">
              <div v-if="row.liveone">
                <a
                  v-if="row.liveone.msgFilePath"
                  :href="GLOBAL.getPicPath(row.liveone.msgFilePath)"
                  target="_blank"
                >{{GLOBAL.getPicPath(row.liveone.msgFilePath)}}</a>
              </div>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      tableData: [],
      isColNative: true
    };
  },

  methods: {
    livePlay(row) {
      var live = {
        liveId: row.liveId, //liveId
        type: row.liveType, //1视频 2电台
        room: row.liveone.roomId, //roomId
        url: this.renderStreamPath(row), //flvUrl
        name: row.title + "|" + row.userInfo.nickname, //直播间名
        isLive: true //是否直播
      };
      console.log("liveplay@TableLive.vue", live);
      var url = `./static/liveplay.html?${window.btoa(
        encodeURIComponent(JSON.stringify(live))
      )}`;
      window.open(
        url,
        "_blank",
        "height=660, width=375, top=0, left=0, toolbar=no, menubar=no, scrollbars=no, resizable=no,location=no, status=no"
      );
    },

    renderStreamPath(row) {
      if (!row.liveone.playStreamPath) {
        return;
      }
      var dateObj = new Date(row.ctime);
      var actualDate = `${dateObj.getFullYear()}${dateObj.getMonth() +
        1}${dateObj.getDate()}`;
      // 修复一直播链接
      function isYizhiboHost(host) {
        return host.toLowerCase() === "alcdn.hls.xiaoka.tv";
      }
      var streamPath = row.liveone.playStreamPath.replace(
        /^(http|https):\/\/([^\/]+)\/(\d+)/,
        function(pathPrefix, protocol, host, date) {
          // 不是一直播或者日期正确则直接使用给的链接
          if (!isYizhiboHost(host) || actualDate === date) {
            return pathPrefix;
          }

          return `${protocol}://${host}/${actualDate}`;
        }
      );
      return streamPath;
    }
  },
  props: {
    list: Array, //列表
    isCol: Boolean, //是否收起
    isLive: Boolean //是否为直播
    //loading: Boolean //是否加载中
  },
  updated() {
    //this.GLOBAL.mdui.updateTables();
  }
};
</script>