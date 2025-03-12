---
title: "Solving mkcert HTTPS Certificate Issue in WSL & Windows Chrome"
datePublished: Tue Feb 11 2025 07:40:57 GMT+0000 (Coordinated Universal Time)
cuid: cm706akan003309jpdd350jzv
slug: solving-mkcert-https-certificate-issue-in-wsl-windows-chrome
tags: technology

---

### **🚀 Solving mkcert HTTPS Certificate Issue in WSL & Windows Chrome**

If you're running **Google Chrome on Windows**, but your PHP website (with `mkcert`) is set up inside **WSL**, Chrome doesn’t recognize the local **mkcert** certificates because they are inside WSL's filesystem.

### **✅ Why is this Happening?**

* `mkcert` installs certificates inside **WSL** (`/etc/ssl/certs` or `~/.local/share/mkcert`).
    
* But **Google Chrome on Windows** uses **Windows' certificate store** (`certlm.msc`).
    
* This means Chrome on **Windows** **doesn't trust** the certificate generated inside **WSL**.
    

---

## **✅ Solution: Copy the mkcert Root Certificate to Windows**

You need to **export the root certificate from WSL and import it into Windows**.

### **Step 1: Locate mkcert’s Root Certificate in WSL**

Run this inside **WSL**:

```bash
mkcert -CAROOT
```

It will return a path like:

```plaintext
/home/your-user/.local/share/mkcert
```

Inside this folder, find `rootCA.pem`.

---

### **Step 2: Copy the Certificate to Windows**

Run this inside WSL to copy the certificate to Windows:

```bash
cp $(mkcert -CAROOT)/rootCA.pem /mnt/c/Users/your-windows-username/Desktop/
```

This puts `rootCA.pem` on your **Windows Desktop**.

---

### **Step 3: Import the Certificate into Windows**

Now, you need to add the `rootCA.pem` to Windows' **Trusted Root Certificate Store**.

1. Open **Windows Start Menu** → Type **"Manage Computer Certificates"** and open it.
    
2. Go to **Trusted Root Certification Authorities** → **Certificates**.
    
3. **Right-click → All Tasks → Import**.
    
4. Select `rootCA.pem` from your **Desktop**.
    
5. Click **Next**, then **Finish**.
    

✅ **Now, Windows Chrome will trust your mkcert certificates!**

---

### **✅ Solution 2: Run Chrome on WSL Instead of Windows**

Instead of running Chrome on **Windows**, you can run **Google Chrome inside WSL** to avoid certificate issues.

Run:

```bash
google-chrome-stable --no-sandbox --use-gl=egl
```

Since it's inside WSL, it will recognize the mkcert certificates automatically.

---