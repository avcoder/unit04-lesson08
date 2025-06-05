---
# You can also start simply with 'default'
theme: default
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: /assets/intro.jpg
# some information about your slides (markdown enabled)
title: Software Development | Foundations
info: |
  ## Software Development | Foundations
# apply unocss classes to the current slide
class: text-left
drawings:
  persist: false
transition: slide-left
mdc: true
---

# API Design + Wrap up
Full-Stack Development - part 8/8


- [ ] Best Practices when Designing APIs
- [ ] Practice API design
- [ ] Midterm Q&A

<div class="abs-br m-6 text-xl">
  <a href="https://github.com/slidevjs/slidev" target="_blank" class="slidev-icon-btn">
    <carbon:logo-github />
  </a>
</div>

<!--
-->

---
transition: slide-left
---

# What is good API design?
Think endpoints that outputs pure JSON (no ejs server side pages)

- Analogy: Imagine if every car manufacturer placed the brake pedal in a different spot
- A well-designed API is like a standard car dashboard: intuitive, consistent, and safe to use.
- APIs are the interfaces through which developers interact with your system.
- Just like a good UI helps users accomplish tasks easily, a good API helps developers integrate, extend, and trust your product
- https://www.openapis.org/
1. Improves Developer Experience (DX)
1. Encourages Consistency and Scalability
1. Reduces Bugs and Misuse
1. Enhances Collaboration and Onboarding
1. Supports Automation and Tooling
1. Future-Proofing

<!--
1. Easier to learn: Developers can guess endpoints and behaviors without constantly reading docs.
2. If you follow patterns (e.g., GET /users, POST /users), your API becomes predictable.
Teams can scale without needing deep context on every route.
3. Clear design means less room for misunderstanding.
4. Makes your API easier to extend and maintain over time.
5. Well-designed APIs can be more easily described using OpenAPI/Swagger.
6. A well-thought-out design helps avoid breaking changes.
-->
---
transition: slide-left
---

# Use Clear Naming
Use clear, meaningful, concise names that mirrors real-world resources

- What makes an API easy or hard to understand?
- ❌ `/getTheUserInfoNow`
- ✅ `/users`
- ❌ `/processTransactionData`
- ✅ `/transactions`
- avoid implementation details
- use lowercase, hyphenated or camelCase consistently
- avoid vague terms like `/data`, `/info`, `/stuff`


---
transition: slide-left
---

# Use Plural Nouns
REST is resource-oriented so endpoints should represent Collections or individual items

| Operation | Bad Endpoint  | Good Endpoint |
| --------- | ------------- | ------------------------ |
| Get all   | `/booklist`, `/getBooks` | `/books`      |
| Get one   | `/book?id=1`             | `/books/1`    |
| Create    | `/addBook`               | `POST /books` |

- clearer for developers and more consistent with HTTP semantics

---
transition: slide-left
---

# param `users/99` vs. query `users?id=99`?

- Use Path Parameters `/users/99`
1. Semantic Meaning
   - path parameters indicate specific resources
2. Consistent with REST conventions
   - REST treats URLS as a hierarchy of resources (`/users`  > collections), not operations
3. Better for routing
   - Frameworks like Express handle path params more naturally `app.get('/users/:id'...)`
4. Cleaner and easier to cache
   - URLs like `/users/99` are more cache-friendly and search engine friendly than `/users?id=99`
- Query parameters like `/users?id=99` are more ambigious (are we filtering? searching? selecting multiple?)

---
transition: slide-left
---

# Exercise: Rename the endpoints
Rewrite each API endpoint to use clear, consistent naming and plural nouns

| Old Endpoint              | Operation         |
| ------------------------- | ----------------- |
| `/getAllTheUsersNow`      | Get all users     |
| `/addNewUserDetails`      | Create a user     |
| `/fetchBookInfo?id=10`    | Get a book by ID  |
| `/deleteAccountRecord/99` | Delete an account |
| `/postNewCommentData`     | Create a comment  |
| `/itemList`               | List items        |

---
transition: slide-left
---

# O

---
transition: slide-left
---

# C

---
transition: slide-left
---

# C

---
transition: slide-left
---

# H

---
transition: slide-left
---

# T

---
transition: slide-left
---

# G

---
transition: slide-left
---

# Exercise: Fix APIs
Identify issues and refactor APIs using best practices as discussed.


| Operation | Method | Endpoint                    |
| ------ | ------ | --------------------------- |
| List users | GET    | `/getAllUsers`              |
| Create user | POST   | `/addNewUser`               |
| Get user by ID | GET    | `/userById?id=123`          |
| Delete user | DELETE | `/deleteAUser/123`          |
| Update user | PUT    | `/updateUserDetails?id=123` |
| List items | GET    | `/getAllItems?version=1` |


---
layout: image-right
transition: slide-left
image: /assets/api.png
backgroundSize: 400px 270px
class: text-left
---

# 10 minute break

🍦 Cool Tips, Trends and Resources:
- 🔵 [SVG Icons for popular brands](https://simpleicons.org/)
- 🔄 [Modern CSS Reset](https://www.joshwcomeau.com/css/custom-css-reset/)
- ⭐ [2024 JavaAscript Rising Stars](https://risingstars.js.org/2024/en)
- 👨‍💻 [Computer Science Papers](https://newsletter.techworld-with-milan.com/p/computer-science-papers-every-developer)
- 💾 [Intro to WebAssembly](https://hemath.dev/blog/webassembly/introduction-to-webassembly/)
- 💽 [GraphQL: API Design](https://www.computer.org/publications/tech-news/trends/graphql)

<br>
<hr>
<br>

- 🧪 [Enter anonymous lab questions](https://docs.google.com/forms/d/e/1FAIpQLSevvGARdHQikso-uLqFCO481MABKE5HofuSrlzEPMNQ2ZLykw/viewform?usp=dialog)
- ℹ️ [Course feedback survey](https://circuitstream.typeform.com/to/ZoyYk7px#course_id=SoftwareAN&instructor=9514)

---
transition: slide-left
---

# Sa

- W

---
transition: slide-left
---

# Ma

- oc

---
transition: slide-left
---

# C

- A

---
transition: slide-left
---

# S



---
transition: slide-left
---

# O

---
transition: slide-left
---

# A

---
transition: slide-left
---

# C

---
transition: slide-left
---

# C

---
transition: slide-left
---

# D

---
transition: slide-left
---

# T

---
transition: slide-left
---

# 3

---
transition: slide-left
---

# Homework
- Work on your "Note-taking" midterm app due June 15 midnight EST