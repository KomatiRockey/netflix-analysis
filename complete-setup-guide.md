# COMPLETE PROJECT SETUP GUIDE - Step by Step

## 📋 **Project Structure Overview**

```
netflix-analytics-project/
│
├── backend/                          # Backend Node.js Application
│   ├── config/
│   │   └── db.js                    # MongoDB connection
│   ├── controllers/
│   │   ├── authController.js        # Authentication logic
│   │   ├── analyticsController.js   # Analytics logic
│   │   └── contentController.js     # Content CRUD logic
│   ├── middleware/
│   │   ├── auth.js                  # JWT authentication middleware
│   │   └── errorHandler.js          # Error handling middleware
│   ├── models/
│   │   ├── User.js                  # User schema
│   │   ├── NetflixContent.js        # Content schema
│   │   └── AnalyticsCache.js        # Cache schema
│   ├── routes/
│   │   ├── auth.js                  # Auth routes
│   │   ├── analytics.js             # Analytics routes
│   │   └── content.js               # Content routes
│   ├── scripts/
│   │   └── seedData.js              # Database seeding script
│   ├── .env                         # Environment variables
│   ├── .gitignore                   # Git ignore
│   ├── package.json                 # Dependencies
│   ├── Procfile                     # Heroku deployment
│   └── server.js                    # Main entry point
│
├── frontend/                         # Frontend React Application
│   ├── public/
│   │   ├── index.html               # HTML template
│   │   ├── manifest.json            # PWA manifest
│   │   └── favicon.ico              # Favicon
│   ├── src/
│   │   ├── components/
│   │   │   ├── PrivateRoute.tsx     # Protected route
│   │   │   ├── Navbar.tsx           # Navigation bar
│   │   │   ├── Loading.tsx          # Loading component
│   │   │   └── Charts/
│   │   │       ├── DistributionChart.tsx
│   │   │       ├── GenreChart.tsx
│   │   │       └── TrendsChart.tsx
│   │   ├── contexts/
│   │   │   └── AuthContext.tsx      # Auth state management
│   │   ├── hooks/
│   │   │   ├── useDebounce.ts       # Debounce hook
│   │   │   ├── useLocalStorage.ts   # LocalStorage hook
│   │   │   └── useWindowSize.ts     # Window size hook
│   │   ├── pages/
│   │   │   ├── Login.tsx            # Login page
│   │   │   ├── Register.tsx         # Register page
│   │   │   ├── Dashboard.tsx        # Dashboard page
│   │   │   ├── Analytics.tsx        # Analytics page
│   │   │   └── NotFound.tsx         # 404 page
│   │   ├── services/
│   │   │   └── api.ts               # API service layer
│   │   ├── types/
│   │   │   └── index.ts             # TypeScript types
│   │   ├── utils/
│   │   │   ├── api.utils.ts         # API utilities
│   │   │   ├── date.utils.ts        # Date utilities
│   │   │   ├── number.utils.ts      # Number utilities
│   │   │   ├── string.utils.ts      # String utilities
│   │   │   ├── validation.utils.ts  # Validation utilities
│   │   │   ├── storage.utils.ts     # Storage utilities
│   │   │   └── chart.utils.ts       # Chart utilities
│   │   ├── constants/
│   │   │   └── index.ts             # App constants
│   │   ├── App.tsx                  # Root component
│   │   ├── App.css                  # Global styles
│   │   ├── index.tsx                # App entry point
│   │   └── index.css                # Base styles
│   ├── .env                         # Environment variables
│   ├── .gitignore                   # Git ignore
│   ├── package.json                 # Dependencies
│   ├── tsconfig.json                # TypeScript config
│   ├── .eslintrc.json              # ESLint config
│   └── .prettierrc                 # Prettier config
│
├── data/
│   └── netflix_titles.csv          # Netflix dataset
│
└── README.md                        # Project documentation
```

---

## 🚀 **COMPLETE INSTALLATION GUIDE**

### **Prerequisites**
```bash
# Check if you have the required software
node --version    # Should be v16+ or v18+
npm --version     # Should be 8+ or 9+
mongod --version  # MongoDB 5.0+
git --version     # Latest version
```

---

## **STEP 1: Setup Backend**

### 1.1 Create Backend Directory
```bash
mkdir netflix-analytics-project
cd netflix-analytics-project
mkdir backend
cd backend
```

### 1.2 Initialize Node.js Project
```bash
npm init -y
```

### 1.3 Install Backend Dependencies
```bash
# Core dependencies
npm install express mongoose bcryptjs jsonwebtoken dotenv cors cookie-parser

# Development dependencies
npm install --save-dev nodemon
```

### 1.4 Create Package.json Scripts
Edit `package.json` and add:
```json
{
  "scripts": {
    "start": "node server.js",
    "dev": "nodemon server.js",
    "seed": "node scripts/seedData.js"
  }
}
```

