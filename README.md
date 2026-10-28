# RASH Telegram Mini App — boshlang'ich loyiha

Bu loyiha rasmlardagiga o'xshash Telegram bot menyusi va **Telegram ichida ochiladigan Mini App** uchun boshlang'ich ishchi namuna. U bot, HTTPS orqali ochiladigan veb-ilova, SQLite bazasi va test natijalarini boshqarish qismlarini birlashtiradi. /start xabaridagi inline tugmalar va chat menyusidagi `RASH Test` tugmasi Mini App'ni Telegram ichida ochadi.

## Hozir ishlaydigan funksiyalar

- `/start` orqali inline tugmali menyuni ko'rsatish; tugmalar Mini App'ni Telegram ichida ochadi. Chat menyusida ham doimiy `RASH Test` tugmasi sozlanadi.
- Test kodini kiritish, variantli (MCQ) yoki yozma javobli testni ochish va javob yuborish.
- Bir foydalanuvchining bitta testga takroriy topshirishiga yo'l qo'ymaslik.
- Natijalar, foiz va profilni ko'rish.
- Administrator uchun test yaratish, faol testlarni ro'yxatlash va yakunlash.
- Test yopilganda javob matritsasidan Rasch 1PL/JMLE uslubidagi taxminiy `theta` (ishtirokchi qobiliyati) va `b` (savol qiyinligi) parametrlarini baholash.
- Telegram Mini App `initData` imzosini serverda bot tokeni bilan tekshirish; noto'g'ri yoki eskirgan sessiyani qabul qilmaslik.

## Muhim cheklov — rasmiy Rasch bahosi

Koddagi Rasch hisoblash funksiyasi amaliy prototip uchun mo'ljallangan, mustaqil ekspert tekshiruvidan o'tkazilmagan. Aynan rasmiy milliy sertifikat natijasini yoki boshqa platformadagi ballni takrorlashini kafolatlamaydi. Ishonchli operatsion baholash uchun yetarli va reprezentativ pilot javoblari, model-fit diagnostikasi, ankor savollar/test shakllari orasidagi tenglashtirish, ekstremal natijalar siyosati va ballni shkalaga o'tkazish usuli belgilanishi kerak. Hozirgi kod foizni rasmiy sertifikat balliga aylantirmaydi.

Yozma javoblar hozircha bo'sh joylar, harf kattaligi, vergul/nuqta va minus belgisi normallashtirilgandan keyin aynan matn bo'yicha solishtiriladi. Murakkab matematik ekvivalentlik (masalan, `1/2` va `0.5`) alohida algebraik tekshiruvni talab qiladi.

## Telegram tugmalari haqida

Loyiha autentifikatsiyalangan profil, natijalar va administrator qismlariga tayanadi. Shu sababli Web App tugmalari inline keyboard va chat menyusidan ochiladi; Telegram hujjatlariga ko'ra oddiy reply-keyboard Mini App ishga tushganda `initData` bo'sh bo'lishi mumkin. Inline/menu usullari esa Mini App ichida foydalanuvchini serverda tasdiqlashga imkon beradi.

## 1. Bot ochish

1. Telegram'da `@BotFather` ni ochib `/newbot` buyrug'ini yuboring.
2. Bot nomi va username tanlang; BotFather bergan tokenni saqlang.
3. `@BotFather` ichida `/setcommands` orqali xohlasangiz `start - Botni boshlash` va `help - Yordam` buyruqlarini qo'shing.

## 2. Loyiha sozlamalari

`.env.example` faylidan `.env` nusxa yarating va quyidagilarni to'ldiring:

```env
BOT_TOKEN=BotFather_BERGAN_TOKEN
WEBAPP_URL=https://sizning-haqiqiy-domeningiz.uz
ADMIN_IDS=123456789
DATABASE_PATH=./rash_bot.sqlite3
WEB_SERVER_HOST=0.0.0.0
WEB_SERVER_PORT=8080
INIT_DATA_MAX_AGE_SECONDS=86400
```

