<script setup>
import { onMounted, ref, watch, onUpdated } from 'vue';
import {
    app,
    auth,
    database,
    ref as refDb,
    set,
    push,
    onValue,
    child,
    get
} from '../firebaseConfig'
import { remove } from '@firebase/database';
let chat = ref("");
let histories = ref([]);
let historyKey = ref("");
const bottomEl = ref(null);
const studentId = "631411031";
const db = refDb(database, 'all_chat/')

const onSend = () => {
    if (chat.value != '') {
        push(refDb(database, `all_chat/${history.value}`), {
            "user": studentId,
            "message": chat.value,
            "dateTime": new Date().toISOString()
        });
        chat.value = "";
    }

}
onValue(db, (snapshot) => {
    const data = snapshot.val() ?? [];
    histories.value = data;
})
onUpdated(() => {
    bottomEl.value.scrollIntoView({ begavior: 'smooth' });
})

const selectGroup = (key) => {
    historyKey.value = key;
}
let groupChatName = ref("");

const createGroup = () => {

    if (groupChatName.value != "") {
        get(child(db,`${groupChatName.value}`))
        .then((snapshot) => {
            if (snapshot.exists()) {
                alert("Canot create chat because chat is exists")
            } else {
                push(refDb(database, `all_chat/${groupChatName.value}`), {
                "user": studentId,
                "message": "",
                "dataTime": new Date().toISOString(),}
                );
                groupChatName.value ="";
            }
        })
        .catch((err) => {
            console.error(err);
        });
    }
};

const deleteGroup = (data) => {
    remove(refDb(database, `all_chat/${data}`))
}
</script>

<template>
  <link
    rel="stylesheet"
    href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css"
    integrity="sha512-iecdLmaskl7CVkqkXNQ/ZH/XLlvWZOJyj7Yy7tcenmpD1ypASozpmT/E0iPtmFIB46ZmdtAc9eNBvH0H/ZpiBw=="
    crossorigin="anonymous"
    referrerpolicy="no-referrer"
  />
  <div class="flex gap-4" data-theme="cupcake">
    <div class="bg-white h-[90vh] w-[30%]">
      <div class="overflow-y-scroll w-full h-[90%]">
        <div
          class="card card-size border border-red-950 bg-slate-50 shadow-xl my-1 mx-1 mb-1 rounded-none"
          style="cursor: pointer"
          v-for="(group, index) in histories"
          :key="index"
          @click="selectGroup(index)"
        >
          <div class="-body">
            <div class="flex">
              <h2 class="card-title w-[80%] overflow-hidden">{{ index }}</h2>
              <p>
                {{
                  group[Object.keys(group)[Object.keys(group).length - 1]]
                    ?.message
                }}
              </p>
              <button
                class="btn btn-outline btn-sm btn-warning ms-auto"
                @click="deleteGroup(index)"
              >
                <i class="fa-regular fa-trash-can"></i>
              </button>
            </div>
          </div>
        </div>
      </div>
      <div class="w-full h-[10%] pt-4">
        <button
          class="btn w-full h-full rounded-none"
          data-theme="cupcake"
          onclick="my_modal_1.showModal()"
        >
          Add Group
        </button>
        <!-- Open the modal using ID.showModal() method -->
        <dialog id="my_modal_1" class="modal">
          <div class="modal-box">
            <div class="fro,-contro; w-full">
              <label class="label">
                <span class="label-text">Group name</span>
              </label>
              <input
                type="text"
                v-model="groupChatName"
                placeholder="Type here"
                class="input input-bordered w-full rounded-none"
              />
            </div>
            <div class="modal-action">
              <form method="dialog">
                <!-- if there is a button in form, it will close the modal -->
                <button
                  @click="createGroup"
                  class="btn btn-success btn-outline me-2"
                >
                  Save
                </button>
                <button class="btn btn-error btn-outline">Close</button>
              </form>
            </div>
          </div>
        </dialog>
      </div>
    </div>
    <div class="bg-transparent h-[90vh] w-[70%]">
      <div class="overflow-y-scroll h-[90%] p-5">
        <div
          v-for="(history, index) in histories[historyKey]"
          :class="`chat ${
            history.user == studentId ? 'chat-end' : 'chat-start'
          }`"
          :key="index"
        >
          <div class="chat-header">
            {{ history.user }}
            <time class="text-xs opacity-50"> {{ history.dateTime }}</time>
          </div>
          <div class="chat-bubble">{{ history.message }}</div>
        </div>
        <div ref="bottomEl"></div>
      </div>
      <div class="flex h-[10%] gap-4">
        <input
          v-on:keyup.enter="onSend"
          v-model="chat"
          type="text"
          placeholder="Type here"
          class="input input-bordered w-[80%] h-full"
        />
        <button @click="onSend" class="w-[20%] btn h-full">Send</button>
      </div>
    </div>
  </div>
</template>
<style scoped></style>