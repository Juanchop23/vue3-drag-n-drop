<script setup>
import { reactive } from "vue";
import InputNew from "./InputNew.vue";

//Monitorear el estado de nuestra app
let boards = reactive([
  {
    id: crypto.randomUUID(),
    name: "tablero 1",
    items: [
      {
        id: crypto.randomUUID(),
        title: "Feature de archivos",
      },
      {
        id: crypto.randomUUID(),
        title: "Resolver bug",
      },
    ],
  },
  {
    id: crypto.randomUUID(),
    name: "tablero 2",
    items: [
      {
        id: crypto.randomUUID(),
        title: "mandar reporte",
      },
      {
        id: crypto.randomUUID(),
        title: "Code review",
      },
    ],
  },
]);

//Insertar datos a la variable boards
function handleNewItem(text, board) {
  // console.log(text.value, board.id, board.name)
  board.items.push({
    id: crypto.randomUUID(),
    title: text.value,
  });
}

//Añadir nuevo tablero a la UI
function handleNewBoard() {
  const name = prompt("Name of the board");
  if (!!name) {
    boards.push({
      id: crypto.randomUUID(),
      name: name,
      items: [],
    });
  }
}

//Comenzar transferencia de datos
function startDrag(event, board, item) {
  event.dataTransfer.setData(
    "text/plain",
    JSON.stringify({ boardId: board.id, itemId: item.id })
  );
}

function onDrop(event, dest) {
  const { boardId, itemId } = JSON.parse(
    event.dataTransfer.getData("text/plain")
  );

  //Buscar el elemento que sea igual al boardId
  const originBoard = boards.find((item) => item.id == boardId);
  //Encontrar el elemento que sea igual a item.id
  const originItem = originBoard.items.find((item) => item.id === itemId);

  //Actualizar el tablero destino
  dest.items.push({ ...originItem });
  originBoard.items = originBoard.items.filter(
    (item) => item !== originItem
  );
}
</script>

<template>
  <nav>
    <ul>
      <li><a href="#" @click.prevent="handleNewBoard">Create board</a></li>
    </ul>
  </nav>

  <div class="boards-container">
    <div class="boards">
      <div
        class="board"
        @drop="onDrop($event, board)"
        @dragover.prevent
        @dragenter.prevent
        v-for="board in boards"
        :key="board.id"
      >
        <div>
          {{ board.name }}
        </div>
        <InputNew @on-new-item="(text) => handleNewItem(text, board)" />
        <div class="items">
          <div
            class="item"
            draggable="true"
            @dragstart="startDrag($event, board, item)"
            v-for="item in board.items"
            :key="item.id"
          >
            {{ item.title }}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.boards {
  display: flex;
  gap: 10px;
}

.board {
  background: #efefef;
  padding: 10px;
}

.items {
  display: flex;
  flex-direction: column;
  gap: 5px;
}

.item {
  background-color: white;
  padding: 10px;
  box-sizing: border-box;
}
</style>
