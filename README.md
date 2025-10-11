<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
  <title>📧 联信资匿名意见箱 📧</title>
  <script src="https://cdn.jsdelivr.net/npm/emailjs-com@3/dist/email.min.js"></script>

  <style>
    * {
      box-sizing: border-box;
    }

    html, body {
      margin: 0;
      height: 100%;
      overflow: hidden; /* 🚫 禁止滚动 */
      touch-action: none; /* 🚫 禁止触摸滑动 */
      font-family: "Microsoft YaHei", "Helvetica Neue", Arial, sans-serif;
      background: linear-gradient(135deg, #dce9ff 0%, #f0e6ff 50%, #fff3e0 100%);
      display: flex;
      justify-content: center;
      align-items: center;
    }

    .container {
      background: rgba(255, 255, 255, 0.96);
      border-radius: 20px;
      padding: 45px 40px;
      width: 90%;
      max-width: 520px;
      box-shadow: 0 10px 35px rgba(0, 0, 0, 0.12);
      text-align: center;
      animation: fadeIn 0.8s ease-out;
    }

    @keyframes fadeIn {
      from { opacity: 0; transform: translateY(15px); }
      to { opacity: 1; transform: translateY(0); }
    }

    h2 {
      font-size: 28px;
      color: #0d47a1;
      font-weight: 800;
      letter-spacing: 1px;
      margin-bottom: 30px;
    }

    label {
      display: block;
      text-align: left;
      margin-top: 18px;
      font-size: 16px;
      font-weight: 600;
      color: #222;
    }

    select, textarea, input[type="file"] {
      width: 100%;
      border: 1px solid #d0d7e2;
      border-radius: 10px;
      padding: 14px;
      margin-top: 6px;
      font-size: 15px;
      background-color: #f9fafc;
      transition: 0.3s;
    }

    select:focus, textarea:focus, input[type="file"]:focus {
      outline: none;
      border-color: #007bff;
      box-shadow: 0 0 8px rgba(0, 123, 255, 0.3);
      background-color: #fff;
    }

    textarea {
      resize: none;
      height: 130px;
      line-height: 1.6;
    }

    input[type="file"]::-webkit-file-upload-button {
      background-color: #007bff;
      color: #fff;
      border: none;
      padding: 9px 16px;
      border-radius: 8px;
      cursor: pointer;
      font-weight: 600;
      transition: 0.3s;
    }

    input[type="file"]::-webkit-file-upload-button:hover {
      background-color: #0056b3;
    }

    button {
      margin-top: 28px;
      width: 100%;
      padding: 15px;
      font-size: 17px;
      font-weight: 700;
      color: #fff;
      border: none;
      border-radius: 10px;
      background: linear-gradient(90deg, #007bff, #0066cc);
      cursor: pointer;
      transition: 0.3s;
      box-shadow: 0 4px 15px rgba(0, 123, 255, 0.3);
    }

    button:hover {
      background: linear-gradient(90deg, #0059b3, #004c99);
      transform: translateY(-2px);
      box-shadow: 0 6px 18px rgba(0, 123, 255, 0.4);
    }

    footer {
      margin-top: 22px;
      text-align: center;
      color: #666;
      font-size: 13px;
      border-top: 1px solid #eee;
      padding-top: 12px;
    }

    @media (max-width: 600px) {
      .container {
        padding: 30px 25px;
        border-radius: 16px;
      }
      h2 { font-size: 24px; }
    }
  </style>
</head>

<body>
  <div class="container">
    <h2>📧 联信资匿名意见箱 📧</h2>
    <form id="feedbackForm">
      <label for="category">意见内容分类：</label>
      <select id="category" required>
        <option value="公司管理">公司管理</option>
        <option value="团队协作">团队协作</option>
        <option value="线下办事处意见">线下办事处意见</option>
        <option value="福利制度">福利制度</option>
        <option value="其他">其他</option>
      </select>

      <label for="message">意见内容：</label>
      <textarea id="message" placeholder="请输入您宝贵的意见及建议，我们会及时进行整改和查证处理" required></textarea>

      <label for="image">上传图片（可选）：</label>
      <input type="file" id="image" accept="image/*">

      <button type="submit">提交匿名意见</button>
    </form>
    <footer>您的意见将被严格保密，仅用于改进与优化工作。</footer>
  </div>

  <script>
    (function() {
      emailjs.init('Vf3g58_uwsuIfMxCI');
    })();

    document.getElementById('feedbackForm').addEventListener('submit', async function(event) {
      event.preventDefault();

      const category = document.getElementById('category').value;
      const message = document.getElementById('message').value.trim();
      const imageInput = document.getElementById('image');
      const timestamp = new Date().toLocaleString();
      let imageLink = '未上传图片';

      if (!message) {
        alert('请填写意见内容！');
        return;
      }

      if (imageInput.files.length > 0) {
        const file = imageInput.files[0];
        if (file.size > 10 * 1024 * 1024) {
          alert('图片大小不能超过 10MB！');
          return;
        }

        try {
          const formData = new FormData();
          formData.append('image', file);
          const uploadRes = await fetch('https://api.imgbb.com/1/upload?key=cc12233f7cd40acce8f24fa3b36c69b4', {
            method: 'POST',
            body: formData
          });
          const uploadData = await uploadRes.json();
          if (uploadData.data && uploadData.data.url) {
            imageLink = uploadData.data.url;
          }
        } catch (err) {
          console.error('图片上传失败：', err);
        }
      }

      // ✅ 修正版 — 只传 EmailJS 模板定义的变量
      const templateParams = {
        category: category,
        message: message + '\n\n📅 提交时间：' + timestamp + '\n🖼 查看图片：' + imageLink,
        image: imageLink
      };

      emailjs.send('service_0nbyy1m', 'template_la7d6sb', templateParams)
        .then(() => {
          alert('✅ 感谢您的匿名反馈，我们已收到！');
          document.getElementById('feedbackForm').reset();
        })
        .catch(error => {
          alert('❌ 发送失败，请稍后再试。');
          console.error('EmailJS 错误：', error);
        });
    });
  </script>
</body>
</html>
