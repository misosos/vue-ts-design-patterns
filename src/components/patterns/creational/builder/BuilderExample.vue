<template>
  <div class="builder-container">
    <h1 class="title">커피 주문건</h1>
    <!-- 라떼 주문 정보 표시 -->
    <div class="coffee-section">
      <h2>라떼 주문</h2>
      <p v-if="latte">
        사이즈: {{ latte.size }} | 온도: {{ latte.temp }} | 샷 추가: {{ latte.shots }}번 | 우유: {{ latte.milkMl }}ml
        <span v-if="latte.iceCubes"> | 얼음 추가: {{ latte.iceCubes }}컵</span>
      </p>
    </div>
    <!-- 아메리카노 주문 정보 표시 -->
    <div class="coffee-section">
      <h2>아메리카노 주문</h2>
      <p v-if="americano">
        사이즈: {{ americano.size }} | 온도: {{ americano.temp }} | 샷 추가: {{ americano.shots }}번 | 물: {{ americano.waterMl }}ml
        <span v-if="americano.iceCubes"> | 얼음 추가: {{ americano.iceCubes }}컵</span>
      </p>
    </div>
  </div>
</template>

<script lang="ts">
import { ref } from 'vue';

// 커피 사이즈와 온도 타입 정의
type Size = 'Short'|'Tall'|'Grande'|'Venti';
type Temp = 'Hot'|'Ice';

// 추상 빌더 클래스: 커피 빌더의 기본 메서드 정의
abstract class CoffeeBuilder{
  //필수 옵션
  abstract setSize(size: Size): void;
  abstract setTemperature(temp: Temp): void;
  abstract pullEspressoShot(count: number): void;

  abstract reset() : void;
  abstract getResult() : Coffee;

  //선택 옵션
  addIce(_iceCubes: number): void {}
  addWater(_waterMl: number): void {}
  addMilk(_milkMl: number): void {}
}

// 기본 커피 빌더: 공통 기능 구현 및 상태 관리
abstract class BaseCoffeeBuilder extends CoffeeBuilder {
  protected coffee!: Coffee;
  protected abstract label: string;

  constructor() {
    super();
    this.reset();
  }

  reset(): void {
    this.coffee = new Coffee();
  }

  setSize(size: Size): void {
    this.coffee.size = size;
    this.coffee.result.push(`${this.label} size set to: ${size}`);
  }

  setTemperature(temp: Temp): void {
    this.coffee.temp = temp;
    this.coffee.result.push(`${this.label} temperature set to: ${temp}`);
  }

  pullEspressoShot(count: number): void {
    this.coffee.shots = count;
    this.coffee.result.push(`Pulled ${count} espresso shot(s) for ${this.label}`);
  }

  addIce(iceCubes: number): void {
    this.coffee.iceCubes = iceCubes;
    this.coffee.result.push(`Added ${iceCubes} ice cube(s) to ${this.label}`);
  }

  getResult(): Coffee {
    return this.coffee;
  }
}

// 커피 클래스
class Coffee{
  public size!: Size;
  public temp!: Temp;
  public shots!: number;
  public milkMl?: number;
  public waterMl?: number;
  public iceCubes?: number;
  public type!: string;
  public result: string[] = [];
}

// 라떼 빌더: 라떼 전용 옵션 추가
class LatteBuilder extends BaseCoffeeBuilder {
  protected label = 'Latte';

  reset(): void {
    super.reset();
    this.coffee.type = 'Latte';
  }

  addMilk(milkMl: number): void {
    this.coffee.milkMl = milkMl;
    this.coffee.result.push(`Added ${milkMl}ml milk to ${this.label}`);
  }
}

// 아메리카노 빌더: 아메리카노 전용 옵션 추가
class AmericanoBuilder extends BaseCoffeeBuilder {
  protected label = 'Americano';

  reset(): void {
    super.reset();
    this.coffee.type = 'Americano';
  }

  addWater(waterMl: number): void {
    this.coffee.waterMl = waterMl;
    this.coffee.result.push(`Added ${waterMl}ml water to ${this.label}`);
  }
}

// 주문 옵션 타입
type LatteOptions = { size: Size; temp: Temp; shots: number; milkMl: number; iceCubes?: number };
type AmericanoOptions = { size: Size; temp: Temp; shots: number; waterMl: number; iceCubes?: number };

// 바리스타 디렉터: 커피 빌더 조립 과정 관리
class BaristaDirector {
  makeLatte(builder: CoffeeBuilder, options: LatteOptions) {
    builder.setSize(options.size);
    builder.setTemperature(options.temp);
    builder.pullEspressoShot(options.shots);
    builder.addMilk(options.milkMl);
    if (options.temp === 'Ice') builder.addIce(options.iceCubes ?? 0);
  }
  makeAmericano(builder: CoffeeBuilder, options: AmericanoOptions) {
    builder.setSize(options.size);
    builder.setTemperature(options.temp);
    builder.pullEspressoShot(options.shots);
    builder.addWater(options.waterMl);
    if (options.temp === 'Ice') builder.addIce(options.iceCubes ?? 0);
  }
}

export default{
  name:"BuilderExample",

  // Vue setup 함수: 상태 관리 및 초기 주문 실행
  setup(){
    const latte = ref<Coffee | null>(null);
    const americano = ref<Coffee | null>(null);

    function order() {
      const director = new BaristaDirector();
      const latteBuilder = new LatteBuilder();

      director.makeLatte(latteBuilder, {
        size: 'Tall',
        temp: 'Ice',
        shots: 2,
        milkMl: 150,
        iceCubes: 3,
      });
      console.log('Latte Result:', latteBuilder.getResult().result);
      latte.value = latteBuilder.getResult();

      const americanoBuilder = new AmericanoBuilder();
      director.makeAmericano(americanoBuilder, {
        size: 'Grande',
        temp: 'Hot',
        shots: 3,
        waterMl: 200,
      });
      console.log('Americano Result:',americanoBuilder.getResult().result);
      americano.value = americanoBuilder.getResult();
    }
    order();

    return {
      latte,
      americano,
    };
  }
}
</script>

<style scoped>
.builder-container {
  background: #ffd189;
  border-radius: 12px;
  padding: 2rem;
}
.title {
  text-align: center;
  font-size: 1.8rem;
  font-weight: bold;
  margin-bottom: 2rem;
}
.coffee-section {
  margin-bottom: 1.5rem;
  padding: 1rem;
  border-radius: 8px;
  background: white;
}
.coffee-section h2 {
  font-size: 1.3rem;
  color: #604949;
}
.coffee-section p {
  font-size: 1rem;
  color: #000000;
}
</style>