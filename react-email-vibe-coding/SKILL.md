---
name: react-email-vibe-coding
description: Generates transactional and promotional email templates using @react-email/components, inline styles, email-safe HTML/CSS, absolute asset URLs, compile-time prop injection, and preview text snippets. Auto-invoked when building or refactoring React email templates.
---

# React Email Vibe Coding Skill

This skill guides AI agents to build production-ready, bulletproof transactional and marketing email templates using **React Email** (`@react-email/components`). It ensures templates render consistently across all major email clients, including Outlook (Desktop & Web), Gmail, Apple Mail, and Yahoo.

---

## 0. Project Compatibility Check & Force Override (STRICT)

Before generating or refactoring any React Email code, execute the following evaluation:

### 1. Check for Force Override

- Scan the user prompt for explicit force keywords (e.g., `force`, `override`, `run anyway`).
- **If detected:** Bypass environment compatibility checks and proceed.

### 2. Validate Environment Compatibility

- Inspect `package.json` at the project root.
- **Target Language:** Must be a JavaScript or TypeScript project environment.
- **Target Library:** Must be a React project (contains `react` or `@react-email/components` in dependencies).

### 3. Automatic Decline Rules

- **Non-JS/TS Projects:** Decline execution immediately if the codebase uses non-JS/TS languages (e.g., Java, Python, Go).
- **Decline Notification Standard:**
  > 🛑 **Skill Execution Stopped (`react-email-vibe-coding`)**
  > This skill requires a JavaScript or TypeScript React environment.
  >
  > **Reason:** Missing React or `@react-email/components` context in project configuration. Use `force` in your prompt to override.

---

## 1. Pure Functional Render Model (STRICT: NO CLIENT STATE)

Email clients (Gmail, Outlook, Apple Mail) completely strip JavaScript execution.

- 🚫 **STRICT PROHIBITION:** **NEVER** use interactive React hooks (`useState`, `useEffect`, `useReducer`, `useContext`, `useRef`) or event handlers (`onClick`, `onChange`, `onSubmit`).
- 📦 **Pure Props Injection:** All dynamic data (user names, order items, reset URLs, financial totals) must be passed strictly as component **Props**.
- 🛡️ **Sensible Default Props:** Always provide complete, realistic default prop values so templates render safely in dev previews, unit tests, and screenshot generators.

---

## 2. Component Primitives & Template Structure

Use primitive components exclusively imported from `@react-email/components`:

```typescript
import {
  Html,
  Head,
  Preview,
  Body,
  Container,
  Section,
  Row,
  Column,
  Text,
  Button,
  Img,
  Hr,
  Link,
} from "@react-email/components";
```

### Essential Template Rules

1. **Mandatory Preview Text (`<Preview>`):**
   - Always place `<Preview>` inside `<Html>` / `<Head>` to control the email inbox preview snippet (preheader text).
   - Include hidden whitespace padding if necessary to prevent email client text leakage.

2. **Absolute Asset HTTPS URLs (`<Img>`):**
   - All image sources (`Img src="..."`) **MUST** use absolute, publicly accessible HTTPS URLs (e.g., `https://assets.example.com/logo.png`).
   - 🚫 **NEVER** use local relative paths (`./logo.png`, `/images/logo.png`, `../assets/logo.png`).

3. **Centered Container Boundary (`<Container>`):**
   - Wrap the primary email body content in a `<Container>` element with a fixed standard max-width (`600px`) and `margin: '0 auto'` for centered rendering on desktop clients.

---

## 3. Email-Safe Styling Rules

Email client rendering engines (especially Microsoft Word engine in Outlook on Windows) have severe CSS limitations.

| Category             | Email-Safe Standard                                                                                           | Prohibited / Unsupported                                                    |
| :------------------- | :------------------------------------------------------------------------------------------------------------ | :-------------------------------------------------------------------------- |
| **Layout**           | Table-based primitives (`<Section>`, `<Row>`, `<Column>`)                                                     | CSS Grid, Flexbox `gap-*` utilities, absolute positioning                   |
| **Styling Strategy** | Inline CSS (`style={{ ... }}`) or `@react-email/tailwind`                                                     | External CSS files, `<style>` block pseudo-classes (`:hover`, `:nth-child`) |
| **Typography**       | Web-safe font stacks (`Arial, Helvetica, sans-serif`) with explicit `lineHeight`, `fontSize`, and `color`     | Custom web fonts requiring `@import`, unstyled raw text tags                |
| **Buttons**          | `<Button>` with explicit `padding`, `backgroundColor`, `borderRadius`, `color`, and `display: 'inline-block'` | Flexbox-aligned buttons, `<div>` buttons with `onClick`                     |

---

## 4. Documentation & JSDoc Standards

Every email template component and prop interface **MUST** include concise 1–3 line JSDoc comments explaining its trigger context and data schema.

