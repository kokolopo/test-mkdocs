# **LLMs OWASP Introduction**

---

## **Apa itu OWASP?**
OWASP (Open Worldwide Application Security Project) adalah organisasi global nirlaba yang fokus pada keamanan aplikasi.
OWASP terkenal dengan daftar OWASP Top 10 untuk aplikasi web, yang membantu developer memahami risiko keamanan utama.
Sekarang, OWASP juga mengeluarkan Top 10 untuk LLMs (Large Language Models), karena LLMs membawa risiko keamanan baru yang unik.

---

## **Kenapa LLMs Perlu Standar Keamanan?**
LLM (*Large Language Model*) memerlukan standar keamanan karena membawa risiko yang unik dan ganda: risiko keamanan siber tradisional (seperti kebocoran data) dan risiko keselamatan baru yang melekat pada kemampuan generatif (seperti penyalahgunaan etika dan penyebaran disinformasi).
Standar keamanan diperlukan untuk mengelola kerentanan model, memastikan keandalan, dan membangun kepercayaan terhadap teknologi yang semakin dominan ini.

---

## **OWASP Top 10 untuk LLMs (2025)**
LLM adalah target baru bagi peretas karena llm berinteraksi langsung dengan data dan sistem. Standar diperlukan untuk mengatasi:

- **Prompt Injection**: Serangan dengan “menyuntikkan” instruksi tersembunyi ke dalam prompt agar model melakukan tindakan di luar batas yang diinginkan, misalnya membocorkan data atau mengeksekusi perintah berbahaya.

- **Sensitive Information Disclosure**: LLM bisa tanpa sengaja membocorkan data sensitif seperti API keys, data pelanggan, atau informasi internal perusahaan.

- **Supply Chain Risks**: LLM sering bergantung pada data, plugin, dan library pihak ketiga. Jika salah satu tidak aman, maka sistem bisa tereksploitasi.

- **Data & Model Poisoning**: *Hacker* bisa menyuntikkan data berbahaya ke dataset *training* sehingga model bias atau menghasilkan output salah.

- **Improper Output Handling**: Output dari LLM bisa berbahaya jika langsung dijalankan (misalnya sebagai query SQL atau perintah shell).

- **Excessive Agency**: LLM yang diberi terlalu banyak kontrol (misalnya mengirim email, melakukan transfer uang, atau mengakses API) bisa salah digunakan.

- **System Prompt Leakage**: Instruksi sistem (system prompt) yang seharusnya rahasia bisa dibocorkan melalui trik tertentu.

- **Vector & Embedding Weaknesses**: Sistem RAG (Retrieval-Augmented Generation) dan embedding bisa dimanipulasi dengan data berbahaya, sehingga hasil LLM tidak akurat atau berisiko.

- **Misinformation (Halusinasi & Disinformasi)**: LLM bisa memberikan jawaban yang salah, bias, atau bahkan dimanipulasi untuk menyebarkan hoaks.

- **Unbounded Consumption**: LLM dapat dikonsumsi secara berlebihan (misalnya input panjang, permintaan masif), yang bisa menyebabkan DoS (Denial of Service) atau biaya cloud yang membengkak.

---