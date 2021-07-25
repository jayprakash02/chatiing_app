<template>
  <div>
    <nav class="navbar navbar-dark bg-dark">
      <a class="navbar-brand" href="#">
        DJ_CHAT
      </a>
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
          <div>
            <h3 class="text-center">Welcome <strong>{{username}}</strong> !</h3>

            <br />

            <p class="text-center">
              To start chatting with friends click on the button below, it'll
              start a new chat session and share your
              <strong>Room Code</strong>, then you can invite your friends over
              to chat!
            </p>

            <br />
            <div class="d-flex flex-row-reverse">
              <span
                >Room Name : <input v-model="roomName" class="mb-1" type="text"
              /></span>
            </div>
            <button
              @click="validateRoomCode"
              class="btn btn-primary btn-lg btn-block"
            >
              Start Chatting
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
const $ = window.jQuery;

export default {
  data() {
    return {
      connection: null,
      roomName: String,
      username:""
    };
  },
  methods: {
    logout() {
      $.ajax({
        url: "http://localhost:8000/auth/token/logout/",
        type: "POST",
        headers: {
          Authorization: "Token " + sessionStorage.getItem("authToken")
        },
        success: function(data) {
          sessionStorage.removeItem("authToken");
          sessionStorage.removeItem("username");
          setTimeout(function() {
            location.reload(); //Refresh page
          }, 1000);
        },
        error: function(data) {
          alert(data.response);
        }
      });
    },
    validateRoomCode() {
      if (this.roomName.length <= 5 && this.roomName.length > 0) {
        this.$router.push("/chats/" + this.roomName);
      } else {
        alert(
          "Room Code should be less than 5 and must not contain any special charracter"
        );
      }
    }
  },
  created: function() {
    this.username=sessionStorage.getItem("username")
    var length = 4,
      charset = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ",
      retVal = "";
    for (var i = 0, n = charset.length; i < length; ++i) {
      retVal += charset.charAt(Math.floor(Math.random() * n));
    }
    this.roomName = retVal;
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
