# Complete Deployment Guide & Testing

## Deployment Guide

### Production Environment Variables

#### Backend .env.production
```
NODE_ENV=production
PORT=5000

# Database - Replace with your MongoDB Atlas URI
MONGODB_URI=mongodb+srv://<username>:<password>@cluster.mongodb.net/netflix-analytics?retryWrites=true&w=majority

# JWT Configuration
JWT_SECRET=your-super-secure-production-secret-key-min-32-characters
JWT_EXPIRE=7d

# Client URL - Your deployed frontend URL
CLIENT_URL=https://your-frontend-app.vercel.app

# CORS Origins (comma-separated)
CORS_ORIGINS=https://your-frontend-app.vercel.app,https://www.your-domain.com
```

#### Frontend .env.production
```
REACT_APP_API_URL=https://your-backend-api.herokuapp.com/api
REACT_APP_NAME=Netflix Analytics Dashboard
REACT_APP_VERSION=1.0.0
```

---

## Backend Deployment

### Option 1: Heroku Deployment

#### 1. Install Heroku CLI
```bash
# macOS
brew tap heroku/brew && brew install heroku

# Windows
# Download from https://devcenter.heroku.com/articles/heroku-cli
```

#### 2. Deploy to Heroku
```bash
# Navigate to backend directory
cd backend

# Login to Heroku
heroku login

# Create new app
heroku create netflix-analytics-api

# Add MongoDB addon (or use MongoDB Atlas)
heroku addons:create mongolab:sandbox

# Set environment variables
heroku config:set NODE_ENV=production
heroku config:set JWT_SECRET=your-secret-key
heroku config:set CLIENT_URL=https://your-frontend.vercel.app

# Deploy
git init
git add .
git commit -m "Initial deployment"
git push heroku main

# Check logs
heroku logs --tail
```

#### 3. Heroku Procfile
```
web: node server.js
```

---

### Option 2: Railway Deployment

#### 1. Create railway.json
```json
{
  "build": {
    "builder": "NIXPACKS"
  },
  "deploy": {
    "startCommand": "node server.js",
    "restartPolicyType": "ON_FAILURE"
  }
}
```

#### 2. Deploy
```bash
# Install Railway CLI
npm i -g @railway/cli

# Login
railway login

# Initialize project
railway init

# Deploy
railway up

# Add environment variables in Railway dashboard
```

---

### Option 3: Render Deployment

#### 1. Create render.yaml
```yaml
services:
  - type: web
    name: netflix-analytics-api
    env: node
    buildCommand: npm install
    startCommand: node server.js
    envVars:
      - key: NODE_ENV
        value: production
      - key: MONGODB_URI
        sync: false
      - key: JWT_SECRET
        generateValue: true
      - key: CLIENT_URL
        sync: false
```

#### 2. Deploy via Render Dashboard
- Connect GitHub repository
- Set environment variables
- Deploy automatically

---

## Frontend Deployment

### Option 1: Vercel Deployment (Recommended)

#### 1. Install Vercel CLI
```bash
npm i -g vercel
```

#### 2. Deploy
```bash
# Navigate to frontend directory
cd frontend

# Login to Vercel
vercel login

# Deploy
vercel

# For production
vercel --prod
```

#### 3. vercel.json Configuration
```json
{
  "version": 2,
  "builds": [
    {
      "src": "package.json",
      "use": "@vercel/static-build",
      "config": {
        "distDir": "build"
      }
    }
  ],
  "routes": [
    {
      "src": "/static/(.*)",
      "dest": "/static/$1"
    },
    {
      "src": "/(.*)",
      "dest": "/index.html"
    }
  ]
}
```

---

### Option 2: Netlify Deployment

#### 1. netlify.toml
```toml
[build]
  command = "npm run build"
  publish = "build"

[[redirects]]
  from = "/*"
  to = "/index.html"
  status = 200

[build.environment]
  REACT_APP_API_URL = "https://your-backend-api.herokuapp.com/api"
```

