<template>
    <div v-if="visible" class="flex items-center w-full max-w-xs p-4 mb-4 text-gray-500 bg-white rounded-lg shadow dark:text-gray-400 dark:bg-gray-800" role="alert">
      <div
        class="inline-flex items-center justify-center flex-shrink-0 w-8 h-8 rounded-lg"
        :class="iconClass"
      >
        <svg class="w-5 h-5" aria-hidden="true" xmlns="http://www.w3.org/2000/svg" fill="currentColor" viewBox="0 0 20 20">
          <path :d="iconPath" />
        </svg>
        <span class="sr-only">Check icon</span>
      </div>
      <div class="ms-3 text-sm font-normal">{{ message }}</div>
      <button
        type="button"
        class="ms-auto -mx-1.5 -my-1.5 bg-white text-gray-400 hover:text-gray-900 rounded-lg focus:ring-2 focus:ring-gray-300 p-1.5 hover:bg-gray-100 inline-flex items-center justify-center h-8 w-8 dark:text-gray-500 dark:hover:text-white dark:bg-gray-800 dark:hover:bg-gray-700"
        @click="closeToast"
      >
        <span class="sr-only">Close</span>
        <svg class="w-3 h-3" aria-hidden="true" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 14 14">
          <path stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="m1 1 6 6m0 0 6 6M7 7l6-6M7 7l-6 6"/>
        </svg>
      </button>
    </div>
  </template>
  
  <script>
  export default {
    name: "Toast",
    props: {
      message: {
        type: String,
        required: true
      },
      type: {
        type: String,
        default: "success" // Default type is 'success'
      },
      duration: {
        type: Number,
        default: 3000 // Toast will auto-dismiss after 3 seconds
      }
    },
    data() {
      return {
        visible: true
      };
    },
    computed: {
      iconClass() {
        switch (this.type) {
          case "error":
            return "text-red-500 bg-red-100";
          case "warning":
            return "text-yellow-500 bg-yellow-100";
          case "info":
            return "text-blue-500 bg-blue-100";
          default:
            return "text-green-500 bg-green-100";
        }
      },
      iconPath() {
        switch (this.type) {
          case "error":
            return "M10 2a8 8 0 1 0 8 8A8 8 0 0 0 10 2Zm3.707 9.707-4 4a1 1 0 0 1-1.414 0l-2-2a1 1 0 0 1 1.414-1.414L9 11.586l3.293-3.293a1 1 0 0 1 1.414 1.414Z";
          case "warning":
            return "M10 2a8 8 0 1 0 8 8A8 8 0 0 0 10 2Zm0 3v5h2V5h-2Zm0 6v2h2v-2h-2Z";
          case "info":
            return "M10 2a8 8 0 1 0 8 8A8 8 0 0 0 10 2Zm0 3v5h2V5h-2Zm0 6v2h2v-2h-2Z";
          default:
            return "M10 .5a9.5 9.5 0 1 0 9.5 9.5A9.51 9.51 0 0 0 10 .5Zm3.707 8.207-4 4a1 1 0 0 1-1.414 0l-2-2a1 1 0 0 1 1.414-1.414L9 10.586l3.293-3.293a1 1 0 0 1 1.414 1.414Z";
        }
      }
    },
    mounted() {
      if (this.duration > 0) {
        setTimeout(() => {
          this.closeToast();
        }, this.duration);
      }
    },
    methods: {
      closeToast() {
        this.visible = false;
        this.$emit("closed"); // Emit event when toast is closed
      }
    }
  };
  </script>
  
  <style scoped>
  /* Scoped styles for the Toast component if necessary */
  </style>
  