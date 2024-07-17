<template>
 <div class="home">
    <div class="table">
      <div class="row">
        <label>User</label>
        <input v-model="user" placeholder="user">
      </div>
      <div class="row second">
        <label>Message</label>
        <input v-model="message" placeholder="message">
      </div>
      <button class="btn" type="button" v-on:click="signalR()">Submit Chat</button>
      <button class="btn second-btn" type="button" v-on:click="clear()">Clear Chat</button>
    </div>
    <div v-for="(k, index) in messages" :key="index">
      <p>{{ k.user }} : {{ k.text }}</p>
    </div>
  </div>
</template>

<script>
import * as signalR from '@microsoft/signalr';
export default {
  data() {
    return {
      user: "",
      message: "",
      messages: [],
      connection: "",
    };
  },

  async created() {
    console.log("signalR");
    this.connection = await new signalR.HubConnectionBuilder()
      .withUrl("https://localhost:7067/chathub")
      .build();

    this.connection.on("ReceiveMessage", (user, message) => {
      this.messages.push({ user : user, text: message });
    });

    this.connection.start().catch((err) => console.Error(err));
  },

  methods: {
    async signalR() {
      console.log("this.user, this.message : ", this.user + " : " + this.message);
      await this.connection.invoke("SendMessage", this.user, this.message); 

      console.log("messages : ", this.messages);
      this.message = "";
    },
    clear() {
      this.messages = [];
    },
  },

};

</script>