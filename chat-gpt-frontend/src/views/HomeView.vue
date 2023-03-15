<template>
  <main>
    <div id="chat_container">
      <div
        v-for="(chat, i) in wrapper"
        :key="i"
        class="wrapper"
        :class="{ ai: chat.isAi }"
      >
        <Chat :chat="chat" :key="i" />
      </div>
    </div>
    <form @keyup.enter="fetchAnswer" @submit.prevent="fetchAnswer">
      <textarea
        cols="1"
        rows="1"
        placeholder="Ask VueChat..."
        v-model="question"
      >
      </textarea>
      <button type="submit">
        <img src="@/assets/send.svg" alt="Send Message" />
      </button>
    </form>
  </main>
</template>

<script setup>
import { ref } from "vue";
import Chat from "@/components/Chat.vue";

const question = ref("");
const wrapper = ref([]);
const loading = ref(false);

const fetchAnswer = async () => {
  try {
    loading.value = true;
    wrapper.value.push({
      isAi: false,
      value: question.value,
    });
    wrapper.value.push({
      isAi: true,
      value: "Loading...",
    });

    const res = await fetch("http://localhost:8000", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify({
        question: question.value,
      }),
    });
    // console.log(res);
    const data = await res.json();
    console.log(data);
    const parsedData = data.bot.trim();
    wrapper.value.pop();
    wrapper.value.push({
      isAi: true,
      value: parsedData,
    });
  } catch (error) {
  } finally {
    loading.value = false;
    question.value = "";
  }
};
</script>

<style scoped>
#loading {
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 20px;
  margin-top: 20px;
}

.dot {
  width: 10px;
  height: 10px;
  border-radius: 50%;
  display: inline-block;
  background-color: black;
  margin-left: 5px;
  animation: pulse 1s ease-in-out infinite;
}

@keyframes pulse {
  0% {
    transform: scale(0.8);
  }
  50% {
    transform: scale(1.2);
  }
  100% {
    transform: scale(0.8);
  }
} ;
</style>
