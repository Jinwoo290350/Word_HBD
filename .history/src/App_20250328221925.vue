<template>
  <div class="container mt-5">
    <h1 class="text-center mb-4">🎂 สร้างจดหมายอวยพรสุดพิเศษ 🎉</h1>
    <div class="card p-4 shadow">
      <div class="mb-3">
        <label class="form-label">ชื่อเล่นของผู้รับ:</label>
        <input 
          v-model="nickname" 
          type="text" 
          class="form-control" 
          placeholder="เช่น น้องโบ๊ท"
        >
      </div>
      
      <div class="mb-3">
        <label class="form-label">เลือกสไตล์การอวยพร:</label>
        <select v-model="theme" class="form-select">
          <option value="ซิกม่า หมาป่า">🐺 Sigma Wolf</option>
          <option value="ออทิสติก">🧩 ออทิสติก</option>
          <option value="ฮิปฮอป"></option>
          <option value="ซักเกอร์แดดดี้">💵 Sugardaddy</option>
          <option value="โรบล็อกซ์">🎮 Roblox</option>
          <option value="อิสลาม">🕌 Islamic</option>
          <option value="คนทั่วไป">😊 ทั่วไป</option>
          <option value="ตลกขบขัน">🤣 ตลก</option>
          <option value="น่ารักอบอุ่น">🥰 น่ารัก</option>
        </select>
      </div>

      <button 
        @click="generateWishes" 
        class="btn btn-primary"
        :disabled="isLoading || !nickname"
      >
        <template v-if="!isLoading">
          ✨ สร้างจดหมายวิเศษ
        </template>
        <template v-else>
          🔮 กำลังวิเคราะห์พลังงานดีๆ...
        </template>
      </button>
    </div>

    <div v-if="result" class="letter-box mt-4 p-4 shadow">
      <div class="envelope-icon">✉️</div>
      <h2 class="text-center mb-3">ถึง {{ nickname }} ที่รัก...</h2>
      <div class="letter-content">{{ result }}</div>
      <button 
        @click="copyText"
        class="btn btn-copy"
      >
        📄 คัดลอกข้อความ
      </button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      nickname: '',
      theme: 'น่ารักอบอุ่น',
      isLoading: false,
      result: ''
    }
  },
  methods: {
    async generateWishes() {
      this.isLoading = true
      try {
        const prompt = [
          `เขียนข้อความอวยพรวันเกิดให้กับ ${this.nickname} ในสไตล์ ${this.theme}`,
          'รูปแบบจดหมายสั้นๆ 2-3 ย่อหน้า',
          'ใช้ภาษาที่ตรงตามธีมที่เลือก',
          'เพิ่มเอโมจิให้เหมาะสม',
          'ลงท้ายด้วยชื่อเล่นหรือนามปากกาที่สร้างสรรค์'
        ].join('. ')

        const response = await fetch('https://api.opentyphoon.ai/v1/chat/completions', {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json',
            'Authorization': 'Bearer YOUR_API_KEY'
          },
          body: JSON.stringify({
            model: "typhoon-v2-70b-instruct",
            messages: [{ role: "user", content: prompt }]
          })
        })

        if (!response.ok) throw new Error('API Error')
        
        const data = await response.json()
        this.result = data.choices[0].message.content
      } catch (error) {
        console.error('เกิดข้อผิดพลาด:', error)
        this.result = '🌀 เกิดความผิดพลาดทางจิตมิติ! กรุณาลองเชื่อมต่อพลังงานดีๆ อีกครั้ง...'
      } finally {
        this.isLoading = false
      }
    },
    async copyText() {
      try {
        await navigator.clipboard.writeText(this.result)
        alert('คัดลอกข้อความไปยังคลิปบอร์ดแล้ว! 📋')
      } catch (err) {
        alert('คัดลอกไม่สำเร็จ กรุณาลองใหม่!')
      }
    }
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Kanit:wght@300;500&display=swap');

body {
  background: linear-gradient(135deg, #191724 0%, #1f1d2e 100%);
  color: #e0def4;
  font-family: 'Kanit', sans-serif;
  min-height: 100vh;
}

.container {
  max-width: 800px;
  padding: 15px;
}

.card {
  background: #26233a;
  border: 2px solid #ebbcba;
  border-radius: 15px;
  transition: transform 0.3s ease;
}

.card:hover {
  transform: translateY(-5px);
}

.form-control, .form-select {
  background: #403d52;
  border: 2px solid #6e6a86;
  color: #e0def4;
  border-radius: 8px;
  padding: 12px;
  font-size: 16px;
}

.form-control::placeholder {
  color: #6e6a86;
}

.btn-primary {
  background: linear-gradient(45deg, #ebbcba, #d8a8a6);
  border: none;
  border-radius: 8px;
  padding: 12px;
  font-weight: 500;
  letter-spacing: 1px;
  text-shadow: 0 2px 4px rgba(0,0,0,0.2);
}

.btn-primary:hover {
  background: linear-gradient(45deg, #d8a8a6, #c69593);
}

.letter-box {
  background: #26233a;
  border: 2px solid #ebbcba;
  border-radius: 15px;
  position: relative;
  overflow: hidden;
}

.letter-box::before {
  content: '';
  position: absolute;
  top: -50%;
  left: -50%;
  width: 200%;
  height: 200%;
  background: linear-gradient(45deg, transparent, rgba(235,188,186,0.1), transparent);
  transform: rotate(45deg);
}

.letter-content {
  white-space: pre-wrap;
  line-height: 1.8;
  font-size: 1.1em;
  position: relative;
  z-index: 1;
}

.btn-copy {
  width: 100%;
  margin-top: 15px;
  background: #403d52;
  color: #ebbcba;
  border: 1px solid #6e6a86;
  transition: all 0.3s ease;
}

.btn-copy:hover {
  background: #504d66;
  transform: scale(1.02);
}

@media (max-width: 576px) {
  .container {
    padding: 10px;
  }
  
  .form-control, .form-select {
    font-size: 14px;
    padding: 10px;
  }
  
  .btn-primary {
    font-size: 14px;
    padding: 10px;
  }
  
  .letter-content {
    font-size: 15px;
  }
}

.envelope-icon {
  animation: float 3s ease-in-out infinite;
}

@keyframes float {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-10px); }
}

.shadow {
  box-shadow: 0 8px 24px rgba(0,0,0,0.3);
}
</style>