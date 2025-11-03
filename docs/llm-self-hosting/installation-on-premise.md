# Instalasi LLaMA 3 On-Premise di Linux

Dokumentasi ini menjelaskan langkah-langkah teknis untuk menginstal dan menjalankan model LLaMA 3 secara lokal di Linux, serta mengekspose API publik-nya. Penjelasan mencakup instalasi dependensi, pengunduhan model, setup inferensi, hingga penyiapan server REST API dengan keamanan dasar.

## 1\. Persiapan Sistem dan Dependensi

*   **Python dan VirtualEnv:** Pastikan Python terbaru (≥3.9) terpasang, karena Transformers memerlukan Python 3.9 ke atas[\[1\]](https://huggingface.co/docs/transformers/en/installation#:~:text=Transformers%20works%20with%20PyTorch,2). Buat lingkungan virtual untuk proyek agar dependensi terisolasi. Contoh perintah:
    ```bash
    python3 -m venv .env  
    source .env/bin/activate
    ```
   (Perintah ini setara dengan contoh di dokumentasi Hugging Face[\[2\]](https://huggingface.co/docs/transformers/en/installation#:~:text=Create%20a%20virtual%20environment%20to,install%20Transformers%20in).) Setelah aktif, pasang paket utama:
    ```bash
    pip install transformers torch
    ```
   Ini juga menginstal PyTorch (CPU) dan Hugging Face Transformers. Jika hanya CPU, bisa menggunakan indeks khusus PyTorch CPU; jika pakai GPU, pasang PyTorch GPU biasa.

*   **CUDA/cuDNN (jika GPU):** Jika menggunakan GPU NVIDIA, pasang driver NVIDIA dan CUDA Toolkit yang sesuai untuk versi PyTorch [\[3\]](https://huggingface.co/docs/transformers/en/installation#:~:text=For%20GPU%20acceleration%2C%20install%20the,appropriate%20CUDA%20drivers%20for%20PyTorch). Periksa deteksi GPU dengan ```nvidia-smi```. Jika ```nvidia-smi``` tidak terdeteksi atau tidak ada GPU, gunakan PyTorch CPU-only. Setelah CUDA terinstal, ```pip pip install torch``` (versi CUDA) sudah mencakup cuDNN secara otomatis.

*   **Dependensi Tambahan:** Jika menggunakan TGI (Text Generation Inference), instal Rust dan protokol Protobuf sesuai petunjuk TGI[\[4\]](https://huggingface.co/docs/text-generation-inference/en/installation#:~:text=To%20install%20and%20launch%20locally%2C,using%20conda)[\[5\]](https://huggingface.co/docs/text-generation-inference/en/installation#:~:text=PROTOC_ZIP%3Dprotoc,f%20%24PROTOC_ZIP). Contoh:
    ```bash
    pip install text-generation-inference
    ```
*   TGI membutuhkan Python 3.9+ dan Rust; dokumentasi resmi mencontohkan instalasi melalui ```git clone``` dan ```make install```[\[4\]](https://huggingface.co/docs/text-generation-inference/en/installation#:~:text=To%20install%20and%20launch%20locally%2C,using%20conda).

## 2\. Unduh dan Siapkan Model LLaMA 3

*   **Via Meta (download.sh):** Kunjungi situs resmi LLaMA 3 (llama.meta.com), setujui perjanjian lisensi, lalu jalankan skrip pengunduhan. Proses ini akan mengunduh berkas model dan tokenizer ke direktori lokal[\[6\]](https://github.com/meta-llama/llama3#:~:text=To%20download%20the%20model%20weights,website%20and%20accept%20our%20License). (Perhatikan link yang dikirim lewat email perlu dimasukkan saat diminta.)
*   **Via Hugging Face:** Model LLaMA 3 juga ada di Hugging Face (contoh: ```meta-llama/Meta-Llama-3-8B-Instruct```). Setelah request akses disetujui, Anda dapat mengunduh model tersebut. Misalnya, menggunakan CLI Hugging Face:
    ```bash
    huggingface-cli login  
    huggingface-cli download meta-llama/Meta-Llama-3-8B-Instruct \  
    --include "original/*" --local-dir Llama3-8B-Instruct
    ```
    atau langsung menggunakan Transformers dalam kode Python:
    ```python
    from transformers import pipeline, torch\_dtype  
    model_id = "meta-llama/Meta-Llama-3-8B-Instruct"  
    pipeline = pipeline(  
        "text-generation",  
        model=model_id,  
        model_kwargs={"torch_dtype": torch.bfloat16},  
        device_map="auto"  
    )
    ```
    Contoh ini diadaptasi dari dokumentasi Meta Hugging Face[\[7\]](https://github.com/meta-llama/llama3#:~:text=,download%20and%20cache%20the%20weights). Setelah diunduh (atau saat diakses pertama kali), model akan tersimpan di cache ```~/.cache/huggingface``` atau direktori yang ditentukan.
*   **Format Model:** Pastikan model dalam format yang sesuai (misal file .pt, .bin, atau .safetensors dari Transformers; atau .gguf jika pakai llama.cpp). Jika perlu, konversi bisa dilakukan dengan skrip ```convert_llama_ggml_to_gguf.py``` dari repositori llama.cpp.

## 3\. Framework Inferensi LLM

Setelah model siap, gunakan framework inferensi untuk menjalankannya. Beberapa pilihan populer:

*   **Hugging Face Transformers (PyTorch):** Pakai Transformers dengan pipeline atau model/tokenizer. Contoh:
    ```python
    from transformers import pipeline  
    pipe = pipeline("text-generation", model="meta-llama/Meta-Llama-3-8B-Instruct")  
    output = pipe("Halo, bagaimana kabarmu?")
    ```
    Ini menggunakan Llama 3 instruksi untuk teks generatif. Sama seperti contoh dokumentasi HF[\[7\]](https://github.com/meta-llama/llama3#:~:text=,download%20and%20cache%20the%20weights), pastikan ```device_map="auto"``` agar model besar terdistribusi ke GPU jika tersedia.
*   **Text Generation Inference (TGI):** TGI adalah toolkit Hugging Face untuk inference berperforma tinggi dengan REST API siap pakai[\[8\]](https://huggingface.co/docs/text-generation-inference/en/index#:~:text=Text%20Generation%20Inference%20,NeoX%2C%20and%20T5). Setelah instalasi (```pip install text-generation-inference```), jalankan server TGI dengan model Llama 3 instruksi:
    ```bash
    text-generation-launch --model-id meta-llama/Meta-Llama-3-8B-Instruct --tensor-parallel 1 --listen-port 8000
    ```
    (Contoh ini mengikuti pola CLI TGI; dokumentasi resmi memberikan contoh serupa[\[9\]](https://huggingface.co/docs/text-generation-inference/en/installation#:~:text=Once%20installation%20is%20done%2C%20simply,run).) TGI mendukung optimasi produksi, batching, quantisasi, dan menyediakan metrik Prometheus bawaan[\[10\]](https://huggingface.co/docs/text-generation-inference/en/index#:~:text=,inference%20using%20%2051%20Flash).
*   **llama.cpp / llama-cpp-python:** Untuk CPU (atau GPU terbatas), **llama.cpp** (C/C++ inference) mendukung LLaMA 3[\[11\]](https://github.com/ggml-org/llama.cpp#:~:text=%23%20Text). Anda bisa menginstal binding Python-nya:
    ```bash
    pip install llama-cpp-python[server]
    ```
    Kemudian jalankan server API kompatibel OpenAI:
    ```bash
    python3 -m llama_cpp.server --model path/to/model.gguf
    ```
    Contoh ini diambil dari dokumentasi llama-cpp-python yang menunjukkan instalasi dan perintah server[\[12\]](https://llama-cpp-python.readthedocs.io/en/latest/server/#:~:text=The%20server%20can%20be%20installed,by%20running%20the%20following%20command). Setelah dijalankan, server akan expose endpoint mirip OpenAI (mis. /v1/completions).
*   **Alternatif Lain:** Misal menggunakan Docker image atau layanan lain; utamakan framework yang sesuai kebutuhan. IntelliSense LLaMA-3 dapat dipanggil lewat pip llama-server atau tugas TorchRun dari repositori Llama 3 Meta, tapi di sini fokus pada opsi di atas.

## 4\. Menyiapkan Server REST API

Setelah model dan inferensi siap, buat service REST API untuk interaksi. Pendekatan umum:

*   **FastAPI + Transformers:** Instal FastAPI dan server ASGI (uvicorn):
    ```bash
    pip install fastapi uvicorn
    ```
    Buat aplikasi (misal ```app.py```):
    ```python
    from fastapi import FastAPI  
    from transformers import pipeline  
      
    app = FastAPI()  
    generator = pipeline("text-generation", model="meta-llama/Meta-Llama-3-8B-Instruct", torch\_dtype="bfloat16", device\_map="auto")  
      
    @app.post("/generate")  
    def generate(prompt: str):  
    result = generator(prompt)  
    return {"generated_text": result[0]["generated_text"]}
    ```
*   Kode di atas menggunakan pipeline LLaMA 3 instruksi sebagai model (diambil dari dokumentasi HF[\[7\]](https://github.com/meta-llama/llama3#:~:text=,download%20and%20cache%20the%20weights)). Jalankan server FastAPI dengan uvicorn:
    ```bash
    uvicorn app:app --host 0.0.0.0 --port 8000
    ```
*   **TGI REST Endpoint:** Jika menggunakan TGI, Anda tidak perlu FastAPI terpisah. Cukup jalankan ```text-generation-launch``` seperti di atas, dan TGI secara otomatis menyediakan endpoint REST (JSON over HTTP) di port yang ditentukan. Klien dapat mengakses misal ```http://localhost:8000/completions``` (bergantung konfigurasi TGI). Dokumentasi TGI merekomendasikan Docker atau binary siap pakai untuk deployment produksi.
*   **llama-cpp-server:** Perintah ```python3 -m llama_cpp.server``` seperti disebut di atas akan membuka HTTP server. Endpoint bawaannya kompatibel OpenAI API. Contoh saat dijalankan, Anda bisa ```POST``` ke ```http://localhost:5000/v1/engines/llama_cpp/completions``` (port default 5000) dengan payload JSON untuk mendapatkan hasil teks. Skrip ```llama-cpp-python``` menyediakan banyak opsi (lihat dokumentasi server-nya).

Setelah server berjalan, lakukan tes dengan ```curl``` atau klien HTTP. Misal:
```bash
    curl -X POST http://localhost:8000/generate -H "Content-Type: application/json" -d '{"prompt": "Apa kabar?"}'
```

Jika menggunakan FastAPI di atas, hasilnya akan berisi teks generatif.

## 5\. Mengekspos API ke Publik

Agar API dapat diakses dari luar jaringan lokal, lakukan salah satu cara berikut:

*   **Reverse Proxy dengan NGINX:** Pasang NGINX di server publik Anda. Buat konfigurasi virtual host yang mem-forward ke aplikasi. Contoh ```/etc/nginx/sites-available/llama3```:
    ```bash
    server {  
        listen 80;  
        server_name api.llama3.example.com;  
        
        location / {  
            proxy_pass http://127.0.0.1:8000;  
            proxy_set_header Host $host;  
            proxy_set_header X-Real-IP $remote_addr;  
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;  
            proxy_set_header X-Forwarded-Proto $scheme;  
        }  
    }
    ```
    Contoh di atas mengarahkan ```api.llama3.example.com``` ke FastAPI/uvicorn di port 8000 pada localhost[\[13\]](https://dev.to/udara_dananjaya/setting-up-a-fastapi-project-with-nginx-reverse-proxy-on-ubuntu-883#:~:text=server%20,your_domain_or_ip). Aktifkan konfigurasi ini (```ln -s``` ke ```sites-enabled```), lalu reload NGINX:
    ```bash
    sudo nginx -t  
    sudo systemctl reload nginx
    ```
    Setelah itu, domain Anda akan melayani API. Untuk keamanan, gunakan HTTPS – misalnya dengan Certbot/Let’s Encrypt:
    ```bash
    sudo apt install certbot python3-certbot-nginx  
    sudo certbot --nginx -d api.llama3.example.com
    ```
    Certbot akan mengonfigurasi SSL secara otomatis[\[14\]](https://dev.to/udara_dananjaya/setting-up-a-fastapi-project-with-nginx-reverse-proxy-on-ubuntu-883#:~:text=sudo%20apt%20install%20certbot%20python3). NGINX juga dapat diaplikasikan limit koneksi atau fitur WAF jika diperlukan.
*   **Tunneling (ngrok, Cloudflare Tunnel):** Jika tidak ada server publik, gunakan tunnel. Contoh ngrok: jalankan
    ```bash
    ngrok http 8000
    ```
    yang akan memberikan URL publik (http/https) yang meneruskan ke localhost:8000[\[15\]](https://www.alexhyett.com/using-ngrok-to-test-local-sites-api/#:~:text=1,see%20an%20output%20like%20this). Atau gunakan _Cloudflare Tunnel_ (```cloudflared```): buat tunnel dan peta ke hostname pilihan, misal:
    ```bash
    cloudflared tunnel --hostname llama3.example.com --url http://localhost:8000
    ```
    Cloudflare akan meneruskan ```llama3.example.com``` ke server lokal tanpa membuka port secara langsung[\[16\]](https://sebastiangogola.me/securely-exposing-a-local-nginx-server-with-cloudflare-tunnel/#:~:text=%E2%9C%94%20No%20exposed%20IPs%20or,monitoring%20available%20via%20Cloudflare%20Dashboard). Keuntungan metode ini: IP dan port server Anda tidak terekspos langsung, dan Cloudflare otomatis menangani sertifikat HTTPS serta proteksi DDoS[\[16\]](https://sebastiangogola.me/securely-exposing-a-local-nginx-server-with-cloudflare-tunnel/#:~:text=%E2%9C%94%20No%20exposed%20IPs%20or,monitoring%20available%20via%20Cloudflare%20Dashboard).
*   **DNS & Firewall:** Pastikan port yang digunakan (80/443) diizinkan lewat firewall/VPS. Jika menggunakan domain khusus, atur A-record ke IP server.

## 6\. Keamanan Dasar

*   **Rate Limiting:** Batasi laju request agar API tidak disalahgunakan atau overload. Jika menggunakan NGINX, aktifkan rate limit. Contoh konfigurasi NGINX:
    ```bash
    http {  
        limit_req_zone $binary_remote_addr zone=mylimit:10m rate=10r/s;  
        ...  
        server {  
        ...  
            location / {  
                limit_req zone=mylimit burst=20 nodelay;  
                proxy_pass http://127.0.0.1:8000;  
            }  
        }  
    }
    ```
    Potongan di atas memastikan tiap IP maksimal ~10 request per detik[\[17\]](https://blog.nginx.org/blog/rate-limiting-nginx#:~:text=limit_req_zone%20%24binary_remote_addr%20zone%3Dmylimit%3A10m%20rate%3D10r%2Fs%3B), dengan kemampuan menampung burst (antrian) agar tidak langsung menolak serangan spasi singkat[\[18\]](https://blog.nginx.org/blog/rate-limiting-nginx#:~:text=location%20%2Flogin%2F%20,burst%3D20). Bila memakai server Python (FastAPI), Anda juga bisa gunakan library seperti ```slowapi``` atau limit di layer kode.
*   **Autentikasi API Key:** Lindungi endpoint publik dengan mekanisme kunci API atau token. Misalnya di FastAPI gunakan ```APIKeyHeader``` untuk mengecek header khusus, seperti contoh dokumentasi FastAPI:
    ```python
    from fastapi import Depends, FastAPI  
    from fastapi.security import APIKeyHeader  
      
    api_key_scheme = APIKeyHeader(name="X-API-Key")  
    @app.get("/secure-data/")  
    async def read_secure(key: str = Depends(api_key_scheme)):  
    return {"status": "OK"}
    ```
    FastAPI akan menolak jika header ```X-API-Key``` tidak benar[\[19\]](https://fastapi.tiangolo.com/reference/security/#:~:text=header_scheme%20%3D%20APIKeyHeader%28name%3D%22x). Anda dapat memeriksa nilai kunci tersebut dengan logika tersendiri (misal mencocokkan dengan string yang disimpan di server). Alternatifnya, Anda bisa menerapkan validasi di layer NGINX (mis. memeriksa header/parameter kunci API) atau menggunakan OAuth 2.0/Bearer token untuk proteksi lebih lanjut.
*   **Pemantauan dan Logging:** Aktifkan logging dan pantau performa. Gunakan sistem monitoring seperti Prometheus + Grafana untuk metrik. Jika pakai TGI, sudah terdapat dukungan metrik Prometheus dan tracing out-of-the-box[\[10\]](https://huggingface.co/docs/text-generation-inference/en/index#:~:text=,inference%20using%20%2051%20Flash). Anda juga bisa menggunakan APM atau tools observabilitas (contoh: Sentry, OpenTelemetry) agar mengetahui error atau bottleneck. Pantau penggunaan GPU (mis. ```nvidia-smi``` atau ```dcgm-exporter```), konsumsi RAM, dan statistik request (melalui NGINX access log atau FastAPI ```logging```).
*   **Tips Lain:** Terapkan CORS jika perlu (FastAPI: middleware CORS), batasi ukuran input prompt, dan jalankan container/service dengan user non-root. Perhatikan pembaruan keamanan pada OS dan library.


**Sumber:** Referensi berasal dari dokumentasi resmi Hugging Face, Meta LLaMA 3, dan sumber terpercaya terkait deployment LLM[\[1\]](https://huggingface.co/docs/transformers/en/installation#:~:text=Transformers%20works%20with%20PyTorch,2)[\[6\]](https://github.com/meta-llama/llama3#:~:text=To%20download%20the%20model%20weights,website%20and%20accept%20our%20License)[\[7\]](https://github.com/meta-llama/llama3#:~:text=,download%20and%20cache%20the%20weights)[\[10\]](https://huggingface.co/docs/text-generation-inference/en/index#:~:text=,inference%20using%20%2051%20Flash)[\[12\]](https://llama-cpp-python.readthedocs.io/en/latest/server/#:~:text=The%20server%20can%20be%20installed,by%20running%20the%20following%20command)[\[13\]](https://dev.to/udara_dananjaya/setting-up-a-fastapi-project-with-nginx-reverse-proxy-on-ubuntu-883#:~:text=server%20,your_domain_or_ip)[\[17\]](https://blog.nginx.org/blog/rate-limiting-nginx#:~:text=limit_req_zone%20%24binary_remote_addr%20zone%3Dmylimit%3A10m%20rate%3D10r%2Fs%3B)[\[19\]](https://fastapi.tiangolo.com/reference/security/#:~:text=header_scheme%20%3D%20APIKeyHeader%28name%3D%22x)[\[15\]](https://www.alexhyett.com/using-ngrok-to-test-local-sites-api/#:~:text=1,see%20an%20output%20like%20this)[\[16\]](https://sebastiangogola.me/securely-exposing-a-local-nginx-server-with-cloudflare-tunnel/#:~:text=%E2%9C%94%20No%20exposed%20IPs%20or,monitoring%20available%20via%20Cloudflare%20Dashboard).

[\[1\]](https://huggingface.co/docs/transformers/en/installation#:~:text=Transformers%20works%20with%20PyTorch,2) [\[2\]](https://huggingface.co/docs/transformers/en/installation#:~:text=Create%20a%20virtual%20environment%20to,install%20Transformers%20in) [\[3\]](https://huggingface.co/docs/transformers/en/installation#:~:text=For%20GPU%20acceleration%2C%20install%20the,appropriate%20CUDA%20drivers%20for%20PyTorch) Installation

[https://huggingface.co/docs/transformers/en/installation](https://huggingface.co/docs/transformers/en/installation)

[\[4\]](https://huggingface.co/docs/text-generation-inference/en/installation#:~:text=To%20install%20and%20launch%20locally%2C,using%20conda) [\[5\]](https://huggingface.co/docs/text-generation-inference/en/installation#:~:text=PROTOC_ZIP%3Dprotoc,f%20%24PROTOC_ZIP) [\[9\]](https://huggingface.co/docs/text-generation-inference/en/installation#:~:text=Once%20installation%20is%20done%2C%20simply,run) Installation from source

[https://huggingface.co/docs/text-generation-inference/en/installation](https://huggingface.co/docs/text-generation-inference/en/installation)

[\[6\]](https://github.com/meta-llama/llama3#:~:text=To%20download%20the%20model%20weights,website%20and%20accept%20our%20License) [\[7\]](https://github.com/meta-llama/llama3#:~:text=,download%20and%20cache%20the%20weights) GitHub - meta-llama/llama3: The official Meta Llama 3 GitHub site

[https://github.com/meta-llama/llama3](https://github.com/meta-llama/llama3)

[\[8\]](https://huggingface.co/docs/text-generation-inference/en/index#:~:text=Text%20Generation%20Inference%20,NeoX%2C%20and%20T5) [\[10\]](https://huggingface.co/docs/text-generation-inference/en/index#:~:text=,inference%20using%20%2051%20Flash) Text Generation Inference

[https://huggingface.co/docs/text-generation-inference/en/index](https://huggingface.co/docs/text-generation-inference/en/index)

[\[11\]](https://github.com/ggml-org/llama.cpp#:~:text=%23%20Text) GitHub - ggml-org/llama.cpp: LLM inference in C/C++

[https://github.com/ggml-org/llama.cpp](https://github.com/ggml-org/llama.cpp)

[\[12\]](https://llama-cpp-python.readthedocs.io/en/latest/server/#:~:text=The%20server%20can%20be%20installed,by%20running%20the%20following%20command) OpenAI Compatible Web Server - llama-cpp-python

[https://llama-cpp-python.readthedocs.io/en/latest/server/](https://llama-cpp-python.readthedocs.io/en/latest/server/)

[\[13\]](https://dev.to/udara_dananjaya/setting-up-a-fastapi-project-with-nginx-reverse-proxy-on-ubuntu-883#:~:text=server%20,your_domain_or_ip) [\[14\]](https://dev.to/udara_dananjaya/setting-up-a-fastapi-project-with-nginx-reverse-proxy-on-ubuntu-883#:~:text=sudo%20apt%20install%20certbot%20python3) Setting Up a FastAPI Project with NGINX Reverse Proxy on Ubuntu - DEV Community

[https://dev.to/udara\_dananjaya/setting-up-a-fastapi-project-with-nginx-reverse-proxy-on-ubuntu-883](https://dev.to/udara_dananjaya/setting-up-a-fastapi-project-with-nginx-reverse-proxy-on-ubuntu-883)

[\[15\]](https://www.alexhyett.com/using-ngrok-to-test-local-sites-api/#:~:text=1,see%20an%20output%20like%20this) Using ngrok to test local websites and APIs | Alex Hyett

[https://www.alexhyett.com/using-ngrok-to-test-local-sites-api/](https://www.alexhyett.com/using-ngrok-to-test-local-sites-api/)

[\[16\]](https://sebastiangogola.me/securely-exposing-a-local-nginx-server-with-cloudflare-tunnel/#:~:text=%E2%9C%94%20No%20exposed%20IPs%20or,monitoring%20available%20via%20Cloudflare%20Dashboard) Securely Exposing a Local Nginx Server with Cloudflare Tunnel - Gogola Nexus

[https://sebastiangogola.me/securely-exposing-a-local-nginx-server-with-cloudflare-tunnel/](https://sebastiangogola.me/securely-exposing-a-local-nginx-server-with-cloudflare-tunnel/)

[\[17\]](https://blog.nginx.org/blog/rate-limiting-nginx#:~:text=limit_req_zone%20%24binary_remote_addr%20zone%3Dmylimit%3A10m%20rate%3D10r%2Fs%3B) [\[18\]](https://blog.nginx.org/blog/rate-limiting-nginx#:~:text=location%20%2Flogin%2F%20,burst%3D20) Rate Limiting with NGINX – NGINX Community Blog

[https://blog.nginx.org/blog/rate-limiting-nginx](https://blog.nginx.org/blog/rate-limiting-nginx)

[\[19\]](https://fastapi.tiangolo.com/reference/security/#:~:text=header_scheme%20%3D%20APIKeyHeader%28name%3D%22x) Security Tools - FastAPI

[https://fastapi.tiangolo.com/reference/security/](https://fastapi.tiangolo.com/reference/security/)