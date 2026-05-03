# Next.js RSC Exploitation: WAF Bypass to Prototype Pollution

## Bug Summary
- **Target:** Next.js Server Actions (React Server Components)
- **Vulnerability Type:** Server-Side Prototype Pollution (SSPP) & WAF Bypass
- **Attack Vector:** `Content-Type: text/plain` Fallthrough + JSON Property Injection (`__proto__` / `constructor.prototype`)
- **Impact:** Information Disclosure

## Attack Chain
1. **WAF Bypass (Content-Type Smuggling):** Filter *proxy* kustom gagal memvalidasi *body request* ketika *header* `Content-Type` diatur sebagai `text/plain`. Hal ini membuat *payload* lolos dari filter mitigasi kata kunci `proto`.
2. **Server-Side Prototype Pollution:** *Payload* JSON yang mengandung struktur `__proto__` dikirimkan ke *endpoint* `Next-Action` yang valid. *Parser* bawaan RSC mendeserialisasi *payload* tersebut dan mencemari *global prototype* Node.js di sisi *backend*.
3. **Object Hijacking (Trigger):** Mengirimkan *request* akhir dengan objek kosong `[{}]` memaksa *backend* untuk melakukan *fallback*. Karena properti asli tidak ditemukan dalam *request*, aplikasi mengambil nilai dari *global prototype* yang sudah tercemar, sehingga memuntahkan *flag* CTF pada respons `undefined`.

