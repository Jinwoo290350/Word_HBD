<template>
  <div class="container mt-5">
    <h1 class="text-center mb-4">🎂 สร้างจดหมายอวยพรสุดพิเศษ 🎉</h1>
    <div class="card p-4 shadow">
      <div class="mb-3">
        <label class="form-label">ชื่อผู้ส่ง:</label>
        <input 
          v-model="senderName" 
          type="text" 
          class="form-control" 
          placeholder="เช่น แฟรงค์ผู้ยิ่งใหญ่"
        >
      </div>

      <div class="mb-3">
        <label class="form-label">ชื่อผู้รับ:</label>
        <input 
          v-model="nickname" 
          type="text" 
          class="form-control" 
          placeholder="เช่น น้องโบ๊ทสุดน่ารัก"
          required
        >
      </div>
      
      <div class="mb-3">
        <label class="form-label">สไตล์การอวยพร:</label>
        <select v-model="theme" class="form-select">
          <option value="ซิกม่า หมาป่า หมาป่าที่พูดภาษาคนไม่ได้">🐺 Sigma Wolf</option>
          <option value="ออทิสติก จะพิมพ์ไม่ถุกและไม่เป็นคำภาษาจะออกมาเพื่อนๆ">🧩 ออทิสติก</option>
          <option value="คนดำ พูดจาไม่ดี ชอบเล่นบาส กินไก่ทอดกับแตงโม">Ninja (Maybe)</option>
          <option value="ซักเกอร์แดดดี้ รวย ชอบแจกตังค์">💵 Sugardaddy</option>
          <option value="โรบล็อกซ์ ชวนเล่นเกมโรบล็อก">🎮 Roblox Master</option>
          <option value="อิสลาม จะพิมพ์ภาษาอาหรับมาเป็นภาษาไทย">🕌 Islamic Style</option>
          <option value="คนทั่วไป">😊 ทั่วไป</option>
          <option value="คนเอเชีย ที่ชอบถามว่าเมื่อไรจะเป็นหมอ">👨⚕️ Asian Parent</option>
          <option value="คนหยาบคาย">คนน่ารัก</option>
        </select>
      </div>

      <button 
        @click="generateWishes" 
        class="btn btn-primary"
        :disabled="isLoading || !nickname || !senderName"
      >
        <template v-if="!isLoading">
          ✨ เริ่มสร้างมหัศจรรย์
        </template>
        <template v-else>
          🔮 กำลังบิดเบือนความเป็นจริง...
        </template>
      </button>
    </div>

    <div v-if="result" class="letter-box mt-4 p-4 shadow">
      <div class="envelope-icon">✉️</div>
      <h2 class="text-center mb-3">ถึง {{ nickname }} ที่รัก...</h2>
      <div class="letter-content">{{ result }}</div>
      <button 
        @click="copyText"
        class="btn btn-copy mt-3"
        :class="{ 'copy-success': copySuccess }"
      >
        {{ copySuccess ? '✅ คัดลอกแล้ว!' : '📄 คัดลอกข้อความ' }}
      </button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      senderName: '',
      nickname: '',
      theme: 'คนทั่วไป',
      isLoading: false,
      result: '',
      copySuccess: false
    }
  },
  methods: {
    async generateWishes() {
      this.isLoading = true
      try {
        const prompt = [
          `เขียนจดหมายอวยพรวันเกิดจาก ${this.senderName} ถึง ${this.nickname} ในสไตล์ ${this.theme}`,
          'รูปแบบจดหมายสั้นๆ 2-3 ย่อหน้า',
          'ใช้ภาษาที่ตรงตามธีมที่เลือกอย่างเคร่งครัด',
          'ใส่เอโมจิให้เหมาะสมกับเนื้อหา',
          `ลงท้ายด้วย "${this.senderName}" ในรูปแบบที่สร้างสรรค์`
        ].join('. ')

        const response = await fetch('https://api.opentyphoon.ai/v1/chat/completions', {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json',
            'Authorization': 'Bearer sk-3wY19YJQdjyYVBnwdjZKlpa3X7KG58tACnkPuAaH5rT8k70u'
          },
          body: JSON.stringify({
            model: "typhoon-v2-70b-instruct",
            messages: [{ role: "user", content: prompt }],
            temperature: 0.9
          })
        })

        if (!response.ok) throw new Error('API Error')
        
        const data = await response.json()
        this.result = data.choices[0].message.content
      } catch (error) {
        console.error('เกิดข้อผิดพลาด:', error)
        this.result = '🌀 เวลาและ空間บิดเบี้ยว! กรุณาทำการทวนควอนตัมรัฐ...'
      } finally {
        this.isLoading = false
      }
    },
    async copyText() {
      try {
        await navigator.clipboard.writeText(this.result)
        this.copySuccess = true
        setTimeout(() => this.copySuccess = false, 2000)
      } catch (err) {
        alert('ระบบคลิปบอร์ดปฏิเสธการเข้าถึง!')
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
  transition: border-color 0.3s ease, box-shadow 0.3s ease;
}

.form-control:focus, .form-select:focus {
  border-color: #ebbcba !important;
  box-shadow: 0 0 0 3px rgba(235, 188, 186, 0.25) !important;
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
  background: linear-gradient(45deg, #191724, #1f1d2e);
  padding: 25px;
  border-radius: 12px;
  border: 1px solid #ebbcba;
}

.letter-content::after {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: url('data:image/svg+xml;utf8,<svg viewBox="0 0 100 100" xmlns="http://www.w3.org/2000/svg"><text x="50%" y="50%" font-size="40" fill="%23ebbcba22" dominant-baseline="middle" text-anchor="middle">${props => props.theme}</text></svg>');
  opacity: 0.1;
  pointer-events: none;
}

.btn-copy {
  width: 100%;
  margin-top: 15px;
  background: #ebbcba !important;
  color: #191724 !important;
  border: none !important;
  padding: 10px 20px !important;
  border-radius: 8px !important;
  transition: all 0.3s ease !important;
}

.btn-copy.copy-success {
  background: #9fcc8a !important;
}

.btn-copy:hover {
  transform: translateY(-2px) !important;
  box-shadow: 0 4px 12px rgba(235, 188, 186, 0.3) !important;
}

@media (max-width: 576px) {
  .container {
    padding: 10px;
  }
  
  .card {
    border-radius: 10px;
    padding: 15px;
  }
  
  h1 {
    font-size: 1.8rem;
  }
  
  .form-control, .form-select {
    font-size: 14px;
  }
  
  .btn-primary {
    font-size: 15px;
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