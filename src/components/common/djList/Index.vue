<template>
  <div class="dj-list">
    <div class="head">
      <div class="item item-play">
        <i class="iconfont niceOutlined_Play"></i> 播放全部
      </div>

      <div class="item">
        <i class="iconfont niceicon-heart">订阅</i>
      </div>

      <div v-for="(item, index) in songList" :key="index">
        <span>
          {{ item }}
        </span>
      </div>
    </div>

    <table class="body">
      <thead>
        <tr>
          <th class="th-index">序号</th>
          <th class="th-name">节目</th>
          <th class="th-count">听众</th>
          <th class="th-create">创建时间</th>
          <th class="th-long">时长</th>
        </tr>
      </thead>

      <tbody>
        <tr v-for="(item, index) in djProgramList" :key="index">
          <td>
            <div class="index">
              <span class="num">{{ utils.formatZero(index + 1, 2) }}</span>
              <i
                class="iconfont nicebofang2 play-btn"
                @click="playSong(item, index)"
              ></i>
              <i class="iconfont nicezanting1 pause-btn"></i>
            </div>
          </td>
          <td>
            <div class="name">
              <div class="avatar">
                <img v-lazy="item.coverUrl" alt="" />
              </div>
              <span>{{ item.name }}</span>
            </div>
          </td>
          <td>
            <div class="count">
              <span>{{ utils.tranNumber(item.listenerCount, 0) }}</span>
            </div>
          </td>
          <td>
            <div class="create">
              <span>{{ utils.dateFormat(item.createTime, 'YYYY-MM-DD') }}</span>
            </div>
          </td>
          <td>
            <div class="long">
              <span>{{
                utils.formatSecondTime(item.mainSong.duration / 1000)
              }}</span>
            </div>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import { mapGetters, mapActions } from 'vuex'

export default {
  data() {
    return {
      songList: []
    }
  },
  props: {
    djProgramList: {
      type: Array
    }
  },
  created() {
    this.getSongList(this.djProgramList)
  },
  computed: {
    ...mapGetters(['currentIndex', 'playing', 'currentSong'])
  },
  methods: {
    //根据获得播放列表的电台id，进一步获得MP3播放地址
    async getSong(id) {
      try {
        let res = await this.$api.getSongUrl(id)
        if (res.code === 200) {
          return res.data.url
        }
      } catch (error) {
        console.log(error)
      }
    },
    //获取电台节目列表
    getSongList(arr) {
      // for (let i = 0; i < arr.length; i++) {
      //   let songUrl = this.getSong(arr[i].mainSong.id)
      //   this.songList.push(songUrl)
      // }
      let newArr = []
      arr.forEach(function(item) {
        let songUrl = this.getSong(item.mainSong.id)
        newArr.push(songUrl.url)
      })

      this.songList = newArr
    }
  }
}
</script>

<style lang="stylus" scoped>
.dj-list {
  .head {
    display: flex;
    justify-content: flex-end;
    margin: 20px 0;

    .item {
      margin: 0 10px;
      padding: 7px 15px;
      background: #f2f2f2;
      border-radius: 50px;
      justify-content: center;
      align-items: center;
      font-size: 15px;
      cursor: pointer;
    }

    .item-play {
      background: #fa2800;
      color: #fff;
    }
  }

  .body {
    width: 100%;

    thead {
      width: 100%;
      height: 50px;
      line-height: 50px;
      background: #fafafa;
      color: #999;

      th {
        padding: 0 9px;
        text-align: left;
        font-weight: 300;
        white-space: nowrap;
        overflow: hidden;

        &.th-index {
          width: 10%;
          text-align: center;
        }

        &.th-name {
          width: 50%;
        }

        &.th-count {
          width: 15%;
        }

        &.th-create {
          width: 15%;
        }

        &.th-long {
          width: 15%;
          text-align: right;
          padding-right: 55px;
        }
      }
    }

    tbody {
      width: 100%;

      tr {
        height: 50px;
        line-height: 50px;
        transition: background-color 0.2s linear;

        &:nth-child(2n) {
          background: #f7f7f7;
        }

        &:hover {
          background: #e8e9ed;

          .index {
            .num {
              display: none;
            }

            .play-btn {
              display: block;
            }
          }
        }

        td {
          padding: 0 9px;
          white-space: nowrap;
          overflow: hidden;
          text-overflow: ellipsis;

          .index {
            text-align: center;

            .num {
              color: #4a4a4a;
              font-weight: 500;
            }

            .play-btn {
              color: #fa2800;
              font-size: 28px;
              display: none;
              cursor: pointer;
            }

            .pause-btn {
              color: #fa2800;
              font-size: 30px;
              display: none;
              cursor: pointer;
            }
          }

          .name {
            display: flex;
            align-items: center;

            .avatar {
              width: 30px;
              height: 30px;
              border-radius: 50%;
              margin-right: 15px;
              cursor: pointer;

              img {
                width: 100%;
                height: 100%;
                border-radius: 50%;
              }
            }
          }

          .long {
            text-align: center;
          }
        }
      }
    }
  }
}
</style>