### 1.5 Create Directory Structure
```bash
# Create all required directories
mkdir config controllers middleware models routes scripts

# Create all backend files
touch server.js
touch config/db.js
touch controllers/authController.js
touch controllers/analyticsController.js
touch controllers/contentController.js
touch middleware/auth.js
touch middleware/errorHandler.js
touch models/User.js
touch models/NetflixContent.js
touch models/AnalyticsCache.js
touch routes/auth.js
touch routes/analytics.js
touch routes/content.js
touch scripts/seedData.js
touch .env
touch .gitignore
touch Procfile
```

### 1.6 Create .env File
```bash
# Copy this content to .env
NODE_ENV=development
PORT=5000
MONGODB_URI=mongodb://localhost:27017/netflix-analytics
JWT_SECRET=your-super-secret-jwt-key-change-this-in-production-min-32-chars
JWT_EXPIRE=7d
CLIENT_URL=http://localhost:3000
```

### 1.7 Copy Backend Code
Now copy all the backend code from the provided files:
- **server.js** from `backend-server-js.md` [42]
- **Models** from `backend-models.md` [43]
- **Auth code** from `backend-auth-code.md` [44]
- **Analytics code** from `backend-analytics.md` [45]
- **Content routes** from `backend-content-routes.md` [54]
- **Package.json** from `project-setup-config.md` [48]

### 1.8 Start MongoDB
```bash
# Option 1: Local MongoDB
mongod

# Option 2: MongoDB as service (macOS)
brew services start mongodb-community

# Option 3: MongoDB as service (Linux)
sudo systemctl start mongod

# Option 4: Use MongoDB Atlas (Cloud) - see deployment guide
```

### 1.9 Test Backend
```bash
# Start the development server
npm run dev

# You should see:
# ✅ MongoDB connected successfully
# 🚀 Server running on port 5000
# 📊 Environment: development
```

---

## **STEP 2: Setup Frontend**

### 2.1 Create Frontend Directory
```bash
# Go back to project root
cd ..
```

### 2.2 Create React App with TypeScript
```bash
npx create-react-app frontend --template typescript
cd frontend
```

### 2.3 Install Frontend Dependencies
```bash
# Material-UI and styling
npm install @mui/material @mui/icons-material @emotion/react @emotion/styled

# Routing and data fetching
npm install react-router-dom @tanstack/react-query axios

# Charts
npm install recharts

# TypeScript types
npm install --save-dev @types/node
```

### 2.4 Create Directory Structure
```bash
# Create all required directories
mkdir -p src/components/Charts
mkdir -p src/contexts
mkdir -p src/hooks
mkdir -p src/pages
mkdir -p src/services
mkdir -p src/types
mkdir -p src/utils
mkdir -p src/constants

# Create all frontend files
touch src/components/PrivateRoute.tsx
touch src/components/Navbar.tsx
touch src/components/Loading.tsx
touch src/components/Charts/DistributionChart.tsx
touch src/components/Charts/GenreChart.tsx
touch src/components/Charts/TrendsChart.tsx
touch src/contexts/AuthContext.tsx
touch src/hooks/useDebounce.ts
touch src/hooks/useLocalStorage.ts
touch src/hooks/useWindowSize.ts
touch src/pages/Login.tsx
touch src/pages/Register.tsx
touch src/pages/Dashboard.tsx
touch src/pages/Analytics.tsx
touch src/pages/NotFound.tsx
touch src/services/api.ts
touch src/types/index.ts
touch src/utils/api.utils.ts
touch src/utils/date.utils.ts
touch src/utils/number.utils.ts
touch src/utils/string.utils.ts
touch src/utils/validation.utils.ts
touch src/utils/storage.utils.ts
touch src/utils/chart.utils.ts
touch src/constants/index.ts
touch .env
```

### 2.5 Create .env File
```bash
# Copy this content to frontend/.env
REACT_APP_API_URL=http://localhost:5000/api
REACT_APP_NAME=Netflix Analytics Dashboard
REACT_APP_VERSION=1.0.0
```

### 2.6 Copy Frontend Code
Now copy all the frontend code from the provided files:
- **App.tsx** from `frontend-app-core.md` [46]
- **Pages** from `frontend-pages.md` [47] and `frontend-analytics.md` [50]
- **Components** from `frontend-components.md` [51]
- **Styles & Config** from `frontend-styling-config.md` [52]
- **Utilities** from `frontend-utilities.md` [53]

### 2.7 Update tsconfig.json
Replace the content with the configuration from `frontend-styling-config.md` [52]

### 2.8 Test Frontend
```bash
# Start the development server
npm start

# Application should open at http://localhost:3000
```

---

## **STEP 3: Seed Database with Netflix Data**

### 3.1 Download Netflix Dataset
```bash
# Option 1: From Kaggle
# Visit: https://www.kaggle.com/datasets/shivamb/netflix-shows
# Download: netflix_titles.csv

# Option 2: Use sample data generator
# (provided in seedData.js)
```

### 3.2 Place Dataset
```bash
# Create data directory in project root
cd .. # Go to project root
mkdir data
# Place netflix_titles.csv in this directory
```

### 3.3 Install CSV Parser for Seeding
```bash
cd backend
npm install csv-parser
```

