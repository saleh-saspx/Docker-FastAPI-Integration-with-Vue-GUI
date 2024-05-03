<template>
  <div v-show="open" class="fixed bottom-0 right-0 w-full md:bottom-8 md:right-12 md:w-auto z-50">
    <div class="bg-slate-800 border border-transparent dark:border-slate-700 text-slate-50 text-sm p-3 md:rounded shadow-lg flex justify-between">
      <div>👉 <a class="hover:underline" href="#" target="_blank" rel="noreferrer">در صورت مشکل لطفا با توسعه دهنده ارتباط برقرار کنید!</a></div>
      <button class="text-slate-500 hover:text-slate-400 ml-5" @click="open = false">
        <span class="sr-only">Close</span>
        <svg class="w-4 h-4 shrink-0 fill-current" viewBox="0 0 16 16">
          <path d="M12.72 3.293a1 1 0 00-1.415 0L8.012 6.586 4.72 3.293a1 1 0 00-1.414 1.414L6.598 8l-3.293 3.293a1 1 0 101.414 1.414l3.293-3.293 3.293 3.293a1 1 0 001.414-1.414L9.426 8l3.293-3.293a1 1 0 000-1.414z" />
        </svg>
      </button>
    </div>
  </div>
</template>

<script>
import { ref } from 'vue'

export default {
  name: 'Banner',
  props: ['open'],
  setup() {
    const open = ref(true)
    return {
      open,
    }    
  }  
}
</script>