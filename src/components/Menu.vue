<template>
    <div class="menu">
      <div
        @click='$emit("reset")'
      >
        <span>Home</span>
      </div>
  </div>
</template>

<style scoped>
.menu {
  color: white;
  font-size: 20px;
  margin-top: 2%;
  margin-left: 3%;
  cursor: pointer;
  width: 0;
  height: 0;
  border-top: 30px solid transparent;
  border-right: 80px solid rgb(25,141,195,0.5);
  border-bottom: 30px solid transparent;
}
.menu:hover {
  color: rgb(230, 0, 120);
  text-shadow: 0 0 15px rgb(230, 0, 120);
}
</style>