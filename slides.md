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

# Use Idempotent APIs for reliability (pg.1)
An API call is idempotent if making the same call multiple times produces the same result as making it once.

- Prevents unintended consequences from retries (e.g., network timeouts)
- Makes APIs safe, predictable, and easier to test/debug.

| Method   | Idempotent?  | Notes                                                                                 |
| -------- | ------------ | ------------------------------------------------------------------------------------- |
| `GET`    | ✅ Yes        | Reads data only - idempotent by default    |
| `PUT`    | ✅ Yes        | Replaces resource with same result each time - typically is idempotent |
| `DELETE` | ✅ Yes        | Multiple deletes of same resource = same end state - typically idempotent |
| `POST`   | ❌ No         | Creates a new resource each time (unless you design it to be idempotent with a token) |

---
transition: slide-left
---

# Use Idempotent APIs for reliability (pg.2)

- ✅ PUT `/users/99 {name: "Alice"}`
   - Replaces user 99 with same data every time
- ❌ POST `/users {name: "Alice"}`
   -  Creates a new user each time, even with same data
- POST is inherently non-idempotent since it implies create a new action or change the state in a unique way every time.
   - Servers may treat each POST as a separate operation, even if the URL suggests "deletion."
- Anti-patterns to avoid:
   - `POST /users/99/edit` — Not RESTful and not idempotent
      - But didn't our express app uses such routes?
      - This approach is okay for a server-rendered Express app when using templating engines like EJS because **you're serving web pages, not JSON**
      - `GET /users/99/edit` is a view that shows HTML form to edit user, not a resource endpoint

---
transition: slide-left
---

# Exercise: Make this Idempotent if possible

- Why is it dangerous if a non-idempotent API gets retried during a network issue?

| Endpoint           | Method   | Idempotent? (Yes/No) | If No, How to Fix It |
| ------------------ | -------- | -------------------- | -------------------- |
| `/users/45`        | `PUT`    |                      |                      |
| `/users`           | `POST`   |                      |                      |
| `/users/45/delete` | `POST`   |                      |                      |
| `/cart/checkout`   | `POST`   |                      |                      |
| `/users/45`        | `DELETE` |                      |                      |

<!--
1. Yes. PUT replaces the resource with the same data every time.
2. No, Use PUT /users/45 if the client knows the ID, or use an idempotency key in the header for repeated requests.
3. No, Replace with DELETE /users/45, which is the correct, idempotent HTTP method.
4. No, Use idempotency keys or create a unique checkoutSessionId to prevent duplicate processing.
5. Yes, Multiple deletes of the same resource result in the same final state (resource gone).
-->

---
transition: slide-left
---

# Use Versioning
Versioning is a technique to manage changes in an API without breaking existing users.

- Prevents breaking existing clients
- Enables incremental improvements, and backward compatibility
- Allows parallel development
- Otherwise, If not using versioning, will have to use [feature flag tools](https://launchdarkly.com)
- ✅ Always start with a version `/v1/` even if it's the first release
- ✅ Keep old versions running until usage drops off
- ✅ Document version differences clearly
- ✅ Avoid changing existing behaviour without bumping the version
- ✅ Use semantic versioning in docs (v1.2, v2.0), but keep URLs simple (v1, v2).

---
transition: slide-left
---

# Exercise: Versioning 
Decide if versioning is handled correctly. If not, rewrite the endpoints

| Endpoint           | Problem | Improved Endpoint |
| ------------------ | ------- | ------- |
| `/getProducts`           |         ||
| `/createUser` |         ||
| `/orders/submit`        |         ||
| `/api/users` |         ||
| `/v3.1/customers` |         ||

<!--
1. /v1/products Use resource noun (products), remove verb, and add version prefix.
2. /v1/users Use POST to /v1/users for creating a user — verb in path is removed
3. /v1/orders This is a creation action — should be POST /v1/orders, not a custom verb like submit.
4. /v1/users Remove the generic /api prefix (optional) and explicitly add a version.
5. /v3/customers Simplify versioning to major version only in the URI; minor/patch versions can go in docs or headers if needed.
-->

---
transition: slide-left
---

# Use Pagination

- split large collections of data into manageable chunks; reduces bandwidth usage; speeds up response times; enables better UX for frontend (ex: infinite scrolling OR "Load more")
- ✅ Always limit the number of items returned
- ✅ Include metadata: total count, next page link, etc.
- ✅ Use reasonable defaults (limit=20, max=100)

| Technique        | Example                     | Pros                                      | Cons                           |
| ---------------- | --------------------------- | ----------------------------------------- | ------------------------------ |
| Offset-based | `/users?limit=10&offset=20` | Simple to implement                       | Can have gaps with deletions   |
| Page-based   | `/users?page=3&pageSize=10` | Easy for users                            | Inconsistent data with updates |
| Cursor-based | `/users?after=abc123`       | Consistent results, better for large data | Harder to implement and debug  |



---
transition: slide-left
---

# Exercise: Pagination
Rewrite these endpoints using offset-based pagination with limit and offset

- Why is returning all data at once a bad idea?
- What problems could occur if you use offset-based pagination with real-time data?

| Original                   | Goal |
| -------------------------- | --------------- |
| `/users`                   | Fetch first 20 users. Clients can adjust offset to paginate forward |
| `/orders`                  | Fetch 50 orders starting from 101st |
| `/products?category=books` | Applies pagination to a filtered list |


<!--
1. /users?limit=20&offset=0 
2. /orders?limit=50&offset=100 Helps prevent large responses.
3. /products?category=books&limit=10&offset=30

2. If New records are inserted or deleted between requests, The same offset may return inconsistent or overlapping results, Or skip records entirely.
-->

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