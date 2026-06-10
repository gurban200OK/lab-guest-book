# Lab: The Guestbook That Won't Remember You

<img src="./public/main.png" width=600 />

## Introduction

You have this guestbook. It is the oldest, friendliest feature on the internet:
type your name, leave a nice message, hit **Sign**, and your words live on for
the next visitor. Simple.

Except this one has a memory problem. You type your name, you write something,
you hit Sign… the page blinks, the boxes empty out, and your message is gone like
it never happened. The next visitor sees nothing. You sign again. Same thing.

This lab is about a skill you will use on basically every app you ever build:
**wiring a real form submit**. By the end you will understand why a form reloads
the page, what `preventDefault()` actually does, and the anatomy of a `fetch`
POST that carries data to a server. Three small things, used constantly.

## The situation

Your team lead wanted to reuse this guestlist app. She poked at it for ten seconds and
sent you this:

> "Okay, two problems. When I hit Sign the whole page reloads and whatever I
> typed just vanishes, that is the obvious one. But there is a second, sneakier
> problem hiding behind it: even if the page sat still, the message would never
> actually be saved. The request your form fires is not the kind of request that
> can save anything. Fix both. And I do not just want the reload to stop, I want
> you to understand *why* the page reloads in the first place."

Anyone can make a symptom disappear.
**You are here to understand the machine**.

## The API

You do not need an external service for this. There is a tiny backend already
living inside the app at `app/api/messages/route.js`. It is a **Route Handler**,
and it is **correct, do not change it**. Your job is to talk to it properly.

It speaks two requests:

- `GET /api/messages` returns the array of messages currently saved.
- `POST /api/messages` saves a new one. It reads the request body as JSON and
  expects the shape `{ "name": "...", "text": "..." }`. On success it returns the
  created message with status `201`.

Before you write a single line, open
[http://localhost:3000/api/messages](http://localhost:3000/api/messages) directly
in your browser. You will see the seeded messages as raw JSON. That is the GET
working. Now go read the `POST` function in that file and notice what it does on
its very first line: `await request.json()`. Sit with that. What does it imply
about the request your form needs to send?

> ⚠️ The messages live in the server's memory, so they reset when you restart the
> dev server. That is expected, do not try to "fix" it, it's ok for this testing phase.

## What is the app doing?

- The form looks finished. The inputs are controlled, they update as you type.
- The list under the form shows the saved messages, and it loads them on first
  paint. That part works.
- But **Sign** does nothing useful. The page reloads, the inputs clear, and no
  new message ever joins the list.

The bug is entirely in how the form submits. Two problems, stacked. Find both.

## Getting started

```bash
npm install
npm run dev
```

Open [http://localhost:3000](http://localhost:3000). You will see the guestbook
with a couple of seeded signatures. Type a name and a message, hit **Sign**, and
watch the page flash and your text disappear. That broken behavior is the
starting line, it is supposed to be like that.


## Your job

Work in this order. Fix one thing, observe what changes, then move to the next.

### 1. Stop the page from reloading

A `<form>` has a built-in default behavior when you submit it: the browser tries
to navigate, which reloads the page and throws away your React state. That is the
flash you are seeing. There is one well known line that tells the browser "I have
got this, do not do your default thing." Find where the submit is handled in
`app/page.js` and add it.

### 2. Make the request actually save something

Open your browser DevTools, go to the **Network** tab, and hit Sign. Look at the
request the form fires. What **method** is it using? Does it carry a **body**?

Compare that against what the route handler needs: it calls
`await request.json()` and reads `name` and `text`. A request with no body, sent
with the wrong method, gives the server nothing to save. Rebuild the `fetch` call
so it is a real POST: the right `method`, a `Content-Type` header that announces
JSON, and a `body` containing your `name` and `text` as a JSON string.

### 3. Show the new message without a manual refresh

Once the POST works, the message is saved on the server, but the list on screen
still will not update until you reload. Close that gap: after a successful sign,
get the new message into the `messages` state so it appears instantly. You can
append what the POST returns, or just re-fetch the list. Pick one and know why.

## 💡 Think about it

- **Why does a form reload at all?** Forms predate JavaScript. Their native job is
  to bundle their fields and send them off to a URL, navigating the browser in the
  process. `e.preventDefault()` is you stepping in to say "stop, I will handle the
  sending myself." Removing the reload is not a hack, it is the whole point of a
  JavaScript-driven form.
- **Why can't a GET save your message?** A GET request asks for data, it does not
  carry a body to hand over. POST is the verb for "here is some data, store it."
  Same destination URL, completely different intent.
- **What is `Content-Type` for?** The server cannot guess what your bytes are. The
  `Content-Type: application/json` header is you telling it "read this body as
  JSON," which is exactly what `request.json()` is waiting for.

## How to work through this

1. Read `app/api/messages/route.js` end to end. Understand the contract before you
   try to fulfil it.
2. Open the Network tab and keep it open. Let the browser show you the truth about
   each request: its method, its payload, its response status.
3. Fix the reload first (step 1). Confirm the page stops flashing.
4. Fix the request second (step 2). Confirm you see a `POST` with a JSON body and
   a `201` response.
5. Close the refresh gap last (step 3).

Work like a detective, not like someone pasting the first Stack Overflow snippet.

## Styling

Use Tailwind. The form and list are already styled, so you can leave the looks
alone and focus on the behavior.

## Checklist before you call it done

✅ Hitting Sign no longer reloads the page.
✅ The Network tab shows a POST to /api/messages with a JSON body.
✅ Your signed message appears in the list right after you sign.
✅ The name and message inputs clear after a successful sign.
✅ A refresh of the page still shows your message (until you restart the server).
✅ No errors in the browser console.

## If you finish early

- Make it **optimistic**: show the new message instantly, before the server even
  responds, and reconcile afterwards.
- **Guard empty submits** on the client so you never fire a request with a blank
  name or message (the server already rejects them with a 400, but the nicer place
  to catch it is before you send).
- Add a **timestamp** to each message and show "just now" style relative times.
- Make new signatures **scroll into view** so the latest one is always visible.
- Real persistence: swap the in-memory array for something that survives a
  restart (a JSON file, or a real database).

## Key concepts to review

- **The default form submit**: [MDN: the submit event and `preventDefault`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLFormElement/submit_event)
- **Controlled components**: [React docs: Reacting to Input with State](https://react.dev/learn/reacting-to-input-with-state)
- **The anatomy of a POST**: [MDN: using `fetch` with a JSON body](https://developer.mozilla.org/en-US/docs/Web/API/Window/fetch)
- **Route Handlers** in this version of Next.js: read
  `node_modules/next/dist/docs/` (the Route Handlers guide).

## Delivering the lab

Work in your assigned groups. When the guestbook remembers you:

1. Fill in `NOTES.md`. We grade the *thinking*, so be honest about what you tried.
2. Everyone opens a Pull Request and shares the link in the students portal.
3. Optional but encouraged: deploy to Vercel and drop the live URL in `NOTES.md`.
