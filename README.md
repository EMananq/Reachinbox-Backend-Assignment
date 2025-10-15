# ReachInBox - AI-Powered Email Outreach Tool

## 📋 Overview

ReachInBox is an intelligent email automation platform that leverages AI to categorize incoming emails, generate contextual responses, and streamline email outreach workflows. The application integrates with Gmail API to provide seamless email management and automated reply generation.

## 🚀 Features

- **OAuth2 Authentication** - Secure Google OAuth integration
- **Email Categorization** - AI-powered email classification (Interested/Not Interested/More Information)
- **Automated Responses** - Context-aware reply generation
- **Gmail API Integration** - Direct access to Gmail messages and labels
- **Queue Management** - Background job processing with BullMQ
- **Modern UI** - React-based responsive frontend
- **Real-time Processing** - Efficient email handling and response system

## 🏗️ Architecture

### System Components

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   React Frontend│    │  Express Server │    │   MongoDB       │
│                 │◄──►│                 │◄──►│                 │
│   - Authentication│    │   - OAuth2      │    │   - User Data   │
│   - Dashboard    │    │   - Gmail API   │    │   - Email Logs  │
│   - Interest Form│    │   - AI Processing│    │                 │
└─────────────────┘    └─────────────────┘    └─────────────────┘
                              │
                              ▼
                    ┌─────────────────┐
                    │   External APIs │
                    │                 │
                    │   - Gmail API   │
                    │   - OpenAI API  │
                    │   - Nodemailer  │
                    └─────────────────┘
```

### Technology Stack

**Backend:**
- Node.js & Express.js
- MongoDB with Mongoose
- Passport.js for OAuth2 authentication
- BullMQ for queue management
- Nodemailer for email sending
- OpenAI API for email analysis

**Frontend:**
- React 18
- React Router DOM
- Axios for API communication
- JWT for token management

**External Services:**
- Google Gmail API
- OpenAI GPT API
- MongoDB Atlas

## 📁 Project Structure

```
Reachinbox-Backend-Assignment/
├── backend/
│   ├── auth.js                          # Google OAuth2 configuration
│   ├── captureEmails.js                 # Email fetching and analysis
│   ├── db.js                           # MongoDB connection
│   ├── emailSend.js                    # Email sending utilities
│   ├── generateReplyBasedOnContext.js  # AI reply generation
│   ├── index.js                        # Main server file
│   ├── replyMessages.js                # Email templates
│   ├── Middleware/
│   │   ├── auth.middleware.js          # Authentication middleware
│   │   └── isLogin.js                  # Login verification
│   ├── modal/
│   │   └── gmail.modal.js              # Gmail data models
│   └── queue/
│       ├── producer.js                 # Queue job producer
│       └── worker.js                   # Background job worker
└── frontend/
    ├── src/
    │   ├── component/
    │   │   ├── Home.jsx                # Landing page
    │   │   ├── Interest.jsx            # Interest form
    │   │   └── Navbar/
    │   │       └── Navbar.jsx          # Navigation component
    │   ├── AllRoutes/
    │   │   └── AllRoutes.jsx           # Route configuration
    │   ├── App.js                      # Main app component
    │   └── index.js                    # Entry point
    └── public/                         # Static assets
```

## ⚙️ Setup Instructions

### Prerequisites

- Node.js (v14 or higher)
- MongoDB (local or Atlas)
- Google Cloud Console account
- OpenAI API key

### 1. Clone the Repository

```bash
git clone https://github.com/EMananq/Reachinbox-Backend-Assignment.git
cd Reachinbox-Backend-Assignment
```

### 2. Backend Setup

```bash
# Install dependencies
npm install

# Create environment file
cp .env.example .env
```

**Configure `.env` file:**

```env
# Server Configuration
PORT=8080
CALL_FRONTEND_URL=http://localhost:3000

# Database
MONGOURL=mongodb://localhost:27017/reachinbox
# or MongoDB Atlas: mongodb+srv://username:password@cluster.mongodb.net/reachinbox

