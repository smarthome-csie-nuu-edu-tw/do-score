<template>
  <div class="app--group-main--target-finished-list">
    <div class="manage-content">
      <div class="title">
        <cy-icon-text iconify-name="heroicons-solid:menu-alt-2">
          未審核清單
        </cy-icon-text>
      </div>
      <div class="content" v-if="pendingList.length > 0">
        <div class="item" v-for="(item, i) in pendingList" :key="item.id">
          <span class="no">#{{ i }}</span>
          <span class="member">
            <cy-icon-text iconify-name="ant-design:user-outlined" class="text-large nickname">
              {{ item.memberNickname }}
            </cy-icon-text>
            <span class="username">{{ item.memberUsername }}</span>
          </span>
          <span class="target-name">
            <cy-icon-text iconify-name="ic-outline-menu-book" class="text-large name">
              {{ item.targetName }}
            </cy-icon-text>
          </span>
          <span class="target-score">{{ item.targetScore }}pt</span>
          <div v-if="item.auditable || item.cancelable">
            <cy-button v-if="item.auditable" iconify-name="ic-round-done"
              style="margin-right: 0.8rem;"
              @click="finishTarget(item.id)" class="inline">
              確認
            </cy-button>
            <cy-button v-if="item.cancelable" iconify-name="ic-round-close"
              style="color: var(--primary-light-2);"
              @click="unsubmitTarget(item.id)" class="inline">
              取消
            </cy-button>
          </div>
        </div>
      </div>
      <cy-default-tips v-else iconify-name="mdi-flower-tulip">
        都已經審核完畢了喔～
      </cy-default-tips>
    </div>
    <div class="manage-content">
      <div class="title">
        <cy-icon-text iconify-name="heroicons-solid:menu-alt-2">
          已完成紀錄
        </cy-icon-text>
      </div>
      <div class="content" v-if="finishedList.length > 0">
        <div class="item" v-for="(item, i) in finishedList" :key="item.id">
          <span class="no">#{{ i }}</span>
          <span class="member">
            <cy-icon-text iconify-name="ant-design:user-outlined" class="text-large nickname">
              {{ item.memberNickname }}
            </cy-icon-text>
            <span class="username">{{ item.memberUsername }}</span>
          </span>
          <span class="target-name">
            <cy-icon-text iconify-name="ic-outline-menu-book" class="text-large name">
              {{ item.targetName }}
            </cy-icon-text>
          </span>
          <span class="target-score">{{ item.targetScore }}pt</span>
        </div>
      </div>
      <cy-default-tips v-else iconify-name="bx-bx-wind">
        這裡目前空空如也。
      </cy-default-tips>
    </div>
  </div>
</template>
<script>
  import store from "@store/user";

  import Vuex from 'vuex';

  import ShowMessage from "@lib/modules/ShowMessage.js";
  import { adminPermission } from "@lib/project.js";

  export default {
    store,
    data() {
      return {
        createTargetVisible: false
      };
    },
    computed: {
      pendingList() {
        return this.groupAttrs.targetFinishedList
          .filter(p => !p.passed)
          .map(p => {
            const memberUser = this.$store.getters['group/memberUser'];
            const per = memberUser ? memberUser.permission : 100;

            return {
              id: p._id,
              targetName: p.target.name,
              targetScore: p.target.score,
              memberNickname: p.member.nickname,
              memberUsername: p.member.username,
              auditable: this.userId != p.member._id && per <= adminPermission,
              cancelable: this.userId == p.member._id || per <= adminPermission
            };
          });
      },
      finishedList() {
        return this.groupAttrs.targetFinishedList
          .filter(p => p.passed)
          .map(p => {
            return {
              id: p._id,
              targetName: p.target.name,
              targetScore: p.target.score,
              memberNickname: p.member.nickname,
              memberUsername: p.member.username
            };
          });
      },
      ...Vuex.mapState('group', {
        groupId: '_id',
        groupAttrs: 'attrs'
      }),
      ...Vuex.mapState('user', {
        userId: '_id'
      })
    },
    methods: {
      finishTarget(finishId) {
        this.$store.dispatch('group/finishTarget', { finishId })
          .then(() => ShowMessage('確認成功。'));
      },
      unsubmitTarget(unsubmitId) {
        this.$store.dispatch('group/unsubmitTarget', { unsubmitId })
          .then(() => ShowMessage('取消成功。'));
      }
    }
  };
</script>
<style lang="less" scoped>
.manage-content {
  .item {
    grid-template-columns: 4rem 18rem 20rem 6rem 9rem;

    > .target-score {
      font-size: 1.2rem;
      color: var(--primary-orange);
    }
  }
}
</style>