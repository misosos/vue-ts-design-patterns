<template>
  <div>
    <h2>Singleton Pattern Example : Lazy Initialization </h2>
  </div>
</template>

<script lang="ts">
export default{
  name: 'LazySingleton',

  setup() {
    class Singleton {
      private static instance: Singleton | null = null;

      private constructor() {
      }

      public static getInstance(): Singleton {
        if (Singleton.instance === null) {
          Singleton.instance = new Singleton();
        }
        return Singleton.instance;
      }

      public say(): void {
        console.log("lazy로 생성된 싱글톤입니다 👋");
      }
    }

    const a = Singleton.getInstance();
    const b = Singleton.getInstance();
    console.log(a === b); // true
    a.say();
  }
}
</script>

<style scoped>

</style>