# Google OAuth2
GOOGLE_CLIENT_ID=your_google_client_id
GOOGLE_CLIENT_SECRET=your_google_client_secret
REDIRECT_URI=http://localhost:8080/auth/google/callback

# Email Configuration
GMAIL_USER=your_gmail@gmail.com
GMAIL_APP_PASSWORD=your_gmail_app_password

# OpenAI
OPENAI_API_KEY=your_openai_api_key
```

### 3. Frontend Setup

```bash
cd frontend
npm install
```

### 4. Google Cloud Console Setup

1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Create a new project or select existing one
3. Enable Gmail API
4. Create OAuth2 credentials
5. Add authorized redirect URI: `http://localhost:8080/auth/google/callback`
6. Download credentials and update `.env` file

### 5. Gmail App Password Setup

1. Enable 2-Factor Authentication on your Gmail account
2. Generate an App Password for this application
3. Use this password in `GMAIL_APP_PASSWORD` environment variable

### 6. Run the Application

**Start Backend:**
```bash
npm run server
```

**Start Frontend (in new terminal):**
```bash
cd frontend
npm start
```

The application will be available at:
- Frontend: http://localhost:3000
- Backend: http://localhost:8080

## 🔧 Feature Implementation

### 1. OAuth2 Authentication Flow

```javascript
// Google OAuth2 Strategy Configuration
passport.use(new GoogleStrategy({
    clientID: process.env.GOOGLE_CLIENT_ID,
    clientSecret: process.env.GOOGLE_CLIENT_SECRET,
    callbackURL: process.env.REDIRECT_URI,
    scope: [
        'email', 'profile',
        'https://www.googleapis.com/auth/gmail.readonly',
        'https://www.googleapis.com/auth/gmail.compose',
        'https://www.googleapis.com/auth/gmail.modify'
    ]
}));
```

### 2. Email Categorization System

The application analyzes incoming emails using keyword-based classification:

```javascript
const checkEmailContent = (content) => {
    content = content.toLowerCase();
    
    const interestedArr = ["developer", "software", "engineer"];
    const moreInfoArr = ["school", "course", "job"];
    
    if (interestedArr.some(keyword => content.includes(keyword))) {
        return "Interested";
    } else if (moreInfoArr.some(keyword => content.includes(keyword))) {
        return "More Information";
    } else {
        return "Not Interested";
    }
};
```

### 3. Automated Reply Generation

Context-aware responses are generated based on email categories:

```javascript
const generateReply = (category) => {
    switch (category) {
        case 'Interested':
            return interestedHtmlInterestReply;
        case 'Not Interested':
            return notInterestedHtmlInterestReply;
        case 'More Information':
            return moreInformationtmlInterestReply;
        default:
            return defaultResponse;
    }
};
```

### 4. Gmail API Integration

```javascript
const getEmails = async (gmail, messages) => {
    try {
        for (let i = 0; i < 5; i++) {
            await captureEmails(gmail, messages, i);
        }
    } catch (err) {
        console.error("Error:", err);
    }
};
```

## 🔌 API Endpoints

### Authentication
- `GET /auth/google` - Initiate Google OAuth
- `GET /auth/google/callback` - OAuth callback handler
- `GET /auth/google/success` - Success redirect with token

### Email Management
- `POST /user/interest` - Submit interest form
- `GET /` - Welcome endpoint

## 🚀 Deployment

### Environment Variables for Production

Ensure all environment variables are properly configured:
- Update `CALL_FRONTEND_URL` to your production frontend URL
- Configure MongoDB Atlas connection string
- Set up production OAuth2 credentials
- Use secure session secrets

### Recommended Deployment Platforms

**Backend:**
- Render.com
- Railway
- Heroku
- DigitalOcean App Platform

**Frontend:**
- Vercel
- Netlify
- GitHub Pages

**Database:**
- MongoDB Atlas

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📝 License

This project is licensed under the ISC License.

## 🆘 Support

For support and questions, please open an issue in the GitHub repository.

---

**Built with ❤️ using Node.js, React, and AI**