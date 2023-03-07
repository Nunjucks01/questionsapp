<script setup>
import { ref, watch } from "vue";
import axios from "axios";

const baseUrl = ref("http://localhost:3000/questions");

let pageNumber = 1;

const allQuestions = ref(
  axios
    .get(baseUrl.value)
    .then((response) => (allQuestions.value = response.data))
);

const questions = ref(
  axios
    .get(baseUrl.value, {
      params: {
        _page: pageNumber,
        _limit: 10,
      },
    })
    .then((response) => (questions.value = response.data))
);

const search = ref("");

watch(search, (searchValue) => {
  axios
    .get(baseUrl.value, {
      params: {
        title_like: searchValue,
        _limit: 10,
      },
    })
    .then((response) => (questions.value = response.data));
  pageNumber = 1;
});

const typeFilter = (type) => {
  if (type.target.value === "all") {
    return axios
      .get(baseUrl.value, {
        params: {
          _page: pageNumber,
          _limit: 10,
        },
      })
      .then((response) => (questions.value = response.data));
  }
  axios
    .get(baseUrl.value, {
      params: {
        level: type.target.value,
        _limit: 10,
      },
    })
    .then((response) => (questions.value = response.data));
};

const nextPage = () => {
  pageNumber++;
  axios
    .get(baseUrl.value, {
      params: {
        _page: pageNumber,
        _limit: 10,
      },
    })
    .then((response) => (questions.value = response.data));
};

const previousPage = () => {
  pageNumber--;
  axios
    .get(baseUrl.value, {
      params: {
        _page: pageNumber,
        _limit: 10,
      },
    })
    .then((response) => (questions.value = response.data));
};
</script>

<template>
  <section>
    <div class="inputs">
      <div class="search-input">
        <input v-model="search" type="text" placeholder="Input search text.." />
        <span><font-awesome-icon icon="fa-solid fa-magnifying-glass" /></span>
      </div>
      <select @change="typeFilter($event)" name="" id="">
        <option value="" hidden>Select type..</option>
        <option value="all">all</option>
        <option value="junior">junior</option>
        <option value="middle">middle</option>
        <option value="senior">senior</option>
      </select>
    </div>
    <div>
      <div class="list list-header">
        <strong>Name</strong>
        <div class="side-content">
          <strong>Type</strong>
          <strong>Actions</strong>
        </div>
      </div>
      <ul>
        <li v-for="question in questions" :key="question.id">
          <div class="list list-item">
            <span>{{ question.title }}</span>
            <div class="side-content">
              <span>{{ question.level }}</span>
              <div class="icons">
                <RouterLink :to="`/edit/${question.id}`">
                  <button style="color: black">
                    <font-awesome-icon icon="fa-solid fa-pen-to-square" />
                  </button>
                </RouterLink>
                <button @click="axios.delete(`${baseUrl}/${question.id}`)">
                  <font-awesome-icon icon="fa-solid fa-trash" />
                </button>
              </div>
            </div>
          </div>
        </li>
      </ul>
    </div>
    <div class="list-nav">
      <button
        class="list-nav--button"
        :disabled="pageNumber === 1"
        @click="previousPage()"
        :class="{
          disabled: pageNumber === 1,
        }"
      >
        &#60;
      </button>
      <button class="list-nav--button">{{ pageNumber }}</button>
      <button
        class="list-nav--button"
        :disabled="pageNumber === Math.ceil(allQuestions.length / 10)"
        :class="{
          disabled: pageNumber === Math.ceil(allQuestions.length / 10),
        }"
        @click="nextPage()"
      >
        &#62;
      </button>
    </div>
  </section>
</template>

<style scoped>
.inputs {
  display: flex;
  justify-content: flex-end;
  padding-bottom: 32px;
}

.search-input {
  margin-right: 20px;
  border: solid 2px var(--color-inactive);
  border-radius: 4px;
  padding: 8px;
  min-width: 300px;
}

input {
  border: none;
  outline: none;
  font-size: 16px;
  width: 94%;
}

.search-input span {
  color: var(--color-inactive-text);
}

select {
  min-width: 200px;
}

.list {
  padding: 20px 32px 20px 16px;
  border-bottom: solid 1px var(--color-inactive);
  display: flex;
  justify-content: space-between;
}

.side-content {
  width: 25%;
  display: flex;
  justify-content: space-between;
}

.side-content button {
  border: none;
  background: local;
  cursor: pointer;
  margin-right: 12px;
}

.list-header {
  background-color: var(--color-background);
  padding-right: 36px;
}

ul {
  list-style: none;
  padding: 0;
}

.list-nav {
  display: flex;
  justify-content: flex-end;
  margin: 20px 0;
}

.list-nav--button {
  background: local;
  margin-left: 8px;
  height: 32px;
  width: 32px;
  border: solid 1px var(--color-blue);
  color: var(--color-blue);
  cursor: pointer;
}

.disabled {
  border-color: var(--color-inactive);
  color: var(--color-inactive);
  cursor: unset;
}
</style>
