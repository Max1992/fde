<template>
  <div class="participants">
    <MarkdownContent :content="participantContent"/>
  </div>
</template>

<script lang="ts">
import { Component, Vue, Watch, Inject } from 'vue-property-decorator';
import MarkdownContent from '@/components/MarkdownContent.vue';
import { EventPayloadBuilder, EventPayload } from '@/services/EventPayloadBuilder';

@Component({
  components: {
    MarkdownContent
  }
})
export default class Participants extends Vue {

  public participantContent: string = "";

  @Inject(EventPayloadBuilder.Name)
  private builder: EventPayloadBuilder;

  @Watch('$route.params.locale', { immediate: true, deep: true })
  onUrlChange() {
    this.$store.dispatch('GET_ACTIVE_EVENT');
  }

  created() {
    this.$store.subscribe((mutation, state) => {
      if (mutation.type === 'SET_ACTIVE_EVENT_OK') {

        const payload: EventPayload = this.builder.makePayload(this.$route.params.locale, state.activeEvent);

        this.$store.dispatch('GET_PARTICIPANT_EVENT_CONTENT', payload);
      }

      if (mutation.type === 'SET_PARTICIPANT_EVENT_CONTENT_OK') {
        this.participantContent = state.participantContent;
      }
    });
  }
}
</script>
