<template>
    <div class="countdown-app">
      <h1>25分钟倒计时</h1>
      
      <div class="timer-section">
        <div id="timer" class="timer-display">{{ minutes }}:{{ seconds }}</div>
        <div class="controls">
          <button id="startBtn" @click="startTimer" :disabled="isRunning">开始</button>
          <button id="pauseBtn" @click="pauseTimer" :disabled="!isRunning">暂停</button>
          <button id="resetBtn" @click="resetTimer">重置</button>
        </div>
      </div>
      
      <div class="grid-section">
        <h2>已完成次数</h2>
        <div id="gridContainer" class="grid-container">
          <div v-for="i in maxSessions" :key="i" class="grid-square" 
               :class="{ 'completed': i <= completedSessions }"></div>
        </div>
      </div>
    </div>
  </template>
  
  <script setup>
  import { ref, onMounted, computed } from 'vue';
  import { useTimerStore } from '@/stores/timerStore'

  const timerStore = useTimerStore()
  // 响应式数据
  const timeLeft = ref(25 * 60); // 25分钟，以秒为单位
  const isRunning = ref(false);
  const timer = ref(null);
  const maxSessions = 31; // 最多显示的方格数量
  
  // 使用store中的completedSessions
  const completedSessions = computed(() => timerStore.completedSessions)


  // 计算属性：格式化时间显示
  const minutes = computed(() => {
    return Math.floor(timeLeft.value / 60).toString().padStart(2, '0');
  });
  
  const seconds = computed(() => {
    return (timeLeft.value % 60).toString().padStart(2, '0');
  });
  
  // 开始倒计时
  const startTimer = () => {
    if (!isRunning.value) {
      isRunning.value = true;
      timer.value = setInterval(() => {
        timeLeft.value--;
        
        if (timeLeft.value <= 0) {
          completeSession();
        }
      }, 1000);
    }
  };
  
  // 暂停倒计时
  const pauseTimer = () => {
    isRunning.value = false;
    clearInterval(timer.value);
  };
  
  // 完成一次倒计时
  const completeSession = () => {
    clearInterval(timer.value);
    isRunning.value = false;
    timerStore.incrementCompletedSessions() // 使用store的方法
    timeLeft.value = 25 * 60; // 重置倒计时
  };
  
  // 重置倒计时
  const resetTimer = () => {
    clearInterval(timer.value);
    isRunning.value = false;
    timeLeft.value = 25 * 60;
  };
  
  // 页面挂载后初始化
  onMounted(() => {
   
  });
  </script>
  
  <style>
  .countdown-app {
    font-family: Arial, sans-serif;
    max-width: 600px;
    margin: 0 auto;
    padding: 20px;
    text-align: center;
  }
  
  .timer-section {
    margin-bottom: 30px;
  }
  
  .timer-display {
    font-size: 48px;
    margin: 20px 0;
    font-weight: bold;
    color: #333;
  }
  
  .controls button {
    padding: 8px 16px;
    margin: 0 5px;
    cursor: pointer;
    background-color: #4bb883;
    color: white;
    border: none;
    border-radius: 4px;
  }
  
  .controls button:disabled {
    background-color: #cccccc;
    cursor: not-allowed;
  }
  
  .grid-section {
    margin-top: 20px;
  }
  
  .grid-container {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    margin-top: 15px;
  }
  
  .grid-square {
    width: 30px;
    height: 30px;
    background-color: #e0e0e0;
    margin: 5px;
    border-radius: 4px;
  }
  
  .grid-square.completed {
    background-color: #4bb883;
  }
  </style>