<template>
  <div class="h-screen flex flex-col">
    <router-view />
    <nav class="bg-purple-100 fixed bottom-px w-full">
      <router-link to="/" class="px-4 py-2 mr-2 bg-white rounded-md"
        >Weather</router-link
      >
      <router-link to="/disaster" class="px-4 py-2 ml-2 bg-white rounded-md"
        >Disaster</router-link
      >
    </nav>
  </div>
</template>

<style>
nav {
  display: flex;
  justify-content: center;
  padding: 1rem;
  height: 10%;
}
</style>
