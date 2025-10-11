<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>联信资匿名意见箱</title>
  <script src="https://cdn.jsdelivr.net/npm/emailjs-com@3/dist/email.min.js"></script>
  <style>
    body {
      font-family: 'Microsoft YaHei', Arial, sans-serif;
      background-color: #f4f6f8;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      margin: 0;
    }
    .container {
      background-color: #fff;
      padding: 30px;
      border-radius: 12px;
      box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
      width: 100%;
      max-width: 500px;
    }
    h2 {
      text-align: center;
      color: #007bff;
      margin-bottom: 20px;
    }
    label {
      font-weight: bold;
      margin-top: 10px;
      display: block;
    }
    select, textarea, input[type="file"] {
      width: 100%;
      padding: 10px;
      border: 1px solid #ccc;
      border-radius: 6px;
      margin-top: 5px;
    }
    textarea {
      resize: none;
      height: 120px;
    }
    button {
      width: 100%;
      background-color: #007bff;
      color: #fff;
      border: none;
      padding: 12px;
      font-size: 16px;
      border-radius: 6px;
      cursor: pointer;
      margin-top: 15px;
    }
    button:hover {
      background-color: #0056b3;
    }
  </style>
</head>
<body>
  <div class="container">
    <h2>联信资匿名意见箱</h2>
    <form id="feedbackForm">
      <label for="category">举报内容分类：</label>
      <select id="category" required>
        <option value="公司管理">公司管理</option>
        <option value="团队协作">团队协作</option>
        <option value="线下办事处意见">线下办事处意见</option>
        <option value="福利制度">福利制度</option>
        <option value="其他">其他</option>
      </select>

      <label for="message">意见内容：</label>
      <textarea id="message" placeholder="请输入您的意见或建议..." required></textarea>

      <label for="image">上传图片（可选，最大5MB）：</label>
      <input type="file" id="image" accept="image/*">

      <button type="submit">提交匿名意见</button>
    </form>
  </div>

  <script>
    (function() {
      emailjs.init('Vf3g58_uwsuIfMxCI'); // 你的 EmailJS Public Key
    })();

    document.getElementById('feedbackForm').addEventListener('submit', function(event) {
      event.preventDefault();

      const category = document.getElementById('category').value;
      const message = document.getElementById('message').value.trim();
      const imageInput = document.getElementById('image');
      const timestamp = new Date().toLocaleString();

      if (!message) {
        alert('请填写意见内容！');
        return;
      }

      if (imageInput.files.length > 0) {
        const file = imageInput.files[0];
        if (file.size > 5 * 1024 * 1024) {
          alert('图片大小超过 5MB，请选择更小的图片！');
          return;
        }
        const reader = new FileReader();
        reader.onload = function(e) {
          sendEmail(category, message, e.target.result, timestamp);
        };
        reader.readAsDataURL(file);
      } else {
        sendEmail(category, message, null, timestamp);
      }
    });

    function sendEmail(category, message, imageBase64, timestamp) {
      const templateParams = {
        subject: '联信资匿名意见箱 - ' + category + '（' + timestamp + '）',
        to_email: 'lianxinzi2025@outlook.com',
        category: category,
        message: message + '\\n\\n📅 提交时间：' + timestamp,
        image: imageBase64 || ''
      };

      emailjs.send('service_0nbyy1m', 'template_la7d6sb', templateParams)
        .then(function() {
          alert('✅ 感谢您的匿名反馈，我们已收到！');
          document.getElementById('feedbackForm').reset();
        }, function(error) {
          alert('❌ 发送失败，请稍后再试。');
          console.error('EmailJS 错误：', error);
        });
    }
  </script>
</body>
</html>
