---
title: Modern Ruby on Rails 8 – Core Philosophies & Architectural Patterns (2025)
tags: [Ruby, Rails, Architecture, QuickNotes]
style: border
color: danger
description: Discover how Rails 8 blends simplicity, developer joy, and powerful patterns like Hotwire & Solid adapters to build modern, scalable apps.
---

For two decades, Ruby on Rails has shaped web development—but Rails 8 marks a renaissance. It brings back focus on **simplicity**, **developer happiness**, and **long‑term ownership**, all while delivering powerful solutions for web and mobile.

<br/>

<img src="{{ site.baseurl }}/public/images/rails-modern-architecture.png" alt="Modern Rails Architecture">  
<br/>

### 🌱 Core Philosophies  

**✔️ Convention over Configuration (CoC)**  
Rapid feature delivery by embracing defaults and reducing setup.

**😊 Developer Joy**  
Readable, elegant Ruby syntax makes programming a pleasure—not just work.

**🧩 Simplicity over Complexity**  
Move away from heavy JS SPAs; use server‑rendered HTML and tools like Hotwire to keep apps maintainable.

**🏡 Ownership & Permanence**  
Host your apps, reduce third‑party reliance, and build software that can run for decades.

**📱 Unified Codebase**  
Share logic across web and native mobile apps, cutting duplication and speeding up delivery.

---

### 🛠 Architectural Patterns & Modern Tools  

#### ⚡ Hotwire (HTML Over The Wire)  
- **Turbo Drive:** SPA‑like navigation, no page reloads.  
- **Turbo Frames:** Update partial regions via server‑rendered HTML.  
- **Turbo Streams:** Real‑time updates with minimal JS.  
- **Turbo Native:** Build mobile apps that reuse your Rails views.

> Hotwire handles ~80% of interactivity server‑side; you sprinkle JS only where needed.

---

#### ✨ Stimulus JS  
Lightweight framework to add small dynamic touches (dropdowns, keyboard shortcuts) without turning into a JS-heavy app.

---

#### 🏗 Monolithic First, Microservices as Needed  
Start with a single app; split into microservices later for scaling, complex workloads, or team separation.

---

#### 🪄 Solid Adapters (Rails 8 Innovations)  
- **Solid Cache:** Redis‑free caching via DB.  
- **Solid Queue:** Background jobs without Redis.  
- **Solid Cable:** Real‑time web sockets backed by DB.

→ Reduce external dependencies, perfect for self‑hosting.

---

#### 🐳 Kamal 2 (Deployments)  
Docker‑based tool to deploy Rails apps to your own servers easily, supporting the “No PaaS Required” mission.

---

#### 🔐 Built‑in Authentication  
Rails 8 adds authentication generators: secure defaults, simpler setup, and customisable UI.

---

#### 🎨 Flexible View Layers  
- **ERB:** Fast, familiar templates.  
- **ViewComponent:** Modular, testable UI blocks.  
- **Phlex:** Ruby‑only components, React‑style structure.

Works seamlessly with Tailwind, Hotwire, and Stimulus.

---

### ✏️ No‑Build Asset Pipeline  
Rails 8 (via Propshaft) skips JS/CSS bundling or transpiling. Write modern JS & CSS directly, powered by browser import maps.

---

### 📦 SQLite & Serverless Simplicity  
SQLite becomes a powerful default DB—no separate DB service needed, supporting low‑cost self‑hosting.

---

### 🎯 Key Takeaways  
✅ Build fast, joyful apps with minimal config.  
✅ Use Hotwire to replace complex front‑end JS.  
✅ Own your stack: Solid adapters & Kamal make self‑hosting viable.  
✅ Share logic across web + mobile, keeping teams small and code DRY.

---

💡 **Quick Tip:** 

Stay curious, keep coding, and let Rails 8 help you build apps that last.

