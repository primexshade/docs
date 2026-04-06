# Lab 11: Implementation Details

## 1. Project Directory Structure
The project follows the standard Next.js 14 App Router structure, optimized for scalability and maintainability.

```mermaid
graph TD
    Root[Root Directory]
    Src[src/]
    App[app/]
    Components[components/]
    Lib[lib/]
    Public[public/]
    Types[types/]
    
    Root --> Src
    Root --> Public
    
    Src --> App
    Src --> Components
    Src --> Lib
    Src --> Types
    
    App --> Layout[layout.tsx (Global Layout)]
    App --> Page[page.tsx (Landing Page)]
    App --> API[api/]
    
    Components --> UI[ui/ (Shadcn Components)]
    Components --> Features[features/ (EventCard, Forms)]
    
    Lib --> DB[firebase.ts]
    Lib --> CMS[contentful.ts]
    Lib --> Utils[utils.ts]
```

## 2. Key Modules & Code Snippets

### 2.1 Firebase Configuration (`lib/firebase.ts`)
This module initializes the Firebase application only once (Singleton pattern) to prevent hot-reloading errors.

```typescript
import { initializeApp, getApps } from "firebase/app";
import { getFirestore } from "firebase/firestore";

const firebaseConfig = {
  apiKey: process.env.NEXT_PUBLIC_FIREBASE_API_KEY,
  authDomain: process.env.NEXT_PUBLIC_FIREBASE_AUTH_DOMAIN,
  projectId: process.env.NEXT_PUBLIC_FIREBASE_PROJECT_ID,
  storageBucket: process.env.NEXT_PUBLIC_FIREBASE_STORAGE_BUCKET,
  messagingSenderId: process.env.NEXT_PUBLIC_FIREBASE_MESSAGING_SENDER_ID,
  appId: process.env.NEXT_PUBLIC_FIREBASE_APP_ID,
};

// Initialize Firebase
const app = getApps().length === 0 ? initializeApp(firebaseConfig) : getApps()[0];
const db = getFirestore(app);

export { db };
```

### 2.2 API Route: Event Registration (`app/api/register/route.ts`)
This server-side function handles form submissions, validates data, checks capacity, and triggers emails.

```typescript
import { NextResponse } from 'next/server';
import { db } from '@/lib/firebase';
import { collection, addDoc, query, where, getDocs } from 'firebase/firestore';

export async function POST(request: Request) {
  try {
    const body = await request.json();
    const { name, email, phone, eventId } = body;

    // 1. Validation
    if (!name || !email || !eventId) {
      return NextResponse.json({ error: 'Missing fields' }, { status: 400 });
    }

    // 2. Check for Duplicate Registration
    const q = query(
      collection(db, 'registrations'), 
      where('email', '==', email), 
      where('eventId', '==', eventId)
    );
    const existing = await getDocs(q);
    
    if (!existing.empty) {
      return NextResponse.json({ error: 'Already registered' }, { status: 409 });
    }

    // 3. Save to Database
    const docRef = await addDoc(collection(db, 'registrations'), {
      name, email, phone, eventId,
      status: 'confirmed',
      createdAt: new Date().toISOString()
    });

    return NextResponse.json({ success: true, id: docRef.id }, { status: 201 });
  } catch (error) {
    return NextResponse.json({ error: 'Internal Server Error' }, { status: 500 });
  }
}
```

## 3. Environment Variables
The application relies on the following environment variables for security. These are stored in `.env.local` locally and in Vercel Project Settings for production.

| Variable Name | Description |
| :--- | :--- |
| `NEXT_PUBLIC_FIREBASE_API_KEY` | Public key for Firebase Client SDK. |
| `CONTENTFUL_SPACE_ID` | ID of the Contentful Content Space. |
| `CONTENTFUL_ACCESS_TOKEN` | Read-only delivery token for fetching events. |
| `RESEND_API_KEY` | Server-side key for sending emails via Resend. |
