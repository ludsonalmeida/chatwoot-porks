<script setup>
import { computed, ref } from 'vue';
import { getInboxIconByType } from 'dashboard/helper/inbox';
import { useRouter, useRoute } from 'vue-router';
import { frontendURL, conversationUrl } from 'dashboard/helper/URLHelper.js';
import { dynamicTime, shortTimestamp } from 'shared/helpers/timeHelper';

import Icon from 'dashboard/components-next/icon/Icon.vue';
import Avatar from 'dashboard/components-next/avatar/Avatar.vue';
import CardMessagePreview from './CardMessagePreview.vue';
import CardMessagePreviewWithMeta from './CardMessagePreviewWithMeta.vue';
import CardPriorityIcon from './CardPriorityIcon.vue';

const props = defineProps({
  conversation: {
    type: Object,
    required: true,
  },
  contact: {
    type: Object,
    required: true,
  },
  stateInbox: {
    type: Object,
    required: true,
  },
  accountLabels: {
    type: Array,
    required: true,
  },
});

const router = useRouter();
const route = useRoute();

const cardMessagePreviewWithMetaRef = ref(null);

const currentContact = computed(() => props.contact);

const currentContactName = computed(() => currentContact.value?.name);
const currentContactThumbnail = computed(() => currentContact.value?.thumbnail);
const currentContactStatus = computed(
  () => currentContact.value?.availabilityStatus
);

const inbox = computed(() => props.stateInbox);

const inboxName = computed(() => inbox.value?.name);

const inboxIcon = computed(() => {
  const { channelType, medium } = inbox.value;
  return getInboxIconByType(channelType, medium);
});

const lastActivityAt = computed(() => {
  const timestamp = props.conversation?.timestamp;
  return timestamp ? shortTimestamp(dynamicTime(timestamp)) : '';
});

// Extrai utm_source/origem do contato, conversa, ou fallback para o canal da inbox
const origemSource = computed(() => {
  const src =
    props.conversation?.custom_attributes?.utm_source ||
    props.conversation?.additional_attributes?.utm_source ||
    props.conversation?.custom_attributes?.origem ||
    props.contact?.custom_attributes?.utm_source ||
    props.contact?.additional_attributes?.utm_source ||
    props.contact?.custom_attributes?.origem ||
    props.contact?.additional_attributes?.origem ||
    null;
  if (src) return String(src).toLowerCase().trim();

  // Fallback: deduzir do canal da inbox
  const channelType = props.stateInbox?.channelType || '';
  if (channelType.includes('Whatsapp') || channelType.includes('whatsapp')) return 'whatsapp';
  if (channelType.includes('FacebookPage') || channelType.includes('facebook')) return 'facebook';
  if (channelType.includes('Instagram') || channelType.includes('instagram')) return 'instagram';
  if (channelType.includes('Email') || channelType.includes('email')) return 'email';
  if (channelType.includes('WebWidget') || channelType.includes('web_widget')) return 'site';
  if (channelType.includes('Telegram') || channelType.includes('telegram')) return 'telegram';
  if (channelType.includes('Sms') || channelType.includes('sms')) return 'sms';
  return null;
});

