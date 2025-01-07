# Complete Next.js Tutorial with Concepts and Logic

This tutorial will cover all the concepts used in your project, focusing on the logic and explanation for each step. It assumes a basic understanding of JavaScript and React.

---

## 1. **Introduction to Next.js**

### What is Next.js?
Next.js is a React framework for building server-rendered and static web applications. It provides features like:
- Server-Side Rendering (SSR)
- Static Site Generation (SSG)
- API Routes
- Image Optimization
- Built-in CSS and Sass support
- File-based routing

---

## 2. **Folder Structure in Next.js (Latest)**

### Typical Project Structure:
```plaintext
project-name/
├── app/                # Application routing (app router)
│   ├── page.tsx       # Default entry point for the root route
│   ├── login/         # Nested routes (e.g., /login)
│   │   └── page.tsx   # Login page
│   ├── reset-password/ # Password reset route
│   │   └── page.tsx
├── public/            # Static assets (images, fonts, etc.)
├── styles/            # Global styles
│   ├── globals.css
├── components/        # Reusable React components
├── lib/               # Helper functions or utilities
├── middleware.ts      # Middleware for handling requests
├── pages/             # Legacy folder for page-based routing
├── api/               # API routes for backend logic (in `app/api`)
├── model/             # Database models (e.g., User model)
├── dbConfig/          # Database connection logic
└── next.config.js     # Next.js configuration
```

---

## 3. **Key Features and Concepts Used in Your Project**

### 3.1 File-Based Routing
- In Next.js, the `app` folder introduces the **App Router**, which enables file-based routing.
- Example: To create a `/login` route, add a `login` folder and include a `page.tsx` file.

#### Code Example:
```tsx
// app/login/page.tsx
export default function LoginPage() {
  return (
    <div>
      <h1>Login Page</h1>
    </div>
  );
}
```

---

### 3.2 Server-Side Rendering (SSR) and Static Site Generation (SSG)
- **SSR:** Fetches data on the server before rendering the page.
- **SSG:** Generates static HTML at build time.

#### Code Example:
```tsx
// app/dashboard/page.tsx
import { getServerSideProps } from "next";

export async function getServerSideProps() {
  const data = await fetchData(); // Fetch data from API
  return { props: { data } };
}

export default function DashboardPage({ data }) {
  return <div>Data: {JSON.stringify(data)}</div>;
}
```

---

### 3.3 API Routes
- API routes let you build your backend directly in Next.js.
- Place API files in the `app/api` directory.

#### Code Example:
```tsx
// app/api/auth/route.ts
import { NextRequest, NextResponse } from "next/server";

export async function POST(req: NextRequest) {
  const body = await req.json();
  return NextResponse.json({ message: "Login Successful", body });
}
```

---

### 3.4 Database Integration
- Use Mongoose for MongoDB integration.

#### Code Example:
```tsx
// dbConfig/dbConfig.ts
import mongoose from "mongoose";

export async function connect() {
  try {
    if (mongoose.connections[0].readyState) return;
    await mongoose.connect(process.env.MONGODB_URI!, {
      useNewUrlParser: true,
      useUnifiedTopology: true,
    });
    console.log("Database connected");
  } catch (error) {
    console.error("Database connection error", error);
  }
}
```

---

### 3.5 Authentication Logic

#### Sending Verification Emails
Use a mailer library like Nodemailer for sending emails.

```javascript
// lib/mailer.ts
import nodemailer from "nodemailer";

export const sendEmail = async ({ email, subject, htmlContent }: any) => {
  const transporter = nodemailer.createTransport({
    service: "Gmail",
    auth: {
      user: process.env.EMAIL,
      pass: process.env.EMAIL_PASSWORD,
    },
  });

  await transporter.sendMail({
    from: process.env.EMAIL,
    to: email,
    subject,
    html: htmlContent,
  });
};
```

#### Example Email Content:
```javascript
const htmlContent = `
  <div>
    <h1>Verify Your Email</h1>
    <p>Click <a href="${verifyUrl}">here</a> to verify your email address.</p>
  </div>
`;
await sendEmail({ email, subject: "Verify Your Email", htmlContent });
```

---

### 3.6 Password Reset Flow
1. User clicks **Forgot Password** on the login page.
2. User submits their email, triggering an API call to generate a reset token.
3. Email with reset token is sent to the user.
4. User clicks the link, which takes them to the reset password page.
5. User enters a new password, and the backend updates it in the database.

#### Code for Reset Password API:
```javascript
// app/api/reset-password/route.ts
import User from "@/model/userModel";

export async function POST(req: NextRequest) {
  const { token, email, newPassword } = await req.json();

  const user = await User.findOne({
    resetToken: token,
    resetTokenExpiry: { $gt: Date.now() },
  });

  if (!user) return NextResponse.json({ error: "Invalid token" }, { status: 400 });

  user.password = await bcrypt.hash(newPassword, 10);
  user.resetToken = undefined;
  user.resetTokenExpiry = undefined;
  await user.save();

  return NextResponse.json({ message: "Password reset successful" });
}
```

---

### 3.7 Environment Variables
- Store sensitive information like API keys and database credentials in `.env`.

#### Example:
```plaintext
NEXTAUTH_URL=http://localhost:3000
MONGODB_URI=mongodb+srv://<user>:<password>@cluster.mongodb.net/<dbname>
EMAIL=<your-email>
EMAIL_PASSWORD=<your-password>
```

---

### 3.8 Reusable Components
Encapsulate repeated UI elements into components for better reusability and readability.

#### Example:
```tsx
// components/Button.tsx
export default function Button({ text, onClick }: { text: string; onClick: () => void }) {
  return (
    <button onClick={onClick} className="bg-blue-500 text-white py-2 px-4 rounded">
      {text}
    </button>
  );
}
```

---

### 3.9 Middleware for Authentication
Use middleware to protect sensitive routes.

#### Example:
```tsx
// middleware.ts
import { NextResponse } from "next/server";

export default function middleware(req) {
  const token = req.cookies.get("token");

  if (!token) {
    return NextResponse.redirect(new URL("/login", req.url));
  }

  return NextResponse.next();
}
```

---

### 3.10 Styling
- Use **CSS Modules** or **Tailwind CSS** for styling.

#### Example (CSS Module):
```css
/* styles/Login.module.css */
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
}
```

#### Example (Tailwind CSS):
```tsx
<div className="flex flex-col items-center">
  <h1 className="text-2xl font-bold">Welcome</h1>
</div>
```

---

This tutorial has covered the essential concepts needed for your Next.js project, along with examples and explanations. Let me know if you'd like further clarification on any topic or feature!

