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

# Use Pagination (pg.1)

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

# Use Pagination (pg.2)

```js
app.get('/users', async (req, res) => { // GET /users?limit=10&page=2
    // Parse query params with defaults
    const limit = parseInt(req.query.limit) || 10;
    const page = parseInt(req.query.page) || 1;

    const skip = (page - 1) * limit;     // Calculate how many documents to skip

    const users = await User.find() // Query the database with pagination
      .skip(skip)
      .limit(limit)
      .sort({ createdAt: -1 }); // Optional: sort newest first

    const total = await User.countDocuments();     // Get total count for metadata

    res.json({
      data: users,
      pagination: {
        total,
        page,
        limit,
        totalPages: Math.ceil(total / limit),
        hasNextPage: page * limit < total,
      }
    }) 
});
```


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

# Use Clear Query Strings for Sorting and Filtering
Query strings allow clients to specify how to fetch the data — filtering, sorting, searching, etc.

- ✅ Use explicit, predictable query keys: sort, filter, limit, etc.
- ✅ Use a - prefix to indicate descending sort (sort=-price)
- ✅ Keep filter field names consistent with schema
- ✅ Avoid confusing, ambiguous names like orderByField=1
- ✅ Document all query parameters clearly in your API docs

| Goal                        | Example                            |
| --------------------------- | ---------------------------------- |
| Filter by range             | `/orders?minPrice=10&maxPrice=100` |
| Sort by field             | `/users?sort=name` |
| Sort by multiple fields   | `/products?sort=category,-price` |

---
transition: slide-left
---

# Exercise: Query Strings 
Identify what’s unclear or non-standard in the following URLs

- Why should filter parameters match public-facing, safe database fields or schema field names (e.g., status=active)?
- Are there any fields we should not allow filtering by?

| URL                                       | What’s Wrong? |
| ----------------------------------------- | ------------- |
| `/users?orderBy=1`                        |               |
| `/items?filterByType=book`                |               |
| `/products?sort=desc&by=price`            |               |
| `/search?category=electronics&active=yes` |               |
| `/posts?order=date_created:desc`          |               |

<!--
1. orderBy=1 is ambiguous. What field are we ordering by? Is 1 ascending or descending? /users?sort=name or /users?sort=-createdAt
2. filterByType is non-standard and verbose. Should just use the field name directly /items?type=book
3. Two separate parameters to express one intent. Use a single sort=-price 
4. active=yes is unclear — boolean values should be consistent (true/false)
5. Uses a non-standard sort format. Colon-based values (:) can cause confusion and require extra parsing. /posts?sort=-date_created

You should not allow filtering by: passwordHash, isSuperAdmin, internalFlag, Any field that would reveal sensitive logic
-->

---
transition: slide-left
---

# Use Security in API Early in Development
 
- ✅ Use authentication & authorization on every endpoint
- ✅ Validate & sanitize all input (body, query, headers)
- ✅ Use HTTPS only — never expose APIs over HTTP
- ✅ Return generic error messages (avoid revealing stack traces)
- ✅ Rate limit and throttle sensitive routes

| Risk                         | Example                                              |
| ---------------------------- | ---------------------------------------------------- |
| **No authentication**        | Anyone can call `/users` or `/orders`                |
| **Insecure endpoints**       | `DELETE /users/99` allowed without permission checks |
| **Lack of rate limiting**    | APIs are vulnerable to brute-force or DDoS           |
| **Unvalidated input**        | SQL/NoSQL injection, XSS via query strings or JSON   |

---
transition: slide-left
---

# Exercise: Security in APIs
Identify what’s wrong from a security perspective.

| Endpoint                                   | What’s Wrong? |
| ------------------------------------------ | ------------- |
| `GET /users` (public)                      |               |
| `POST /users` (no auth)                    |               |
| `DELETE /users/99` (open to all)           |               |
| `POST /login` returns stack trace on error |               |
| `GET /orders?status=shipped OR 1=1`        |               |

<!--
1. No authentication — exposing all user data publicly is a security risk.
2. needs auth checks, rate limiting 
3. Deleting users should be restricted to authenticated and authorized roles only
4. Revealing stack traces gives attackers insight into internal structure. Show generic error instead
5. This suggests injection (e.g., NoSQL or SQL). User input is not being sanitized or validated.
-->

---
transition: slide-left
---

# Use Resource Cross References
Instead of embedding full resource objects in a response or request, you should reference related resources by ID or URI