const origemConfig = computed(() => {
  const src = origemSource.value;
  if (!src) return null;
  const map = {
    instagram: { label: 'Instagram', icon: '📸', bg: 'bg-pink-50', text: 'text-pink-600', border: 'border-pink-200' },
    facebook: { label: 'Facebook', icon: '👥', bg: 'bg-blue-50', text: 'text-blue-700', border: 'border-blue-200' },
    google: { label: 'Google', icon: '🔍', bg: 'bg-amber-50', text: 'text-amber-700', border: 'border-amber-200' },
    'google-ads': { label: 'Google Ads', icon: '🎯', bg: 'bg-amber-50', text: 'text-amber-700', border: 'border-amber-200' },
    'google-maps': { label: 'Maps', icon: '📍', bg: 'bg-emerald-50', text: 'text-emerald-700', border: 'border-emerald-200' },
    meta: { label: 'Meta Ads', icon: '📊', bg: 'bg-blue-50', text: 'text-blue-700', border: 'border-blue-200' },
    'meta-ads': { label: 'Meta Ads', icon: '📊', bg: 'bg-blue-50', text: 'text-blue-700', border: 'border-blue-200' },
    ifood: { label: 'iFood', icon: '🍔', bg: 'bg-red-50', text: 'text-red-600', border: 'border-red-200' },
    site: { label: 'Site', icon: '🌐', bg: 'bg-indigo-50', text: 'text-indigo-700', border: 'border-indigo-200' },
    whatsapp: { label: 'WhatsApp', icon: '💬', bg: 'bg-green-50', text: 'text-green-700', border: 'border-green-200' },
    cardapio: { label: 'Cardápio', icon: '📋', bg: 'bg-orange-50', text: 'text-orange-700', border: 'border-orange-200' },
    'cardapio-digital': { label: 'Cardápio', icon: '📋', bg: 'bg-orange-50', text: 'text-orange-700', border: 'border-orange-200' },
    organico: { label: 'Orgânico', icon: '🌱', bg: 'bg-teal-50', text: 'text-teal-700', border: 'border-teal-200' },
    outro: { label: 'Outro', icon: '📍', bg: 'bg-slate-50', text: 'text-slate-600', border: 'border-slate-200' },
    email: { label: 'Email', icon: '📧', bg: 'bg-sky-50', text: 'text-sky-700', border: 'border-sky-200' },
    telegram: { label: 'Telegram', icon: '✈️', bg: 'bg-cyan-50', text: 'text-cyan-700', border: 'border-cyan-200' },
    sms: { label: 'SMS', icon: '📱', bg: 'bg-purple-50', text: 'text-purple-700', border: 'border-purple-200' },
  };
  return map[src] || { label: src.charAt(0).toUpperCase() + src.slice(1), icon: '📍', bg: 'bg-slate-50', text: 'text-slate-600', border: 'border-slate-200' };
});

const showMessagePreviewWithoutMeta = computed(() => {
  const { labels = [] } = props.conversation;
  return (
    !cardMessagePreviewWithMetaRef.value?.hasSlaThreshold && labels.length === 0
  );
});

const onCardClick = e => {
  const path = frontendURL(
    conversationUrl({
      accountId: route.params.accountId,
      id: props.conversation.id,
    })
  );

  if (e.metaKey || e.ctrlKey) {
    window.open(
      window.chatwootConfig.hostURL + path,
      '_blank',
      'noopener noreferrer nofollow'
    );
    return;
  }
  router.push({ path });
};
</script>

<template>
  <div
    role="button"
    class="flex w-full gap-3 px-3 py-4 transition-all duration-300 ease-in-out cursor-pointer"
    @click="onCardClick"
  >
    <Avatar
      :name="currentContactName"
      :src="currentContactThumbnail"
      :size="24"
      :status="currentContactStatus"
      rounded-full
    />
    <div class="flex flex-col w-full gap-1 min-w-0">
      <div class="flex items-center justify-between h-6 gap-2">
        <div class="flex items-center gap-2 min-w-0 flex-1">
          <h4 class="text-base font-medium truncate text-n-slate-12">
            {{ currentContactName }}
          </h4>
          <span
            v-if="origemConfig"
            :class="[
              'flex items-center gap-1 px-2 py-0.5 rounded-full text-[10px] font-semibold border flex-shrink-0',
              origemConfig.bg,
              origemConfig.text,
              origemConfig.border,
            ]"
            :title="'Origem: ' + origemConfig.label"
          >
            <span>{{ origemConfig.icon }}</span>
            <span>{{ origemConfig.label }}</span>
          </span>
        </div>
        <div class="flex items-center gap-2">
          <CardPriorityIcon :priority="conversation.priority || null" />
          <div
            v-tooltip.left="inboxName"
            class="flex items-center justify-center flex-shrink-0 rounded-full bg-n-alpha-2 size-5"
          >
            <Icon
              :icon="inboxIcon"
              class="flex-shrink-0 text-n-slate-11 size-3"
            />
          </div>
          <span class="text-sm text-n-slate-10">
            {{ lastActivityAt }}
          </span>
        </div>
      </div>
      <CardMessagePreview
        v-show="showMessagePreviewWithoutMeta"
        :conversation="conversation"
      />
      <CardMessagePreviewWithMeta
        v-show="!showMessagePreviewWithoutMeta"
        ref="cardMessagePreviewWithMetaRef"
        :conversation="conversation"
        :account-labels="accountLabels"
      />
    </div>
  </div>
</template>
