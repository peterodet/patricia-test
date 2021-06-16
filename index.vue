<script>
//We have an unstable endpoint that returns current rate of bitcoin. However this endpoint is very unstable and fails occassionallly.
// Make a call to the API, take the average of 3 successful responses and return that as a result.
// However, if the entire process is not complete within 10 seconds, show an error message to the user

// Example done on Vue/Nuxt

import axios from 'axios';

export default {

    data() {
      return {
        timeoutCount: 3330,
        successCount: 0,
        requestCount = 0,
        response: []
      }
    },

    mounted() {
      // make initial call
      this.getBitcoinData();

      // set bitcoin request interval
      setInterval(() => {
        this.getBitcoinData();
      }, this.timeoutCount);
    },

    methods: {
      async getBitcoinData() {
        this.requestCount = this.requestCount + 1;

        try {
          const resp = await axios.get('https://example.com/bitcoin/data', {
            timeout: this.timeoutCount - 330 // 330 miliseconds wait before next call
          });

          this.successCount = this.successCount + 1;

          if (this.successCount === 3) {
            this.response = [...this.response, ...resp.data];
          }
        } catch (e) {
          if (this.requestCount === 3) {
            this.requestCount = 0;
            this.successCount = 0;
            alert('Error getting bitcoin data');
          }
        }
      }
    }
}
</script>
