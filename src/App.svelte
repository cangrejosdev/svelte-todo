<script>
  import { v1 as uuid } from 'uuid'
  import { Toast } from 'bootstrap'
  import Toastify from 'toastify-js'
  import 'toastify-js/src/toastify.css'

  let toastEl
  let instancia
  $: if (toastEl) {
    instancia = new Toast(toastEl)
  }
  let todos = []
  let todo = {
    id: 1,
    texto: '',
    estado: false,
  }
  let opc = { texto: '', color: '' }

  if (localStorage.getItem('todos')) {
    todos = JSON.parse(localStorage.getItem('todos'))
  }

  $: localStorage.setItem('todos', JSON.stringify(todos))

  const addTodo = () => {
    if (!todo.texto.trim()) {
      console.log('Tarea vacia')
      todo.text = ''
      return
    }
    todo.id = uuid()
    todos = [...todos, todo]
    console.log(todos)
    todo = {
      id: '',
      texto: '',
      estado: false,
    }
    toast('Todo agregado', 'green', 'green')
  }

  const delTodo = (id) => {
    todos = todos.filter((todo) => todo.id !== id)
    toast('Todo eliminado', 'red', 'red')
  }
  const editTodo = (id) => {
    todos = todos.map((todo) =>
      todo.id === id ? { ...todo, estado: !todo.estado } : todo,
    )
    toast('Todo Editado', 'orange', 'orange')
  }

  const classIcono = (valor) =>
    valor ? 'bi bi-arrow-clockwise' : 'bi bi-check2'
  const classColor = (valor) => (valor ? 'btn-success' : 'btn-warning')

  const toast = (texto, colori, colorf) => {
    
    Toastify({
      text: texto,
      duration: 3000,
      close: true,
      className: 'info',
      style: {
      
        background: `linear-gradient(to right, ${colori}, ${colorf})`,
      },
    }).showToast()
  }
</script>

<div class="container">
  <h1 class="display-5 my-5">My Personal Todo -- Svelte Power</h1>
  <form on:submit|preventDefault={addTodo}>
    <input
      class="form-control shadow border-0"
      type="text"
      name="todo"
      id="todo"
      placeholder="Agregar Tarea"
      bind:value={todo.texto} />
  </form>
  {#each todos as todo}
    <div class="shadow my-3 p-3 lead">
      <p class={todo.estado ? 'text-decoration-line-through' : ''}>
        {todo.texto}
      </p>
      <button
        class="btn btn-sm {classColor(todo.estado)}"
        on:click={editTodo(todo.id)}>
        <i class={classIcono(todo.estado)} />
      </button>
      <button class="btn btn-sm btn-danger" on:click={delTodo(todo.id)}>
        <i class="bi bi-trash" />
      </button>
    </div>
  {/each}
</div>
