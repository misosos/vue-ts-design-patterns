<template>
  <div>
    <h2>Adapter Pattern Example</h2>
  </div>
</template>

<script lang="ts">

interface OldPaymentProcessor{
  pay(amount: number) : void;
}

class NewPaymentGateway{
  makePayment(value: number): void{
    console.log(`${value}원을 새 결제 게이트웨이로 처리함`);
  }
}

class PaymentAdapter implements OldPaymentProcessor{
  private gateway: NewPaymentGateway;
  constructor(gateway: NewPaymentGateway) {
    this.gateway = gateway;
  }
  pay(amount: number) : void {
    this.gateway.makePayment(amount);
  }
}

export default{
  name: 'Adapter',

  setup() {
    function processPayment(processor: OldPaymentProcessor,amount: number){
      processor.pay(amount);
    }

    const newGateway = new NewPaymentGateway();
    const adapter = new PaymentAdapter(newGateway);

    processPayment(adapter,5000);
  }
}
</script>

<style scoped>

</style>