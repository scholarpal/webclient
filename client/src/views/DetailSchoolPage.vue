<script>
import { QuillEditor } from '@vueup/vue-quill'
import '@vueup/vue-quill/dist/vue-quill.snow.css';
import { mapActions, mapState } from 'pinia';
import { useMainStore } from '../stores/store';

export default {
  data() {
    return {
      expand: true,
      text: "Yes, Ofc.",
      star: 5,
      category: "",
      comment: "",
      options: {
        debug: 'info',
        modules: {
          toolbar: ['bold', 'italic', 'underline']
        },
        placeholder: 'Compose an epic...',
        theme: 'snow'
      },
      info: {
        "Hygine": "#1683ae",
        "Food & Beverage": "#d7a030",
        "Facility": "#4b16ae",
        "Organization": "#99150c"
      }
    }
  },
  computed: {
    ...mapState(useMainStore, ["schoolDetail"]),
    dataShow() {

    }
  },
  methods: {
    ...mapActions(useMainStore, ["fetchDetailSchool", "postCommentRate"]),
    setExpand() {
      this.expand = !this.expand
      let ratebutton = document.getElementById("expand");
      if (this.expand) {
        ratebutton.classList.add("d-none");
      }
      else {
        ratebutton.classList.remove("d-none");
      }
    },
    async submitCreateComment() {
      let comment = this.comment
      let star = this.star
      let category = this.category
      let slug = "smkn-12-jakarta"
      await this.postCommentRate(comment, category, star, slug, () => {
        this.comment = ""
        this.star = 5
        this.category = ""
      })
    }
  },
  components: {
    QuillEditor
  },
  mounted() {
    if (this.$route.path !== "/") {
      let nav = document.getElementById("navbar")
      nav.classList.add("bg-dark");

    }
    var mapProp = {
      center: new google.maps.LatLng(-6.1149000, 106.8861000),
      zoom: 5,
    };
    new google.maps.Map(document.getElementById("googleMap"), mapProp);
  },
  created() {
    this.fetchDetailSchool("smkn-12-jakarta")
  }
}
</script>
<template>
  <span>
  </span>
  <div class="container-lg" style="padding-top: 67px;">
    <section class="row " style="height: 320px;">
      <img class="col-4 object-fit-cover h-100" style="height: 100%;" src="@/assets/foto-1.jpeg" alt="">
      <div class="col-8 d-flex flex-wrap gap-3">
        <img class="col object-fit-cover" height="150" width="260" src="@/assets/2.webp" alt="">
        <img class="col object-fit-cover" height="150" width="260" src="@/assets/3.jpg" alt="">
        <img class="col object-fit-cover" height="150" width="260" src="@/assets/5.jpg" alt="">
        <img class="col object-fit-cover" height="150" width="260" src="@/assets/55.jpg" alt="">
      </div>
    </section>
    <section class="row mt-4">
      <div class="col-4 my-1 ">

        <div class="w-100 p-3 rounded" style="background-color: #F7F9FA;">
          <h2>SMKN 12 Jakarta</h2>
          <div class="w-100 pt-2">
            <p>Overall score by our user is <b>80.75</b></p>
            <div v-for="(el, key) in schoolDetail.ratings" class="mb-2 d-flex d-flex align-items-end">
              <div class="w-75 mt-1">
                <span class="fw-bold">{{ key }}</span>
                <div class="progress ">
                  <div class="progress-bar" role="progressbar"
                    :style="{ width: el.percentage + '%', backgroundColor: info[key] }" :aria-valuenow="el.percentage"
                    aria-valuemin="0" aria-valuemax="100"></div>
                </div>
              </div>
              <p class="p-0 m-0 text-end" style="font-size: 18px; width: 90px;">{{ Math.round(el.percentage) }} / 100</p>
            </div>
          </div>
        </div>
        <div class="w-100 my-5 rounded" id="googleMap" style="width:100%;height:400px;"></div>
      </div>
      <div class="col-8 my-1 px-4">
        <div class="mb-5 d-flex flex-column justify-content-center gap-3">
          <div class="d-flex gap-3 align-items-center">
            <h3>Rate your school?</h3>
            <button @click="setExpand" class="rounded btn btn-primary border-0 fw-bold ms-auto d-none"
              style="background-color: #88c4cc;" id="expand" type="button">Rate Now</button>
            <!-- <div class="form-check form-switch d-flex gap-2">
              <input class="form-check-input" type="checkbox" id="flexSwitchCheckChecked" v-on:change="setExpand" checked>
              <label class="form-check-label" for="flexSwitchCheckChecked">
                <h6>{{ text }}</h6>
              </label>
            </div> -->
          </div>
          <form @submit.prevent="submitCreateComment" v-if="expand" class="border p-2 rounded"
            style="background-color: #c4d4d6;">
            <div class="d-flex gap-2 my-3 align-items-center" style="height: 30px;">
              <div class="d-flex gap-2 align-items-center">
                <label for="">Rate</label>
                <select v-model="star" class="form-select" name="" id="" style="font-family: 'FontAwesome','sans-serif';">
                  <option value="5"><span v-for="i in 5">&#xf005;</span></option>
                  <option value="4"><span v-for="i in 4">&#xf005;</span></option>
                  <option value="3"><span v-for="i in 3">&#xf005;</span></option>
                  <option value="2"><span v-for="i in 2">&#xf005;</span></option>
                  <option value="1"><span>&#xf005;</span></option>
                  <!-- <option value=""><img src="../assets/." alt=""></option> -->
                </select>
              </div>
              <div class="d-flex gap-2 align-items-center">
                <label for="">For </label>
                <select v-model="category" class="form-select" name="" id="" style="width:180px">
                  <option value="" selected disabled>Select a category</option>
                  <option value="Facility">Facility</option>
                  <option value="Organization">Organization</option>
                  <option value="Hygine">Hygine</option>
                  <option value="Food & Baverage">Food & Baverage</option>
                </select>
              </div>

              <div class="ms-auto">
                <a @click="setExpand" class="btn border-0"><i class="fa fa-solid fa-x"></i></a>
              </div>

            </div>
            <div class="mb-2 bg-white">
              <QuillEditor theme="snow" :options="options" v-model:content="comment" contentType="html" />
            </div>
            <div class="d-flex justify-content-end align-items-end">
              <button @click.prevent="submitCreateComment" type="submit" class="btn px-3 text-white fw-bold"
                value="Submit" style="background-color: #98bbc1;">Submit
              </button>
            </div>

          </form>
        </div>

        <div class="rounded pb-2" style="background-color: #c4d4d6;">
          <div class="w-100 p-3 rounded-top d-flex flex-column justify-content-between"
            style="background-color: #c4d4d6;">
            <span class="mb-0 fw-bold" style="font-size: 18px;">Filter :</span>
            <span class="d-flex gap-2">
              <div class="d-flex gap-2 align-items-center" style="height: 30px;">
                <label for="">Category</label>
                <select class="form-select" name="" id="" style="width:120px">
                  <option value="">Select One</option>
                  <option value="">Facility</option>
                  <option value="">Hygine</option>
                  <option value="">Food & Beverage</option>
                  <option value="">Clubs</option>
                </select>
              </div>
              <div class="d-flex gap-2 align-items-center" style="height: 30px;">
                <label for="">Rating</label>
                <div class="rating-filter bg-white py-1 px-2 rounded">
                  <div class="form-check">
                    <input class="form-check-input" type="checkbox" value="" id="5star">
                    <label class="form-check-label" for="5star">
                      <i v-for="i in 5" class="fa text-warning fa-solid fa-star"></i>
                    </label>
                  </div>
                </div>
                <div class="rating-filter bg-white py-1 px-2 rounded">
                  <div class="form-check">
                    <input class="form-check-input" type="checkbox" value="" id="4star">
                    <label class="form-check-label" for="4star">
                      <i v-for="i in 4" class="fa text-warning fa-solid fa-star"></i>
                    </label>
                  </div>
                </div>
                <div class="rating-filter bg-white py-1 px-2 rounded">
                  <div class="form-check">
                    <input class="form-check-input" type="checkbox" value="" id="3star">
                    <label class="form-check-label" for="3star">
                      <i v-for="i in 3" class="fa text-warning fa-solid fa-star"></i>
                    </label>
                  </div>
                </div>
                <div class="rating-filter bg-white py-1 px-2 rounded">
                  <div class="form-check">
                    <input class="form-check-input" type="checkbox" value="" id="2star">
                    <label class="form-check-label" for="2star">
                      <i v-for="i in 2" class="fa text-warning fa-solid fa-star"></i>
                    </label>
                  </div>
                </div>
                <div class="rating-filter bg-white py-1 px-2 rounded">
                  <div class="form-check">
                    <input class="form-check-input" type="checkbox" value="" id="1star">
                    <label class="form-check-label" for="1star">
                      <i class="fa fa-solid fa-star text-warning"></i>
                    </label>
                  </div>
                </div>

              </div>
            </span>
          </div>
          <div class="w-100 p-3 h-100 rounded d-flex flex-column gap-4 flex-column-reverse">
            <div class="bg-white border mt-2 p-2 rounded" v-for="comment in schoolDetail.comments">
              <p class="px-2 text-center mt-1 mr-1 fw-bold rounded" style="width:190px; background-color:#f0f1e8">{{
                comment.category }}
                ⭐️ {{ comment.star }}
              </p>
              <span @click="" style="text-align: justify;" v-html="comment.comment">
              </span>
              <p class="text-end">
                {{ comment.createdAt.split("T")[0] }}
              </p>
            </div>
          </div>
        </div>

      </div>
    </section>

  </div>
</template>

<style scoped>
.rating-filter:has(input:checked) {
  background-color: #fbfde8 !important;
}

div.mb-5.d-flex.flex-column.justify-content-center.gap-3>form>div.mb-2.bg-white>div.ql-container.ql-snow>div.ql-editor.ql-blank {
  min-height: 100px !important;
}
</style>