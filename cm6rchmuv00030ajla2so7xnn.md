---
title: "XSS in React: Understanding Vulnerable Functions and Security Risks"
datePublished: Wed Feb 05 2025 03:24:29 GMT+0000 (Coordinated Universal Time)
cuid: cm6rchmuv00030ajla2so7xnn
slug: xss-in-react-understanding-vulnerable-functions-and-security-risks
tags: reactjs, xss

---

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1738725835121/3c893a6a-f35e-4952-a763-7c35361ea50f.png align="center")

Although React is designed with security in mind, it **does not eliminate XSS (Cross-Site Scripting) vulnerabilities** entirely. Developers can still introduce security issues if they improperly handle user input. Below are the **most common vulnerable functions and patterns in React** that can lead to XSS.

---

## **🚨 Vulnerable Functions in React That Can Lead to XSS**

### **1\.** `dangerouslySetInnerHTML`

* React escapes all content by default, preventing XSS. However, using `dangerouslySetInnerHTML` **disables this protection**.
    
* If the inserted HTML is user-controlled and unescaped, **XSS is possible**.
    

#### **🔴 Vulnerable Code Example**

```jsx
const userComment = "<img src=x onerror=alert(1)>"; // Unsanitized user input

const CommentComponent = () => {
  return <div dangerouslySetInnerHTML={{ __html: userComment }} />;
};
```

✅ **Fix**: Use a proper HTML sanitizer like **DOMPurify** before inserting HTML.

```jsx
import DOMPurify from "dompurify";

const safeComment = DOMPurify.sanitize(userComment);

const SafeComponent = () => {
  return <div dangerouslySetInnerHTML={{ __html: safeComment }} />;
};
```

---

### **2\.** `ref.innerHTML` Manipulation

* Directly setting `innerHTML` on a DOM element retrieved via `ref` **bypasses React’s escaping mechanisms**.
    

#### **🔴 Vulnerable Code Example**

```jsx
import { useRef, useEffect } from "react";

const UnsafeComponent = ({ userInput }) => {
  const divRef = useRef(null);

  useEffect(() => {
    divRef.current.innerHTML = userInput; // XSS possible if userInput is malicious
  }, [userInput]);

  return <div ref={divRef}></div>;
};
```

✅ **Fix**: Use `textContent` instead to avoid HTML parsing.

```jsx
useEffect(() => {
  divRef.current.textContent = userInput; // Safe alternative
}, [userInput]);
```

---

### **3\.** `eval()` or `new Function()` in JSX or Event Handlers

* Using `eval()` or `new Function()` on user input **creates a JavaScript execution vulnerability**.
    

#### **🔴 Vulnerable Code Example**

```jsx
const executeUserCode = (code) => {
  eval(code); // 🚨 User-controlled input can execute arbitrary JS
};

executeUserCode("alert(document.cookie)");
```

✅ **Fix**: **Never** use `eval()`. Instead, use **strict function validation** and controlled execution environments.

---

### **4\.** `window.location` or `document.write()` with User Input

* **Appending user input to** `window.location` or `document.write()` without validation can allow malicious script injection.
    

#### **🔴 Vulnerable Code Example**

```jsx
const userQuery = new URLSearchParams(window.location.search).get("q");

document.write(`<h1>${userQuery}</h1>`); // XSS if q=<script>alert(1)</script>
```

✅ **Fix**: Encode user input before using it in the DOM.

```jsx
const safeQuery = DOMPurify.sanitize(userQuery);
document.write(`<h1>${safeQuery}</h1>`);
```

---

### **5\. Inline Event Handlers (**`onClick`, `onMouseOver`, etc.)

* **React normally prevents XSS in JSX attributes**, but passing unsanitized user input **as an event handler function** can be dangerous.
    

#### **🔴 Vulnerable Code Example**

```jsx
const UserAction = ({ userScript }) => {
  return <button onClick={() => eval(userScript)}>Run</button>; // 🚨 Dangerous!
};
```

✅ **Fix**: Only allow pre-defined functions.

```jsx
const UserAction = () => {
  const handleClick = () => alert("Safe execution");
  return <button onClick={handleClick}>Run</button>;
};
```

---

## **🛡️ Best Practices to Prevent XSS in React**

### **✅ 1. Never Use** `dangerouslySetInnerHTML` (Unless Necessary)

* If you **must** use it, sanitize the input with **DOMPurify** or another HTML sanitizer.
    

### **✅ 2. Always Escape User Input**

* If displaying user content, ensure it is **encoded properly** before inserting it into the DOM.
    

### **✅ 3. Avoid Using** `eval()` and `new Function()`

* These functions should **never be used** in React applications.
    

### **✅ 4. Be Careful with** `refs` and `innerHTML`

* Direct DOM manipulation bypasses React’s security protections.
    

### **✅ 5. Use Security Headers to Prevent Injection**

* Set **Content Security Policy (CSP)** headers to mitigate the risk of inline scripts being executed.
    

### **✅ 6. Use Trusted Libraries for Input Sanitization**

* **DOMPurify**, `xss-filters`, or other sanitization libraries should be used to clean user input.
    

---

### **🛑 Key Takeaways**

* **React’s default escaping helps prevent XSS, but bad coding practices can still introduce vulnerabilities.**
    
* **Avoid** `dangerouslySetInnerHTML` unless you sanitize input with a trusted library like DOMPurify.
    
* **Never use** `eval()`, `new Function()`, or directly set `innerHTML` in refs.
    
* **Sanitize all user input before rendering or processing it.**
    

By following these security principles, you can keep your React applications **safe from XSS attacks**. 🚀