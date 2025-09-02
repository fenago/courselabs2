# CourseLabs.io - High-Level Architecture Diagram

## System Architecture Overview

```mermaid
graph TB
    subgraph "Client Layer"
        Browser[Web Browser]
        Mobile[Mobile Browser]
    end

    subgraph "Frontend - Next.js Application"
        subgraph "Pages/Routes"
            Home[Landing Page<br/>'/']
            Blog[Blog Section<br/>'/blog']
            Dashboard[User Dashboard<br/>'/dashboard']
            Privacy[Privacy Policy<br/>'/privacy-policy']
            TOS[Terms of Service<br/>'/tos']
        end

        subgraph "UI Components"
            Hero[Hero Section]
            Features[Features<br/>Grid/Accordion/Listicle]
            Pricing[Pricing Component]
            Testimonials[Testimonials]
            CTA[Call to Action]
            Auth[Auth Components<br/>ButtonSignin/ButtonAccount]
            Payment[Payment Components<br/>ButtonCheckout]
        end

        subgraph "Styling"
            Tailwind[Tailwind CSS]
            DaisyUI[DaisyUI Themes]
            CustomCSS[Custom Styles]
        end
    end

    subgraph "API Layer - Next.js API Routes"
        AuthAPI["/api/auth/*<br/>NextAuth Handler"]
        LeadAPI["/api/lead<br/>Lead Collection"]
        StripeAPI["/api/stripe/*<br/>Checkout & Portal"]
        WebhookAPI["/api/webhook/stripe<br/>Payment Events"]
    end

    subgraph "Authentication Layer"
        NextAuth[NextAuth.js]
        GoogleOAuth[Google Provider]
        EmailAuth[Email Provider<br/>Magic Links]
        JWT[JWT Sessions]
    end

    subgraph "External Services"
        Stripe[Stripe<br/>Payment Processing]
        Resend[Resend<br/>Email Service]
        GoogleAPI[Google OAuth API]
        Crisp[Crisp Chat<br/>Customer Support]
    end

    subgraph "Data Layer"
        MongoDB[(MongoDB Atlas)]
        subgraph "Models"
            UserModel[User Model<br/>- name<br/>- email<br/>- customerId<br/>- priceId<br/>- hasAccess]
            LeadModel[Lead Model<br/>- email<br/>- timestamps]
        end
        Mongoose[Mongoose ODM]
    end

    subgraph "Infrastructure"
        Vercel[Vercel Hosting<br/>or Similar]
        CDN[CDN/Static Assets]
    end

    %% Client connections
    Browser --> Home
    Mobile --> Home
    
    %% Page connections
    Home --> Hero
    Home --> Features
    Home --> Pricing
    Home --> Testimonials
    Home --> CTA
    
    Dashboard --> Auth
    Dashboard --> Payment
    
    %% Component to API connections
    Auth --> AuthAPI
    Payment --> StripeAPI
    CTA --> LeadAPI
    
    %% API to Auth Layer
    AuthAPI --> NextAuth
    NextAuth --> GoogleOAuth
    NextAuth --> EmailAuth
    NextAuth --> JWT
    
    %% Auth to External
    GoogleOAuth --> GoogleAPI
    EmailAuth --> Resend
    
    %% API to External Services
    StripeAPI --> Stripe
    WebhookAPI --> Stripe
    LeadAPI --> Resend
    
    %% Data connections
    AuthAPI --> MongoDB
    LeadAPI --> MongoDB
    StripeAPI --> MongoDB
    WebhookAPI --> MongoDB
    
    MongoDB --> UserModel
    MongoDB --> LeadModel
    Mongoose --> MongoDB
    
    %% Styling connections
    UI Components -.-> Tailwind
    Tailwind --> DaisyUI
    
    %% Infrastructure
    Frontend --> Vercel
    API Layer --> Vercel
    CDN --> Browser
    
    %% Customer Support
    Home --> Crisp

    classDef frontend fill:#60A5FA,stroke:#328CC1,stroke-width:2px,color:#fff
    classDef api fill:#D9B310,stroke:#FBBF24,stroke-width:2px,color:#000
    classDef external fill:#1D5F6B,stroke:#0B3C5D,stroke-width:2px,color:#fff
    classDef data fill:#328CC1,stroke:#0B3C5D,stroke-width:2px,color:#fff
    classDef infra fill:#F7FAFC,stroke:#4A5568,stroke-width:2px,color:#000

    class Home,Blog,Dashboard,Privacy,TOS,Hero,Features,Pricing,Testimonials,CTA,Auth,Payment frontend
    class AuthAPI,LeadAPI,StripeAPI,WebhookAPI api
    class Stripe,Resend,GoogleAPI,Crisp external
    class MongoDB,UserModel,LeadModel,Mongoose data
    class Vercel,CDN infra
```

