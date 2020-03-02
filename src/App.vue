<template>
  <div id="app" :style="{backgroundImage: 'url(' + path + ')'}">
    <div id="overlay"></div>
    <div id="content" class="p-3">
      <p class="mb-2" v-if="is_selected">Today's your lucky parrots is...</p>
      <p class="mb-0" v-else>Choose today's your lucky parrot</p>
      <div class="parrots d-flex my-4" v-if="!is_selected">
        <img
          v-for="(src, index) in random_parrots_list"
          :key="index"
          class="random_parrots"
          :src="src"
        />
      </div>
      <button
        type="button"
        class="btn btn-info btn-lg w-100"
        v-show="!is_selected"
        @click="show_random_parrot"
      >
        <div class="choose_btn">
          <div>Choose</div>
          <div>today's Parrot</div>
        </div>
      </button>
      <div class="parrots d-flex my-4" v-if="!is_selected">
        <img
          v-for="(src, index) in reverse_random_parrots_list"
          :key="index"
          class="random_parrots"
          :src="src"
        />
      </div>
      <h2>{{ parrot_name }}</h2>
      <img v-show="is_selected" class="my-1" alt="parrot" :src="path" />
      <a :href="tweet_url" class="btn btn-info btn-lg w-100 my-1" v-show="is_selected">Tweet</a>
      <button
        type="button"
        class="btn btn-warning btn-lg w-100 mt-1"
        v-show="is_selected"
        @click="show_random_parrot"
      >Choose parrot again</button>
      <a class="mt-3" v-show="is_selected" @click="reset">Back to Top</a>
    </div>
  </div>
</template>

<script>
export default {
  name: "App",
  data: function() {
    return {
      is_selected: false,
      path: "/images/parrot.gif",
      parrot_name: "",
      random_parrots_list: [],
      reverse_random_parrots_list: []
    };
  },
  methods: {
    show_random_parrot: async function() {
      const parrot_array = await this.get_all_parrot();
      const path = parrot_array[this.get_random_int(parrot_array.length)];
      this.path = path;
      this.parrot_name = path.slice(8, -4);
      this.is_selected = true;
    },
    get_ramdom_parrots: async function() {
      const parrot_array = await this.get_all_parrot();
      for (let i = 0; i < 9; i++) {
        this.random_parrots_list.push(
          parrot_array[this.get_random_int(parrot_array.length)]
        );
      }
      this.get_reverse_ramdom_parrots();
    },
    get_reverse_ramdom_parrots: async function() {
      for (let i = 0; i <= 8; i++) {
        this.reverse_random_parrots_list[i] = this.random_parrots_list[8 - i];
      }
    },
    get_all_parrot: async function() {
      const response = await fetch("/list.txt");
      const parrot_list = await response.text();
      const parrot_array = parrot_list.split("\n");
      return parrot_array;
    },
    get_random_int: function(max) {
      return Math.floor(Math.random() * Math.floor(max));
    },
    reset: function() {
      this.is_selected = false;
      this.parrot_name = "";
      this.path = "/images/parrot.gif";
    },
    set_params_data: function() {
      let params = new URLSearchParams(location.search);
      if (params.get("parrot_name") != null) {
        this.parrot_name = params.get("parrot_name");
        this.path = params.get("path");
        this.is_selected = true;
      }
    }
  },
  computed: {
    tweet_url: function() {
      let parrot_params = new URLSearchParams();
      let url_params = new URLSearchParams();
      parrot_params.append("parrot_name", this.parrot_name);
      parrot_params.append("path", this.path);
      url_params.append(
        "text",
        "http://localhost:8080?" + parrot_params.toString()
      );
      let tweet_url =
        "https://twitter.com/intent/tweet?" + url_params.toString();
      return tweet_url;
    }
  },
  mounted: function() {
    this.get_ramdom_parrots();
    this.get_reverse_ramdom_parrots();
    this.set_params_data();
  }
};
</script>

<style>
p {
  font-size: 12px;
  text-align: center;
}
img {
  height: 200px;
  width: auto;
}
h2 {
  font-size: 18px;
}
a {
  cursor: pointer;
}
.random_parrots {
  width: 30px;
  height: auto;
}
.choose_btn {
  font-size: 16px;
}
.btn-lg {
  font-size: 10px;
}
#app {
  height: 100vh;
  background-position: center;
  padding-top: 100px;
  font-family: "Press Start 2P";
}
#overlay {
  background-color: rgba(0, 0, 0, 0.5);
  height: 100vh;
  width: 100vw;
  position: fixed;
  top: 0;
  left: 0;
  z-index: 1;
}
#content {
  z-index: 10;
  position: relative;
  flex-direction: column;
  display: flex;
  width: 290px;
  margin: auto;
  align-items: center;
  background-color: whitesmoke;
}
@media screen and (min-width: 768px) {
  p {
    font-size: 15px;
  }
  h2 {
    font-size: 30px;
  }
  .random_parrots {
    width: 60px;
    height: auto;
  }
  .choose_btn {
    font-size: 30px;
  }
  #app {
    height: 100vh;
    background-position: center;
    padding-top: 100px;
    font-family: "Press Start 2P";
  }
  #overlay {
    background-color: rgba(0, 0, 0, 0.5);
    height: 100vh;
    width: 100vw;
    position: fixed;
    top: 0;
    left: 0;
    z-index: 1;
  }
  #content {
    z-index: 10;
    position: relative;
    flex-direction: column;
    display: flex;
    width: 600px;
    margin: auto;
    align-items: center;
    background-color: whitesmoke;
  }
  img {
    height: 400px;
    width: auto;
  }
}
</style>
