<template>
<button @click="save(exelList)" class="btn1">Save and Export to Excel</button>      
</template>



<script setup>
import { useToast } from 'vue-toastification';
import { defineProps } from 'vue';
import { computed } from 'vue'; 
import { ref } from 'vue';




const props = defineProps({
   exelList :{
   type: Array,
    required: true,
   }
});

const emit = defineEmits(['saveAndExport']);
const save = (exelList) => {
emit('saveAndExport', exelList)
};




</script>