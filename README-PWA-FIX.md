# Stockmat PWA Icon Fix - คำแนะนำการแก้ไข

## ปัญหาที่แก้ไข
Android Chrome ติดตั้ง Stockmat เป็น Shortcut แทน Native App เพราะใช้ SVG inline แทน PNG icon จริง

## ไฟล์ที่แก้ไข

### 1. `manifest.json` (แก้ไขแล้ว ✅)
**เปลี่ยนจาก:**
- `"start_url": "./"` → `"start_url": "/"`
- `"scope": "./"` → `"scope": "/"`
- Icons ใช้ `data:image/svg+xml` inline → เปลี่ยนเป็น `/icons/icon-*.png`
- เพิ่ม `"purpose": "maskable"` สำหรับ Android 11+

**เปลี่ยนเป็น:**
```json
{
  "start_url": "/",
  "scope": "/",
  "icons": [
    {
      "src": "/icons/icon-192.png",
      "sizes": "192x192",
      "type": "image/png",
      "purpose": "any"
    },
    {
      "src": "/icons/icon-192-maskable.png",
      "sizes": "192x192",
      "type": "image/png",
      "purpose": "maskable"
    },
    {
      "src": "/icons/icon-512.png",
      "sizes": "512x512",
      "type": "image/png",
      "purpose": "any"
    },
    {
      "src": "/icons/icon-512-maskable.png",
      "sizes": "512x512",
      "type": "image/png",
      "purpose": "maskable"
    }
  ]
}
```

### 2. `index.html` (แก้ไขแล้ว ✅)
**เปลี่ยนจาก:**
```html
<link rel="manifest" href="manifest.json">
<link rel="icon" type="image/svg+xml" href="data:image/svg+xml,...">
<link rel="apple-touch-icon" href="data:image/svg+xml,...">
```

**เปลี่ยนเป็น:**
```html
<link rel="manifest" href="/manifest.json">
<link rel="icon" type="image/png" href="/icons/icon-192.png">
<link rel="apple-touch-icon" href="/icons/icon-192.png">
```

## ขั้นตอนถัดไป: สร้าง PNG Icons

### วิธีที่ 1: ใช้ Icon Generator (ง่ายสุด)
1. เปิด `generate-icons.html` ในเบราว์เซอร์
2. คลิกปุ่มเพื่อดาวน์โหลด 4 ไฟล์:
   - `icon-192.png`
   - `icon-192-maskable.png`
   - `icon-512.png`
   - `icon-512-maskable.png`

3. สร้าง folder `icons` ในโฟลเดอร์โปรเจค
4. เอา 4 ไฟล์ลงใน `icons/`

### วิธีที่ 2: ใช้ Figma / Design Tool
สร้าง PNG icon เอง ขนาด:
- 192x192px (regular)
- 192x192px (maskable - no safe zone)
- 512x512px (regular)
- 512x512px (maskable - no safe zone)

ตั้งชื่อเป็น `icon-{size}{-maskable}.png` และวาง `icons/` folder

## โครงสร้าง Folder
```
dmp-l2-material/
├── index.html
├── manifest.json
├── sw.js
├── icons/
│   ├── icon-192.png
│   ├── icon-192-maskable.png
│   ├── icon-512.png
│   └── icon-512-maskable.png
└── generate-icons.html (ช่วยสร้าง icons)
```

## Push ขึ้น GitHub

```bash
# 1. สร้าง icons folder และเอาไฟล์ลงไป
mkdir icons
# (ลงไฟล์ 4 ไฟล์ icon ที่ดาวน์โหลดมา)

# 2. Add ไฟล์
git add .

# 3. Commit
git commit -m "Fix PWA: Add PNG icons and fix manifest for Android standalone install"

# 4. Push
git push
```

## Render Deploy
Render จะ auto deploy ใน 1-2 นาที หลังจาก push

## ทดสอบบน Android
1. ไปที่ https://dmp-l2-material.onrender.com
2. Chrome → ⋮ → "Install app"
3. ต้องเห็น icon Stockmat (กล่องส้ม) ไม่มี Chrome logo ซ้อน
4. ลองกด Install

## Verification Checklist
- ✅ `manifest.json` อ้างอิง `/icons/icon-*.png`
- ✅ `index.html` link `href="/manifest.json"`
- ✅ Folder `icons/` มีไฟล์ 4 ตัว
- ✅ `start_url` และ `scope` เป็น `/`
- ✅ `display: "standalone"` ยังอยู่
- ✅ Push ขึ้น GitHub แล้ว
- ✅ Render deploy สำเร็จ

ถ้ายังไม่ได้ลองดูที่ Chrome DevTools:
- F12 → Application → Manifest
- ตรวจสอบ icons path ถูกต้องไหม
