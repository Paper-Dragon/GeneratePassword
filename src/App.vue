<script setup>
import { ref } from 'vue';

const generatePassword = (length, useUppercase, useLowercase, useNumbers, useSpecialChars) => {
  const upper = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ';
  const lower = 'abcdefghijklmnopqrstuvwxyz';
  const numbers = '0123456789';
  const special = '!@#%^&*';

  let characterPool = '';
  let password = '';

  // Ensure at least one character from each selected type is included
  if (useUppercase) {
    characterPool += upper;
    password += upper[Math.floor(Math.random() * upper.length)];
  }
  if (useLowercase) {
    characterPool += lower;
    password += lower[Math.floor(Math.random() * lower.length)];
  }
  if (useNumbers) {
    characterPool += numbers;
    password += numbers[Math.floor(Math.random() * numbers.length)];
  }
  if (useSpecialChars) {
    characterPool += special;
    password += special[Math.floor(Math.random() * special.length)];
  }

  if (!characterPool) return '请选择至少一种字符类型';

  // Fill the rest of the password length with random characters from the pool
  for (let i = password.length; i < length; i++) {
    const randomIndex = Math.floor(Math.random() * characterPool.length);
    password += characterPool[randomIndex];
  }

  // Shuffle the password to avoid predictable patterns
  return password
    .split('')
    .sort(() => Math.random() - 0.5)
    .join('');
};

const passwordLength = ref(12);
const passwordCount = ref(1);
const useUppercase = ref(true);
const useLowercase = ref(true);
const useNumbers = ref(true);
const useSpecialChars = ref(true);
const generatedPasswords = ref([]);
const errorMessage = ref('');

const validateInputs = () => {
  if (passwordLength.value < 1 || passwordLength.value > 100) {
    errorMessage.value = '密码长度必须在 1 到 100 之间';
    return false;
  }
  if (passwordCount.value < 1 || passwordCount.value > 50) {
    errorMessage.value = '密码数量必须在 1 到 50 之间';
    return false;
  }
  errorMessage.value = '';
  return true;
};

const generatePasswords = () => {
  if (!validateInputs()) return;
  generatedPasswords.value = [];
  for (let i = 0; i < passwordCount.value; i++) {
    generatedPasswords.value.push(
      generatePassword(
        passwordLength.value,
        useUppercase.value,
        useLowercase.value,
        useNumbers.value,
        useSpecialChars.value
      )
    );
  }
};
</script>

<template>
  <div class="password-generator">
    <h2 class="title">密码随机生成器</h2>

    <div class="settings">
      <label class="setting-item">
        密码长度:
        <input type="number" v-model="passwordLength" min="1" max="100" />
      </label>

      <label class="setting-item">
        密码数量:
        <input type="number" v-model="passwordCount" min="1" max="50" />
      </label>

      <label class="setting-item">
        <input type="checkbox" v-model="useUppercase" /> 大写字母 (A-Z)
      </label>

      <label class="setting-item">
        <input type="checkbox" v-model="useLowercase" /> 小写字母 (a-z)
      </label>

      <label class="setting-item">
        <input type="checkbox" v-model="useNumbers" /> 数字 (0-9)
      </label>

      <label class="setting-item">
        <input type="checkbox" v-model="useSpecialChars" /> 特殊字符 (!@#%^&*)
      </label>
    </div>

    <div class="button-container">
      <button class="generate-button" @click="generatePasswords">生成密码</button>
    </div>

    <div v-if="errorMessage" class="error-message">{{ errorMessage }}</div>

    <div v-if="generatedPasswords.length > 0" class="result">
      <h3>生成的密码:</h3>
      <ul>
        <li v-for="(password, index) in generatedPasswords" :key="index" class="password-item">
          {{ password }}
        </li>
      </ul>
    </div>
  </div>
</template>

<style scoped>
.password-generator {
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
  border: 1px solid #ddd;
  border-radius: 10px;
  background-color: #f9f9f9;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.title {
  text-align: center;
  font-size: 24px;
  color: #333;
  margin-bottom: 20px;
}

.settings {
  display: flex;
  flex-wrap: wrap;
  gap: 15px;
  margin-bottom: 20px;
}

.setting-item {
  flex: 1 1 calc(50% - 10px);
  display: flex;
  align-items: center;
  gap: 10px;
}

.button-container {
  display: flex;
  justify-content: center;
  margin-bottom: 20px;
}

.generate-button {
  padding: 8px 20px;
  font-size: 16px;
  background-color: #4caf50;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  text-align: center;
}

.generate-button:hover {
  background-color: #45a049;
}

.error-message {
  color: #e74c3c;
  font-size: 16px;
  margin-top: 10px;
  text-align: center;
}

.result {
  margin-top: 20px;
}

.password-item {
  font-family: 'Courier New', Courier, monospace;
  background-color: #eee;
  padding: 5px 10px;
  border-radius: 3px;
  margin-bottom: 5px;
}

@media (max-width: 768px) {
  .settings {
    flex-direction: column;
    gap: 10px;
  }

  .setting-item {
    flex: 1 1 100%;
  }

  .generate-button {
    width: 100%;
  }
}
</style>
