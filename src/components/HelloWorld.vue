<script setup lang="ts">
import { computed } from '@vue/reactivity';
import { ref } from 'vue'

const cells = ref(4);
const rows = ref([] as number[][]);
const tableHeadingCells = computed(() => new Array(cells.value).fill('').map((v,i) => i+1));
const addRows = () => rows.value.push([...tableHeadingCells.value].map(() => 1));
const mergeRow = (rowIndex: number, cellIndex: number) => {
  const rowsList = rows.value;
  const row = rowsList[rowIndex];
  const rowspan = row[cellIndex];
  const nextRow = rowsList[rowIndex + rowspan];
  nextRow.splice(0, 1);
  row[cellIndex] = rowspan + 1;
};
const separateRow = (rowIndex: number, cellIndex: number) => {
  const rowsList = rows.value;
  const row = rowsList[rowIndex];
  const rowspan = row[cellIndex];
  const nextRow = rowsList[rowIndex + rowspan - 1];
  row[cellIndex] = rowspan - 1;
  nextRow.push(1);
};
const print = () => window.print();
addRows();
</script>

<template>
  <div>
    <button @click="addRows">
      Add row
    </button>
    <button @click="print">
      Print
    </button>
  </div>
  <table>
    <thead>
      <tr>
        <th v-for="(tableHeadingCell, index) in tableHeadingCells" :key="index">
          {{tableHeadingCell}}
        </th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="(row, index) in rows" :key="index">
        <td v-for="(rowspan, cellIndex) in row" :key="cellIndex" :rowspan="rowspan">
          {{index}}<br/>
          <button :disabled="rowspan + index === rows.length" @click="mergeRow(index, cellIndex)">
            Merge Row
          </button>
          <button :disabled="rowspan === 1" @click="separateRow(index, cellIndex)">
            separate Row
          </button>
        </td>
      </tr>
    </tbody>
  </table>
</template>

<style scoped>
table, td, th {
  border: 1px solid #000;
  border-collapse: collapse;
  vertical-align: middle;
  text-align: center;
}

tr td:first-child, tr th:first-child {
  background-color: lightgray;
}

@media print {
  thead {
    display: table-row-group;
  }

  tr {
    page-break-inside: avoid;
  }

  td, th {
    page-break-inside: avoid;
  }
}
</style>
