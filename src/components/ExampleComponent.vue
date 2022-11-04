<template>
  <div>
    <ul>
      <li v-for="(todo, id) in msgs" :key="id" @click="increment">
        {{ todo }}
      </li>
    </ul>
    <q-btn
      v-for="(b, i) in buttons"
      :key="i"
      color="primary"
      icon="mail"
      :label="b.label"
      @click="doc(b)"
    />
    <input v-model=" input" placeholder="edit me">
    <p>Message is: {{  }}</p>
  </div>
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
function useDisplayTodo(todos: Ref<Todo[]>) {
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
    const data = ref({ msg: '', ID: 1 });
    const arr: any[] = [];
    const msgs = reactive(arr);

    function doc(option: any) {
      if (option) msgs.push(option);
      axios
        .post('http://localhost:5000/', {
          option: option.next ? option.next : '',
          ID: data.value.ID,
        })
        .then((r: any) => {
          msgs.push(r.data.msg);

          buttons.value = r.data.options;
          data.value = r.data;
        });

    }
    doc('');
    return {
      doc,
      msgs,
      buttons,
      data,
      ...useClickCount(),
      ...useDisplayTodo(toRef(props, 'todos')),
    };
  },
});
</script>