### 3.4 Run Seed Script
```bash
npm run seed

# You should see:
# ✅ MongoDB connected
# 🗑️  Cleared existing data
# ✅ Successfully seeded 7789 records
# 🔌 Database connection closed
```

---

## **STEP 4: Run Complete Application**

### 4.1 Terminal 1 - Backend
```bash
cd backend
npm run dev

# Backend should be running on http://localhost:5000
```

### 4.2 Terminal 2 - Frontend
```bash
cd frontend
npm start

# Frontend should be running on http://localhost:3000
```

### 4.3 Test the Application
1. Open browser: `http://localhost:3000`
2. Click "Sign Up" to create an account
3. Register with:
   - Name: Test User
   - Email: test@example.com
   - Password: password123
4. Login with your credentials
5. Explore Dashboard
6. Navigate to Analytics page
7. Test all 5 analytics tabs

---

## **STEP 5: Verify All Features**

### ✅ Authentication
- [ ] User registration works
- [ ] User login works
- [ ] JWT token is stored
- [ ] Protected routes work
- [ ] Logout works

### ✅ Dashboard
- [ ] Statistics cards display
- [ ] Pie chart renders
- [ ] Bar charts render
- [ ] Data loads from API

### ✅ Analytics
- [ ] All 5 tabs load
- [ ] Charts render properly
- [ ] Filters work
- [ ] Data updates correctly

### ✅ API Endpoints
Test with Postman or curl:
```bash
# Health check
curl http://localhost:5000/api/health

# Register
curl -X POST http://localhost:5000/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{"name":"Test","email":"test@test.com","password":"test123"}'

# Login
curl -X POST http://localhost:5000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"test@test.com","password":"test123"}'

# Get analytics (replace TOKEN with actual JWT)
curl http://localhost:5000/api/analytics/distribution \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"
```

---

## **STEP 6: Git Setup**

### 6.1 Create .gitignore Files

**Backend .gitignore:**
```
node_modules/
.env
.env.local
.env.production
npm-debug.log*
.DS_Store
```

**Frontend .gitignore:**
```
node_modules/
build/
.env
.env.local
.env.production
npm-debug.log*
.DS_Store
```

### 6.2 Initialize Git
```bash
# In project root
git init
git add .
git commit -m "Initial commit: Netflix Analytics Dashboard - Complete MERN Stack"

# Create GitHub repository and push
git remote add origin https://github.com/your-username/netflix-analytics.git
git branch -M main
git push -u origin main
```

---

## **STEP 7: Production Deployment** (Optional)

Follow the comprehensive deployment guide in `deployment-testing.md` [55] for:
- Heroku deployment
- Vercel deployment
- MongoDB Atlas setup
- Environment configuration
- SSL setup
- Monitoring

---

## 🎉 **PROJECT COMPLETE!**

You now have a fully functional, production-ready Netflix Analytics Dashboard with:

✅ **Backend (Node.js + Express + MongoDB)**
- Complete authentication system
- RESTful API with 15+ endpoints
- MongoDB aggregation pipelines
- Caching system
- Error handling

✅ **Frontend (React + TypeScript + Material-UI)**
- 5 complete pages
- 15+ reusable components
- Advanced analytics with 5 tabs
- 10+ chart visualizations
- 30+ utility functions
- Custom hooks

✅ **Features**
- User authentication with JWT
- Interactive dashboards
- Real-time analytics
- Content distribution analysis
- Genre and country analytics
- Trends over time
- Responsive design
- Netflix-themed UI

---

## 📚 **All Project Files Reference**

| File ID | Name | Content |
|---------|------|---------|
| [42] | backend-server-js.md | Express server configuration |
| [43] | backend-models.md | MongoDB schemas |
| [44] | backend-auth-code.md | Authentication system |
| [45] | backend-analytics.md | Analytics controllers |
| [46] | frontend-app-core.md | React app core |
| [47] | frontend-pages.md | Login, Register, Dashboard |
| [48] | project-setup-config.md | Configuration files |
| [49] | complete-readme.md | Documentation |
| [50] | frontend-analytics.md | Analytics page |
| [51] | frontend-components.md | Reusable components |
| [52] | frontend-styling-config.md | Styles and config |
| [53] | frontend-utilities.md | Utility functions |
| [54] | backend-content-routes.md | Content API |
| [55] | deployment-testing.md | Deployment guide |

---

## 🆘 **Troubleshooting**

### MongoDB Connection Issues
```bash
# Check if MongoDB is running
ps aux | grep mongod

# Check MongoDB logs
tail -f /usr/local/var/log/mongodb/mongo.log

# Restart MongoDB
brew services restart mongodb-community
```

### Port Already in Use
```bash
# Kill process on port 5000
lsof -ti:5000 | xargs kill -9

# Kill process on port 3000
lsof -ti:3000 | xargs kill -9
```

### CORS Issues
Make sure backend `.env` has correct `CLIENT_URL`:
```
CLIENT_URL=http://localhost:3000
```

---

**Congratulations! Your complete Netflix Analytics Dashboard is ready! 🚀**