#### 2. Deploy
```bash
# Install Netlify CLI
npm install -g netlify-cli

# Login
netlify login

# Deploy
netlify deploy --prod
```

---

### Option 3: AWS S3 + CloudFront

#### 1. Build the app
```bash
npm run build
```

#### 2. Deploy to S3
```bash
# Install AWS CLI
pip install awscli

# Configure AWS
aws configure

# Create S3 bucket
aws s3 mb s3://netflix-analytics-dashboard

# Upload build files
aws s3 sync build/ s3://netflix-analytics-dashboard

# Enable static website hosting
aws s3 website s3://netflix-analytics-dashboard \
  --index-document index.html \
  --error-document index.html
```

---

## MongoDB Atlas Setup

### 1. Create Cluster
- Go to https://www.mongodb.com/cloud/atlas
- Create free cluster
- Choose cloud provider and region

### 2. Configure Network Access
- Add IP address: 0.0.0.0/0 (Allow from anywhere)
- Or add specific IPs

### 3. Create Database User
- Username: `netflixadmin`
- Password: Generate secure password
- Role: Atlas admin

### 4. Get Connection String
```
mongodb+srv://netflixadmin:<password>@cluster0.xxxxx.mongodb.net/netflix-analytics?retryWrites=true&w=majority
```

### 5. Import Data to Atlas
```bash
# Using mongoimport
mongoimport --uri "mongodb+srv://username:password@cluster.mongodb.net/netflix-analytics" \
  --collection netflixcontents \
  --type csv \
  --headerline \
  --file netflix_titles.csv

# Or use MongoDB Compass GUI
```

---

## Testing Guide

### Backend Testing

#### 1. Install Testing Dependencies
```bash
npm install --save-dev jest supertest mongodb-memory-server
```

#### 2. Test Configuration (jest.config.js)
```javascript
module.exports = {
  testEnvironment: 'node',
  coveragePathIgnorePatterns: ['/node_modules/'],
  testTimeout: 10000,
};
```

#### 3. Sample Tests (tests/auth.test.js)
```javascript
const request = require('supertest');
const app = require('../server');
const User = require('../models/User');

describe('Authentication Tests', () => {
  beforeAll(async () => {
    // Setup test database
  });

  afterAll(async () => {
    // Cleanup
  });

  describe('POST /api/auth/register', () => {
    it('should register a new user', async () => {
      const res = await request(app)
        .post('/api/auth/register')
        .send({
          name: 'Test User',
          email: 'test@example.com',
          password: 'password123'
        });
      
      expect(res.statusCode).toBe(201);
      expect(res.body.success).toBe(true);
      expect(res.body.data.user).toHaveProperty('email', 'test@example.com');
    });

    it('should not register duplicate email', async () => {
      const res = await request(app)
        .post('/api/auth/register')
        .send({
          name: 'Test User',
          email: 'test@example.com',
          password: 'password123'
        });
      
      expect(res.statusCode).toBe(400);
    });
  });

  describe('POST /api/auth/login', () => {
    it('should login with valid credentials', async () => {
      const res = await request(app)
        .post('/api/auth/login')
        .send({
          email: 'test@example.com',
          password: 'password123'
        });
      
      expect(res.statusCode).toBe(200);
      expect(res.body.data).toHaveProperty('token');
    });

    it('should not login with invalid credentials', async () => {
      const res = await request(app)
        .post('/api/auth/login')
        .send({
          email: 'test@example.com',
          password: 'wrongpassword'
        });
      
      expect(res.statusCode).toBe(401);
    });
  });
});
```

#### 4. Run Tests
```bash
npm test
```

---

### Frontend Testing

#### 1. Install Testing Libraries
```bash
npm install --save-dev @testing-library/react @testing-library/jest-dom @testing-library/user-event
```

