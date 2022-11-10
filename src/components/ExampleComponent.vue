<template>
  <div class="image">
    <img src="/bot.jpg"/>
  </div>
  <div>
    <div
      v-for="(item, id) in msgs"
      :key="id"
      @click="increment"
      class="msg"
      :class="item.label ? 'yellow' : ''"
    >
      <div v-html="item.msg"></div>

      <a v-if="item.href" :href="item.href">{{ item.href }}</a>
    </div>
  </div>

  <q-btn
    v-for="(b, i) in buttons"
    :key="i"
    color="primary"
    :label="b.label"
    @click="doc(b)"
  />

  <p>Message is: {{}}</p>
</template>

<script lang="ts">
import { defineComponent, PropType, computed, ref, toRef, reactive } from 'vue';
import axios from 'axios';
import { Todo, Meta } from './models';

function useClickCount() {
  const clickCount = ref(0);
  function increment() {
    clickCount.value += 1;
    return clickCount.value;
  }

  return { clickCount, increment };
}
function useDisplayTodo(todos: ref<Todo[]>) {
  const todoCount = computed(() => todos.value.length);
  return { todoCount };
}
export default defineComponent({
  name: 'ExampleComponent',
  props: {
    title: {
      type: String,
      required: true,
    },
    todos: {
      type: Array as PropType<Todo[]>,
      default: () => [],
    },
    meta: {
      type: Object as PropType<Meta>,
      required: true,
    },
    active: {
      type: Boolean,
    },
  },

  setup(props) {
    const buttons = ref([]);
    const hola = ref('<b>Tron</b>');
    const data = ref({ msg: '', ID: 1,continue : '' });
    const arr: any[] = [];
    const msgs = reactive(arr);

    function doc(option: any) {
      if (option) {
        option.msg = option.label;
        msgs.push(option);
      }

      axios
        .post('http://localhost:5000/', {
          option: option.next ? option.next : '',

          ID: data.value.ID,
        })
        .then((r: any) => {
          msgs.push(r.data);

          buttons.value = r.data.options;
          data.value = r.data;
        });
    }
    doc('');

    return {
      doc,
      hola,
      msgs,
      buttons,
      data,
      ...useClickCount(),
      ...useDisplayTodo(toRef(props, 'todos')),
    };
  },
});
</script>

