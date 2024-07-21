<template>
  <div>
    <div class="main">
      <button
        class="btn open-modal"
        type="button"
        data-toggle="modal"
        data-target="#modal"
      >
        <i class="fa-solid fa-pencil" />
        <span>Edit flights</span>
      </button>

      <p v-if="message != null">
        {{ message }}
      </p>
    </div>

    <the-modal
      :initial-flights-left="2"
      @changes="flightChanges"
      @error="displayError"
    />
  </div>
</template>

<script>
import TheModal from './components/TheModal.vue';

export default {
  components: {
    TheModal,
  },
  data() {
    return {
      initialFlights: 2,
      message: null,
    };
  },
  methods: {
    flightChanges(changes) {
      this.message =
        'Applied changes: ' +
        changes.variation +
        ' of ' +
        changes.flightsLeft +
        ' flights left with motive ' +
        changes.motive +
        '.';
      this.initialFlights = changes.flightsLeft;
    },
    displayError(error) {
      this.message = error;
    },
  },
};
</script>

<style scoped>
.main {
  margin: 40px;
}

.open-modal {
  text-transform: uppercase;
  display: flex;
  flex-direction: row;
  align-items: center;
  margin: auto;
  gap: 12px;
}

.open-modal:hover {
  background: #ccc;
  border-radius: 4px;
}
</style>
