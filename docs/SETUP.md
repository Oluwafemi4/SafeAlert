# SafeAlert Setup Guide

## Prerequisites

- Node.js 16+ and npm 8+
- Git
- Firebase Account
- Android Studio (for Android development)
- Xcode 13+ (for iOS development)
- Android SDK 21+ (API level)
- iOS deployment target: 12.0+

## Backend Setup

### 1. Install Dependencies

```bash
cd backend
npm install
```

### 2. Environment Configuration

```bash
cp .env.example .env
```

**Edit `.env` with your configuration:**
```env
# Server
NODE_ENV=development
PORT=3000
HOST=localhost

# Database
DATABASE_URL=postgresql://user:password@localhost:5432/safealert
MONGODB_URI=mongodb://localhost:27017/safealert

# Firebase
FIREBASE_PROJECT_ID=your-project-id
FIREBASE_PRIVATE_KEY=your-private-key
FIREBASE_CLIENT_EMAIL=your-service-account@project.iam.gserviceaccount.com

# JWT
JWT_SECRET=your-secret-key
JWT_EXPIRY=3600

# APIs
GOOGLE_MAPS_API_KEY=your-api-key
MAPBOX_API_KEY=your-api-key

# Social Media
FACEBOOK_APP_ID=your-app-id
FACEBOOK_APP_SECRET=your-app-secret
TWITTER_API_KEY=your-api-key
TWITTER_API_SECRET=your-api-secret

# Redis
REDIS_URL=redis://localhost:6379

# Logging
LOG_LEVEL=debug
```

### 3. Database Setup

#### Using PostgreSQL
```bash
# Install PostgreSQL
# macOS
brew install postgresql

# Ubuntu
sudo apt-get install postgresql

# Start service
brew services start postgresql  # macOS
sudo service postgresql start   # Linux

# Create database
createdb safealert

# Run migrations
npm run migrate
```

#### Using MongoDB
```bash
# Install MongoDB
# macOS
brew install mongodb-community

# Ubuntu
sudo apt-get install mongodb

# Start service
brew services start mongodb-community  # macOS
sudo service mongod start              # Linux
```

### 4. Start Backend Server

```bash
npm run dev
```

Server will start at `http://localhost:3000`

### 5. Verify Backend

```bash
# Test API
curl http://localhost:3000/api/v1/health

# Should return
{"status":"ok","timestamp":"2026-06-12T10:30:00Z"}
```

## Mobile Setup

### 1. Install Dependencies

```bash
cd mobile
npm install
cd ios && pod install && cd ..
```

### 2. Configure Firebase

#### For iOS
1. Go to Firebase Console
2. Create new iOS app
3. Download `GoogleService-Info.plist`
4. Add to Xcode: `ios/SafeAlert/GoogleService-Info.plist`

#### For Android
1. Go to Firebase Console
2. Create new Android app
3. Download `google-services.json`
4. Add to: `android/app/google-services.json`

### 3. Configure Environment

Create `.env` file in mobile root:
```env
API_URL=http://localhost:3000
API_KEY=your-api-key
ENVIRONMENT=development
LOG_LEVEL=debug
```

### 4. iOS Setup

```bash
# Install CocoaPods dependencies
cd mobile/ios
pod install
cd ..

# Start development server
npm start

# In another terminal, run iOS app
npm run ios
```

**Requirements:**
- Xcode 13+
- iOS 12.0+ deployment target
- Apple Developer account (for physical device)

### 5. Android Setup

```bash
# Start development server
npm start

# In another terminal, run Android app
npm run android
```

**Requirements:**
- Android Studio
- Android SDK 21+ (API level)
- Emulator or physical device with USB debugging enabled

### 6. Permissions Configuration

#### iOS (ios/SafeAlert/Info.plist)
```xml
<key>NSLocationWhenInUseUsageDescription</key>
<string>SafeAlert needs access to your location to track your position</string>

<key>NSLocationAlwaysAndWhenInUseUsageDescription</key>
<string>SafeAlert needs access to your location</string>

<key>NSBluetoothPeripheralUsageDescription</key>
<string>SafeAlert needs Bluetooth access for mesh networking</string>

<key>NSBluetoothAlwaysUsageDescription</key>
<string>SafeAlert needs Bluetooth access</string>

<key>NSLocalNetworkUsageDescription</key>
<string>SafeAlert needs WiFi access for mesh networking</string>
```