### Standard Template Structure Example

```typescript
import {
  Html,
  Head,
  Preview,
  Body,
  Container,
  Section,
  Text,
  Button,
  Img,
  Hr,
} from '@react-email/components';
import * as React from 'react';

/**
 * Props for the Order Confirmation email template.
 */
export interface OrderConfirmationEmailProps {
  customerName?: string;
  orderNumber?: string;
  totalAmount?: string;
  receiptUrl?: string;
}

/**
 * Transactional email template sent automatically upon successful checkout completion.
 */
export const OrderConfirmationEmail: React.FC<OrderConfirmationEmailProps> = ({
  customerName = 'Valued Customer',
  orderNumber = '#ORD-12345',
  totalAmount = '$99.00',
  receiptUrl = 'https://example.com/orders/12345',
}) => {
  return (
    <Html>
      <Head />
      <Preview>Your order {orderNumber} has been confirmed!</Preview>
      <Body style={mainBodyStyle}>
        <Container style={containerStyle}>
          <Img
            src="https://assets.example.com/logo.png"
            width="150"
            height="40"
            alt="Company Logo"
            style={logoStyle}
          />
          <Section style={contentSectionStyle}>
            <Text style={headingStyle}>Order Confirmed</Text>
            <Text style={textStyle}>
              Hi {customerName}, thank you for your purchase! We are preparing your order **{orderNumber}**.
            </Text>
            <Text style={totalStyle}>Total Paid: {totalAmount}</Text>
            <Button style={buttonStyle} href={receiptUrl}>
              View Receipt
            </Button>
          </Section>
          <Hr style={hrStyle} />
          <Text style={footerStyle}>
            If you have any questions, reply directly to this email or contact support.
          </Text>
        </Container>
      </Body>
    </Html>
  );
};

// Inline Email-Safe Styles
const mainBodyStyle: React.CSSProperties = {
  backgroundColor: '#f6f9fc',
  fontFamily: 'Arial, Helvetica, sans-serif',
};

const containerStyle: React.CSSProperties = {
  backgroundColor: '#ffffff',
  margin: '0 auto',
  padding: '40px 20px',
  maxWidth: '600px',
  borderRadius: '8px',
};

const logoStyle: React.CSSProperties = {
  margin: '0 auto 20px auto',
  display: 'block',
};

const contentSectionStyle: React.CSSProperties = {
  textAlign: 'center' as const,
};

const headingStyle: React.CSSProperties = {
  fontSize: '24px',
  fontWeight: 'bold',
  color: '#333333',
  margin: '0 0 16px 0',
};

const textStyle: React.CSSProperties = {
  fontSize: '16px',
  lineHeight: '24px',
  color: '#555555',
};

const totalStyle: React.CSSProperties = {
  fontSize: '18px',
  fontWeight: 'bold',
  color: '#111111',
  margin: '20px 0',
};

const buttonStyle: React.CSSProperties = {
  backgroundColor: '#0070f3',
  borderRadius: '5px',
  color: '#ffffff',
  fontSize: '16px',
  fontWeight: 'bold',
  textDecoration: 'none',
  textAlign: 'center' as const,
  display: 'inline-block',
  padding: '12px 24px',
};

const hrStyle: React.CSSProperties = {
  borderColor: '#e6ebf1',
  margin: '20px 0',
};

const footerStyle: React.CSSProperties = {
  color: '#8898aa',
  fontSize: '12px',
  lineHeight: '18px',
};
```

---

## 5. Dependency Suggestions & Local Preview Protocol

- 🚫 **DO NOT** execute terminal installation commands automatically (`npm install`, `yarn add`, `pnpm add`).
- Append missing package suggestions at the end of the response:

````markdown
### 📦 Required Dependencies

```bash
npm install @react-email/components @react-email/render
```
````

### 🔍 Local Email Preview

To preview and test email templates locally in your browser:

```bash
npx email dev
```

---

## 6. Operational Workflow for AI Agents

When building or refactoring React Email templates, follow this step-by-step checklist:

1. **Compatibility Gate:** Inspect `package.json` for React environment and handle force overrides.
2. **Props & Schema Definition:** Define the TypeScript interface for template props with optional default values for every field.
3. **Template Composition:** Construct the layout hierarchy using strictly `@react-email/components` (`Html`, `Head`, `Preview`, `Body`, `Container`, `Section`, `Row`, `Column`).
4. **Enforce Email Safety:**
   - Verify **zero React hooks / state** are used.
   - Verify all image URLs are **absolute HTTPS URLs**.
   - Verify layout uses **table-based primitives** and web-safe inline fonts.
5. **JSDoc & Output Formatting:** Include 1–3 line JSDoc comments for the props interface and component export, then append dependency install & `npx email dev` instructions.
