<template>
  <div class="footer">
    <div class="title d-flex justify-content-center h-100">
      <div class="row m-0 p-0 w-100 m-auto text-center">
        <div class="col-4"></div>
        <div class="col-4">
          <strong class="m-auto">{{ msg }}</strong>
        </div>
        <div class="col-4"></div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Footer",
  props: {
    msg: {
      type: String,
      default: "rheavsiion",
    },
  },
};
</script>

<style lang="scss" scoped>
a {
  color: black;
}

a:hover {
  color: grey;
}

.title {
  font-size: 15px;
}

.footer {
  height: 50px;
  border-top: 1px solid lightgrey;
}
</style>
