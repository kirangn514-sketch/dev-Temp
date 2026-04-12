# Next.js Interview Topics: Basic to Advanced

A comprehensive guide covering all major Next.js concepts for interview preparation.

---

## Table of Contents

1. [Introduction to Next.js](#1-introduction-to-nextjs)
2. [Project Structure](#2-project-structure)
3. [Routing — Pages Router](#3-routing--pages-router)
4. [Routing — App Router (Next.js 13+)](#4-routing--app-router-nextjs-13)
5. [Rendering Strategies](#5-rendering-strategies)
6. [Data Fetching](#6-data-fetching)
7. [Server Components vs Client Components](#7-server-components-vs-client-components)
8. [API Routes](#8-api-routes)
9. [Middleware](#9-middleware)
10. [Image Optimization](#10-image-optimization)
11. [Font Optimization](#11-font-optimization)
12. [Metadata & SEO](#12-metadata--seo)
13. [Navigation & Linking](#13-navigation--linking)
14. [Dynamic Routes](#14-dynamic-routes)
15. [Layouts & Templates](#15-layouts--templates)
16. [Error Handling](#16-error-handling)
17. [Loading UI & Streaming](#17-loading-ui--streaming)
18. [Caching in Next.js](#18-caching-in-nextjs)
19. [Environment Variables](#19-environment-variables)
20. [Authentication](#20-authentication)
21. [Internationalization (i18n)](#21-internationalization-i18n)
22. [Performance Optimization](#22-performance-optimization)
23. [Deployment](#23-deployment)
24. [Next.js vs React — Key Differences](#24-nextjs-vs-react--key-differences)

---

## 1. Introduction to Next.js

### What is Next.js?
Next.js is a **React framework** built on top of React that provides additional features and optimizations out of the box. It was created by Vercel and enables developers to build full-stack web applications with React.

### Why Next.js over plain React?

Plain React is a UI library — it only handles the view layer. You must manually set up:
- Routing (React Router)
- Server-side rendering
- Code splitting
- SEO optimization
- API layer
- Image optimization

Next.js gives you all of this **built-in**.

### Key Features of Next.js

| Feature | Description |
|---------|-------------|
| File-based Routing | Pages/routes are created by adding files to folders |
| SSR | Server-Side Rendering out of the box |
| SSG | Static Site Generation for pre-built pages |
| ISR | Incremental Static Regeneration — update static pages without full rebuild |
| API Routes | Build backend APIs inside the same project |
| Image Optimization | Automatic image resizing, lazy loading, format conversion |
| Code Splitting | Automatic — only loads JS needed for current page |
| TypeScript Support | Built-in, zero config |
| CSS Support | CSS Modules, Tailwind, Sass — all supported |
| Full Stack | Frontend + Backend in one project |

### Next.js Version History (Key Milestones)

| Version | Key Addition |
|---------|-------------|
| Next.js 9 | API Routes introduced |
| Next.js 10 | Image component, i18n support |
| Next.js 12 | Middleware, React 18 support |
| Next.js 13 | App Router, Server Components, Layouts |
| Next.js 14 | Server Actions stable, Partial Prerendering (PPR) |
| Next.js 15 | Async APIs, improved caching, React 19 support |

---

## 2. Project Structure

### Creating a Next.js App

```bash
# Using create-next-app (recommended)
npx create-next-app@latest my-app

# With TypeScript
npx create-next-app@latest my-app --typescript

# With Tailwind CSS
npx create-next-app@latest my-app --tailwind
```

### App Router Project Structure (Next.js 13+)

```
my-app/
├── app/                        ← App Router root
│   ├── layout.tsx              ← Root layout (wraps all pages)
│   ├── page.tsx                ← Home page (route: /)
│   ├── globals.css             ← Global styles
│   ├── about/
│   │   └── page.tsx            ← About page (route: /about)
│   ├── blog/
│   │   ├── page.tsx            ← Blog list (route: /blog)
│   │   └── [slug]/
│   │       └── page.tsx        ← Blog detail (route: /blog/:slug)
│   └── api/
│       └── hello/
│           └── route.ts        ← API endpoint (route: /api/hello)
├── components/                 ← Reusable components
├── lib/                        ← Utility functions, DB clients
├── public/                     ← Static files (images, icons)
├── next.config.js              ← Next.js configuration
├── tsconfig.json               ← TypeScript configuration
└── package.json
```

### Pages Router Project Structure (Next.js 12 and below)

```
my-app/
├── pages/
│   ├── index.tsx               ← Home page (route: /)
│   ├── about.tsx               ← About page (route: /about)
│   ├── blog/
│   │   ├── index.tsx           ← Blog list (route: /blog)
│   │   └── [slug].tsx          ← Blog detail (route: /blog/:slug)
│   └── api/
│       └── hello.ts            ← API route (route: /api/hello)
├── components/
├── public/
├── styles/
└── next.config.js
```

### Special Files in App Router

| File | Purpose |
|------|---------|
| `page.tsx` | Defines a unique UI for a route (makes the route publicly accessible) |
| `layout.tsx` | Shared UI that wraps pages (persists across navigation) |
| `loading.tsx` | Loading UI shown while page is loading (uses React Suspense) |
| `error.tsx` | Error UI for a route segment |
| `not-found.tsx` | UI shown when `notFound()` is called |
| `template.tsx` | Like layout but re-mounts on every navigation |
| `route.ts` | API endpoint (replaces `pages/api`) |
| `middleware.ts` | Runs before request is processed |

---

## 3. Routing — Pages Router

The Pages Router uses the file system to define routes. Each file in the `pages/` directory becomes a route.

### Basic Routing

```
pages/index.tsx          → /
pages/about.tsx          → /about
pages/contact.tsx        → /contact
pages/blog/index.tsx     → /blog
pages/blog/post.tsx      → /blog/post
```

### Dynamic Routes

```
pages/blog/[slug].tsx    → /blog/any-post-name
pages/user/[id].tsx      → /user/123
```

```tsx
// pages/blog/[slug].tsx
import { useRouter } from 'next/router';

export default function BlogPost() {
  const router = useRouter();
  const { slug } = router.query;  // Access the dynamic segment

  return <h1>Post: {slug}</h1>;
}
```

### Catch-All Routes

```
pages/docs/[...slug].tsx  → /docs/a  /docs/a/b  /docs/a/b/c
```

```tsx
// router.query.slug = ['a', 'b', 'c'] for /docs/a/b/c
const { slug } = router.query; // ['a', 'b', 'c']
```

### Optional Catch-All Routes

```
pages/docs/[[...slug]].tsx → /docs  /docs/a  /docs/a/b
```

### Navigating Programmatically (Pages Router)

```tsx
import { useRouter } from 'next/router';

const router = useRouter();

router.push('/about');                        // Navigate
router.push('/blog/[slug]', '/blog/hello');   // Dynamic route
router.replace('/login');                     // Replace (no history entry)
router.back();                                // Go back
```

---

## 4. Routing — App Router (Next.js 13+)

The App Router is the modern routing system introduced in Next.js 13. It is built on top of React Server Components.

### Folder-Based Routing

Every folder inside `app/` that contains a `page.tsx` becomes a route.

```
app/page.tsx              → /
app/about/page.tsx        → /about
app/blog/page.tsx         → /blog
app/blog/[slug]/page.tsx  → /blog/:slug
```

### Route Groups

Use parentheses `(groupName)` to organize routes without affecting the URL.

```
app/
├── (marketing)/
│   ├── about/page.tsx     → /about  (not /marketing/about)
│   └── contact/page.tsx   → /contact
├── (dashboard)/
│   ├── layout.tsx         ← Separate layout for dashboard only
│   ├── overview/page.tsx  → /overview
│   └── settings/page.tsx  → /settings
```

### Parallel Routes

Show multiple pages simultaneously in the same layout using `@slot` folders.

```
app/
├── layout.tsx
├── @team/page.tsx         ← Rendered in @team slot
├── @analytics/page.tsx    ← Rendered in @analytics slot
└── page.tsx
```

```tsx
// app/layout.tsx
export default function Layout({ children, team, analytics }) {
  return (
    <>
      {children}
      {team}
      {analytics}
    </>
  );
}
```

### Intercepting Routes

Display a route in the context of another route (e.g., open a modal without leaving the page).

```
app/
├── feed/page.tsx
├── (.)photo/[id]/page.tsx   ← Intercepts /photo/:id from /feed
└── photo/[id]/page.tsx      ← Full page for /photo/:id
```

`(.)` = intercept at same level
`(..)` = intercept one level up
`(...)` = intercept from root

---

## 5. Rendering Strategies

This is one of the most important Next.js concepts for interviews.

### 1. Static Site Generation (SSG)

Pages are rendered at **build time**. The HTML is pre-generated and served from a CDN.

**Best for:** Content that doesn't change often — blogs, docs, marketing pages.

```tsx
// Pages Router: getStaticProps
export async function getStaticProps() {
  const posts = await fetchPosts();
  return {
    props: { posts },           // Passed to component as props
    revalidate: 60,             // ISR: Regenerate every 60 seconds
  };
}

export default function Blog({ posts }) {
  return <ul>{posts.map(p => <li key={p.id}>{p.title}</li>)}</ul>;
}
```

```tsx
// App Router: Fetch with no cache option (default = static)
export default async function Blog() {
  const posts = await fetch('https://api.example.com/posts', {
    cache: 'force-cache'  // Default: static generation
  }).then(r => r.json());

  return <ul>{posts.map(p => <li key={p.id}>{p.title}</li>)}</ul>;
}
```

### 2. Server-Side Rendering (SSR)

Pages are rendered on the **server at request time**. Fresh HTML on every request.

**Best for:** Personalized pages, real-time data, pages requiring auth.

```tsx
// Pages Router: getServerSideProps
export async function getServerSideProps(context) {
  const { params, req, res, query } = context;
  const data = await fetchUserData(params.id);

  return { props: { data } };
}

export default function UserPage({ data }) {
  return <div>{data.name}</div>;
}
```

```tsx
// App Router: Fetch with no-store (SSR)
export default async function UserPage({ params }) {
  const user = await fetch(`https://api.example.com/users/${params.id}`, {
    cache: 'no-store'   // SSR: Fetch on every request
  }).then(r => r.json());

  return <div>{user.name}</div>;
}
```

### 3. Incremental Static Regeneration (ISR)

Static pages that **automatically regenerate** in the background after a specified time. Best of both SSG (speed) and SSR (fresh data).

```tsx
// Pages Router
export async function getStaticProps() {
  const data = await fetchData();
  return {
    props: { data },
    revalidate: 60,   // Regenerate at most once every 60 seconds
  };
}
```

```tsx
// App Router: using next.revalidate
export default async function Page() {
  const data = await fetch('https://api.example.com/data', {
    next: { revalidate: 60 }   // ISR: Revalidate every 60 seconds
  }).then(r => r.json());

  return <div>{data.title}</div>;
}
```

### 4. Client-Side Rendering (CSR)

Rendering happens in the **browser**. The server sends a minimal HTML shell and JavaScript handles everything.

```tsx
'use client';  // Required in App Router for client components

import { useState, useEffect } from 'react';

export default function Dashboard() {
  const [data, setData] = useState(null);

  useEffect(() => {
    fetch('/api/dashboard').then(r => r.json()).then(setData);
  }, []);

  if (!data) return <p>Loading...</p>;
  return <div>{data.value}</div>;
}
```

### Rendering Strategy Comparison

| Strategy | When HTML is Generated | Data Freshness | Speed | Use Case |
|----------|----------------------|----------------|-------|----------|
| SSG | Build time | Stale | Fastest (CDN) | Blogs, docs, marketing |
| ISR | Build time + revalidate | Mostly fresh | Fast | Product pages, news |
| SSR | Every request | Always fresh | Slower | Auth pages, dashboards |
| CSR | In browser | Always fresh | Slowest initial | Highly interactive UIs |

---

## 6. Data Fetching

### Pages Router Data Fetching Functions

#### getStaticProps — Fetch at Build Time
```tsx
export async function getStaticProps(context) {
  const { params, locale, preview } = context;

  const data = await fetch(`https://api.example.com/posts`).then(r => r.json());

  // Return 404 if data not found
  if (!data) return { notFound: true };

  // Redirect
  if (data.moved) return { redirect: { destination: '/new-url', permanent: true } };

  return {
    props: { data },
    revalidate: 30,   // ISR
  };
}
```

#### getStaticPaths — Define Dynamic Static Routes
```tsx
// Required when using getStaticProps with dynamic routes
export async function getStaticPaths() {
  const posts = await fetch('https://api.example.com/posts').then(r => r.json());

  const paths = posts.map(post => ({
    params: { slug: post.slug }
  }));

  return {
    paths,
    fallback: false,        // 404 for unknown paths
    // fallback: true       // Show loading state, then generate
    // fallback: 'blocking' // SSR the page on first request, then cache
  };
}
```

#### getServerSideProps — Fetch on Every Request
```tsx
export async function getServerSideProps({ req, res, params, query }) {
  // Access cookies, headers
  const token = req.cookies.token;

  // Set cache headers
  res.setHeader('Cache-Control', 'public, s-maxage=10, stale-while-revalidate=59');

  const data = await fetchProtectedData(token);
  if (!data) return { redirect: { destination: '/login', permanent: false } };

  return { props: { data } };
}
```

### App Router Data Fetching

In the App Router, data fetching is done directly in Server Components using `async/await`.

```tsx
// app/posts/page.tsx — Server Component (default)
export default async function PostsPage() {
  // This runs on the server — secrets are never exposed to client
  const posts = await fetch('https://api.example.com/posts', {
    cache: 'force-cache',          // SSG (default)
    // cache: 'no-store',          // SSR
    // next: { revalidate: 60 },   // ISR
  }).then(r => r.json());

  return (
    <ul>
      {posts.map(post => <li key={post.id}>{post.title}</li>)}
    </ul>
  );
}
```

### Parallel Data Fetching

Fetch multiple data sources simultaneously to avoid waterfall.

```tsx
// BAD: Sequential — total time = time(A) + time(B)
const user = await fetchUser(id);
const posts = await fetchUserPosts(id);

// GOOD: Parallel — total time = max(time(A), time(B))
const [user, posts] = await Promise.all([
  fetchUser(id),
  fetchUserPosts(id),
]);
```

### Server Actions (Next.js 14+)

Server Actions let you run server-side code directly from form submissions or event handlers — no API route needed.

```tsx
// app/contact/page.tsx
export default function ContactPage() {

  async function submitForm(formData: FormData) {
    'use server';  // This function runs on the server
    const name = formData.get('name');
    const email = formData.get('email');
    await saveToDatabase({ name, email });
  }

  return (
    <form action={submitForm}>
      <input name="name" placeholder="Your name" />
      <input name="email" placeholder="Your email" />
      <button type="submit">Submit</button>
    </form>
  );
}
```

---

## 7. Server Components vs Client Components

This is a critical concept introduced in Next.js 13 with the App Router.

### Server Components (Default in App Router)

- Render on the **server only**
- Can use `async/await` directly
- Can access backend resources (DB, file system, secrets) directly
- **Cannot** use browser APIs (`window`, `document`)
- **Cannot** use React hooks (`useState`, `useEffect`)
- **Cannot** use event handlers (`onClick`, `onChange`)
- Zero JavaScript sent to the browser for the component itself

```tsx
// app/products/page.tsx — Server Component (no 'use client' directive)
import { db } from '@/lib/db';

export default async function ProductsPage() {
  // Direct DB access — never exposed to client
  const products = await db.query('SELECT * FROM products');

  return (
    <ul>
      {products.map(p => <li key={p.id}>{p.name} — ₹{p.price}</li>)}
    </ul>
  );
}
```

### Client Components

- Rendered on the **client (browser)**
- Must add `'use client'` directive at the top of the file
- Can use hooks, browser APIs, event handlers
- JavaScript IS sent to the browser

```tsx
'use client';  // ← This directive marks it as a Client Component

import { useState } from 'react';

export default function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(c => c + 1)}>Increment</button>
    </div>
  );
}
```

### When to Use Which

| Requirement | Component Type |
|-------------|---------------|
| Fetch data from DB | Server Component |
| Access backend secrets (API keys) | Server Component |
| Use useState / useEffect | Client Component |
| Handle onClick, onChange | Client Component |
| Use browser APIs (localStorage, window) | Client Component |
| Use third-party client libraries | Client Component |
| Reduce JS bundle size | Server Component |

### Composition Pattern

Keep Server Components as the outer shell and push Client Components to the leaves.

```tsx
// app/dashboard/page.tsx — Server Component (outer)
import Chart from './Chart';       // Client Component
import { fetchStats } from '@/lib/stats';

export default async function DashboardPage() {
  const stats = await fetchStats();  // Server-side fetch

  // Pass data DOWN to Client Component as props
  return (
    <div>
      <h1>Dashboard</h1>
      <Chart data={stats} />   {/* Client Component receives server data */}
    </div>
  );
}

// components/Chart.tsx — Client Component (leaf)
'use client';
import { LineChart } from 'recharts';

export default function Chart({ data }) {
  return <LineChart data={data} />;
}
```

---

## 8. API Routes

### Pages Router API Routes

Files in `pages/api/` become API endpoints.

```tsx
// pages/api/properties.ts
import type { NextApiRequest, NextApiResponse } from 'next';

export default function handler(req: NextApiRequest, res: NextApiResponse) {
  if (req.method === 'GET') {
    res.status(200).json({ properties: [] });
  } else if (req.method === 'POST') {
    const { title, price } = req.body;
    // Save to DB...
    res.status(201).json({ message: 'Created', title, price });
  } else {
    res.setHeader('Allow', ['GET', 'POST']);
    res.status(405).json({ error: 'Method Not Allowed' });
  }
}
```

### App Router API Routes (Route Handlers)

Files named `route.ts` inside `app/` define API endpoints.

```tsx
// app/api/properties/route.ts
import { NextRequest, NextResponse } from 'next/server';

// GET /api/properties
export async function GET(request: NextRequest) {
  const { searchParams } = new URL(request.url);
  const city = searchParams.get('city');

  const properties = await fetchProperties(city);
  return NextResponse.json({ properties });
}

// POST /api/properties
export async function POST(request: NextRequest) {
  const body = await request.json();
  const { title, price, city } = body;

  const property = await createProperty({ title, price, city });
  return NextResponse.json({ property }, { status: 201 });
}
```

### Dynamic API Routes

```tsx
// app/api/properties/[id]/route.ts
export async function GET(
  request: NextRequest,
  { params }: { params: { id: string } }
) {
  const property = await getPropertyById(params.id);
  if (!property) {
    return NextResponse.json({ error: 'Not Found' }, { status: 404 });
  }
  return NextResponse.json(property);
}

export async function DELETE(
  request: NextRequest,
  { params }: { params: { id: string } }
) {
  await deleteProperty(params.id);
  return NextResponse.json({ message: 'Deleted' }, { status: 200 });
}
```

### Setting Headers and Cookies in Route Handlers

```tsx
import { cookies, headers } from 'next/headers';

export async function GET() {
  // Read cookies
  const cookieStore = cookies();
  const token = cookieStore.get('token');

  // Read headers
  const headersList = headers();
  const userAgent = headersList.get('user-agent');

  // Set cookie in response
  const response = NextResponse.json({ data: 'ok' });
  response.cookies.set('session', 'abc123', {
    httpOnly: true,
    secure: true,
    maxAge: 60 * 60 * 24,  // 1 day
  });

  return response;
}
```

---

## 9. Middleware

Middleware runs **before** the request is processed. It sits between the incoming request and the route handler.

```
Request → Middleware → Route Handler / Page → Response
```

### Creating Middleware

Create a `middleware.ts` file in the **root** of your project (not inside `app/`).

```tsx
// middleware.ts
import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';

export function middleware(request: NextRequest) {
  const token = request.cookies.get('token')?.value;
  const isAuthPage = request.nextUrl.pathname.startsWith('/login');
  const isProtectedRoute = request.nextUrl.pathname.startsWith('/dashboard');

  // Redirect unauthenticated users from protected routes
  if (isProtectedRoute && !token) {
    return NextResponse.redirect(new URL('/login', request.url));
  }

  // Redirect authenticated users away from login page
  if (isAuthPage && token) {
    return NextResponse.redirect(new URL('/dashboard', request.url));
  }

  // Allow request to continue
  return NextResponse.next();
}

// Define which routes middleware applies to
export const config = {
  matcher: ['/dashboard/:path*', '/login'],
};
```

### Middleware Use Cases

- **Authentication:** Redirect unauthenticated users
- **Authorization:** Role-based access (admin, user)
- **Geolocation:** Redirect users based on country
- **A/B Testing:** Show different versions to different users
- **Rate Limiting:** Block excessive requests
- **Rewriting URLs:** Change URLs without redirecting
- **Adding Headers:** Add security headers to all responses

### URL Rewriting vs Redirecting

```tsx
// Rewrite: User sees /dashboard but server serves /app/dashboard
// URL in browser does NOT change
return NextResponse.rewrite(new URL('/app/dashboard', request.url));

// Redirect: Browser is told to go to a new URL
// URL in browser CHANGES
return NextResponse.redirect(new URL('/login', request.url));
```

---

## 10. Image Optimization

Next.js provides a built-in `<Image>` component that automatically:
- Resizes images to the correct size
- Converts to modern formats (WebP, AVIF)
- Lazy loads images by default
- Prevents Cumulative Layout Shift (CLS) with size reservation

### Basic Usage

```tsx
import Image from 'next/image';

// Local image (Next.js automatically knows width and height)
import profilePic from './profile.jpg';

export default function Profile() {
  return (
    <Image
      src={profilePic}
      alt="Profile picture"
      // width and height not needed for local imports
    />
  );
}

// Remote image (must specify width and height)
export default function PropertyCard() {
  return (
    <Image
      src="https://cdn.example.com/property.jpg"
      alt="Property exterior"
      width={800}
      height={600}
      priority={false}   // true = preload (use for above-the-fold images)
    />
  );
}
```

### Fill Mode (Responsive Images)

```tsx
<div style={{ position: 'relative', width: '100%', height: '400px' }}>
  <Image
    src="/hero.jpg"
    alt="Hero"
    fill                         // Fills the parent container
    style={{ objectFit: 'cover' }}
    sizes="(max-width: 768px) 100vw, 50vw"  // Responsive size hints
  />
</div>
```

### Configuring Remote Image Domains

```js
// next.config.js
module.exports = {
  images: {
    remotePatterns: [
      {
        protocol: 'https',
        hostname: 'cdn.example.com',
        pathname: '/images/**',
      },
    ],
  },
};
```

### Why use next/image over <img>?

| Feature | `<img>` tag | `next/image` |
|---------|------------|--------------|
| Lazy loading | Manual | Automatic |
| Format conversion | No | Yes (WebP/AVIF) |
| Responsive sizing | Manual | Automatic |
| CLS prevention | Manual | Automatic |
| Performance | Manual optimization | Built-in |

---

## 11. Font Optimization

Next.js automatically optimizes fonts by hosting them locally — no external network request to Google Fonts at runtime.

```tsx
// app/layout.tsx
import { Inter, Roboto_Mono } from 'next/font/google';

const inter = Inter({
  subsets: ['latin'],
  variable: '--font-inter',    // CSS variable
  display: 'swap',
});

const robotoMono = Roboto_Mono({
  subsets: ['latin'],
  variable: '--font-roboto-mono',
});

export default function RootLayout({ children }) {
  return (
    <html lang="en" className={`${inter.variable} ${robotoMono.variable}`}>
      <body>{children}</body>
    </html>
  );
}
```

```css
/* globals.css */
body {
  font-family: var(--font-inter);
}
code {
  font-family: var(--font-roboto-mono);
}
```

### Local Fonts

```tsx
import localFont from 'next/font/local';

const myFont = localFont({
  src: './fonts/MyFont.woff2',
  variable: '--font-my-font',
});
```

---

## 12. Metadata & SEO

### Static Metadata (App Router)

```tsx
// app/about/page.tsx
import type { Metadata } from 'next';

export const metadata: Metadata = {
  title: 'About Us | My App',
  description: 'Learn more about our company and mission.',
  keywords: ['nextjs', 'react', 'web development'],
  openGraph: {
    title: 'About Us',
    description: 'Learn more about our mission',
    images: ['/og-image.jpg'],
    type: 'website',
  },
  twitter: {
    card: 'summary_large_image',
    title: 'About Us',
    images: ['/twitter-image.jpg'],
  },
};

export default function AboutPage() {
  return <h1>About Us</h1>;
}
```

### Dynamic Metadata

```tsx
// app/blog/[slug]/page.tsx
import type { Metadata } from 'next';

type Props = { params: { slug: string } };

export async function generateMetadata({ params }: Props): Promise<Metadata> {
  const post = await fetchPost(params.slug);

  return {
    title: `${post.title} | Blog`,
    description: post.excerpt,
    openGraph: {
      images: [post.coverImage],
    },
  };
}

export default async function BlogPostPage({ params }: Props) {
  const post = await fetchPost(params.slug);
  return <article>{post.content}</article>;
}
```

### Metadata Template (Root Layout)

```tsx
// app/layout.tsx
export const metadata: Metadata = {
  title: {
    template: '%s | My App',   // %s = page-specific title
    default: 'My App',         // Fallback when no title is set
  },
  description: 'My awesome Next.js application',
};
```

Pages that set `title: 'About'` will render as `"About | My App"`.

---

## 13. Navigation & Linking

### Link Component

```tsx
import Link from 'next/link';

// Basic link
<Link href="/about">About</Link>

// Dynamic link
<Link href={`/blog/${post.slug}`}>{post.title}</Link>

// With object href
<Link href={{ pathname: '/blog', query: { page: 2 } }}>Page 2</Link>

// Prefetch control (default: true for visible links)
<Link href="/heavy-page" prefetch={false}>Heavy Page</Link>

// Replace history instead of push
<Link href="/login" replace>Login</Link>
```

### Programmatic Navigation (App Router)

```tsx
'use client';
import { useRouter } from 'next/navigation';  // ← Note: 'next/navigation' not 'next/router'

export default function MyComponent() {
  const router = useRouter();

  const handleSubmit = () => {
    router.push('/dashboard');      // Navigate forward
    router.replace('/login');       // Navigate, no history entry
    router.back();                  // Go back
    router.forward();               // Go forward
    router.refresh();               // Refresh current route (re-fetch server data)
    router.prefetch('/about');      // Manually prefetch
  };
}
```

### usePathname, useSearchParams, useParams (App Router)

```tsx
'use client';
import { usePathname, useSearchParams, useParams } from 'next/navigation';

export default function NavComponent() {
  const pathname = usePathname();         // '/blog/hello-world'
  const searchParams = useSearchParams(); // URLSearchParams object
  const params = useParams();             // { slug: 'hello-world' }

  const page = searchParams.get('page'); // '2'

  return <p>Current path: {pathname}</p>;
}
```

---

## 14. Dynamic Routes

### Basic Dynamic Segment

```
app/blog/[slug]/page.tsx   →  /blog/hello-world
```

```tsx
// app/blog/[slug]/page.tsx
export default async function BlogPost({ params }: { params: { slug: string } }) {
  const post = await fetchPost(params.slug);
  return <article>{post.content}</article>;
}
```

### Catch-All Segments

```
app/docs/[...slug]/page.tsx  →  /docs/a  /docs/a/b  /docs/a/b/c
```

```tsx
// params.slug = ['a', 'b', 'c'] for /docs/a/b/c
export default function DocsPage({ params }: { params: { slug: string[] } }) {
  const path = params.slug.join('/');
  return <p>Viewing: {path}</p>;
}
```

### Optional Catch-All

```
app/docs/[[...slug]]/page.tsx  →  /docs  /docs/a  /docs/a/b
```

```tsx
// params.slug = undefined for /docs
// params.slug = ['a'] for /docs/a
export default function DocsPage({ params }: { params: { slug?: string[] } }) {
  if (!params.slug) return <p>Docs Home</p>;
  return <p>Viewing: {params.slug.join('/')}</p>;
}
```

### generateStaticParams — Static Generation for Dynamic Routes

```tsx
// app/blog/[slug]/page.tsx
export async function generateStaticParams() {
  const posts = await fetchAllPosts();

  // Return array of params objects to pre-render
  return posts.map(post => ({
    slug: post.slug,   // Pre-render /blog/post-1, /blog/post-2, etc.
  }));
}
```

---

## 15. Layouts & Templates

### Root Layout (Required)

Every Next.js App Router app must have a root layout. It wraps ALL pages.

```tsx
// app/layout.tsx
import type { Metadata } from 'next';

export const metadata: Metadata = { title: 'My App' };

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="en">
      <body>
        <header>Nav Bar</header>
        <main>{children}</main>
        <footer>Footer</footer>
      </body>
    </html>
  );
}
```

### Nested Layouts

Create a `layout.tsx` inside any route segment to apply a layout only to that segment and its children.

```
app/
├── layout.tsx              ← Applied to ALL pages
├── page.tsx
└── dashboard/
    ├── layout.tsx          ← Applied only to /dashboard and its children
    ├── page.tsx            ← /dashboard
    └── settings/
        └── page.tsx        ← /dashboard/settings
```

```tsx
// app/dashboard/layout.tsx
export default function DashboardLayout({ children }) {
  return (
    <div className="dashboard-container">
      <aside>Sidebar</aside>
      <section>{children}</section>
    </div>
  );
}
```

### Layout vs Template

| Feature | Layout | Template |
|---------|--------|----------|
| Re-mounts on navigation | No (state is preserved) | Yes (fresh instance) |
| Use case | Persistent UI (nav, sidebar) | Reset state on navigation, page-enter animations |

```tsx
// app/dashboard/template.tsx
export default function DashboardTemplate({ children }) {
  return <div className="fade-in">{children}</div>;
}
```

---

## 16. Error Handling

### error.tsx

Catches errors in a route segment and its children. Must be a Client Component.

```tsx
// app/dashboard/error.tsx
'use client';

export default function Error({
  error,
  reset,
}: {
  error: Error & { digest?: string };
  reset: () => void;
}) {
  return (
    <div>
      <h2>Something went wrong!</h2>
      <p>{error.message}</p>
      <button onClick={reset}>Try again</button>
    </div>
  );
}
```

### not-found.tsx

Shown when `notFound()` is called or a route doesn't exist.

```tsx
// app/blog/[slug]/page.tsx
import { notFound } from 'next/navigation';

export default async function BlogPost({ params }) {
  const post = await fetchPost(params.slug);

  if (!post) notFound();  // Renders not-found.tsx

  return <article>{post.content}</article>;
}
```

```tsx
// app/blog/[slug]/not-found.tsx
export default function NotFound() {
  return (
    <div>
      <h2>Post Not Found</h2>
      <p>The blog post you are looking for does not exist.</p>
    </div>
  );
}
```

### Global Error Boundary

```tsx
// app/global-error.tsx — Catches errors in root layout
'use client';

export default function GlobalError({ error, reset }) {
  return (
    <html>
      <body>
        <h2>Something went wrong globally!</h2>
        <button onClick={reset}>Try again</button>
      </body>
    </html>
  );
}
```

---

## 17. Loading UI & Streaming

### loading.tsx

Automatically wraps the page in a React `<Suspense>` boundary. Shows while the page is loading.

```tsx
// app/dashboard/loading.tsx
export default function Loading() {
  return (
    <div className="spinner">
      <p>Loading dashboard...</p>
    </div>
  );
}
```

### Streaming with Suspense

Stream parts of the UI as they become ready, instead of waiting for all data.

```tsx
// app/dashboard/page.tsx
import { Suspense } from 'react';
import RevenueChart from './RevenueChart';   // Slow component
import LatestLeads from './LatestLeads';     // Fast component
import CardsSkeleton from './CardsSkeleton';

export default function DashboardPage() {
  return (
    <div>
      <h1>Dashboard</h1>

      {/* Fast content renders immediately */}
      <p>Welcome back!</p>

      {/* Slow content streams in when ready */}
      <Suspense fallback={<CardsSkeleton />}>
        <RevenueChart />   {/* Shows skeleton until this resolves */}
      </Suspense>

      <Suspense fallback={<p>Loading leads...</p>}>
        <LatestLeads />    {/* Independent — doesn't wait for RevenueChart */}
      </Suspense>
    </div>
  );
}
```

**Why streaming matters:** Without streaming, the user sees nothing until ALL data is fetched. With streaming, parts of the page appear progressively.

---

## 18. Caching in Next.js

Next.js has multiple layers of caching. Understanding this is critical for interviews.

### 1. Request Memoization

Within a single server render, identical `fetch()` calls are deduplicated — the network request is only made once.

```tsx
// Both components call the same URL — only ONE network request is made
async function ComponentA() {
  const data = await fetch('https://api.example.com/user'); // Request made
  return <div>{data.name}</div>;
}

async function ComponentB() {
  const data = await fetch('https://api.example.com/user'); // Served from memo cache
  return <div>{data.email}</div>;
}
```

### 2. Data Cache

Persists fetch results across requests and deployments (server-side persistent cache).

```tsx
// Cached indefinitely (default: force-cache)
fetch(url, { cache: 'force-cache' });

// Not cached — always fresh
fetch(url, { cache: 'no-store' });

// Cached with time-based revalidation (ISR)
fetch(url, { next: { revalidate: 3600 } });

// Cached with tag-based revalidation
fetch(url, { next: { tags: ['products'] } });
```

### 3. Full Route Cache

Static routes are cached at build time and served from the server's file system.

### 4. Router Cache (Client-Side Cache)

The browser caches visited route segments in memory so navigation back is instant.

### On-Demand Revalidation

```tsx
// app/api/revalidate/route.ts
import { revalidateTag, revalidatePath } from 'next/cache';

export async function POST(request: NextRequest) {
  const { tag } = await request.json();

  // Purge all cached data with this tag
  revalidateTag('products');

  // Or revalidate a specific path
  revalidatePath('/products');

  return NextResponse.json({ revalidated: true });
}
```

---

## 19. Environment Variables

### Types of Environment Variables

```bash
# .env.local (gitignored — for local development secrets)
DATABASE_URL=postgresql://localhost:5432/mydb
JWT_SECRET=my-super-secret-key

# Exposed to browser — prefix with NEXT_PUBLIC_
NEXT_PUBLIC_API_URL=https://api.example.com
NEXT_PUBLIC_GOOGLE_MAPS_KEY=AIzaSyXXXX
```

### Rules

| Variable | Available in Server | Available in Browser |
|----------|--------------------|--------------------|
| `MY_SECRET` | Yes | No |
| `NEXT_PUBLIC_MY_VAR` | Yes | Yes |

```tsx
// Server Component or API route — can access any env var
const dbUrl = process.env.DATABASE_URL;

// Client Component — can ONLY access NEXT_PUBLIC_ variables
const apiUrl = process.env.NEXT_PUBLIC_API_URL;
```

### Environment Files Load Order

```
.env.local          ← Always loaded, gitignored, highest priority
.env.development    ← Only in development (npm run dev)
.env.production     ← Only in production (npm run build)
.env                ← All environments, lowest priority
```

---

## 20. Authentication

### Using NextAuth.js (Auth.js)

The most popular authentication library for Next.js.

```bash
npm install next-auth
```

```tsx
// app/api/auth/[...nextauth]/route.ts
import NextAuth from 'next-auth';
import GoogleProvider from 'next-auth/providers/google';
import CredentialsProvider from 'next-auth/providers/credentials';

const handler = NextAuth({
  providers: [
    GoogleProvider({
      clientId: process.env.GOOGLE_CLIENT_ID!,
      clientSecret: process.env.GOOGLE_CLIENT_SECRET!,
    }),
    CredentialsProvider({
      name: 'Credentials',
      credentials: {
        email: { label: 'Email', type: 'email' },
        password: { label: 'Password', type: 'password' },
      },
      async authorize(credentials) {
        const user = await verifyUser(credentials.email, credentials.password);
        return user || null;
      },
    }),
  ],
  callbacks: {
    async jwt({ token, user }) {
      if (user) token.role = user.role;
      return token;
    },
    async session({ session, token }) {
      session.user.role = token.role;
      return session;
    },
  },
  pages: {
    signIn: '/login',   // Custom login page
  },
});

export { handler as GET, handler as POST };
```

### Protecting Routes with Middleware

```tsx
// middleware.ts
export { default } from 'next-auth/middleware';

export const config = {
  matcher: ['/dashboard/:path*', '/profile/:path*'],
};
```

### Accessing Session

```tsx
// Server Component
import { getServerSession } from 'next-auth';

export default async function DashboardPage() {
  const session = await getServerSession();
  if (!session) redirect('/login');

  return <p>Welcome {session.user.name}</p>;
}

// Client Component
'use client';
import { useSession, signIn, signOut } from 'next-auth/react';

export default function AuthButton() {
  const { data: session, status } = useSession();

  if (status === 'loading') return <p>Loading...</p>;
  if (session) return <button onClick={() => signOut()}>Sign Out</button>;
  return <button onClick={() => signIn('google')}>Sign In with Google</button>;
}
```

---

## 21. Internationalization (i18n)

### Built-in i18n (Pages Router)

```js
// next.config.js
module.exports = {
  i18n: {
    locales: ['en', 'hi', 'mr'],
    defaultLocale: 'en',
    localeDetection: true,
  },
};
```

```tsx
// pages/about.tsx
import { useRouter } from 'next/router';

export default function About() {
  const { locale, locales, defaultLocale } = useRouter();
  return <p>Current locale: {locale}</p>;
}
```

### App Router i18n (with next-intl)

```bash
npm install next-intl
```

```
app/
└── [locale]/
    ├── layout.tsx
    └── page.tsx
```

```tsx
// messages/en.json
{ "home": { "title": "Welcome", "description": "Hello World" } }

// messages/hi.json
{ "home": { "title": "स्वागत है", "description": "नमस्ते दुनिया" } }
```

```tsx
// app/[locale]/page.tsx
import { useTranslations } from 'next-intl';

export default function HomePage() {
  const t = useTranslations('home');
  return <h1>{t('title')}</h1>;
}
```

---

## 22. Performance Optimization

### Code Splitting & Lazy Loading

Next.js automatically code-splits by page. For component-level splitting:

```tsx
import dynamic from 'next/dynamic';

// Lazy load a heavy component
const HeavyChart = dynamic(() => import('./HeavyChart'), {
  loading: () => <p>Loading chart...</p>,
  ssr: false,   // Don't render on server (for browser-only libs)
});

// Lazy load with named export
const Modal = dynamic(() => import('./Modal').then(mod => mod.Modal));
```

### Bundle Analysis

```bash
npm install @next/bundle-analyzer

# next.config.js
const withBundleAnalyzer = require('@next/bundle-analyzer')({
  enabled: process.env.ANALYZE === 'true',
});
module.exports = withBundleAnalyzer({});

# Run
ANALYZE=true npm run build
```

### Prefetching

```tsx
// Automatic: Links in viewport are prefetched automatically
<Link href="/about">About</Link>

// Manual programmatic prefetch
const router = useRouter();
router.prefetch('/dashboard');

// Disable prefetch for infrequently visited heavy pages
<Link href="/reports" prefetch={false}>Reports</Link>
```

### Script Optimization

```tsx
import Script from 'next/script';

// Load strategies:
<Script src="https://example.com/script.js" strategy="beforeInteractive" />
// beforeInteractive: Before page hydration (analytics, critical scripts)

<Script src="https://example.com/script.js" strategy="afterInteractive" />
// afterInteractive: After hydration (default, Google Analytics)

<Script src="https://example.com/script.js" strategy="lazyOnload" />
// lazyOnload: During idle time (chat widgets, social buttons)

<Script src="https://example.com/script.js" strategy="worker" />
// worker: In web worker (experimental — offload to separate thread)
```

---

## 23. Deployment

### Deploying to Vercel (Recommended)

```bash
# Install Vercel CLI
npm install -g vercel

# Deploy
vercel

# Production deploy
vercel --prod
```

Vercel automatically:
- Detects Next.js
- Builds and optimizes
- Deploys to global edge network
- Handles ISR, middleware, edge functions
- Sets up CI/CD from GitHub

### Deploying with Docker

```dockerfile
# Dockerfile
FROM node:18-alpine AS base

FROM base AS deps
WORKDIR /app
COPY package*.json ./
RUN npm ci

FROM base AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN npm run build

FROM base AS runner
WORKDIR /app
ENV NODE_ENV=production

COPY --from=builder /app/.next/standalone ./
COPY --from=builder /app/.next/static ./.next/static
COPY --from=builder /app/public ./public

EXPOSE 3000
CMD ["node", "server.js"]
```

```js
// next.config.js — Required for standalone Docker output
module.exports = {
  output: 'standalone',
};
```

### Deploying to AWS / Other Platforms

```bash
# Self-hosting: Build and run
npm run build
npm start

# Export as static HTML (only for fully static sites)
npm run build
# next.config.js: output: 'export'
```

### next.config.js Common Options

```js
/** @type {import('next').NextConfig} */
const nextConfig = {
  output: 'standalone',          // For Docker
  reactStrictMode: true,         // Highlight potential issues
  poweredByHeader: false,        // Remove X-Powered-By header
  compress: true,                // Enable gzip compression
  images: {
    remotePatterns: [{ hostname: 'cdn.example.com' }],
    formats: ['image/avif', 'image/webp'],
  },
  redirects: async () => [
    { source: '/old-page', destination: '/new-page', permanent: true },
  ],
  rewrites: async () => [
    { source: '/api/:path*', destination: 'https://backend.example.com/:path*' },
  ],
  headers: async () => [
    {
      source: '/(.*)',
      headers: [
        { key: 'X-Frame-Options', value: 'DENY' },
        { key: 'X-Content-Type-Options', value: 'nosniff' },
      ],
    },
  ],
};

module.exports = nextConfig;
```

---

## 24. Next.js vs React — Key Differences

| Feature | React | Next.js |
|---------|-------|---------|
| Type | UI Library | Full-stack Framework |
| Routing | Manual (React Router) | Built-in file-based |
| Rendering | CSR only | SSR, SSG, ISR, CSR |
| SEO | Poor (empty HTML shell) | Excellent (pre-rendered HTML) |
| API Layer | Separate server needed | Built-in API routes |
| Image Opt | Manual | Built-in `next/image` |
| Code Splitting | Manual | Automatic |
| TypeScript | Manual setup | Built-in |
| Font Opt | Manual | Built-in `next/font` |
| Learning Curve | Lower | Higher (more concepts) |
| Bundle Size | Smaller | Larger (framework overhead) |

---

## Quick Reference Cheatsheet

### Data Fetching Cache Options
```
cache: 'force-cache'          → SSG (build time, cached forever)
cache: 'no-store'             → SSR (every request, never cached)
next: { revalidate: N }       → ISR (cached for N seconds)
next: { tags: ['tag'] }       → Tag-based revalidation
```

### Rendering Decision Tree
```
Is content the same for all users?
  YES → Is data real-time?
          YES → SSR (no-store)
          NO  → SSG or ISR (revalidate)
  NO  → Does it need SEO?
          YES → SSR with user data
          NO  → CSR (useEffect + fetch)
```

### Component Decision Tree
```
Does it need useState / useEffect / browser APIs?
  YES → Client Component ('use client')
  NO  → Does it fetch data or access server resources?
          YES → Server Component (async/await)
          NO  → Either (prefer Server Component)
```

### Key Directives
```tsx
'use client'   // Top of file — marks as Client Component
'use server'   // Top of file or function — marks as Server Action
```

### File Conventions (App Router)
```
page.tsx        → Route UI (makes segment publicly accessible)
layout.tsx      → Shared persistent UI
loading.tsx     → Suspense fallback UI
error.tsx       → Error boundary UI (must be 'use client')
not-found.tsx   → 404 UI
route.ts        → API endpoint (GET, POST, PUT, DELETE, PATCH)
middleware.ts   → Runs before every request (project root)
```

---

*This guide covers Next.js from fundamentals through advanced patterns. Practice by building projects — a blog (SSG), a dashboard (SSR), and an e-commerce site (ISR) will cover nearly all these concepts hands-on.*
