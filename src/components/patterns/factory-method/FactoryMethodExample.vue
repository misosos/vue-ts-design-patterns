<template>
  <div>
    <h2>FactoryMethod Pattern Example</h2>
  </div>
</template>

<script lang="ts">

/*
  Factory Method Pattern 예제
  ----------------------------
  Button 인터페이스: “제품 설계도”
  Dialog 추상 클래스: “공장 설계도”
  WindowsDialog / MacDialog: “공장”
  WindowsButton / MacButton: “제품”
*/

// 1. Product 인터페이스 (버튼의 공통 인터페이스)
interface Button {
  render(): void;
  onClick(): void;
}
// 2. 구체적인 Product 클래스들
class WindowsButton implements Button {
  render(): void {
    console.log("윈도우 스타일 버튼 렌더링");
  }
  onClick(): void {
    console.log("윈도우 버튼 클릭 이벤트 처리");
  }
}

class MacButton implements Button {
  render(): void {
    console.log("맥 스타일 버튼 렌더링");
  }
  onClick(): void {
    console.log("맥 버튼 클릭 이벤트 처리");
  }
}
// 3. Creator 추상 클래스 (팩토리 메서드 정의)
abstract class Dialog {
  // 팩토리 메서드
  abstract createButton() : Button;

  renderDialog(): void{
    const button = this.createButton();
    button.render();
    button.onClick();
  }
}
// 4. 구체적인 Creator 클래스들
class WindowsDialog extends Dialog {
  createButton():Button{
    return new WindowsButton();
  }
}

class MacDialog extends Dialog {
  createButton(): Button {
    return new MacButton();
  }
}

export default{
  name: 'FactoryMethod',

  setup() {
    // 5. 클라이언트 코드
    function clientApp(osType: string){
      let dialog: Dialog;

      if (osType === "Windows") {
        dialog = new WindowsDialog();
      }else{
        dialog = new MacDialog();
      }

      dialog.renderDialog()
    }

    clientApp("Windows");

    clientApp("Mac");
  }
}
</script>

<style scoped>

</style>