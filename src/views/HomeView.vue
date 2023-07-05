<template>
  <main>
    <h1>Home</h1>
    <div class="inputsContainer">
      <div class="inputGroup">
        <label for="channelNameInput">Channel Name:</label>
        <input
          :disabled="broadcasting"
          id="channelNameInput"
          @keyup.enter="startBroadcasting"
          name="channelName"
          placeholder="Channel Name"
          type="text"
          v-model="channelName"
        >
      </div>
      <div class="inputGroup">
        <label for="messageInput">Message:</label>
        <input
          id="messageInput"
          @keyup.enter="sendMessage"
          name="message"
          placeholder="Message"
          type="text"
          v-model="message"
        >
      </div>
    </div>
    <div class="buttonsContainer">
      <button
        class="successButton"
        @click="startBroadcasting"
        :disabled="broadcasting"
        v-if="!broadcasting"
      >Start Broadcasting</button>
      <button
        class="errorButton"
        @click="stopBroadcasting"
        :disabled="!broadcasting"
        v-else
      >Stop Broadcasting</button>
      <button
        class="infoButton"
        @click="openANewContext"
      >Open a new Context (Window/Tab)</button>
      <button
        class="successButton"
        @click="sendMessage"
        :disabled="!canSendMessage"
      >Send Message</button>
    </div>
  </main>
</template>

<script>
export default {
  name: 'HomeView',
  data: () => ({
    broadcastChannel: null,
    channelName: 'broadcasting-test',
    message: 'Here is a message!',
    windowId: null
  }),
  computed: {
    broadcasting() {
      return !!this.broadcastChannel
    },
    canSendMessage() {
      return !!this.message && this.broadcasting
    }
  },
  methods: {
    openANewContext() {
      window.open('/')
    },
    receivedMessage(e) {
      console.debug('message received', e)
      alert(e?.data)
    },
    sendMessage() {
      if (!this.canSendMessage) return
      else this.broadcastChannel.postMessage(this.message)
    },
    startBroadcasting() {
      // create a new connection to the 'contributorPathwayPayments' channel.
      this.broadcastChannel = new BroadcastChannel(this.channelName)
      // create handler for message event
      this.broadcastChannel.onmessage = (e) => this.receivedMessage(e)

    },
    stopBroadcasting() {
      this.broadcastChannel.close()
      this.broadcastChannel = null
    }
  },
}
</script>

<style>
.buttonsContainer {
  display: flex;
  gap: 1rem;
}

.inputsContainer {
  padding-bottom: 1rem;
}

.inputsContainer,
label {
  margin-right: 1rem;
}

.inputGroup {
  margin-top: 1rem;
  margin-bottom: 1rem;
}
</style>