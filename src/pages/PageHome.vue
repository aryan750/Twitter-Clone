<template>
  <q-page class="relative-position">
    <q-scroll-area class="absolute fullscreen">
      <div class="q-py-lg q-px-md row items-end q-col-gutter-md">
        <div class="col">
          <q-input
            bottom-slots
            v-model="newQweetContent"
            placeholder="What's happening?"
            class="new-qweet"
            counter
            maxlength="280"
            autogrow
          >
            <template v-slot:before>
              <q-avatar size="xl">
                <img src="https://cdn.quasar.dev/img/avatar5.jpg" />
              </q-avatar>
            </template>
          </q-input>
        </div>
        <div class="col col-shrink">
          <q-btn
            @click="addNewQweet()"
            :disable="!newQweetContent"
            class="q-mb-lg"
            unelevated
            rounded
            color="primary"
            label="Qweet"
            no-caps
          />
        </div>
      </div>
      <q-separator class="divider" size="10px" color="grey-2" />

      <q-list separator>
        <transition-group
          appear
          enter-active-class="animated fadeIn slow"
          leave-active-class="animated fadeOut slow"
        >
          <q-item
            class="qweet q-py-md"
            v-for="qweet in qweets"
            :key="qweet.date"
          >
            <q-item-section avatar top>
              <q-avatar size="xl">
                <img src="https://cdn.quasar.dev/img/avatar2.jpg" />
              </q-avatar>
            </q-item-section>

            <q-item-section>
              <q-item-label class="text-subtitle1">
                <strong>Aryan Gupta</strong>
                <span class="text-grey-7">
                  @aryangupta750 &bull; {{ qweet.date }}
                </span>
              </q-item-label>
              <q-item-label class="qweet-content text-body1">
                {{ qweet.content }}
              </q-item-label>
              <div class="qweet-icons row justify-between">
                <q-btn
                  flat
                  round
                  size="sm"
                  color="grey"
                  icon="far fa-comment"
                />
                <q-btn
                  flat
                  round
                  size="sm"
                  color="grey"
                  icon="fas fa-retweet"
                />
                <q-btn flat round size="sm" color="grey" icon="far fa-heart" />
                <q-btn
                  @click="deleteQweet(qweet)"
                  flat
                  round
                  size="sm"
                  color="grey"
                  icon="fas fa-trash"
                />
              </div>
            </q-item-section>
          </q-item>
        </transition-group>
      </q-list>
    </q-scroll-area>
  </q-page>
</template>

<script>
import { defineComponent } from "vue";
import { db } from "src/boot/firebase";
import { collection, query, onSnapshot } from "firebase/firestore";

// import * as dateFns from 'date-fns';

export default defineComponent({
  name: "PageHome",
  data() {
    return {
      newQweetContent: "",
      qweets: [
        // {
        //   content: 'Hi! My name is Aryan Gupta.This is project is made using Vue.js, Quasar framework, and Firebase',
        //   date: 1630147052504
        // },
        // {
        //   content: 'Lorem ipsum dolor sit amet, consectetur adipiscing elit. Cras iaculis quam sed est pharetra, faucibus tempor lectus vestibulum. Vestibulum vitae lacinia nisi. Nunc fringilla hendrerit orci, quis pellentesque arcu. Vivamus id ante eu enim molestie varius. Donec feugiat pulvinar lectus, at ornare diam laoreet vitae. Nunc ultricies sollicitudin massa ut mattis. Praesent dolor nisl, tristique at dignissim a, bibendum at est. Sed rhoncus magna eu bibendum elementum. ',
        //   date: 1630147105686
        // },
      ],
    };
  },
  methods: {
    addNewQweet() {
      let newQweet = {
        content: this.newQweetContent,
        date: Date.now(),
      };
      this.qweets.unshift(newQweet);
      this.newQweetContent = "";
    },
    deleteQweet(qweet) {
      let dateToDelete = qweet.date;
      let index = this.qweets.findIndex((qweet) => qweet.date === dateToDelete);
      this.qweets.splice(index, 1);
    },
  },
  filters: {
    relativeDate(value) {
      return formatDistance(value, new Data());
    },
  },
  mounted() {

    const q = query(collection(db, "qweet"));
    const unsubscribe = onSnapshot(q, (snapshot) => {
      console.log(snapshot);
      snapshot.docChanges().forEach((change) => {
        if (change.type === "added") {
          console.log("New qweet: ", change.doc.data());
        }
        if (change.type === "modified") {
          console.log("Modified qweet: ", change.doc.data());
        }
        if (change.type === "removed") {
          console.log("Removed qweet: ", change.doc.data());
        }
      });
    });

    // db.collection("qweets").onSnapshot((snapshot) => {
    //   console.log(snapshot);
    //   snapshot.docChanges().forEach((change) => {
    //     if (change.type === "added") {
    //       console.log("New qweet: ", change.doc.data());
    //     }
    //     if (change.type === "modified") {
    //       console.log("Modified qweet: ", change.doc.data());
    //     }
    //     if (change.type === "removed") {
    //       console.log("Removed qweet: ", change.doc.data());
    //     }
    //   });
    // });
  },
});
</script>

<style lang="sass">
.new-Qweet
  textarea
    font-size: 19px
    line-height:1.4 !important
.divider
  border-top: 1px solid
  border-bottom: 1px solid
  border-color: $grey-4
.qweet:not(:first-child)
  border-top: 1px solid rgba(0,0,0,0.12)
.qweet-content
  white-space: pre-line
.qweet-icons
  margin-left: -5px
</style>
