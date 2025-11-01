<template>
  <div class="facade-container">
    <h1 class="title">결제 시뮬레이터</h1>

    <div class="amount-row">
      <label for="amount">결제 금액</label>
      <input id="amount" type="number" v-model.number="amount" min="0" />
      <button class="small-btn" @click="run">실행</button>
    </div>

    <div class="btn-group">
      <button @click="useKakao">카카오페이</button>
      <button @click="useNaver">네이버페이</button>
      <button @click="useToss">토스페이</button>
    </div>

    <div class="log-panel">
      <p>{{ message }}</p>
    </div>
  </div>
</template>

<script lang="ts">

interface Strategy {
  pay(amount: number): { method: string; fee: number; total: number };
}

abstract class BasePayStrategy implements Strategy {
  private readonly rate: number;
  private readonly label: string;

  protected constructor(rate: number, label: string) {
    this.rate = rate;
    this.label = label;
  }

  pay(amount: number): { method: string; fee: number; total: number } {
    const fee = this.rate * amount; // 수수료 계산
    const total = amount + fee;     // 총 결제금액 계산
    return {
      method: this.label, // 결제 수단명
      fee: fee,           // 계산된 수수료
      total: total        // 최종 결제금액
    };
  }
}

class KakaoPay extends BasePayStrategy {
  constructor() {
    super(0.0172, 'KakaoPay');
  }
}

class NaverPay extends BasePayStrategy {
  constructor() {
    super(0.0222, 'NaverPay');
  }
}

class TossPay extends BasePayStrategy {
  constructor() {
    super(0.0180, 'TossPay');
  }
}

class PaymentProcessor{
  private strategy: Strategy;
  constructor(strategy: Strategy) {
    this.strategy = strategy;
  }
  setStrategy(strategy: Strategy): void {
    this.strategy = strategy;
  }
  execute(amount: number): string {
    const result = this.strategy.pay(amount);
    return `결제수단: ${result.method}, 수수료: ${result.fee.toFixed(0)}원, 총 결제금액: ${result.total.toFixed(0)}원`;
  }
}

import { ref } from 'vue';
export default{
  name: 'strategyExample',

  setup() {
    const processor = new PaymentProcessor(new KakaoPay());
    const amount = ref(10000);
    const message = ref('');

    const run = () => {
      message.value = processor.execute(amount.value);
    };

    const useKakao = () => {
      processor.setStrategy(new KakaoPay());
      run();
    };
    const useNaver = () => {
      processor.setStrategy(new NaverPay());
      run();
    };
    const useToss = () => {
      processor.setStrategy(new TossPay());
      run();
    };

    return {
      amount,
      message,
      run,
      useKakao,
      useNaver,
      useToss
    };
  }
}
</script>

<style scoped>
.facade-container {
  padding: 2rem;
  background: #f9f9f9;
  border-radius: 12px;
  text-align: center;
}

.title {
  font-size: 1.8rem;
  font-weight: bold;
  color: #000000;
  margin-bottom: 1.5rem;
}

.amount-row {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 0.75rem;
  margin-bottom: 1.25rem;
}

.amount-row input {
  width: 160px;
  padding: 0.5rem 0.75rem;
  border: 1px solid #ddd;
  border-radius: 8px;
}

.small-btn {
  padding: 0.5rem 0.75rem;
  background: #ffd189;
  border-radius: 8px;
  color: #000;
}

.btn-group {
  display: flex;
  gap: 1rem;
  margin-bottom: 2rem;
  justify-content: center;
}

.btn-group button {
  background: #ffc3c3;
  color: white;
  border-radius: 8px;
  padding: 0.6rem 1rem;
}

.btn-group button:hover {
  color: #000000;
}

.log-panel {
  background: white;
  border: 1px solid #ddd;
  border-radius: 8px;
  padding: 1rem;
  display: flex;
  color: #333;
  min-height: 48px;
  align-items: center;
  justify-content: center;
}
</style>