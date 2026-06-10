# Detective notes

This is your deliverable. We grade the *thinking*, not the word count. Be honest
about what confused you and what unstuck you. Short and real beats long and fake.

---

## Bug 1: the page reloads and my message vanishes

**What I think is wrong (before fixing):**

_Your hypothesis here. Why does hitting Sign reload the whole page?_

**What did I ask the AI / what did I look up:**

_The prompt or doc that helped. What did it tell you?_

**What was the solution:**

_The actual fix, in your own words. What line did you add, and what does it do?_

---

## Bug 2: even without the reload, nothing gets saved

**What I think is wrong (before fixing):**

_What is wrong with the request the form was firing? What did the Network tab
show you about its method and body?_

**What did I ask the AI / what did I look up:**

_Your prompt or doc here._

**What was the solution:**

_How did you rebuild the fetch call? Which pieces did the POST need that the
broken version was missing?_

---

## Closing reflection

_Answer in a few sentences each:_

- Why does a `<form>` reload the page by default, and what does `preventDefault`
  actually prevent?
- Why can a GET not carry your message, while a POST can?
- What is the `Content-Type` header telling the server, and what breaks without
  it?

---

**Live URL (Vercel):** _paste here_
