iamazonu/
├── backend/
│   ├── models/
│   ├── routes/
│   ├── middleware/
│   └── server.js
├── frontend/
│   ├── public/
│   ├── src/
│   └── package.json


---

### 🧩 Key Backend Features (Node.js/Express)

#### 1. **User Model (with age & ID/selfie upload)**

js
// models/User.js
const mongoose = require('mongoose');

const UserSchema = new mongoose.Schema({
  email: { type: String, required: true, unique: true },
  password: { type: String, required: true },
  ageVerified: { type: Boolean, default: false },
  selfieWithID: { type: String }, // link to file in S3 or Cloudinary
  dateOfBirth: { type: Date },
  createdAt: { type: Date, default: Date.now }
});

module.exports = mongoose.model('User', UserSchema);


#### 2. **Ad Model**

js
// models/Ad.js
const mongoose = require('mongoose');

const AdSchema = new mongoose.Schema({
  userId: { type: mongoose.Schema.Types.ObjectId, ref: 'User' },
  title: String,
  description: String,
  category: String, // e.g., "men seeking women"
  city: String,
  state: String,
  isPaid: { type: Boolean, default: false },
  paymentType: { type: String, enum: ['daily', 'weekly', 'monthly', 'spotlight'], default: 'free' },
  createdAt: { type: Date, default: Date.now }
});

module.exports = mongoose.model('Ad', AdSchema);


#### 3. **Age Verification Middleware**

js
// middleware/ageVerify.js
module.exports = (req, res, next) => {
  const dob = new Date(req.user.dateOfBirth);
  const age = new Date().getFullYear() - dob.getFullYear();
  if (age < 18) {
    return res.status(403).json({ error: "Must be 18+" });
  }
  next();
};


#### 4. **Upload Route (ID + Selfie Upload)**

js
// routes/upload.js
const express = require('express');
const multer = require('multer');
const router = express.Router();

// Set up storage (you can integrate Cloudinary or S3 here)
const storage = multer.diskStorage({
  destination: 'uploads/',
  filename: (req, file, cb) => {
    cb(null, Date.now() + "-" + file.originalname);
  }
});
const upload = multer({ storage });

router.post('/upload-id', upload.single('selfieWithID'), (req, res) => {
  // Save file path to user's profile
  // In production, validate & process further
  res.json({ file: req.file.path });
});

module.exports = router;


---

### 💡 Frontend (React or Next.js)

Build forms for:

* **Sign up/Login**
* **Upload ID/selfie**
* **Post new ad (free or paid)**
* **Filter/search by city/state/category**
* **Display posts with spotlight/headline banners for paid ads**

---
