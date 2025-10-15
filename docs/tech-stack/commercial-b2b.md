# **Commercial B2B License**

Berikut versi **tabel lisensi dan konsolidasi LLM provider** untuk produk **komersial (SaaS publik / B2B)**

Tabel ini membantu Anda menentukan kapan perlu melakukan **konsolidasi atau perjanjian komersial** dengan penyedia model, serta **apa risikonya jika tidak**.

---

## 🧠 **Tabel: Ketentuan Penggunaan Komersial Model LLM (API & On-Premise)**

| **Provider / Model** | **Kepemilikan Output** | **Boleh untuk SaaS / B2B Komersial?** | **Monetisasi Output** | **Perlu Konsolidasi / Lisensi Enterprise?** | **Risiko / Catatan Hukum & Kepatuhan** |
| :------------------- | :--------------------- | :------------------------------------ | :-------------------- | :------------------------------------------ | -------------------------------------- |
| **OpenAI (GPT-3.5 / GPT-4 / o1)** | Pengguna **memiliki semua hak atas output**. Data API **tidak digunakan** untuk pelatihan model (kecuali ChatGPT gratis).([openai][1]) ([openai][2]) | ✅ Diperbolehkan membuat aplikasi publik atau B2B ([openai][3]). Tidak boleh menjual ulang API key ([openai][4]). | ✅ Ya, bebas komersial. | ⚙️ Tidak wajib, tapi **disarankan untuk Enterprise** jika memproses data sensitif (DPA tersedia). | Pelanggaran Terms (mis. sharing key, bypass rate limit) dapat menyebabkan **pemblokiran akun**. Tanpa DPA → risiko privasi data di sisi hukum GDPR/HIPAA. |
| **Anthropic (Claude 3 series)** | Anda **memiliki hak penuh** atas input dan output ([anthropic][5]). | ✅ Diperbolehkan untuk produk SaaS dan B2B ([anthropic][6]). | ✅ Ya, termasuk produk berbayar ([anthropic][7]). | ⚙️ Tidak wajib, tapi **disarankan untuk enterprise** (Claude for Work/Enterprise) ([anthropic][7]). | Anthropic **tidak melatih model dengan data Anda**, namun tetap tunduk pada Usage Policy ([anthropic][8]). |
| **Google (Gemini / Vertex AI)** | Pengguna **memiliki output**, tapi Google dapat menghasilkan konten serupa untuk orang lain ([google][9]). | ✅ Diperbolehkan, tapi wilayah dibatasi (region-based). Pengguna EEA/UK wajib pakai versi **berbayar** (Google Cloud) ([google][10]). | ✅ Ya. | ⚙️ Disarankan pakai **Google Cloud Enterprise** untuk produk B2B. | Layanan gratis bisa **menggunakan data Anda untuk pelatihan** ([google][11]). Gunakan versi berbayar agar data tidak disimpan ([google][12]). |
| **Mistral AI (Mixtral, Mistral 7B, Codestral)** | Pengguna **memiliki hak penuh** atas output ([mistral][13]). | ✅ Diperbolehkan ([mistral][14]) ([mistral][15]). | ✅ Ya. | ⚙️ Tidak wajib, tapi **untuk data sensitif disarankan aktifkan Zero Data Retention (ZDR)** ([mistral][16]). | Tanpa ZDR, data bisa disimpan sementara di log. Tidak digunakan untuk training, tapi raw log tetap ada.  |
| **LLaMA (Meta LLaMA 2 / 3)** | Model **open-weight (OSS)**. Meta **tidak mengklaim output**. | ⚠️ **Perlu izin lisensi komersial** untuk aplikasi dengan **lebih dari 700 juta MAU** atau digunakan di platform besar. | ✅ Ya, tapi tunduk pada **LLaMA 2/3 Commercial License**. | ⚙️ Jika aplikasi Anda berskala besar atau *revenue-driven*, **harus mendaftar lisensi komersial Meta** (gratis untuk sebagian besar startup). | Pelanggaran lisensi (misal penggunaan tanpa izin pada skala besar) dapat mengakibatkan klaim pelanggaran lisensi. Jika dimodifikasi dan dijual ulang, wajib mencantumkan atribusi Meta.                   |
| **DeepSeek (R1, Coder, Chat)**                  | Open-weight model (MIT License atau Apache 2.0 tergantung distribusi). **Pengguna memiliki hak output.**                  | ✅ Diperbolehkan. Tidak ada batasan eksplisit untuk SaaS/B2B.                                                            | ✅ Ya, bebas komersial karena lisensi **Apache 2.0** memperbolehkan penggunaan komersial tanpa izin tambahan. | ⚙️ Tidak perlu konsolidasi — cukup mencantumkan atribusi jika diwajibkan oleh lisensi (mis. Apache 2.0 Notice).                               | Pastikan tidak melanggar lisensi dependensi (mis. data training publik). DeepSeek menyatakan tidak menyimpan data pengguna, tapi tanggung jawab tetap di pihak developer untuk data privacy (GDPR/HIPAA). |
| **LlamaIndex / LangChain (Integrator Layer)**   | Framework OSS (MIT License). Tidak menyimpan data pengguna secara otomatis.                                               | ✅ Diperbolehkan digunakan untuk produk SaaS & B2B.                                                                      | ✅ Ya.                                                                                                        | ❌ Tidak perlu lisensi komersial.                                                                                                              | Pastikan Anda mematuhi lisensi model dan penyedia API yang diintegrasikan melalui framework ini.                                                                                                          |
| **OpenRouter / LiteLLM (Aggregator Layer)**     | Tidak memproses isi untuk pelatihan. Output tetap milik pengguna.                                                         | ✅ Diperbolehkan, tapi **mengikuti syarat tiap model di dalamnya.**                                                      | ✅ Ya.                                                                                                        | ⚙️ Tidak perlu lisensi khusus, tapi Anda **bertanggung jawab atas semua model yang digunakan.**                                               | Risiko: jika salah satu model melarang redistribusi output, Anda tetap bisa dituntut. Pastikan Terms tiap model terpenuhi.                                                                                |

