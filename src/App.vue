<script setup lang="ts">
  import { ref, Ref } from "vue"
  import { Subject } from "rxjs"
  import { filter } from "rxjs/operators"

  const count = ref(0)
  const evenCount = ref(0)
  const errorMessage = ref("")
  const source: Ref<Subject<number> | null> = ref(null)

  const buildCounter = () => {
    source.value = new Subject();
    count.value = 0;
    evenCount.value = 0;

    const evenSource = source.value!.pipe(
      filter((countNumber: number) => {
        return countNumber % 2 === 0;
      }),
    );

    source.value.subscribe((data: number) => {
      count.value = data;
    });

    evenSource.subscribe((evenCounterNumber: number) => {
      evenCount.value = evenCounterNumber;
    });
  }

  const startCount = () => {
    if (!source.value) {
      console.error("please generate a counter instance");
      return;
    }

    source.value.next(++count.value);
  }

  const generateError = () => {
    if (!source.value) {
      console.error("need to build counter instance");
      return;
    }
    if (errorMessage.value === "") {
      console.error("need to input error message");
      return;
    }
    source.value.error(errorMessage.value);
  } 

  const complete = () => {
    if (!source.value) {
      console.error("need to build counter instance");
      return;
    }
    source.value.complete();
    console.log("console complete");
  }

</script>

<template>
  <div class="flex flex-col space-y-2 px-4 mt-32">
    <button @click="buildCounter" class="px-3 py-2 rounded-xl bg-blue-400 hover:(bg-blue-800 text-white)">
      Build Counter
    </button>
    <button @click="startCount" class="px-3 py-2 rounded-xl bg-blue-400 hover:(bg-blue-800 text-white)">
      Start Count
    </button>
    <button @click="generateError" class="px-3 py-2 rounded-xl bg-red-400 hover:(bg-red-800 text-white)">
      Generate Error
    </button>
    <button @click="complete" class="px-3 py-2 rounded-xl bg-red-400 hover:(bg-red-800 text-white)">
      Complete
    </button>
    <input v-model="errorMessage" type="text" placeholder="Error Message"/>
    <p>{{ count }}</p>
    <p>{{ evenCount }}</p>
  </div>

</template>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
</style>
