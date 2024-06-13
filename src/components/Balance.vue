<template>
 <div class="inc-exp-container">
        <div>
          <h4>GCash Balance</h4>
          <p :style="{ color: total < 0 ? 'red' : 'black' }" id="balance">{{ total }}</p>
        </div>

       
        <div>
          <h4>Cash on Hand</h4>
          <!-- <p id="balance" >{{ cashtotal }}</p> -->
           <p :style="{ color: cashtotal < 0 ? 'red' : 'black' }" id="balance">{{ cashtotal }}</p>
        </div>
      </div>
</template>







<script setup>
import { defineProps } from 'vue';

const props = defineProps({
   total: {
    type: Number,
    required: true,
   },
   cashtotal: {
    type: Number,
    required: true,
   },
   
});
</script>
