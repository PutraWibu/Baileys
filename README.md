# 🚀 Baileys WhatsApp API - By kagenouReal

![GitHub stars](https://img.shields.io/github/stars/kagenouReal/Baileys?style=social)
![GitHub license](https://img.shields.io/github/license/kagenouReal/Baileys)
![Node.js](https://img.shields.io/badge/node-%3E%3D14.0-green)
![Contributors](https://img.shields.io/github/contributors/kagenouReal/Baileys)

Baileys WhatsApp API adalah library berbasis Node.js untuk berkomunikasi dengan WhatsApp Web tanpa perlu WebSocket tambahan. Ini adalah hasil modifikasi dari Whiskey Baileys agar lebih stabil dan mendukung lebih banyak tipe pesan.

Dikembangkan dengan performa tinggi untuk kebutuhan bot, otomatisasi pesan, dan integrasi aplikasi WhatsApp lainnya.

## 📌 Tentang Proyek Ini
Repositori ini dikembangkan dan dikelola oleh **kagenou** bersama para kontributor open-source lainnya.  
Dukungan dan kontribusi dari komunitas sangat diapresiasi! 💖  

---

## ✨ Fitur Utama

✅ **Autentikasi tanpa QR** menggunakan session authentication  
✅ **Dukungan Multi-Device (MD)** terbaru dari WhatsApp  
✅ **Kirim & terima pesan** dalam berbagai format  
✅ **Mengelola grup** (buat grup, tambahkan/kick anggota, atur deskripsi, dll.)  
✅ **Integrasi event** seperti masuk/keluar grup, pesan diterima, pesan terbaca  
✅ **Mendukung TypeScript** untuk pengembangan yang lebih aman  

---

## 📦 Instalasi

Pastikan **Node.js ≥ 14.0** sudah terpasang,
Kemudian jalankan perintah berikut di terminal:

```sh
npm install @kagenoureal/baileys
```

Atau dengan **Yarn**:

```sh
yarn add @kagenoureal/baileys
```

---

## 🚀 Penggunaan Dasar

```javascript
import makeWASocket, { DisconnectReason } from '@kagenouReal/baileys'
import { Boom } from '@hapi/boom'

async function connectToWhatsApp () {
    const sock = makeWASocket({
        // can provide additional config here
        printQRInTerminal: true
    })
    sock.ev.on('connection.update', (update) => {
        const { connection, lastDisconnect } = update
        if(connection === 'close') {
            const shouldReconnect = (lastDisconnect.error as Boom)?.output?.statusCode !== DisconnectReason.loggedOut
            console.log('connection closed due to ', lastDisconnect.error, ', reconnecting ', shouldReconnect)
            // reconnect if not logged out
            if(shouldReconnect) {
                connectToWhatsApp()
            }
        } else if(connection === 'open') {
            console.log('opened connection')
        }
    })
    sock.ev.on('messages.upsert', m => {
        console.log(JSON.stringify(m, undefined, 2))

        console.log('replying to', m.messages[0].key.remoteJid)
        await sock.sendMessage(m.messages[0].key.remoteJid!, { text: 'Hello there!' })
    })
}
// run in main file
connectToWhatsApp()
```

---

## 📜 Dokumentasi API

| Fitur               | Deskripsi |
|---------------------|----------|
| `sendMessage()`    | Mengirim pesan teks, gambar, video, dll. |
| `updateProfile()`  | Mengubah foto profil dan nama pengguna |
| `getChats()`       | Mendapatkan daftar chat pengguna |
| `groupParticipantsUpdate()` | Menambahkan/menghapus anggota grup |

📖 **Dokumentasi lengkap:** [Baileys API Docs](https://github.com/adiwajshing/Baileys/wiki)

---

## 📩 Contoh Kode Tambahan

### 🪀 Mengirim Semua Interaktif Msg  
```ts
const { generateWAMessageFromContent, proto } = require("@kagenouReal/baileys")
let msg = generateWAMessageFromContent(m.chat, {
 viewOnceMessage: {
   message: {
       "messageContextInfo": {
         "deviceListMetadata": {},
         "deviceListMetadataVersion": 2
       },
       interactiveMessage: proto.Message.InteractiveMessage.create({
         body: proto.Message.InteractiveMessage.Body.create({
           text: "Hello world"
         }),
         footer: proto.Message.InteractiveMessage.Footer.create({
           text: "p"
         }),
         header: proto.Message.InteractiveMessage.Header.create({
           title: "halo",
           subtitle: "test",
           hasMediaAttachment: false
         }),
         nativeFlowMessage: proto.Message.InteractiveMessage.NativeFlowMessage.create({
           buttons: [
             {
               "name": "single_select",
               "buttonParamsJson": "{\"title\":\"title\",\"sections\":[{\".menu\":\".play dj webito\",\"highlight_label\":\"label\",\"rows\":[{\"header\":\"header\",\"title\":\"title\",\"description\":\"description\",\"id\":\"id\"},{\"header\":\"header\",\"title\":\"title\",\"description\":\"description\",\"id\":\"id\"}]}]}"
             },
             {
               "name": "cta_reply",
               "buttonParamsJson": "{\"display_text\":\"quick_reply\",\"id\":\"message\"}"
             },
               {
                "name": "cta_url",
                "buttonParamsJson": "{\"display_text\":\"url\",\"url\":\"https://www.google.com\",\"merchant_url\":\"https://www.google.com\"}"
             },
             {
                "name": "cta_call",
                "buttonParamsJson": "{\"display_text\":\"call\",\"id\":\"message\"}"
             },
             {
                "name": "cta_copy",
                "buttonParamsJson": "{\"display_text\":\"copy\",\"id\":\"123456789\",\"copy_code\":\"message\"}"
             },
             {
                "name": "cta_reminder",
                "buttonParamsJson": "{\"display_text\":\"Recordatorio\",\"id\":\"message\"}"
             },
             {
                "name": "cta_cancel_reminder",
                "buttonParamsJson": "{\"display_text\":\"cta_cancel_reminder\",\"id\":\"message\"}"
             },
             {
                "name": "address_message",
                "buttonParamsJson": "{\"display_text\":\"address_message\",\"id\":\"message\"}"
             },
             {
                "name": "send_location",
                "buttonParamsJson": ""
             }
          ],
         })
       })
   }
 }
}, {})

return sock.relayMessage(msg.key.remoteJid, msg.message, { messageId: msg.key.id })>
```


### 🏓 Mengirim Pesan Button  
```ts
sock.sendMessage(m.chat, {
     text: "Hello World !",
     footer: "JustKagenou",
     buttons: [ 
         { buttonId: `.play`,
          buttonText: {
              displayText: 'ini button'
          }, type: 1 }
     ],
     headerType: 1,
     viewOnce: true
 },{ quoted: null })
```

### 📢 Mengirim Pesan Toko  
```ts
sock.sendMessage(msg.key.remoteJid, {
    text: "Isi Pesan",
    title: "Judul",
    subtitle: "Subjudul",
    footer: "Footer",
    viewOnce: true,
    shop: 3,
    id: "199872865193",
}, { quoted: m })
```

### 📊 Hasil Polling dari Newsletter  
```ts
await sock.sendMessage(msg.key.remoteJid, {
    pollResult: {
        name: "Judul Polling",
        votes: [
            ["Opsi 1", 10], 
            ["Opsi 2", 10]
        ],
    }
}, { quoted: m })
```

### 🏷️ Mention di Status  
```ts
await sock.StatusMentions({ text: "Halo!" }, [
    "123456789123456789@g.us",
    "123456789@s.whatsapp.net",
])
```

### 🃏 Pesan dengan Kartu  
```ts
await sock.sendMessage(msg.key.remoteJid, {
    text: "Halo!",
    footer: "Pesan Footer",
    cards: [
        {
            image: { url: 'https://example.jpg' }, 
            title: 'Judul Kartu',
            caption: 'Keterangan Kartu',
            footer: 'Footer Kartu',
            buttons: [
                { name: "quick_reply", buttonParamsJson: JSON.stringify({ display_text: "Tombol Cepat", id: "ID" }) },
                { name: "cta_url", buttonParamsJson: JSON.stringify({ display_text: "Buka Link", url: "https://www.example.com" }) }
            ]
        }
    ]
}, { quoted: m })
```

### 📷 Pesan Album  
```ts
await sock.sendAlbumMessage(msg.key.remoteJid, [
    { image: { url: "https://example.jpg" }, caption: "Halo Dunia" },
    { video: { url: "https://example.mp4" }, caption: "Halo Dunia" }
], { quoted: m, delay: 2000 })
```

### 📌 Menyimpan & Menyematkan Pesan  
```ts
await sock.sendMessage(msg.key.remoteJid, { keep: message.key, type: 1, time: 86400 })
await sock.sendMessage(msg.key.remoteJid, { pin: message.key, type: 1, time: 86400 })
```

### 📨 Pesan Undangan Grup  
```ts
await sock.sendMessage(msg.key.remoteJid, { 
    groupInvite: { 
        subject: "Nama Grup",
        jid: "1234@g.us",
        text: "Undangan Grup",
        inviteCode: "KODE",
        inviteExpiration: 86400 * 3,
    } 
}, { quoted: m })
```
**Dan Banyak Lagi Kode Pesan Baharu⚡**
---

## 🤝 Kontribusi

Kami menyambut kontribusi dari siapa saja! Jika ingin membantu:  
1. **Fork** repositori ini  
2. **Buat branch baru**  
3. **Buat Pull Request (PR)**  

💡 Punya ide keren? Silakan buat **Issue** di [repository ini](https://github.com/kagenouReal/Baileys/issues).  

---

## 📬 Kontak

📩 **Email**: kagenoureal@gmail.com  
🌍 **Website**: [Baileys API](https://github.com/kagenouReal/Baileys)  

---

🚀 **Semoga kamu suka & semangat ngoding!** 🎉
# Baileys
