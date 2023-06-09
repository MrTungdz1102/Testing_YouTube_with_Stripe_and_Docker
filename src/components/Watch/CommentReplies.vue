<script setup>
import {
  NCollapse,
  NCollapseItem,
  NSpace,
  NAvatar,
  NText,
  NIcon,
  NTag,
  NEllipsis,
  NButton,
  NSpin,
} from 'naive-ui';
import { ThumbsUp, FavoriteFilled } from '@vicons/carbon';
import { ref } from 'vue';
import axios from 'axios';
import { renderHTML } from '../../utils/render-html';
import { formatViews } from '../../utils/format-view-count';
import { useRouter, useRoute } from 'vue-router';
import { getNextData } from '../../utils/get-next-data';

const { replyCount, repliesPage, uploaderUrl, uploaderAvatar } = defineProps([
  'replyCount',
  'repliesPage',
  'uploaderUrl',
  'uploaderAvatar',
]);
const replies = ref([]);
const nextReplies = ref(repliesPage);
const isLoading = ref(false);
const router = useRouter();
const videoId = useRoute().query.v;

const handleGetReplies = async () => {
  isLoading.value = true;
  try {
    const res = await getNextData({
      id: videoId,
      type: 'comments',
      nextpage: nextReplies.value,
    });
    replies.value = [...replies.value, ...res.comments];
    nextReplies.value = res.nextpage;
  } catch (err) {
    console.error(err);
  } finally {
    isLoading.value = false;
  }
};
</script>

<template>
  <n-collapse
    arrow-placement="right"
    :style="{ marginTop: '6px' }"
    @item-header-click="handleGetReplies"
  >
    <n-collapse-item
      :title="replyCount === 1 ? '1 Reply' : `${replyCount} Replies`"
    >
      <n-space
        v-for="reply in replies"
        :key="reply.commentId"
        align="start"
        :wrap="false"
        :style="{ marginBottom: '20px' }"
      >
        <n-avatar
          :src="reply.thumbnail"
          circle
          :size="28"
          :style="{ cursor: 'pointer' }"
          @click="router.push(reply.commentorUrl)"
        />
        <n-text tag="div" :style="{ display: 'flex', flexDirection: 'column' }">
          <n-space :size="4">
            <template v-if="reply.commentorUrl === uploaderUrl">
              <n-tag
                :bordered="false"
                round
                size="small"
                :style="{
                  fontWeight: 500,
                  fontSize: '14px',
                  cursor: 'pointer',
                }"
                @click="router.push(reply.commentorUrl)"
              >
                {{ reply.author }}
              </n-tag>
            </template>
            <template v-else>
              <n-text
                strong
                :style="{ cursor: 'pointer' }"
                @click="router.push(reply.commentorUrl)"
              >
                {{ reply.author }}
              </n-text>
            </template>
            •
            <n-text depth="3" :style="{ fontSize: '13px' }">{{
              reply.commentedTime
            }}</n-text>
          </n-space>
          <n-ellipsis expand-trigger="click" line-clamp="3" :tooltip="false">
            <span v-html="renderHTML(reply.commentText)" />
          </n-ellipsis>
          <n-space align="center" :size="14" :style="{ marginTop: '6px' }">
            <n-space align="start" :size="6">
              <n-icon :component="ThumbsUp" :size="17" />
              <template v-if="reply.likeCount !== 0">
                <n-text :style="{ fontSize: '12px' }">{{
                  formatViews(reply.likeCount)
                }}</n-text>
              </template>
            </n-space>
            <n-icon
              :component="ThumbsUp"
              :size="17"
              :style="{ transform: 'rotate(180deg)' }"
            />
            <template v-if="reply.hearted">
              <n-text tag="div" :style="{ position: 'relative' }">
                <n-avatar :src="uploaderAvatar" :size="16" circle />
                <n-icon
                  :component="FavoriteFilled"
                  :size="14"
                  color="#f00"
                  :depth="1"
                  :style="{
                    position: 'absolute',
                    bottom: 0,
                    right: '-6px',
                  }"
                />
              </n-text>
            </template>
          </n-space>
        </n-text>
      </n-space>
      <template v-if="isLoading">
        <n-spin size="small" />
      </template>
      <template v-else>
        <template v-if="nextReplies">
          <n-button
            secondary
            size="small"
            round
            type="primary"
            @click="handleGetReplies"
            >Load more</n-button
          >
        </template>
      </template>
    </n-collapse-item>
  </n-collapse>
</template>