## Technology Stack

### Frontend
- **Framework**: Next.js 14 (React 18)
- **Language**: TypeScript
- **Styling**: Tailwind CSS + DaisyUI
- **State Management**: React Hooks
- **Forms**: Native React forms with validation
- **Animations**: Framer Motion (planned)
- **Icons**: Heroicons

### Backend
- **Runtime**: Node.js
- **API**: Next.js API Routes
- **Authentication**: NextAuth.js
- **Database**: MongoDB with Mongoose ODM
- **Payment Processing**: Stripe
- **Email Service**: Resend
- **Customer Support**: Crisp Chat

### DevOps & Infrastructure
- **Hosting**: Vercel (or similar serverless platform)
- **Database Hosting**: MongoDB Atlas
- **CDN**: Cloudfront/AWS S3 (configured)
- **Environment Management**: .env variables
- **Build Tool**: Next.js build system
- **Package Manager**: npm

## Data Flow

### User Registration Flow
1. User clicks "Sign In" → Triggers NextAuth flow
2. Choose Google OAuth or Email Magic Link
3. Authentication verified → User created in MongoDB
4. Session created with JWT → User redirected to dashboard

### Payment Flow
1. User selects plan on Pricing page
2. Stripe Checkout session created via API
3. User completes payment on Stripe
4. Stripe webhook confirms payment
5. User model updated with `hasAccess: true` and `priceId`
6. User gains access to premium features

### Lead Collection Flow
1. Visitor enters email in CTA form
2. Email validated and sent to `/api/lead`
3. Lead stored in MongoDB
4. Confirmation email sent via Resend
5. Lead can be nurtured for conversion

## Key Features

### Security
- JWT-based session management
- OAuth 2.0 with Google
- Secure payment processing with Stripe
- Environment variable protection
- CORS and CSRF protection

### Scalability
- Serverless architecture via Vercel
- MongoDB Atlas for database scaling
- CDN for static asset delivery
- Stateless API design
- Efficient caching strategies

### User Experience
- Mobile-first responsive design
- Dark/Light theme support
- Real-time chat support via Crisp
- Magic link authentication option
- Fast page loads with Next.js optimization

## Component Library Structure

### Marketing Components
- Hero, Features, Pricing, Testimonials
- Problem/Solution sections
- Call-to-action buttons
- Social proof elements

### Application Components
- Authentication flows
- Dashboard layouts
- Payment integration
- User account management
- Blog system with MDX support

## API Endpoints

### Public Endpoints
- `GET /` - Landing page
- `GET /blog/*` - Blog content
- `GET /privacy-policy` - Privacy policy
- `GET /tos` - Terms of service

### Protected Endpoints
- `GET /dashboard` - User dashboard (requires auth)
- `POST /api/lead` - Lead collection
- `POST /api/stripe/create-checkout` - Initialize payment
- `POST /api/stripe/create-portal` - Customer portal
- `POST /api/webhook/stripe` - Payment webhooks

### Authentication Endpoints
- `/api/auth/signin` - Sign in page
- `/api/auth/signout` - Sign out
- `/api/auth/callback/*` - OAuth callbacks
- `/api/auth/verify-request` - Email verification

---

*This architecture diagram represents the current state of the CourseLabs.io codebase, built on a modern Next.js stack with emphasis on reliability, scalability, and user experience - directly addressing the core pain points of corporate technical training companies.*
