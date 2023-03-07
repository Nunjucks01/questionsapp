<script setup>
import { RouterLink } from "vue-router";
</script>

<template>
  <header>
    <div class="logo"><strong>PGS Upskill</strong></div>
    <nav>
      <ul>
        <li>
          <RouterLink active-class="active" to="/">Home</RouterLink>
        </li>
        <li>
          <RouterLink active-class="active" to="/create">Create</RouterLink>
        </li>
      </ul>
    </nav>
  </header>
</template>

<style scoped>
header {
  background-color: var(--color-header);
  display: flex;
  margin-bottom: 16px;
}

.logo {
  padding: 24px 64px;
  color: var(--color-white);
}

ul {
  list-style: none;
  display: flex;
  padding: 0;
}

li {
  padding: 24px 0px;
}

li:hover {
  background-color: var(--color-blue);
}

a {
  text-decoration: none;
  color: var(--color-white);
  padding: 26px;
}

.active {
  background-color: var(--color-blue);
}
</style>
