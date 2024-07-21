<template>
  <div
    id="modal"
    class="modal fade"
    tabindex="-1"
    role="dialog"
    aria-labelledby="modal"
    aria-hidden="true"
  >
    <div class="modal-dialog modal-lg" role="document">
      <div class="modal-content">
        <section class="modal-header">
          <div class="modal-titles">
            <h5 id="exampleModalLabel" class="modal-title">Edit flights</h5>
            <p class="modal-description">
              Add or remove flights from the subscriber
            </p>
          </div>
          <button
            ref="close"
            type="button"
            class="modal-close"
            data-dismiss="modal"
            aria-label="Close"
            @click="closeModal()"
          >
            <i class="fa-regular fa-circle-xmark" />
          </button>
        </section>

        <section class="modal-body">
          <section class="controls control-quota">
            <p class="label">Flights left</p>
            <div class="flight-control">
              <button
                type="button"
                class="btn btn-control"
                @click="decreaseFlights()"
              >
                <i class="fa-solid fa-minus" />
              </button>
              <span class="flights-number">{{ flightsLeft }}</span>
              <button
                type="button"
                class="btn btn-control"
                @click="increaseFlights()"
              >
                <i class="fa-solid fa-plus" />
              </button>
            </div>
            <p v-if="maxReached" class="max-warning">
              Maximum of 3 flights reached.
            </p>
          </section>
          <section class="controls">
            <select
              v-model="selectedMotive"
              class="form-select motives"
              aria-label="Default select example"
              :disabled="motives.length === 0"
            >
              <option value="null">What is the motive?</option>
              <option v-for="motive in motives" :key="motive" :value="motive">
                {{ motive }}
              </option>
            </select>
          </section>
        </section>

        <section class="modal-footer">
          <button
            type="button"
            class="btn"
            :disabled="selectedMotive === null"
            @click="submitFlightChanges()"
          >
            Save changes
          </button>
        </section>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'TheModal',
  props: {
    initialFlightsLeft: { type: Number, required: false, default: 0 },
  },
  data() {
    return {
      flightsLeft: this.initialFlightsLeft,
      motives: [],
      incrementReasons: [
        'Subscriber canceled flight',
        'Airline canceled flight',
        'Customer compensation',
        'Other',
      ],
      decrementReasons: [
        'Flight not redeposited after a flight cancellation',
        'Subscriber had log in or password issues',
        'Subscriber had issues when booking',
        'Subscription has not renewed correctly',
        'Other',
      ],
      selectedMotive: null,
    };
  },
  computed: {
    maxReached() {
      return this.flightsLeft === 3 ? true : false;
    },
  },
  watch: {
    flightsLeft: {
      handler: function (newVal, oldVal) {
        if (this.initialFlightsLeft === newVal) {
          this.motives = [];
        } else if (newVal > oldVal) {
          this.motives = this.incrementReasons;
        } else if (newVal < oldVal) {
          this.motives = this.decrementReasons;
        } else {
          this.motives = [];
        }
      },
    },
  },
  methods: {
    closeModal() {
      this.flightsLeft = this.initialFlightsLeft;
    },
    decreaseFlights() {
      if (this.flightsLeft > 0) {
        this.flightsLeft--;
        this.selectedMotive = null;
      }
    },
    increaseFlights() {
      if (this.flightsLeft < 3) {
        this.flightsLeft++;
        this.selectedMotive = null;
      }
    },
    submitFlightChanges() {
      let variation =
        this.flightsLeft > this.initialFlightsLeft ? 'increment' : 'decrement';
      let changes = {
        flightsLeft: this.flightsLeft,
        variation: variation,
        motive: this.selectedMotive,
      };

      fetch('https://httpstat.us/random/200,500')
        .then((response) => {
          if (response.ok) {
            this.$emit('changes', changes);
            this.$refs.close.click();
          } else {
            this.closeModal();
            throw new Error('Changes not applied. Please try again.');
          }
        })
        .catch((error) => {
          this.closeModal();
          this.$emit('error', error);
          this.$refs.close.click();
        });
    },
  },
};
</script>

<style scoped>
/* Header */
.modal-header {
  border-bottom: none;
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  gap: 12px;
  align-items: start;
}
.modal-description {
  margin: 0;
}
.modal-close {
  border: none;
  border-radius: 4px;
  display: flex;
  align-items: center;
  background-color: transparent;
}
.modal-close .fa-circle-xmark {
  font-size: 20px;
  border-radius: 50%;
}

/* Body */
.modal-body {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 24px;
}
.control-quota {
  display: flex;
  flex-direction: column;
  gap: 12px;
  background: #8ed1fc;
  padding: 12px 32px;
  text-align: center;
  border-radius: 4px;
  height: 130px;
}
.label {
  margin: 0;
}
.flight-control {
  display: flex;
  justify-content: space-around;
  align-items: center;
}
.btn-control {
  border-radius: 50px;
  width: 40px;
  height: 40px;
  background: #fff;
}
.btn-control:hover,
.btn-control:active {
  background: #eee;
  border: none;
}
.max-warning {
  font-size: 12px;
  margin: 0;
}

/* Footer */
.modal-footer {
  border-top: none;
}
.modal-footer .btn {
  margin: auto;
  background-color: #009dff;
  border: #009dff;
  color: #fff;
}
.modal-footer .btn:hover {
  background-color: #004ca5;
  border: #004ca5;
}
.modal-footer .btn:disabled {
  background: #e9ecef;
}
</style>
