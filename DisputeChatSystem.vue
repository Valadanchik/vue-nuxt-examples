<template>
  <div class="owner-chat col-12">
    <div class="message-box">
      <div class="container">
        <div class="top">
          <div class="messages d-flex flex-column" ref="messageBox">
            <div v-show="loadingMessages" class="loading">
              <p>Loading messages... Please wait</p>
            </div>
            <div :class="'message w-25'+(parseInt(mainUserID) === parseInt(message.sender.id) ? ' send' : ' receive')" v-for="message in messages">
              {{ message.message }}
              <br v-if="message.type === 'file'">
              <a v-show="message.type === 'file'" :href="'/fs-admin/file-download/'+message.file_name"><i class="fas fa-arrow-to-bottom"></i> {{ message.file_name }}</a>
              <img v-if="parseInt(message.sender.id) === parseInt(mainUserID)" :src="'/images/messenger/tick/' + (message.status === 'read' ? 'tick-read.svg' : 'tick-send.svg')" alt="">
            </div>
          </div>
        </div>
        <div class="bottom d-flex flex-nowrap">
          <textarea class="form-control flex-grow-1" type="text" placeholder="Start typing..." rows="1" v-model="newMessage"></textarea>
          <span class="send-btn text-secondary" @click="sendMessage">
                        <i class="fas fa-paper-plane"></i>
                    </span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "DisputeChatSystem",
  props: ['data'],
  data: () => {
    return {
      owner: null,
      loadingMessages: false,
      messages: [],
      mainUserID: document.getElementById('mainID').getAttribute('content'),
      newMessage: '',
      roomID: null
    }
  },
  created() {
    this.owner = JSON.parse(this.data);
    this.addRoom(this.owner.id);
  },
  methods: {
    sendMessage() {
      let app = this;
      if (app.newMessage !== '') {
        axios.post('/api/messages/send', {
          sender_id: app.mainUserID,
          receiver_id: app.owner.id,
          message: app.newMessage,
          chat_room_id: app.roomID
        }).then((resp) => {
          app.messages.push(resp.data);
          app.newMessage = '';
          axios.post('/api/user/notify', {
            type: 'private_message',
            receiver_id: app.owner.id,
            message: resp.data,
            mail: true
          })
        })
      }
    },
    openChat(userID, roomID) {
      let app = this;
      // Start socket.io listener
      Echo.join('newMessage-' + userID + '-' + app.mainUserID)
          .listen('.private.message', (e) => {
            if (userID) {
              axios.post('/api/messages/status', {
                sender_id: e.message.sender_id
              }).then((resp) => {
                app.messages.push(e.message);
              });
            }
          });
      app.loadMessages();
      app.roomID = roomID;
    },
    loadMessages() {
      let app = this;
      app.loadingMessages = true;
      app.messages = [];
      // Start socket.io listener
      Echo.join('newMessage-' + app.owner.id + '-' + app.mainUserID)
          .listen('.message.read', (e) => {
            axios.post('/api/messages', {
              sender_id: app.owner.id
            }).then((resp) => {
              app.messages = resp.data;
              app.loadingMessages = false;
            });
          });
      axios.post('/api/messages', {
        sender_id: app.owner.id
      }).then((resp) => {
        app.messages = resp.data;
        app.loadingMessages = false
      })
    },
    addRoom(userID) {
      let app = this;
      axios.post('/api/rooms/add', {
        sender_id: app.mainUserID,
        receiver_id: userID,
        type: 'private_message'
      }).then((resp) => {
        if (resp.data) {
          app.openChat(userID, resp.data.id)
        }
      });
    }
  }
}
</script>
