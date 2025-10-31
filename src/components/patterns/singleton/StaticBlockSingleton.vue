<template>
  <div>
    <h2>Singleton Pattern Example : Static block initialization </h2>
  </div>
</template>

<script lang="ts">

class Singleton{
  private static instance: Singleton;

  private constructor() {}

  static {
    try {
      Singleton.instance = new Singleton();
      console.log("✅ 싱글톤 인스턴스가 정상적으로 생성되었습니다.");
    } catch (error) {
      throw new Error("싱글톤 객체 생성 오류");
    }
  }

  public static getInstance() : Singleton {
    return Singleton.instance;
  }

  public greet(): void {
    console.log("안녕하세요! 저는 static block으로 생성된 싱글톤입니다 👋");
  }
}
export default {
  name: 'StaticBlockSingleton',

  setup() {

    const s1 = Singleton.getInstance();
    const s2 = Singleton.getInstance();

    s1.greet();

    console.log(s1 === s2);
  }
}
</script>

<style scoped>

</style>