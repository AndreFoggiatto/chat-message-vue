<template>
  <q-layout class="container-message-pixstream" view="lHh Lpr lFf">
    <div class="header-pixstream">
      <div class="container-title">
        <img src="./assets/icon-stream.png" width="40" />
        Chat PixStream
      </div>
      <q-input
        outlined
        placeholder="Informe seu nome"
        v-model="message.name"
        class="q-mt-sm q-pa-sm input-pixstream"
        dense
      >
      </q-input>
    </div>
    <q-page-container>
      <ChatComponent :messages="messages" :userActual="message.name" />

      <q-input
        outlined
        @keyup.enter="send"
        placeholder="Digite uma mensagem"
        v-model="message.body"
        class="q-mt-xl q-pa-sm input-send-message"
        dense
      >
        <template v-slot:append>
          <q-icon size="sm" name="search" />
        </template>
      </q-input>
    </q-page-container>
  </q-layout>
</template>

<script>
import { ref, reactive, onMounted } from "vue";
import ChatComponent from "./components/ChatComponent.vue";
import Hub from "./Hub";

export default {
  name: "LayoutDefault",

  components: {
    ChatComponent,
  },

  setup() {
    let messages = ref([]);
    let message = reactive({
      name: "",
      body: "",
    });

    function send() {
      if (message.body == "") return;
      _hub.connection.invoke("SendMessage", message);
      message.body = "";
    }

    let _hub = new Hub();

    onMounted(() => {
      _hub.connection
        .start()
        .then(() => {
          console.log("Connected");
          _hub.connection.on("ReceivedMessage", (msg) => {
            console.log(msg);
            messages.value.push(msg);
          });
        })
        .catch((e) => console.log("Connection failed", e));
    });

    return {
      send,
      messages,
      message,
    };
  },
};
</script>

<style scoped>
.container-message-pixstream {
  background: url(./assets/bg-mensagem.webp);
  background-size: cover;
  background-repeat: no-repeat;
}

.container-title {
  display: flex;
  align-items: center;
  gap: 10px;
}

.header-pixstream {
  padding: 10px;
  background-color: #202c33;
  color: #fff;
  font-weight: 500;
  font-size: 24px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  flex-wrap: wrap;
}
.input-pixstream {
  background-color: rgb(183, 218, 236);
}

.input-send-message {
  background-color: rgb(183, 218, 236);
  position: fixed;
  bottom: 0;
  width: 100%;
  padding: 15px;
}
</style>