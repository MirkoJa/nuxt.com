<script setup lang="ts">
interface Props {
  feedback: FeedbackItem
}

interface Emits {
  (e: 'delete', id: number): void
}

const props = defineProps<Props>()

const emit = defineEmits<Emits>()

const { getRatingFromFeedback, getScoreColor } = useFeedbackRatings()

const rating = computed(() => getRatingFromFeedback(props.feedback))

const isDeleting = ref(false)

async function handleDelete() {
  if (!confirm('Are you sure you want to delete this feedback?')) {
    return
  }

  isDeleting.value = true
  try {
    emit('delete', props.feedback.id)
  } finally {
    isDeleting.value = false
  }
}
</script>

<template>
  <div class="border border-default rounded-lg p-4">
    <div class="flex items-start justify-between mb-3">
      <div class="flex items-center gap-3">
        <div class="flex flex-col items-center">
          <span class="text-2xl">{{ rating.emoji }}</span>
          <span class="text-xs font-bold" :class="getScoreColor(rating.score)">
            {{ rating.score }}/4
          </span>
        </div>
        <div>
          <div class="flex items-center gap-2 mb-1">
            <span class="text-sm font-medium">{{ rating.label }}</span>
          </div>
          <div class="flex items-center gap-3 text-xs text-muted">
            <span class="flex items-center gap-1">
              <UIcon name="i-lucide-calendar" class="size-3" />
              {{ new Date(feedback.updatedAt).toLocaleDateString('en-US', {
                month: 'short',
                day: 'numeric',
                year: 'numeric',
                hour: '2-digit',
                minute: '2-digit'
              }) }}
            </span>
            <span v-if="feedback.country" class="flex items-center gap-1">
              <UIcon name="i-lucide-map-pin" class="size-3" />
              {{ feedback.country }}
            </span>
          </div>
        </div>
      </div>
      <UButton
        color="error"
        variant="ghost"
        size="sm"
        icon="i-lucide-trash-2"
        :loading="isDeleting"
        :disabled="isDeleting"
        @click="handleDelete"
      />
    </div>

    <div v-if="feedback.feedback" class="text-sm leading-relaxed bg-muted/30 rounded p-3">
      "{{ feedback.feedback }}"
    </div>
    <div v-else class="text-sm text-muted italic">
      No additional comment provided
    </div>
  </div>
</template>
