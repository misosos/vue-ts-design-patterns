<template>
  <div class="facade-container">
    <h1 class="title">호텔 프런트 문의</h1>
    <div class="btn-group">
      <button @click="handle('CheckIn')">체크인</button>
      <button @click="handle('RoomService')">룸서비스</button>
      <button @click="handle('Laundry')">세탁</button>
      <button @click="handle('Cleaning')">객실 청소</button>
      <button @click="handle('CheckOut')">체크아웃</button>
    </div>
    <div class="log-panel">
      <p>{{ message }}</p>
    </div>
  </div>
</template>

<script lang="ts">
import { ref } from 'vue';

type Action = 'CheckIn' | 'RoomService' | 'Laundry' | 'Cleaning' | 'CheckOut';

// 퍼사드 클래스
class HotelFrontDesk{
  private roomService = new RoomService();
  private laundryService = new LaundryService();
  private housekeeping = new Housekeeping();
  private reception = new Reception();

  requestRoomService() {
    return this.roomService.orderMeal();
  }
  requestLaundry() {
    return this.laundryService.washClothes();
  }
  requestCleaning() {
    return this.housekeeping.cleanRoom();
  }
  checkIn() {
    return this.reception.checkIn();
  }
  checkOut() {
    return this.reception.checkOut();
  }
  request(action: Action, _payload?: Record<string, any>) {
    switch (action) {
      case 'CheckIn':
        return this.checkIn();
      case 'RoomService':
        return this.requestRoomService();
      case 'Laundry':
        return this.requestLaundry();
      case 'Cleaning':
        return this.requestCleaning();
      case 'CheckOut':
        return this.checkOut();
      default:
        return '지원하지 않는 요청입니다.';
    }
  }
}
// 서브시스템 클래스
class RoomService {
  orderMeal(): string {
    return "음식 주문 도와드릴까요?";
  }
}
class LaundryService {
  washClothes(): string {
    return "세탁 관련 업무 도와드릴까요?";
  }
}
class Housekeeping{
  cleanRoom(): string {
    return "객실 청소 도와드릴까요?";
  }
}
class Reception{
  checkIn(): string {
    return "체크인 도와드릴까요?";
  }
  checkOut(): string {
    return "체크아웃 도와드릴까요?"
  }
}
export default{
  name: 'FacadeExample',
  setup() {
    const frontDesk = new HotelFrontDesk();
    const message = ref('');
    const handle = (action: Action,payload?: Record<string, any>) => {
      message.value = frontDesk.request(action, payload);
    };

    return {
      message,
      handle,
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

.btn-group {
  display: flex;
  gap: 1rem;
  margin-bottom: 2rem;
}

.btn-group button {
  background: #ffc3c3;
  color: white;
  border-radius: 8px;
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
}
</style>