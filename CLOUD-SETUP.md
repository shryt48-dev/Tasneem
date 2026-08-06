# تفعيل المزامنة السحابية (خطوة واحدة، 5 دقايق)

الكود بتاع تسجيل الدخول والمزامنة جاهز في `js/cloud-sync.js`، بس محتاج
بيانات مشروع Firebase بتاعك عشان يشتغل. Firebase مجاني تمامًا للاستخدام ده.

## 1) اعمل مشروع Firebase
1. افتح https://console.firebase.google.com من المتصفح (تقدر من الموبايل عادي)
2. "Add project" → اكتب اسم زي `الرحمان` → كمّل الخطوات (تقدر تسيب Google Analytics)
3. لما يخلص، من نفس الصفحة دوس على أيقونة الويب `</>` عشان تضيف "Web app"
4. اديله اسم (أي اسم) → "Register app"
5. هيوريك object فيه 6 قيم: `apiKey, authDomain, projectId, storageBucket, messagingSenderId, appId`
   — انسخهم، هتحتاجهم في خطوة 3.

## 2) فعّل تسجيل الدخول بالإيميل
1. من القائمة الجانبية: Build → Authentication → "Get started"
2. Sign-in method → دوس على "Email/Password" → فعّله (Enable) → Save

## 3) فعّل قاعدة البيانات (Firestore)
1. من القائمة الجانبية: Build → Firestore Database → "Create database"
2. اختار أي location قريب (مثلاً eur3) → Start in **production mode** → Create
3. لما تتعمل، روح تبويب "Rules" وامسح اللي فيه وحط بدله:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /backups/{uid} {
      allow read, write: if request.auth != null && request.auth.uid == uid;
    }
  }
}
```

ده معناه إن كل مستخدم يقدر يقرا ويكتب نسخته بس، محدش يشوف بيانات حد تاني. دوس "Publish".

## 4) حط البيانات في الكود
افتح `js/cloud-sync.js` (من GitHub مباشرة) ولاقي أول جزء فيه:

```js
var FIREBASE_CONFIG = {
  apiKey: 'PASTE_API_KEY_HERE',
  authDomain: 'PASTE_PROJECT_ID.firebaseapp.com',
  projectId: 'PASTE_PROJECT_ID',
  storageBucket: 'PASTE_PROJECT_ID.appspot.com',
  messagingSenderId: 'PASTE_SENDER_ID',
  appId: 'PASTE_APP_ID'
};
```

بدّل كل قيمة بالقيمة الحقيقية اللي نسختها من خطوة 1 (6 قيم). احفظ (commit) وسيب
الـ build يشتغل زي العادة.

## بعد كده
- في "المزيد" هيظهر قسم "الحساب والمزامنة السحابية" — تسجيل دخول / إنشاء حساب
- أول مرة يسجّل حساب، بياناته الحالية (اللي على الجهاز) بتترفع على السحابة فورًا
- أي تغيير بعد كده (قراءة ورد، ختمة، ذكر...) بيتزامن تلقائيًا بعد كام ثانية
- لو مسح التطبيق أو غيّر جهاز وسجّل بنفس الإيميل وكلمة السر، بياناته بترجعله تلقائي

## ملاحظة
لو مسبتش القيم دي (سيبتها زي ما هي PASTE_...)، قسم المزامنة في التطبيق هيبقى
موجود بس هيقول "غير مفعّلة بعد" وميعملش مشاكل — باقي التطبيق يفضل شغال أوفلاين
زي ما هو بالظبط.
