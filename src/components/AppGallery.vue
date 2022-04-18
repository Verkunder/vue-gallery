<template>
  <div class="wrapper">
    <h2>Галерея картинок</h2>
    <AppLoader v-if="loading" />
    <div class="gallery">
      <div class="gallery__block" v-for="(item, id) in gallery" key="id">
        <div class="gallery__block-item">
          <img :src="item.url" alt="" @click="showModal(item.id)" />
        </div>
      </div>
    </div>
  </div>
  <ModalImage ref="modal">
    <template v-slot:title>
      <h3 class="modal-title"><img :src="bigImg.url" alt="" /></h3>
    </template>
    <template v-slot:body>
      <div class="wrap_modal">
        <input type="text" placeholder="Имя" v-model="name" />
        <textarea
          class="modal-textarea"
          placeholder="Добавьте отзыв"
          v-model="comment"
        >
        </textarea>
      </div>
      <div class="comment" v-if="bigImg.comments.length != 0">
        <div
          class="comment__data"
          v-for="(item, id) in bigImg.comments"
          key="id"
        >
          {{ item.date }}
        </div>
        <div
          class="comment__title"
          v-for="(item, id) in bigImg.comments"
          key="id"
        >
          {{ item.text }}
        </div>
      </div>
      <div class="comment__title" v-else>Комментариев нет</div>
    </template>
    <template v-slot:footer>
      <button class="modal-footer__button" @click="sendDataFunction()">
        Отправить
      </button>
    </template>
  </ModalImage>
</template>

<script>
import axios from "axios";
import AppLoader from "./AppLoader.vue";
import ModalImage from "./ModalImage.vue";
export default {
  data() {
    return {
      gallery: null,
      loading: false,
      comment: "",
      id: null,
      bigImg: null,
      name: "",
    };
  },
  components: {
    AppLoader,
    ModalImage,
  },
  methods: {
    async load() {
      this.loading = true;
      await axios
        .get("https://boiling-refuge-66454.herokuapp.com/images")
        .then((response) => (this.gallery = response.data));
      this.loading = false;
    },
    async showModal(id) {
      this.id = id;
      await axios
        .get(`https://boiling-refuge-66454.herokuapp.com/images/${this.id}`)
        .then((response) => (this.bigImg = response.data));
      this.$refs.modal.show = true;
    },
    sendDataFunction() {
      if (confirm("Оставить комментарий?")) {
        axios.post(
          `https://boiling-refuge-66454.herokuapp.com/images/${this.id}/comments`,
          {
            name: this.name,
            comment: this.comment,
          }
        );
        this.comment = "";
        this.name = "";
        alert("Комментарий отправлен");
      } else {
        alert("Комментарий не оставлен");
        this.comment = "";
        this.name = "";
      }
    },
  },
  mounted() {
    this.load();
  },
};
</script>

<style lang="sass">
@import "../assets/include/_config"
.gallery
        display: flex
        flex-wrap: wrap
        margin: 0 -16px -16px
        justify-content: center

        +max-width($mobail)
                margin: 0

        &__block
                max-width: calc(100% / 4)
                padding: 16px 16px

                +max-width($desktop)
                        max-width: calc(100%/3)

                +max-width($mobail)
                        max-width: 100%
                        width: 100%
                        flex-direction: row
                        padding: 0 0 16px
                        align-items: flex-start

                        &:last-child
                                margin-bottom: 0

        &__block-item
                border-radius: 5px
                box-shadow: 0 2px 5px 0 rgba(165, 169, 176, 0.3)
                width: 100%
                display: flex
                flex-direction: column
                align-items: center
                position: relative
                height: 100%
                +max-width($mobail)
                        padding: 12px 12px 24px
                        flex-direction: row
                        align-items: flex-start

h2
        text-align: center
        margin-bottom: 26px
        +alegreya-black(52px, 150%)
        +max-width($desktop)
                +alegreya-black(40px, 150%)
        +max-width($mobail)
                +alegreya-black(28px, 150%)

.wrap_modal
    display: flex
    flex-direction: column
    justify-content: flex-start

input[type="text"]
    padding: 10px
    margin: 10px

textarea.modal-textarea
    margin: 10px
    padding: 10px

.comment

    &__title
        margin-bottom: 6px
        transition: color 0.3s ease-in-out
        text-align: center
        display: flex
        padding: 0 30px
        +alegreya-extrabold(24px, 150%)
        +max-width($desktop)
                text-align: left
                margin-top: 8px
                text-align: center
                +alegreya-extrabold(20px, 150%)
        +max-width($mobail)
                text-align: start
                +alegreya-extrabold(18px, 150%)

    &__data
            text-align: center
            margin-bottom: 8px
            display: block
            +roboto(12px, 100%)
            line-height: 18px
            color: $iron
            height: auto
            display: flex
            padding: 0 15px
            +max-width($mobail)
                    font-size: 11px
                    line-height: 1.5
                    margin-bottom: 4px
                    text-align: left
</style>
