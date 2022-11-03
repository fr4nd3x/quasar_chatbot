<template>
  <div>
    <p>{{ title }}</p>
    <ul>
      <li v-for="todo in todos" :key="todo.id" @click="increment">
        {{ todo.id }} - {{ todo.content }}
      </li>
    </ul>
    {{ msgs }}
    <q-btn color="primary"  label="Activar alerta" @click="soss" />

    <q-btn color="primary" icon="mail" label="DOCUMENTO" @click="doc" />

    <q-btn color="primary" icon="mail" label="CONTACTO" @click="contact" />

    {{ data.msg }}

    <q-btn
      v-for="(b, i) in buttons"
      :key="i"
      color="primary"
      icon="mail"
      :label="b"
      @click="gere(b), ambi(b)"
    />



    <p>Count: {{ todoCount }} / {{ meta.totalCount }}</p>
    <p>Active: {{ active ? 'yes' : 'no' }}</p>
    <p>Clicks on todos: {{ clickCount }}</p>
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

function soss() {
  axios.get('http://localhost:5000/1').then((r: any) => {
    alert(r.data);
  });
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
    const data = ref({ msg: '', ID: 2 });
    const arr: any[] = [];
    const msgs = reactive(arr);
    function contact(option: any) {
      axios
        .post('http://localhost:5000/contact', {
          option: option.target ? option : '',
          ID: data.value.ID,
        })
        .then((r: any) => {
          msgs.push(['' + buttons.value, '' + data.value]);
          buttons.value = r.data.options;
          data.value = r.data;
        });
    }

    function ambi(option: any) {
      axios
        .post('http://localhost:5000/ambi', {
          option: option.target ? option : '',
          ID: data.value.ID,
        })
        .then((r: any) => {
          buttons.value = r.data.options;
          //msgs.push(['' + buttons.value, '' + data.value]);
          data.value = r.data;
        });
    }
    function gere(option: any) {
      axios
        .post('http://localhost:5000/gere', {
          option: option.target ? option : '',
          ID: data.value.ID,
        })
        .then((r: any) => {
          buttons.value = r.data.options;
          //msgs.push(['' + buttons.value, '' + data.value]);
          data.value = r.data;
        });
    }

    function doc(option: any) {
      axios
        .post('http://localhost:5000/', {
          option: option.target ? option : '',
          ID: data.value.ID,
        })
        .then((r: any) => {
          buttons.value = r.data.options;
          data.value = r.data;
        });
    }

    return {
      msgs,
      buttons,
      data,
      ambi,
      gere,
      doc,
      soss,
      contact,
      ...useClickCount(),
      ...useDisplayTodo(toRef(props, 'todos')),
    };
  },
});
</script>
