<template>
  <div class="container">
    <b-field label="Question Search" label-position="on-border">
      <b-input
        ref="search"
        v-model="query"
        placeholder="file system"
        type="search"
        icon-pack="fas"
        icon="search"
      />
    </b-field>
    <div class="columns is-multiline">
      <div
        v-for="question in sortedQuestions"
        v-show="filteredQuestions.includes(question)"
        :key="question.id"
        class="column is-variable is-12-mobile is-6-tablet is-4-desktop is-3-widescreen"
      >
        <QuestionCard
          :title="question.title"
          :asker="question.asker"
          :tweet-url="question.tweetUrl"
          :timestamp="question.timestamp"
          :overcast-slug="question.episode.overcastSlug"
          :episode-title="question.episode.title"
          :episode-number="question.episode.number"
          :release-date="question.episode.releaseDate.split('-').join('/')"
        />
      </div>
    </div>
    <p v-if="!filteredQuestions.length">
      No questions found. Try another query.
    </p>
  </div>
</template>

<script>
export default {
  props: {
    questions: {
      type: Array,
      required: true,
    },
  },
  data() {
    return {
      query: '',
    }
  },
  computed: {
    sortedQuestions() {
      const clonedQuestions = this.questions

      function toSeconds(hrs, mins, seconds) {
        return hrs * 60 * 60 + mins * 60 + seconds
      }

      function comapareEpisodeNumbersAndTimestamps(a, b) {
        return (
          b.episode.number - a.episode.number ||
          toSeconds(...a.timestamp.split(':')) -
            toSeconds(...b.timestamp.split(':'))
        )
      }

      return clonedQuestions.sort(comapareEpisodeNumbersAndTimestamps)
    },
    filteredQuestions() {
      return this.sortedQuestions.filter((question) =>
        question.title.toLowerCase().includes(this.query.toLowerCase().trim())
      )
    },
  },
  mounted() {
    this.$refs.search.focus()
  },
}
</script>
