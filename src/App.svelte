<script>
	// IMPORTAMOS EL MODAL DE BOOTSTRAP 5 PARA PRESENTAR UN MENSAJE SEGUN LA ACCION
  import { Toast } from "bootstrap";
	// HACEMOS REFERENCIA DESDE EL BIND DEL DOM CON ESTA VARIABLE
  let toastEl;
	// CREAMOS LAS OPCIONES A CAMBIAR SEGUN LA ACCION
  let opc = { text: "", color: "" };
	// FUNCION PARA LLAMAR EL MODAL LE PASAMOS TANTO EL TEXTO COMO EL COLOR DEL MODAL A PRESENTAR ESTAS VIENEN SEGUN LA ACCION
  const mostrarMensaje = (text, color) => {
    opc = {
      text: text,
      color: color,
    };
	// CREAMOS UNA NUEVA INSTANCIA ADA VEZ QUE SE REALIZE UNA OPCION DE NUESTRO CRUD
    new Toast(toastEl).show();
  };
//   ALMACENAMOS TODOS LAS TAREAS EN ESTE ARRAY
  let todos = [];
//   LE DAMOS SU FORMATO A CADA TAREA
  let todo = { id: "", texto: "", estado: false };
//  TRABAJAMOS CON EL LOCALSTORAGE, ALMACENAMOS EN LA VARIABLE LO QUE ESTE EN ESE MOMENTO EN EL LOCALSTORAGE
  if (localStorage.getItem("todos")) {
	//   CONVERTIMOS O PARSEAMOS LOS DATOS TRAIDOS DEL LOCALSTORAGE
    todos = JSON.parse(localStorage.getItem("todos"));
  }
// AGREGAMOS AL LOCALSTORAGE LA TAREA Y DE IGUAL FORMA LO PARSEAMOS 
  $: localStorage.setItem("todos", JSON.stringify(todos));
// METODO PARA AGREGAR UNA TAREA
  const addTodo = () => {
	//   VERIFICAMOS SI NO HAY TEXTO, SI ES VERDAD NO LO GUARDA Y MANDA UN MENSAJE EN EL MODAL
    if (!todo.texto.trim()) {
    //   console.log("TEXTO VACIO NO VALIDO");
      todo.texto = "";
	  mostrarMensaje("NO HAY NADA PARA GUARDAR", "danger");
      return;
    }
//  CASO CONTRARIO GUARDA LA TAREA PERO CON UN ID DE EJEMPLO YA QUE SOLO ESTOY TRABAJANDO CON LOCALSTORAGE
    todo.id = Date.now();
	// ALMACENA EN EL ARRAY NUESTRA NUEVA TAREA
    todos = [...todos, todo];
    // console.log(todos);
	//  FORMATEAMOS EL VALOR DE LAS VARIABLES YA ALMACENADAS
    todo = { id: "", texto: "", estado: false };
	mostrarMensaje("Tarea Agregada", "primary");
  };
// METODO PARA ELIMINAR UNA TAREA
  const delTodo = (id) => {
	//   FILTRA UN NUEVO ARRAY PERO SIN EL QUE HEMOS ELIMINADO
    todos = todos.filter((item) => {
      return item.id !== id;
    });
	mostrarMensaje("Tarea Eliminada", "danger");
  };
// EDITA NUESTRA TAREA
  const editTodo = (id) => {
	//   DE IGUAL FORMA GUARDA UN NUEVO ARRAY CON LOS NUEVOS VALORES AGREGADOS
    todos = todos.map((item) => {
      if (item.id === id) {
        return {
          ...item,
          estado: !item.estado,
        };
      } else {
        return item;
      }
    });
	mostrarMensaje("Tarea Editada", "warning");
  };
</script>
<!-- HTML -->
<!-- FALTA METERLE DISEÑITO - ESO QUEDA PARA MAS TARDE -->
<h1 class="text-center mt-2 mb-2" style="color:brown;">TAREAS</h1>
<main class="container mt-4">
  <form on:submit|preventDefault={addTodo}>
    <input
      type="text"
      placeholder="Enter para agregar tarea"
      class="form-control shadow border-0"
      bind:value={todo.texto}
    />
  </form>
  {#each todos as item}
    <div class="shadow my-3 p-3 lead">
      <p class={item.estado ? "text-decoration-line-through" : ""}>
        {item.texto}
      </p>
      <button class="btn btn-sm btn-warning" on:click={editTodo(item.id)}
        ><i class="bi bi-pencil-square" /></button
      >
      <button class="btn btn-sm btn-danger" on:click={delTodo(item.id)}
        ><i class="bi bi-trash" /></button
      >
    </div>
  {/each}
  <div class="toast-container position-absolute top-0 end-0 p-3">
    <div
      bind:this={toastEl}
      class="toast align-items-center text-white bg-{opc.color} border-0"
      role="alert"
      aria-live="assertive"
      aria-atomic="true"
    >
      <div class="d-flex">
        <div class="toast-body">{opc.text}</div>
        <button
          type="button"
          class="btn-close btn-close-white me-2 m-auto"
          data-bs-dismiss="toast"
          aria-label="Close"
        />
      </div>
    </div>
  </div>
</main>
