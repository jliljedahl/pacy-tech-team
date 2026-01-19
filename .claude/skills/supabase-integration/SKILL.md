---
name: supabase-integration
description: |
  Patterns for connecting applications to Supabase (auth, database, storage).
  Use when: implementing authentication, setting up Supabase client in frontend
  or backend, creating auth middleware, or connecting React apps to Supabase.
allowed-tools:
  - Read
  - Write
  - Edit
  - Glob
---

# Supabase Integration

## Client Types

| Client | Use | RLS |
|--------|-----|-----|
| Service Role | Server-side, admin operations | Bypasses |
| Anon Key | Client-side, user requests | Respects |
| User Client | Server-side with user token | Respects |

## Environment Variables

### Backend (.env)

```bash
SUPABASE_URL=https://PROJECT.supabase.co
SUPABASE_SERVICE_KEY=eyJ...  # service_role key
SUPABASE_ANON_KEY=eyJ...     # anon key
```

### Frontend (.env)

```bash
# Vite: VITE_ prefix required
VITE_SUPABASE_URL=https://PROJECT.supabase.co
VITE_SUPABASE_ANON_KEY=eyJ...

# Next.js: NEXT_PUBLIC_ prefix required
NEXT_PUBLIC_SUPABASE_URL=https://PROJECT.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=eyJ...
```

## Backend Client Setup

File: `backend/src/lib/supabase.ts`

```typescript
import { createClient } from '@supabase/supabase-js';

// Service role client - bypasses RLS
// ONLY use server-side, never expose to client
export const supabaseAdmin = createClient(
  process.env.SUPABASE_URL!,
  process.env.SUPABASE_SERVICE_KEY!,
  {
    auth: {
      autoRefreshToken: false,
      persistSession: false,
    },
  }
);

// Create client for specific user's token (respects RLS)
export function createUserClient(accessToken: string) {
  return createClient(
    process.env.SUPABASE_URL!,
    process.env.SUPABASE_ANON_KEY!,
    {
      global: {
        headers: {
          Authorization: `Bearer ${accessToken}`,
        },
      },
    }
  );
}
```

## Frontend Client Setup

File: `frontend/src/lib/supabase.ts`

```typescript
import { createClient } from '@supabase/supabase-js';

const supabaseUrl = import.meta.env.VITE_SUPABASE_URL;
const supabaseAnonKey = import.meta.env.VITE_SUPABASE_ANON_KEY;

if (!supabaseUrl || !supabaseAnonKey) {
  console.warn('Supabase credentials not configured.');
}

export const supabase = createClient(
  supabaseUrl || 'https://placeholder.supabase.co',
  supabaseAnonKey || 'placeholder'
);
```

## Auth Middleware (Express)

File: `backend/src/middleware/auth.ts`

```typescript
import { Request, Response, NextFunction } from 'express';
import { supabaseAdmin } from '../lib/supabase';

// Extend Express Request type
declare global {
  namespace Express {
    interface Request {
      user?: {
        id: string;
        email: string;
        name?: string;
      };
    }
  }
}

export async function authMiddleware(
  req: Request,
  res: Response,
  next: NextFunction
) {
  const authHeader = req.headers.authorization;

  if (!authHeader?.startsWith('Bearer ')) {
    return res.status(401).json({ error: 'No token provided' });
  }

  const token = authHeader.replace('Bearer ', '');

  // Verify token with Supabase
  const { data: { user }, error } =
    await supabaseAdmin.auth.getUser(token);

  if (error || !user) {
    return res.status(401).json({ error: 'Invalid token' });
  }

  req.user = {
    id: user.id,
    email: user.email!,
    name: user.user_metadata?.name,
  };

  next();
}
```

## React Auth Context

File: `frontend/src/contexts/AuthContext.tsx`

```typescript
import { createContext, useContext, useEffect, useState, ReactNode } from 'react';
import { User, Session } from '@supabase/supabase-js';
import { supabase } from '../lib/supabase';

interface AuthContextType {
  user: User | null;
  session: Session | null;
  loading: boolean;
  signIn: (email: string, password: string) => Promise<{ error: Error | null }>;
  signUp: (email: string, password: string, name?: string) => Promise<{ error: Error | null }>;
  signOut: () => Promise<void>;
  getAccessToken: () => Promise<string | null>;
}

const AuthContext = createContext<AuthContextType | undefined>(undefined);

export function AuthProvider({ children }: { children: ReactNode }) {
  const [user, setUser] = useState<User | null>(null);
  const [session, setSession] = useState<Session | null>(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    // Get initial session
    supabase.auth.getSession().then(({ data: { session } }) => {
      setSession(session);
      setUser(session?.user ?? null);
      setLoading(false);
    });

    // Listen for auth changes
    const { data: { subscription } } = supabase.auth.onAuthStateChange(
      (_event, session) => {
        setSession(session);
        setUser(session?.user ?? null);
        setLoading(false);
      }
    );

    return () => subscription.unsubscribe();
  }, []);

  const signIn = async (email: string, password: string) => {
    const { error } = await supabase.auth.signInWithPassword({ email, password });
    return { error };
  };

  const signUp = async (email: string, password: string, name?: string) => {
    const { error } = await supabase.auth.signUp({
      email,
      password,
      options: { data: { name, full_name: name } },
    });
    return { error };
  };

  const signOut = async () => {
    await supabase.auth.signOut();
  };

  const getAccessToken = async () => {
    const { data: { session } } = await supabase.auth.getSession();
    return session?.access_token ?? null;
  };

  return (
    <AuthContext.Provider value={{
      user, session, loading, signIn, signUp, signOut, getAccessToken
    }}>
      {children}
    </AuthContext.Provider>
  );
}

export function useAuth() {
  const context = useContext(AuthContext);
  if (!context) {
    throw new Error('useAuth must be used within AuthProvider');
  }
  return context;
}
```

## Using Auth in Components

```typescript
import { useAuth } from '../contexts/AuthContext';

function Dashboard() {
  const { user, loading, signOut } = useAuth();

  if (loading) return <div>Loading...</div>;
  if (!user) return <Navigate to="/login" />;

  return (
    <div>
      <p>Welcome, {user.email}</p>
      <button onClick={signOut}>Sign Out</button>
    </div>
  );
}
```

## API Calls with Auth

```typescript
const { getAccessToken } = useAuth();

async function fetchData() {
  const token = await getAccessToken();

  const response = await fetch('/api/data', {
    headers: {
      Authorization: `Bearer ${token}`,
    },
  });

  return response.json();
}
```

## Security Checklist

- [ ] Service key NEVER exposed to frontend
- [ ] Anon key used for client-side
- [ ] VITE_/NEXT_PUBLIC_ prefix for frontend vars
- [ ] Token validated on every protected route
- [ ] RLS policies configured in Supabase
