<script>
import { mapActions, mapState } from 'pinia'
import { useMainStore } from '../stores/store'

export default {
  data() {
    return {
      username: ""
    }
  },
  computed: {
    ...mapState(useMainStore, ["isLogin"]),
    username() {
      if (this.isLogin) {
        return localStorage.getItem("username")
      } else {
        return ""
      }
    }
  },
  methods: {
    ...mapActions(useMainStore, ["logOut"]),
    toLandingPage() {
      this.$router.push("/")
    },
    logoutNow() {
      this.logOut(this.toLandingPage)
    },
  },
  mounted() {
    let nav = document.getElementById("navbar")
    if (document.URL !== "http://localhost:5173/" && document.URL !== "http://localhost:5173/profile") {
      nav.classList.add("bg-dark");
    }
    document.addEventListener('scroll', () => {
      if (document.URL == "http://localhost:5173/") {
        if (window.scrollY >= nav.getBoundingClientRect().height) {
          nav.classList.add("bg-dark");
        } else {
          nav.classList.remove("bg-dark");
        }
      }
    })
  }
}
</script>
<template>
  <nav id="navbar" class="navbar navbar-expand-lg fixed-top" v-on:scroll="">
    <div class="container">
      <RouterLink class="navbar-brand fw-bold" to="/">
        <!-- <h3 class="text-white">ScholarPal</h3> -->
        <img src="../assets/logo_scholarpal_inv.png" alt="" class="img-fluid" style="width: 2rem;">
      </RouterLink>
      <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNavAltMarkup"
        aria-controls="navbarNavAltMarkup" aria-expanded="false" aria-label="Toggle navigation">
        <span class="navbar-toggler-icon"></span>
      </button>
      <div class="collapse navbar-collapse" id="navbarNavAltMarkup">
        <div class="navbar-nav gap-3">
          <RouterLink class="nav-link fw-bold" to="/">Scholarpal</RouterLink>
          <RouterLink class="nav-link fw-bold" to="/events">Events</RouterLink>
        </div>
        <div class="ms-auto">

          <div class="dropdown" v-if="isLogin">
            <a href="#" data-bs-toggle="dropdown" aria-expanded="false"
              class="d-flex gap-3 align-items-center text-decoration-none" v-if="isLogin">
              <img class="rounded-circle img-fluid border border-white border-2 text-white" style="width: 3rem;"
                src="../assets/user.png" alt="">
              <span class="text-white"><b>@{{ username }}</b></span>
            </a>

            <ul class="dropdown-menu dropdown-menu-dark">
              <li>
                <RouterLink class="dropdown-item fw-bold" to="/profile">Profile</RouterLink>
              </li>
              <li>
                <a @click.prevent="logoutNow" class="dropdown-item text-danger fw-bold">Log Out</a>
              </li>
            </ul>
          </div>
          <div class="g_id_signin" v-show="!isLogin">
          </div>
        </div>
      </div>
    </div>
  </nav>
</template>

<style scoped>
.navbar {
  transition: 0.2s all ease-in-out;
}

.navbar-nav .nav-link {
  color: white !important;
}

.navbar.nav-bg {
  background-color: white;
}
</style>