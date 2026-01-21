import cv2
import numpy as np
import base64
from flask import Flask, render_template, request

# Inisialisasi Flask
app = Flask(__name__)

# Halaman utama yang akan dibuka oleh HP
@app.route('/')
def index():
    return render_template('index.html')

# Jalur untuk menerima gambar dari HP
@app.route('/upload_frame', methods=['POST'])
def upload_frame():
    try:
        # 1. Terima data gambar (dalam format base64 string)
        data_url = request.form['image']
        header, encoded = data_url.split(",", 1)
        
        # 2. Decode data menjadi gambar yang bisa dibaca komputer
        data = base64.b64decode(encoded)
        np_arr = np.frombuffer(data, np.uint8)
        frame = cv2.imdecode(np_arr, cv2.IMREAD_COLOR)

        # 3. Tampilkan gambar di layar Laptop
        if frame is not None:
            cv2.imshow("Video dari HP (Tekan 'q' untuk keluar)", frame)
            
            # Wajib ada agar window tidak hang (tunggu 1ms)
            cv2.waitKey(1)

        return "Sukses", 200
    except Exception as e:
        print(f"Error: {e}")
        return "Gagal", 500

if __name__ == '__main__':
    print("=== Server Berjalan ===")
    print("Menunggu koneksi dari HP...")
    # Menjalankan server di port 5000
    app.run(host='0.0.0.0', port=5000, debug=True)
