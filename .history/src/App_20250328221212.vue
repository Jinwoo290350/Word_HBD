<template>
  <div class="container mt-5">
    <h1 class="text-center mb-4">สร้างจดหมายอวยพรวันเกิดน่ารักๆ</h1>
    <div class="card p-4 shadow">
      <div class="mb-3">
        <label class="form-label">ชื่อเล่นของคุณ:</label>
        <input 
          v-model="nickname" 
          type="text" 
          class="form-control" 
          placeholder="เช่น น้องโบ๊ท"
        >
      </div>
      
      <div class="mb-3">
        <label class="form-label">แนวข้อความอวยพร:</label>
        <select v-model="theme" class="form-select">
          <option value="ตลกขบขัน">ตลกขบขัน</option>
          <option value="น่ารักอบอุ่น">น่ารักอบอุ่น</option>
          <option value="สร้างสรรค์">สร้างสรรค์</option>
          <option value="เป็นทางการ">เป็นทางการ</option>
        </select>
      </div>

      <button 
        @click="generateWishes" 
        class="btn btn-primary"
        :disabled="isLoading || !nickname"
      >
        <span v-if="!isLoading">สร้างจดหมายอวยพร</span>
        <span v-else>กำลังสร้าง...</span>
      </button>
    </div>

    <div v-if="result" class="letter-box mt-4 p-4 shadow">
      <div class="envelope-icon">✉️</div>
      <h2 class="text-center mb-3">จดหมายอวยพรถึง {{ nickname }}</h2>
      <div class="letter-content">{{ result }}</div>
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
          `เขียนข้อความอวยพรวันเกิดให้กับ ${this.nickname} ในแนว ${this.theme}`,
          'รูปแบบเป็นจดหมายน่ารัก สั้นๆ 2-3 ย่อหน้า',
          'ใช้ภาษาที่เป็นกันเอง พิมพ์เอโมจิเสริมบางประโยค',
          'ลงท้ายด้วย "เพื่อนรัก" หรือนามปากกาน่ารัก'
        ].join('. ')

        const response = await fetch('https://api.opentyphoon.ai/v1/chat/completions', {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json',
            'Authorization': 'Bearer sk-3wY19YJQdjyYVBnwdjZKlpa3X7KG58tACnkPuAaH5rT8k70u'
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
        this.result = '⚠️ เกิดข้อผิดพลาดในการสร้างข้อความ กรุณาลองอีกครั้ง'
      } finally {
        this.isLoading = false
      }
    }
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Kanit&display=swap');

body {
  background-color: #191724;
  color: #e0def4;
  min-height: 100vh;
}

.container {
  max-width: 800px;
  padding: 20px;
}

.card {
  background-color: #26233a;
  border: 1px solid #403d52;
}

.form-control, .form-select {
  background-color: #26233a;
  border: 1px solid #403d52;
  color: #e0def4;
}

.form-control:focus, .form-select:focus {
  background-color: #26233a;
  border-color: #ebbcba;
  box-shadow: 0 0 0 0.2rem rgba(235, 188, 186, 0.25);
}

.btn-primary {
  background-color: #ebbcba;
  border-color: #ebbcba;
  color: #191724;
  width: 100%;
  transition: all 0.3s ease;
}

.btn-primary:hover {
  background-color: #d8a8a6;
  border-color: #d8a8a6;
}

.btn-primary:disabled {
  background-color: #1f1d2e;
  border-color: #1f1d2e;
  color: #6e6a86;
}

.letter-box {
  background: #26233a;
  border-radius: 15px;
  border: 2px solid #ebbcba;
  font-family: 'Kanit', sans-serif;
  color: #e0def4;
}

.letter-content {
  white-space: pre-wrap;
  font-size: 1.1em;
  line-height: 1.6;
  padding: 20px;
  background-color: #191724;
  border-radius: 10px;
  margin-top: 15px;
}

.envelope-icon {
  font-size: 3em;
  text-align: center;
  margin-bottom: 15px;
  color: #ebbcba;
}

.shadow {
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.3);
}

.text-center {
  color: #ebbcba;
}

.form-label {
  color: #908caa;
}
</style>