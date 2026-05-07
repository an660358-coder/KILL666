<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>隱形報修系統</title>
    <style>
        :root { --main: #4a90e2; --bg: #f5f7fa; }
        body { font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif; background: var(--bg); padding: 15px; }
        .container { background: white; padding: 25px; border-radius: 12px; box-shadow: 0 4px 12px rgba(0,0,0,0.08); max-width: 450px; margin: auto; }
        h2 { text-align: center; color: #333; margin-top: 0; }
        .hint { font-size: 13px; color: #888; text-align: center; margin-bottom: 20px; }
        .field { margin-bottom: 15px; }
        label { display: block; margin-bottom: 6px; font-weight: 600; font-size: 14px; }
        input, select, textarea { width: 100%; padding: 12px; border: 1px solid #ddd; border-radius: 8px; font-size: 16px; box-sizing: border-box; }
        button { width: 100%; padding: 14px; background: var(--main); color: white; border: none; border-radius: 8px; font-size: 16px; font-weight: bold; cursor: pointer; transition: 0.2s; }
        button:disabled { background: #cbd5e0; }
        #preview { width: 100%; border-radius: 8px; margin-top: 10px; display: none; }
        #status { text-align: center; padding: 10px; border-radius: 8px; margin-top: 15px; display: none; }
    </style>
</head>
<body>

<div class="container">
    <h2>🛠 設備維修報修</h2>
    <div class="hint">免打電話、免面對面。請填寫資訊並拍照即可。</div>
    
    <form id="repairForm">
        <div class="field">
            <label>報修人/代號</label>
            <input type="text" name="name" placeholder="例如：小張" required>
        </div>
        <div class="field">
            <label>具體位置 (部門或房間號)</label>
            <input type="text" name="location" placeholder="例如：4樓茶水間" required>
        </div>
        <div class="field">
            <label>故障種類</label>
            <select name="device">
                <option value="電氣設備">電氣設備</option>
                <option value="給排水/漏水">給排水/漏水</option>
                <option value="空調系統">空調系統</option>
                <option value="辦公家具/門窗">辦公家具/門窗</option>
                <option value="其他">其他</option>
            </select>
        </div>
        <div class="field">
            <label>狀況說明</label>
            <textarea name="description" rows="3" placeholder="請簡述狀況..." required></textarea>
        </div>
        <div class="field">
            <label>拍照 (點擊拍照，免登入)</label>
            <input type="file" id="photoFile" accept="image/*" capture="environment">
            <img id="preview">
        </div>
        <button type="submit" id="submitBtn">確認送出報修</button>
    </form>
    <div id="status"></div>
</div>

<script>
    let base64Image = "";
    let imageType = "";
    const preview = document.getElementById('preview');
    const photoFile = document.getElementById('photoFile');

    photoFile.onchange = e => {
        const file = e.target.files[0];
        if(!file) return;
        imageType = file.type;
        const reader = new FileReader();
        reader.onload = event => {
            preview.src = event.target.result;
            preview.style.display = 'block';
            base64Image = event.target.result.split(',')[1];
        };
        reader.readAsDataURL(file);
    };

    document.getElementById('repairForm').onsubmit = async function(e) {
        e.preventDefault();
        const btn = document.getElementById('submitBtn');
        const status = document.getElementById('status');
        btn.disabled = true;
        btn.innerText = "傳送中...";

        const data = {
            name: this.name.value,
            location: this.location.value,
            device: this.device.value,
            description: this.description.value,
            imageData: base64Image,
            imageType: imageType
        };

        try {
            const response = await fetch('你的WEB_APP_URL_貼在這裡', {
                method: 'POST',
                body: JSON.stringify(data)
            });
            status.innerText = "✅ 報修成功！資料已自動存檔。";
            status.style.display = "block";
            status.style.background = "#dcfce7";
            status.style.color = "#166534";
            this.reset();
            preview.style.display = 'none';
        } catch (err) {
            status.innerText = "❌ 傳送失敗，請檢查網路。";
            status.style.display = "block";
            status.style.background = "#fee2e2";
        } finally {
            btn.disabled = false;
            btn.innerText = "確認送出報修";
        }
    };
</script>
</body>
</html>
