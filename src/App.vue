<template>
  <div class="container">
    <header>
      <h1>Wiki Search</h1>
    </header>
    <form @submit.prevent="handleSubmit">
      <input
        type="text"
        v-model="search"
        class="input"
        placeholder="What do you want to search?"
      />
      <br />
      <input type="submit" class="submit" />
    </form>
    <span v-show="searchInfo.totalhits" class="total">
      Search Results: {{ searchInfo.totalhits }}
    </span>
    <div v-for="(result, idx) in results" :key="idx" class="card">
      <h3>{{ result.title }}</h3>
      <br />
      <p v-html="result.snippet" />
      <br />
      <a :href="pageUrl(result)" target="_blank"> Read More </a>
    </div>
    <div class="loader">
      <button @click="loadSize += 20" class="load" v-show="submitted">
        Load More...
      </button>
    </div>
  </div>
</template>

<script>
import { ref, watch } from "vue";
export default {
  setup() {
    const search = ref("");
    const results = ref([]);
    const searchInfo = ref({});
    const loadSize = ref(20);
    const submitted = ref(false);
    const handleSubmit = async () => {
      if (search.value == "") return;
      const getAPI = async () => {
        const wikiAPI = `https://en.wikipedia.org/w/api.php?action=query&list=search&prop=info&inprop=url&utf8=&format=json&origin=*&srlimit=${loadSize.value}&srsearch=${search.value}`;
        const res = await fetch(wikiAPI);
        if (!res.ok) {
          console.error(res.statusText);
        }
        const json = await res.json();
        results.value = json.query.search;
        searchInfo.value = json.query.searchinfo;
      };
      await getAPI();
      submitted.value = !submitted.value;
      watch(loadSize, async () => {
        getAPI();
      });
    };
    const pageUrl = (result) => {
      return `https://en.wikipedia.org/?curid=${result.pageid}`;
    };
    return {
      search,
      results,
      searchInfo,
      handleSubmit,
      pageUrl,
      loadSize,
      submitted,
    };
  },
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: "Fira Sans", sans-serif;
  background-color: #333;
  color: white;
}

.container {
  display: flex;
  flex-direction: column;
  padding: 16px;
}

header {
  margin: 0 auto;
}

header h1 {
  margin-bottom: 10px;
  text-decoration: underline;
}

.input {
  margin-bottom: 10px;
  padding: 0.8rem;
  width: 100%;
  border-radius: 5px;
  overflow: hidden;
  border: 3px solid #555;
  transition: 0.3s;
  font-size: 16px;
}

.submit {
  border: 3px solid #555;
  padding: 5px 16px;
  border-radius: 5px;
  transition: 0.4s;
  display: block;
  outline: none;
  font-weight: 600;
  font-size: 18px;
  overflow: hidden;
  cursor: pointer;
  margin-bottom: 6px;
}

.submit:hover {
  background-color: #555;
  color: #fff;
}

.total {
  font-size: 25px;
}

.card {
  margin-top: 10px;
  color: #fff;
  padding: 15px 20px;
  border-radius: 5px;
  border: 3px solid #c62e2e;
  margin-bottom: 10px;
}

.card h3 {
  font-size: 25px;
}

.card p {
  font-size: 18px;
}

.card p::after {
  content: "...";
}

.card a {
  display: inline-block;
  padding: 12px 10px;
  background-color: #555;
  color: #fff;
  font-weight: 700;
  text-decoration: none;
  border-radius: 5px;
  transition: 0.4s;
}
.card a:hover {
  background-color: #444;
}
.loader {
  margin: 0 auto;
}
.load {
  border: 3px solid #555;
  padding: 5px 16px;
  border-radius: 5px;
  transition: 0.4s;
  display: block;
  outline: none;
  font-weight: 600;
  font-size: 18px;
  overflow: hidden;
  cursor: pointer;
}

.load:hover {
  background-color: #555;
  color: #fff;
}
</style>
