<template>
  <div id="app">
    <div>無限カルーセル</div>
    <div>
      <label for="copyElement">コピー要素の色付け：</label>
      <input v-model="$data.isCheck" id="copyElement" type="checkbox" />
    </div>
    <HelloWorld :isCopyElementColoring="$data.isCheck" />
  </div>
</template>

<script>
import HelloWorld from "./components/HelloWorld";

export default {
  name: "App",
  components: {
    HelloWorld,
  },
  data() {
    return {
      isCheck: true,
    };
  },
};
</script>