- Keeps responses lightweight
- Favours linking over embedding unless tight coupling is required
- Avoids circular references
- In requests:
  - ❌ `/api/v1/items?card_id=123&item_id=321`
  - ✅ `/api/v1/carts/123/items/321`
- In responses:
  ```js
  // ✅
  {
    "id": "order-123",
    "customer": "/customers/456",
    "items": ["/products/789", "/products/101"]
  }
  ```

---
transition: slide-left
---

# Exercise: Resource Cross References

- You are developing a REST API for a university management system. The main resources are:
`/students`, `/courses`, `/instructors`
- Each student can enroll in multiple courses, and each course is taught by one instructor.
- `[ /students/1001 ] --enrolledIn--> [ /courses/CS101 ] --taughtBy--> [ /instructors/88 ]`
- Fill in the responses below:
  ```js
  { // GET /students/1001
    "id": "1001",
    "name": "Jamie Rivera",
    "enrolled_courses":  _______________________ 
  }

  {  // GET /courses/CS101
    "code": "CS101",
    "title": "Intro to Computer Science",
    "instructor": _______________________
  }
  ```
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

# Other API design principles

- Use of HTTP methods properly
   - Use GET to retrieve, POST to create, PUT/PATCH to update, DELETE to remove
   - Follow standard semantics for HTTP status codes
- Error Handling
   - Provide meaningful error messages and appropriate HTTP status codes
   - Use a consistent error response format
- Documentation
   - Provide clear, detailed, and up-to-date documentation


---
transition: slide-left
---

# Alternative ways to designing APIs (pg.1)

| Approach                         | Description                                                                                             | Pros                                                      | Cons                                                         | Tools / Notes                          |
|---------------------------------|-----------------------------------------------------------------------------------------------------|-----------------------------------------------------------|--------------------------------------------------------------|--------------------------------------|
| **Mocking JSON First**           | Start by mocking JSON data to define API responses early. Allows frontend and backend parallel work. | - Speeds up frontend/backend parallel development<br>- Encourages API contract design<br>- Easy testing and iteration | - Mocks may diverge from real backend if not updated         | Postman, JSON Server, MSW, MirageJS  |
| **API-First Design (OpenAPI)**  | Define API contract/spec first with OpenAPI/Swagger, then generate mocks and stubs.                   | - Generates documentation and tests<br>- Avoids miscommunication | - Steeper initial setup                                        | OpenAPI, Swagger                     |

---
transition: slide-left
---

# Alternative ways to designing APIs (pg.2)

| Approach                         | Description                                                                                             | Pros                                                      | Cons                                                         | Tools / Notes                          |
|---------------------------------|-----------------------------------------------------------------------------------------------------|-----------------------------------------------------------|--------------------------------------------------------------|--------------------------------------|
| **Backend-First Development**   | Build real backend endpoints first, then frontend connects to live API.                              | - Produces working real data early                          | - Slows frontend development<br>- Tight coupling              | Frameworks like Express, Django       |
| **Frontend-First with Local Mocks** | Develop UI with local mock data in component/state without real API initially.                        | - Rapid prototyping and UI validation                       | - Risk of frontend/backend divergence                          | React state, fixtures, [Storybook](https://storybook.js.org/)      |
| **Best Practice: Combine Approaches** | Start with API contract, mock endpoints for frontend dev, build real backend incrementally.          | - Maintains agility<br>- Keeps frontend/backend in sync    | - Requires discipline to keep mocks and real API aligned      | Mix of above tools                   |

---
transition: slide-left
---

# Exercise: Design API endpoints

- Pick a social media app you use and observe the UI on how to make a post.  For example:
<img src="/assets/twitter.png">
- Putting everything you've learned in this unit together, create an express app that provides  JSON via API endpoints for the app you've chosen (no ejs)
- Create API endpoints that will take care of any CRUD operations necessary to make a post
- ex: In my twitter screenshot, I have to think about getting/posting text, images, gifs, polls etc.

---
transition: slide-left
---

# Work Period

- I'll create breakout rooms which you can freely choose to enter
- Opportunity to work on midterm in class
- Opportunity to have 1:1 with me re: midterm or any questions 
   - DM me if you want to meet in a breakout room
- Work on your "Note-taking" midterm app due June 15 midnight EST
- Exercise: summarize our unit by creating a [Movie Ratings and Review app](https://courses.circuitstream.com/d2l/le/lessons/9514/topics/49843)