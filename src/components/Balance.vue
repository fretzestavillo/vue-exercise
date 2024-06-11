<template>
 <div class="inc-exp-container">
        <div>
          <h4>Your Balance</h4>
          <p id="balance" >{{ total }}</p>
        </div>
        <div>
          <h4>Cash on Hand</h4>
          <p id="balance" >{{ total }}</p>
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
   
});
</script>
