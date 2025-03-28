<template>
  <div class="container mt-5">
    <h1 class="text-center mb-4">สร้างจดหมายอวยพรวันเกิดน่ารักๆ</h1>
    <div class="card p-4 shadow">
      <div class="mb-3">
        <label class="form-label">ชื่อเล่นของคุณ:</label>
        <input v-model="nickname" type="text" class="form-control" placeholder="เช่น น้องโบ๊ท">
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
        :disabled="isLoading"
      >
        <span v-if="!isLoading">สร้างจดหมายอวยพร</span>
        <span v-else>กำลังสร้าง...</span>
      </button>
    </div>

    <div v-if="result" class="letter-box mt-4 p-4 shadow">
      <div class="envelope-icon">✉️</div>
      <h2 class="text-center mb-3">จดหมายอวยพรถึง {{ nickname }}</h2>
      <pre class="letter-content">{{ result }}</pre>
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
            'Authorization': 'sk-3wY19YJQdjyYVBnwdjZKlpa3X7KG58tACnkPuAaH5rT8k70u'
          },
          body: JSON.stringify({
            model: "typhoon-v2-70b-instruct",
            messages: [{ role: "user", content: prompt }]
          })
        })

        const data = await response.json()
        this.result = data.choices[0].message.content
      } catch (error) {
        console.error('เกิดข้อผิดพลาด:', error)
        alert('สร้างข้อความไม่สำเร็จ กรุณาลองใหม่')
      } finally {
        this.isLoading = false
      }
    }
  }
}
</script>

<style scoped>
.letter-box {
  background: #fff9f9;
  border-radius: 15px;
  border: 2px solid #ffb6c1;
  font-family: 'Kanit', sans-serif;
}

.letter-content {
  white-space: pre-wrap;
  font-size: 1.1em;
  line-height: 1.6;
  color: #4a4a4a;
}

.envelope-icon {
  font-size: 3em;
  text-align: center;
  margin-bottom: 15px;
}

.shadow {
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}
</style>