#### Android (android/app/src/main/AndroidManifest.xml)
```xml
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.BLUETOOTH" />
<uses-permission android:name="android.permission.BLUETOOTH_ADMIN" />
<uses-permission android:name="android.permission.BLUETOOTH_SCAN" />
<uses-permission android:name="android.permission.BLUETOOTH_CONNECT" />
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
<uses-permission android:name="android.permission.CHANGE_WIFI_STATE" />
<uses-permission android:name="android.permission.INTERNET" />
```

## Development

### Running in Development Mode

**Terminal 1 - Backend**
```bash
cd backend
npm run dev
```

**Terminal 2 - React Native Metro**
```bash
cd mobile
npm start
```

**Terminal 3 - iOS Simulator**
```bash
cd mobile
npm run ios
```

Or **Terminal 3 - Android Emulator**
```bash
cd mobile
npm run android
```

### Debugging

#### Backend
```bash
# Start with Node inspector
node --inspect-brk backend/src/server.ts

# Access in Chrome: chrome://inspect
```

#### Mobile - React Native Debugger
```bash
# Install debugger (optional)
npm install -g react-native-debugger

# Open debugger
react-native-debugger

# Shake device or press Cmd+D (iOS) / Cmd+M (Android)
# Select "Debug JS Remotely"
```

### Code Formatting and Linting

```bash
# Format all code
npm run format

# Lint backend
npm run backend:lint

# Lint mobile
npm run mobile:lint

# Fix linting issues
npm run backend:lint -- --fix
npm run mobile:lint -- --fix
```

## Testing

### Backend Tests
```bash
npm run backend:test

# With coverage
npm run backend:test -- --coverage

# Watch mode
npm run backend:test -- --watch
```

### Mobile Tests
```bash
npm run mobile:test

# With coverage
npm run mobile:test -- --coverage
```

## Building for Production

### Backend
```bash
cd backend
npm run build

# Start production build
npm start
```

### iOS
```bash
cd mobile

# Build for App Store
npm run ios:release

# Or using Xcode
# Select "Product" → "Scheme" → "SafeAlert Release"
# Select "Product" → "Build"
```

### Android
```bash
cd mobile

# Build release APK
npm run android:release

# Or using Android Studio
# Select "Build" → "Generate Signed Bundle / APK"
```

## Troubleshooting

### Backend Issues

**Port Already in Use**
```bash
# Find process using port 3000
lsof -i :3000

# Kill process
kill -9 <PID>
```

**Database Connection Error**
- Ensure PostgreSQL/MongoDB is running
- Verify connection string in `.env`
- Check database exists

### Mobile Issues

**Metro Bundler Won't Start**
```bash
# Clear cache
npm run mobile:clean

# Restart
npm start
```

**Pod Install Fails (iOS)**
```bash
# Remove old pods
rm -rf ios/Pods ios/Podfile.lock

# Reinstall
cd ios && pod install && cd ..
```

**Gradle Build Fails (Android)**
```bash
# Clean build
cd android && ./gradlew clean && cd ..

# Rebuild
npm run android
```

**Permission Errors**
- iOS: Check Info.plist permissions
- Android: Check AndroidManifest.xml permissions
- Grant permissions in app settings

**Emulator Issues**
- Restart emulator: `emulator -avd <emulator_name>`
- Clear emulator data: `emulator -avd <emulator_name> -wipe-data`

## Production Deployment

### Backend

#### Using Docker
```bash
# Build image
docker build -t safealert-backend .

# Run container
docker run -p 3000:3000 -e NODE_ENV=production safealert-backend
```

#### Using Firebase Functions
```bash
npm install -g firebase-tools

firebase login
firebase deploy
```

### Mobile

#### App Store (iOS)
1. Generate signing certificate
2. Create App ID in Apple Developer
3. Build in Xcode using Release configuration
4. Archive and upload to App Store Connect

#### Google Play (Android)
1. Generate signing key
2. Sign APK/AAB
3. Upload to Google Play Console

## Monitoring

### Backend Monitoring
```bash
# Install monitoring tools
npm install pm2
npm install prometheus-client

# Start with PM2
pm2 start backend/src/server.ts
pm2 logs
```

### Mobile Monitoring
- Use Firebase Crashlytics
- Monitor network requests with Charles Proxy
- Use React Native Debugger

## Documentation

For more detailed information, see:
- [Architecture](./ARCHITECTURE.md)
- [API Documentation](./API.md)
- [Mesh Protocol](./MESH_PROTOCOL.md)
- [Contributing](./CONTRIBUTING.md)

---

**Last Updated**: 2026-06-12
