# Detective notes

This is your deliverable. We grade the *thinking*, not the word count. Be honest
about what confused you and what unstuck you. Short and real beats long and fake.

---

## Bug 1: the page reloads and my message vanishes

**What I think is wrong (before fixing):**

When you hit Sign, the browser runs the form's default submit behavior: it tries to
navigate to the form's action URL (or reload the current page). That full navigation
wipes React state, so the inputs clear and the page "flashes." The handler was
calling `fetch`, but it never told the browser to skip that default navigation.

**What did I ask the AI / what did I look up:**

Read the README step 1 and MDN on the submit event — forms predate JavaScript and
their native job is to bundle fields and send them off while navigating the browser.

**What was the solution:**

Added `e.preventDefault()` as the first line of `handleSubmit`. It stops the browser
from doing its default form navigation so React can handle the submit in JavaScript
instead.

---

## Bug 2: even without the reload, nothing gets saved

**What I think is wrong (before fixing):**

The broken code called `fetch("/api/messages")` with no options. That defaults to a
GET request with no body. The route handler's POST function starts with
`await request.json()` and expects `{ name, text }` — a GET gives the server nothing
to parse or save.

**What did I ask the AI / what did I look up:**

Checked the Network tab (README step 2) and read `app/api/messages/route.js` to see
what method, headers, and body shape the server expects.

**What was the solution:**

Rebuilt the fetch as a real POST:

- `method: "POST"` — tells the server "here is data to store"
- `headers: { "Content-Type": "application/json" }` — so `request.json()` knows how to read the body
- `body: JSON.stringify({ name, text })` — sends the controlled input values as JSON

After a successful response (`201`), append the returned message to `messages` state
so the list updates without a manual refresh, then clear the inputs.

---

## Closing reflection

_Answer in a few sentences each:_

- **Why does a `<form>` reload the page by default, and what does `preventDefault` actually prevent?**  
  Forms were built to send field data to a server and navigate the browser to the
  response. That navigation is the reload you see. `preventDefault()` cancels that
  default navigation on the submit event so your JavaScript handler can send data
  without leaving the page.

- **Why can a GET not carry your message, while a POST can?**  
  GET is for asking the server for data; it has no request body in normal use.
  POST is for sending data to be stored or processed, and it carries a body the
  server can read.

- **What is the `Content-Type` header telling the server, and what breaks without it?**  
  It tells the server how to interpret the bytes in the body — here,
  `application/json` means "parse this as JSON." Without it, the server may not
  know to call `request.json()`, and parsing or saving can fail.

---

**Live URL (Vercel):** _paste here_