`ADMIN_IDS` uchun Telegram ID ni bilish: botni ishga tushirib `/id` yuboring. ID lar bir nechta bo'lsa vergul bilan ajrating. Tokenni hech kimga yubormang va GitHub kabi ochiq repozitoriyga joylamang.

## 3. HTTPS hosting

Telegram Web App tugmasidagi URL ommaviy va **HTTPS** bo'lishi kerak. Faqat `localhost` manzili bilan boshqa foydalanuvchilarga Mini App ochilmaydi.

Tavsiya etilgan deploy usuli: Docker qo'llab-quvvatlaydigan server/hosting.

1. Ushbu papkani o'zingizning Git repozitoriyingizga joylang yoki Docker bilan serverga nusxalang.
2. Hostingda web service yarating, Dockerfile orqali deploy qiling va `BOT_TOKEN`, `WEBAPP_URL`, `ADMIN_IDS` environment variable larini kiriting.
3. Hosting domeni berilgandan keyin uning HTTPS manzilini `WEBAPP_URL` ga yozing va servisni qayta ishga tushiring.
4. Brauzerda `https://sizning-domeningiz/health` ochilganda `{"ok":true,...}` qaytishi kerak.
5. Botga `/start` yuboring. Menyudagi tugmalar Telegram ichidagi Mini App'ni ochishi kerak.

Bitta bot token bilan polling'ni faqat **bitta** ishlayotgan nusxada ishga tushiring. Bir nechta replica polling update'larini bir-biridan tortib olishi mumkin.

## 4. Lokal ishga tushirish

Lokal kompyuterda Python 3.11+ kerak:

```bash
python -m venv .venv
# Windows:
.venv\\Scripts\\activate
# macOS/Linux:
source .venv/bin/activate
pip install -r requirements.txt
python app.py
```

Lokal `localhost` URL Telegram Web App uchun ommaviy HTTPS manzil o'rnini bosa olmaydi. Avval HTTPS hosting yoki o'zingiz boshqaradigan xavfsiz HTTPS tunnel sozlang; so'ng uning domenini `WEBAPP_URL` ga yozing.

## 5. Test ishlash tartibi

1. Administrator (ID `ADMIN_IDS` ichida bo'lgan foydalanuvchi) botdagi **Test yaratish** Mini App'ini ochadi.
2. Test nomi va savollarini kiritadi. Variantli savol uchun har bir javob variantini alohida qatorga yozadi; `To'g'ri javob` maydoni variantlardan biri bilan aynan mos kelishi kerak. Yozma savolda javob matnini kiriting.
3. Tizim test kodini beradi. Ishtirokchilar **Javoblarni tekshirish** bo'limida shu kodni kiritadi.
4. Ishtirokchi barcha javobni bir marta yuboradi. Shu vaqtning o'zida to'g'ri javoblar soni va foizi qayd etiladi.
5. Administrator **Faol testlarni yakunlash** bo'limida testni yopadi. Javoblar matritsasi asosida `theta` va savol qiyinligi `b` taxminlari qayta hisoblanadi.

## Ishga tushirishdan oldingi tekshiruv

- [ ] `.env` da token va haqiqiy HTTPS domen mavjud.
- [ ] `ADMIN_IDS` da test yaratuvchining Telegram ID si bor.
- [ ] Hostingda doimiy disk yoki persistent volume biriktirilgan (SQLite ma'lumotlari yo'qolmasligi uchun).
- [ ] Zaxira nusxa olish siyosati sozlangan.
- [ ] Sinov testida bir nechta foydalanuvchi bilan test ochish, topshirish, yopish va natijalarni ko'rish tekshirilgan.
- [ ] Ishlab chiqarishdan oldin Rasch algoritmi psixometrik mutaxassis bilan mustaqil validatsiya qilingan.
