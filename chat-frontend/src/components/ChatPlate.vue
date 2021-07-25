<template>
  <div>
    <nav class="navbar navbar-dark bg-dark">
      <a class="navbar-brand" href="#">
        DJ_CHAT
      </a>
      <p>
        {{ username }}
      </p>
      <form class="form-inline my-2 my-lg-0" @submit.prevent="logout">
        <button class="btn btn-outline-light my-2 my-sm-0" type="submit">
          Logout
        </button>
      </form>
    </nav>
    <br />

    <div class="container">
      <div class="row">
        <div class="col-sm-6 offset-3">
          <p class="text-center">
            To start chatting with friends share your Room Code
            <strong>{{ roomName }}</strong
            >, or share the url.
          </p>
          <div id="chat-container" class="card">
            <div
              class="card-header text-white text-center font-weight-bold subtle-blue-gradient"
            >
              {{ roomName }}
            </div>

            <div class="card-body">
              <div class="container chat-body">
                <div class="row chat-section">
                  <div class="col-sm-7" id="other-chat">
                    <!-- <span class="card-text speech-bubble speech-bubble-peer"
                      >Hello!</span
                    > -->
                  </div>
                </div>
                <div class="row chat-section">
                <div class="col-sm-7 offset-3" id="my-chat">
                  <!-- <span class="card-text speech-bubble speech-bubble-user float-right text-white subtle-blue-gradient">
                    Whatsup, another chat app?
                  </span> -->
                </div>
              </div>
                
              </div>
            </div>

            <div class="card-footer text-muted">
              <form @submit.prevent="sendMessage">
                <div class="row">
                  <div class="col-sm-10">
                    <input
                      required
                      type="text"
                      placeholder="Type a message"
                      v-model="message"
                    />
                  </div>
                  <div class="col-sm-2">
                    <button class="btn btn-primary">
                      Send
                    </button>
                  </div>
                </div>
              </form>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
const $ = window.jQuery; // JQuery

export default {
  data() {
    messagelist: [];
    return {
      message: "",
      roomName: String,
      connection: null,
      username: "",
      id: 0,
      messagelist: []
    };
  },
  methods: {
    sendMessage() {
      if (sessionStorage.getItem("username").length > 0) {
        this.connection.send(
          JSON.stringify({
            message: this.message,
            user: sessionStorage.getItem("username")
          })
        );
      }
      this.message = "";
    }
  },
  created: function() {
    this.username = sessionStorage.getItem("username");
    this.roomName = this.$route.params.roomCode;
    console.log("Connecting with WebSocket Server");
    this.connection = new WebSocket(
      "ws://localhost:8000/ws/chat/" + this.roomName + "/"
    );
    this.connection.onmessage = function(event) {
      const data = JSON.parse(event.data);
      if (data.username == this.username) {
        var msg =
          '<span class="card-text speech-bubble speech-bubble-user float-right text-white subtle-blue-gradient">' +
          data.message +
          "</span>";
        $("#my-chat").append(msg);
      } else {
        var msg =
          '<span class="card-text speech-bubble speech-bubble-peer">' +
          data.message +
          "</span>";
        $("#other-chat").append(msg);
      }
    };
    this.connection.onopen = function(event) {
      //   console.log(event);
      console.log("Successfully connected to the echo websocket server...");
    };
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1,
h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}

.btn {
  border-radius: 0 !important;
}

.card-footer input[type="text"] {
  background-color: #ffffff;
  color: #444444;
  padding: 7px;
  font-size: 13px;
  border: 2px solid #cccccc;
  width: 100%;
  height: 38px;
}

.card-header a {
  text-decoration: underline;
}

.card-body {
  background-color: #ddd;
}

.chat-body {
  margin-top: -15px;
  margin-bottom: -5px;
  height: 380px;
  overflow-y: unset;
}

.speech-bubble {
  display: inline-block;
  position: relative;
  border-radius: 0.4em;
  padding: 10px;
  background-color: #fff;
  font-size: 14px;
}

.subtle-blue-gradient {
  background: linear-gradient(45deg, #004bff, #007bff);
}

.speech-bubble-user:after {
  content: "";
  position: absolute;
  right: 4px;
  top: 10px;
  width: 0;
  height: 0;
  border: 20px solid transparent;
  border-left-color: #007bff;
  border-right: 0;
  border-top: 0;
  margin-top: -10px;
  margin-right: -20px;
}

.speech-bubble-peer:after {
  content: "";
  position: absolute;
  left: 3px;
  top: 10px;
  width: 0;
  height: 0;
  border: 20px solid transparent;
  border-right-color: #ffffff;
  border-top: 0;
  border-left: 0;
  margin-top: -10px;
  margin-left: -20px;
}

.chat-section:first-child {
  margin-top: 10px;
}

.chat-section {
  margin-top: 15px;
}

.send-section {
  margin-bottom: -20px;
  padding-bottom: 10px;
}
</style>