---

## ⚙️ **Kesimpulan & Rekomendasi**

| **Situasi**                                           | **Perlu Konsolidasi / Perjanjian Komersial?**   | **Catatan**                                                                                                           |
| ----------------------------------------------------- | ----------------------------------------------- | --------------------------------------------------------------------------------------------------------------------- |
| Startup SaaS publik (skala kecil-menengah, <1M MAU)   | ❌ Tidak wajib                                   | Gunakan API publik (OpenAI, Mistral, Claude) sesuai terms. Pastikan data sensitif dienkripsi.                         |
| B2B Enterprise (kontrak dengan perusahaan besar)      | ⚙️ **Disarankan**                               | Lakukan **Data Processing Agreement (DPA)** dan dapatkan *Enterprise Terms* untuk SLA dan kepatuhan (ISO, SOC2, dsb). |
| Penggunaan model OSS (LLaMA, DeepSeek, Mistral local) | ❌ Tidak wajib, tapi ⚠️ **cek lisensi modelnya** | LLaMA memerlukan lisensi komersial untuk platform besar; DeepSeek bebas komersial.                                    |
| Penggunaan multi-model router (LiteLLM, OpenRouter)   | ⚙️ **Pastikan setiap model sesuai Terms-nya**   | Router tidak menghapus tanggung jawab legal Anda terhadap Terms tiap API.                                             |
| Produk dengan data sensitif (HR, medis, finansial)    | ✅ **Wajib DPA / Enterprise SLA**                | Hindari API publik tanpa perjanjian; gunakan versi *enterprise* (mis. OpenAI Enterprise, Claude for Work).            |

---


[1]: https://openai.com/policies/services-agreement/#:~:text=4 "OpenAI Services Agreement - Customer Content"
[2]: https://openai.com/enterprise-privacy/#:~:text=,your%20business%20data%20by%20default "OpenAI Enterprise Privacy"
[3]: https://openai.com/policies/services-agreement "OpenAI Services Agreement"
[4]: https://openai.com/policies/services-agreement/#:~:text=login%20credentials%20between%20multiple%20users,the%20Account%20or%20the%20Services "OpenAI Services Agreement - Sharing Account"
[5]: https://www.anthropic.com/legal/commercial-terms#:~:text=As%20between%20the%20parties%20and,Outputs%20together%20are%20%E2%80%9CCustomer%20Content%E2%80%9D "Anthropic Commercial Terms - Customer Content"
[6]: https://www.anthropic.com/legal/commercial-terms#:~:text=1,to%20use "Anthropic Commercial Terms"
[7]: https://www.anthropic.com/news/expanded-legal-protections-api-improvements#:~:text=Our%20Commercial%20Terms%20of%20Service,using%20Claude%20through%20Amazon%20Bedrock "Anthropic - Improved terms of service"
[8]: https://www.anthropic.com/legal/commercial-terms#:~:text=C "Anthropic - Privacy"
[9]: https://ai.google.dev/gemini-api/terms#:~:text=Some%20of%20our%20Services%20allow,all%20rights%20to%20do%20so "Google - Generate Content"
[10]: https://ai.google.dev/gemini-api/terms#:~:text=You%20may%20only%20access%20the,Switzerland%2C%20or%20the%20United%20Kingdom "Google - Restrictions"
[11]: https://ai.google.dev/gemini-api/terms#:~:text=When%20you%20use%20Unpaid%20Services%2C,consistent%20with%20our%20Privacy%20Policy "Google - Unpaid Services"
[12]: https://ai.google.dev/gemini-api/terms#:~:text=When%20you%20use%20Paid%20Services%2C,may%20be%20stored%20transiently%20or "Google - Paid Services"
[13]: https://mistral.ai/terms#:~:text=Output,Output%20that%20we%20may%20have "Mistral - Customer Content"
[14]: https://mistral.ai/terms#:~:text=individual%20use%20only%20and%20you,You%20must%20promptly%20notify "Mistral - Terms"
[15]: https://mistral.ai/terms#:~:text=,are%20contrary%20to%20applicable%20law "Mistral - Legal"
[16]: https://iamistral.com/api/#:~:text=Data%20Retention%20and%20the%20Zero,potentially%20required%20by%20applicable%20law "Mistral - Zero Data Retention"