#### 2. Sample Tests (src/components/__tests__/Login.test.tsx)
```typescript
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import { BrowserRouter } from 'react-router-dom';
import Login from '../pages/Login';
import { AuthProvider } from '../contexts/AuthContext';

const MockLogin = () => (
  <BrowserRouter>
    <AuthProvider>
      <Login />
    </AuthProvider>
  </BrowserRouter>
);

describe('Login Component', () => {
  it('renders login form', () => {
    render(<MockLogin />);
    expect(screen.getByLabelText(/email/i)).toBeInTheDocument();
    expect(screen.getByLabelText(/password/i)).toBeInTheDocument();
    expect(screen.getByRole('button', { name: /sign in/i })).toBeInTheDocument();
  });

  it('shows error for invalid email', async () => {
    render(<MockLogin />);
    
    const emailInput = screen.getByLabelText(/email/i);
    const submitButton = screen.getByRole('button', { name: /sign in/i });
    
    fireEvent.change(emailInput, { target: { value: 'invalid-email' } });
    fireEvent.click(submitButton);
    
    await waitFor(() => {
      expect(screen.getByText(/invalid email/i)).toBeInTheDocument();
    });
  });
});
```

#### 3. Run Tests
```bash
npm test
```

---

## Performance Optimization

### Backend Optimization

#### 1. Enable Compression
```javascript
const compression = require('compression');
app.use(compression());
```

#### 2. Enable Caching Headers
```javascript
app.use((req, res, next) => {
  res.set('Cache-Control', 'public, max-age=300');
  next();
});
```

#### 3. Database Indexing
```javascript
// Add in models
netflixContentSchema.index({ title: 'text', description: 'text' });
netflixContentSchema.index({ type: 1, release_year: -1 });
```

### Frontend Optimization

#### 1. Code Splitting
```typescript
import { lazy, Suspense } from 'react';

const Dashboard = lazy(() => import('./pages/Dashboard'));
const Analytics = lazy(() => import('./pages/Analytics'));

// In routes
<Suspense fallback={<Loading />}>
  <Dashboard />
</Suspense>
```

#### 2. Image Optimization
```typescript
// Use WebP format
// Lazy load images
// Use srcset for responsive images
```

#### 3. Bundle Analysis
```bash
npm install --save-dev webpack-bundle-analyzer
npm run build -- --stats
npx webpack-bundle-analyzer build/bundle-stats.json
```

---

## Monitoring & Logging

### Backend Monitoring

#### 1. Morgan Logger
```javascript
const morgan = require('morgan');
app.use(morgan('combined'));
```

#### 2. Winston Logger
```javascript
const winston = require('winston');

const logger = winston.createLogger({
  level: 'info',
  format: winston.format.json(),
  transports: [
    new winston.transports.File({ filename: 'error.log', level: 'error' }),
    new winston.transports.File({ filename: 'combined.log' })
  ]
});
```

### Frontend Monitoring

#### 1. Google Analytics
```typescript
// Add to index.html
<script async src="https://www.googletagmanager.com/gtag/js?id=GA_ID"></script>
```

#### 2. Error Tracking (Sentry)
```bash
npm install @sentry/react
```

---

## Security Checklist

- ✅ HTTPS enabled
- ✅ Environment variables secured
- ✅ JWT tokens with expiration
- ✅ Password hashing with bcrypt
- ✅ CORS properly configured
- ✅ Input validation
- ✅ Rate limiting
- ✅ Helmet.js for security headers
- ✅ MongoDB injection prevention
- ✅ XSS protection

---

## Final Checklist

### Before Deployment
- [ ] All environment variables configured
- [ ] Database connection tested
- [ ] Authentication working
- [ ] All API endpoints tested
- [ ] Frontend connects to backend
- [ ] CORS configured correctly
- [ ] Error handling implemented
- [ ] Logs configured
- [ ] Security measures in place
- [ ] Performance optimized

### After Deployment
- [ ] SSL certificate active
- [ ] Database accessible
- [ ] All features working
- [ ] No console errors
- [ ] Monitoring active
- [ ] Backup strategy in place
- [ ] Documentation updated
- [ ] User testing completed
