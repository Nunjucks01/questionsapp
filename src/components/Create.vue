<script setup>
import { ref } from "vue";
import { useRoute } from "vue-router";
import axios from "axios";

const baseUrl = ref("http://localhost:3000/questions");

const questions = ref(
  axios.get(baseUrl.value).then((response) => (questions.value = response.data))
);

const route = useRoute();

let id = questions.value.length;

const questionName = ref("");

const questionType = ref("Please select question type");

if (route.path === `/edit/${route.params.id}`) {
  axios.get(`${baseUrl.value}/${route.params.id}`).then((response) => {
    (questionName.value = response.data.title),
      (questionType.value = response.data.level);
  });
}

const createQuestion = () => {
  axios.post(baseUrl.value, {
    id: id++,
    title: questionName.value,
    level: questionType.value,
  });
};

const editQuestion = () => {
  axios.patch(`${baseUrl.value}/${route.params.id}`, {
    title: questionName.value,
    level: questionType.value,
  });
};

const onChange = (type) => {
  questionType.value = type.target.value;
};
</script>

<template>
  <div class="path"><span>Home &nbsp/&nbsp</span> Create question</div>
  <section class="create">
    <h2>Create question</h2>
    <form>
      <label for="question-name"><span>*</span> Question name </label>
      <input v-model="questionName" type="text" id="question-name" required />
      <label for="question-type"><span>*</span> Question type</label>
      <select @change="onChange($event)" name="" id="question-type" required>
        <option value="" hidden>{{ questionType }}</option>
        <option value="junior">junior</option>
        <option value="middle">middle</option>
        <option value="senior">senior</option>
      </select>
      <div class="buttons">
        <button
          class="button-create"
          @click="createQuestion()"
          :disabled="
            questionType === 'Please select question type' ||
            questionName === ''
          "
          :class="{
            disabled:
              questionType === 'Please select question type' ||
              questionName === '',
            hidden: route.path === `/edit/${route.params.id}`,
          }"
        >
          Create
        </button>
        <RouterLink
          to="/"
          :class="{ hidden: route.path !== `/edit/${route.params.id}` }"
        >
          <button
            class="button-create"
            :disabled="questionName === ''"
            :class="{ disabled: questionName === '' }"
            @click="editQuestion()"
          >
            Save
          </button>
        </RouterLink>
        <RouterLink to="">
          <button>Cancel</button>
        </RouterLink>
      </div>
    </form>
  </section>
</template>

<style scoped>
.path {
  max-width: 1400px;
  margin: auto;
  position: relative;
}
span {
  color: var(--color-inactive-text);
  font-weight: 700;
}

h2 {
  margin-bottom: 44px;
}

.create {
  margin-top: 16px;
}
.create span {
  color: red;
}

input,
select {
  width: 100%;
  padding: 8px;
  margin-top: 8px;
  margin-bottom: 24px;
  border: solid 2px var(--color-inactive);
  border-radius: 4px;
  font-size: 16px;
}

.buttons {
  margin-bottom: 32px;
}

button {
  border-color: var(--color-inactive);
  background: local;
  cursor: pointer;
  width: 86px;
  height: 40px;
  margin-right: 12px;
}

.button-create {
  background-color: var(--color-blue);
  color: var(--color-white);
}

.disabled {
  background-color: var(--color-inactive);
  cursor: unset;
}

a {
  cursor: unset;
}

.hidden {
  display: none;
}
</style>
