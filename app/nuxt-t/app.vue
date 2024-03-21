<template>
  <div>
    <div class="flex  space-y-2 mb-[6px]">
      <img src="./images.jpg" alt="Search" class="w-6 h-6 ml-2 mt-3 cursor-pointer">
      <input type="text" v-model="searchOption"
         @input="updateFilteredList" placeholder="Search..." 
         class="border border-solid border-black px-2 py-1 rounded-l"
      >
    </div>
    <table class="table-fixed border border-solid border-black">
      <thead>
        <tr class="space-x-4 border-b border-black">
          <th v-for="(header, index) in headers" class="px-6 py-3 border-black border-r " 
            @click="sortByColumn(index)">{{ header }} 
            <span v-if="sortColumn === index">{{ sortOrder === 'asc' ? '↑' : '↓' }}
            </span>
          </th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="item in sortedList">
          <td v-for="value in item" 
           class="px-6 py-3 border-black border-r">{{ value }}
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';
import data from '../public/test.json';

const list = data;
const searchOption = ref('');
const sortColumn = ref(null);
const sortOrder = ref('asc');

const headers = Object.keys(list[0]);

const filteredList = computed(() => {
  if (!searchOption.value) {
    return list;
  }
  return list.filter(item => {
    return Object.values(item).some(value => {
      return value.toString().toLowerCase().includes(searchOption.value.toLowerCase());
    });
  });
});

const sortedList = computed(() => {
  let sortedData = [...filteredList.value];
  if (sortColumn.value !== null) {
    sortedData.sort((a,b) => {
      const columnA = a[headers[sortColumn.value]];
      const columnB = b[headers[sortColumn.value]];
      if (typeof columnA === 'string' && typeof columnB === 'string') {
        return columnA.localeCompare(columnB) * (sortOrder.value === 'asc' ? 1 : -1);
      } else {
        return (columnA - columnB) * (sortOrder.value === 'asc' ? 1 : -1);
      }
    });
  }
  return sortedData;
});

const updateFilteredList = () => {
  sortColumn.value = null;
};

const sortByColumn = (index) => {
  if (sortColumn.value === index) {
    sortOrder.value = sortOrder.value === 'asc' ? 'desc' : 'asc';
  } else {
    sortColumn.value = index;
    sortOrder.value = 'asc';
  }
};
</script>