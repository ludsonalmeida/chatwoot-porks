<script>
import ConversationCard from './widgets/conversation/ConversationCard.vue';
export default {
  components: {
    ConversationCard,
  },
  inject: [
    'selectConversation',
    'deSelectConversation',
    'assignAgent',
    'assignTeam',
    'assignLabels',
    'removeLabels',
    'updateConversationStatus',
    'toggleContextMenu',
    'markAsUnread',
    'markAsRead',
    'assignPriority',
    'isConversationSelected',
    'deleteConversation',
  ],
  props: {
    source: {
      type: Object,
      required: true,
    },
    teamId: {
      type: [String, Number],
      default: 0,
    },
    label: {
      type: String,
      default: '',
    },
    conversationType: {
      type: String,
      default: '',
    },
    foldersId: {
      type: [String, Number],
      default: 0,
    },
    showAssignee: {
      type: Boolean,
      default: false,
    },
  },
};
</script>

<template>
  <div
    class="conversation-item-wrapper relative rounded-xl transition-all duration-150 ease-out px-1 py-0.5 hover:bg-n-blue-2/60 border-l-4 border-transparent"
    :class="{
      'bg-n-blue-2/80 border-l-n-brand shadow-soft': isConversationSelected(source.id),
    }"
  >
  <ConversationCard
    :active-label="label"
    :team-id="teamId"
    :folders-id="foldersId"
    :chat="source"
    :conversation-type="conversationType"
    :selected="isConversationSelected(source.id)"
    :show-assignee="showAssignee"
    enable-context-menu
    @select-conversation="selectConversation"
    @de-select-conversation="deSelectConversation"
    @assign-agent="assignAgent"
    @assign-team="assignTeam"
    @assign-label="assignLabels"
    @remove-label="removeLabels"
    @update-conversation-status="updateConversationStatus"
    @context-menu-toggle="toggleContextMenu"
    @mark-as-unread="markAsUnread"
    @mark-as-read="markAsRead"
    @assign-priority="assignPriority"
    @delete-conversation="deleteConversation"
  />
  </div>
</template>
