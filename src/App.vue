<template>
  <div class="row">
    <div v-for="(column, index) in columns" :key="index" :class="'col-' + column.width">
      <h3>
        {{ column.title }}
        <button class="btn btn-danger btn-sm ml-2" @click="removeColumn(index)">Remove</button>
      </h3>

      <draggable
        :list="column.list"
        class="list-group"
        :group="dragGroup"
        item-key="id"
        @change="handleDragChange"
      >
        <template #item="{ element }">
          <div class="list-group-item">
            {{ element.name }} - {{ element.description }}
          </div>
        </template>

        <template #footer>
          <div class="btn-group list-group-item" role="group">
            <button class="btn btn-secondary" @click="addColumnItem(index)">Add</button>
            <button class="btn btn-secondary" @click="replaceColumnItem(index)">Replace</button>
          </div>
        </template>
      </draggable>

      <rawDisplayer :value="column.list" :title="column.title" />
    </div>

    <div class="col-4">
      <h3>Add Column</h3>
      <form @submit.prevent="addNewColumn">
        <div class="form-group">
          <label for="columnName">Column Name:</label>
          <input v-model="newColumn.name" type="text" id="columnName" class="form-control" required />
        </div>
        <div class="form-group">
          <label for="columnWidth">Column Width:</label>
          <input v-model.number="newColumn.width" type="number" id="columnWidth" class="form-control" required />
        </div>
        <button type="submit" class="btn btn-primary">Add Column</button>
      </form>

      <!-- Add item form -->
      <h3 class="mt-3">Add Item</h3>
      <form @submit.prevent="addNewItem">
        <div class="form-group">
          <label for="itemName">Item Name:</label>
          <input v-model="newItem.name" type="text" id="itemName" class="form-control" required />
        </div>
        <div class="form-group">
          <label for="itemDescription">Item Description:</label>
          <input v-model="newItem.description" type="text" id="itemDescription" class="form-control" required />
        </div>
        <div class="form-group">
          <label for="itemColumn">Select Column:</label>
          <select v-model="newItem.columnIndex" id="itemColumn" class="form-control">
            <option v-for="(column, index) in columns" :key="index" :value="index">{{ column.title }}</option>
          </select>
        </div>
        <button type="submit" class="btn btn-primary">Add Item</button>
      </form>
    </div>
  </div>
</template>

<script>
import draggable from "vuedraggable";

let id = 1;

export default {
  name: "dynamic-columns",
  data() {
    return {
      columns: [
        { title: "First Column", list: [], width: 4 },
        { title: "Second Column", list: [], width: 4 },
      ],
      newColumn: {
        name: "",
        width: 4,
      },
      newItem: {
        name: "",
        description: "",
        columnIndex: 0,
      },
      dragGroup: "columnGroup", 
    };
  },
  methods: {
    addColumnItem(index) {
      this.columns[index].list.push({
        name: "Juan " + id,
        description: "Description " + id,
        id: id++,
      });
    },
    replaceColumnItem(index) {
      this.columns[index].list = [
        {
          name: "Edgard",
          description: "New Description",
          id: id++,
        },
      ];
    },
    addNewColumn() {
      if (!this.newColumn.name || this.newColumn.width <= 0) {
        alert("Please enter a valid column name and width.");
        return;
      }

      this.columns.push({
        title: this.newColumn.name,
        list: [],
        width: this.newColumn.width,
      });

      this.newColumn.name = "";
      this.newColumn.width = 4;
    },
    removeColumn(index) {
      this.columns.splice(index, 1);
    },
    addNewItem() {
      if (!this.newItem.name || !this.newItem.description) {
        alert("Please enter a valid item name and description.");
        return;
      }

      this.columns[this.newItem.columnIndex].list.push({
        name: this.newItem.name,
        description: this.newItem.description,
        id: id++,
      });

      this.newItem.name = "";
      this.newItem.description = "";
    },
    handleDragChange(evt) {
      const draggedColumnIndex = parseInt(evt.from.getAttribute("data-col-index"), 10);
      const targetColumnIndex = parseInt(evt.to.getAttribute("data-col-index"), 10);

      if (draggedColumnIndex !== targetColumnIndex) {
        const draggedItem = this.columns[draggedColumnIndex].list.splice(evt.oldIndex, 1)[0];
        this.columns[targetColumnIndex].list.splice(evt.newIndex, 0, draggedItem);
      }
    },
  },
  components: {
    draggable,
  },
};
</script>

<style scoped>
*{
  margin: 0;
  padding: 0;
}
.row{
  display: flex;
  justify-content: space-evenly;
  width: 100%;
}
</style>
