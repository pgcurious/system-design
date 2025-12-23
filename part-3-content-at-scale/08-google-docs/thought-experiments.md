# Thought Experiments: Google Docs

These exercises test deep understanding of real-time collaboration systems.

---

## Experiment 1: The Transform Function

**Scenario:**
You're implementing OT for a simple text editor. The only operations are:
- `insert(position, character)`
- `delete(position)`

**Questions to consider:**

1. Write the transform function for `insert` vs `insert`. Given two concurrent inserts, how do you decide which one stays at its original position?

2. What about `insert` vs `delete`? If User A inserts at position 5 and User B deletes position 4, what happens to A's insert?

3. What about `delete` vs `delete`? Both users delete position 5. How do you handle this? (Hint: One delete should become a no-op.)

4. Now consider: `insert(5, "abc")` (multi-character insert). How does this complicate the transform function?

**The insight:** The transform function seems simple for single characters, but edge cases multiply with ranges, and the math must be proven correct for all cases.

---

## Experiment 2: The Undo Problem

**Scenario:**
User A and User B are editing. User A types "Hello", then User B types "World". User A presses Undo.

**Questions to consider:**

1. What should Undo do? Undo A's "Hello" (local undo), or undo the most recent operation "World" (global undo)?

2. If local undo (A undoing "Hello"), B's "World" was inserted after "Hello". After undo, where should "World" be?

3. Implement undo as a "reverse operation." For `insert(5, "Hello")`, the reverse is `delete(5, 5)`. But what if B inserted text at position 6 before A's undo?

4. Google Docs has "Version history" that lets you restore any previous version. How is this different from Undo? What happens to other users when you restore version from 2 hours ago?

**The insight:** Undo in collaborative systems is fundamentally harder than single-user undo. Most implementations choose "local undo" (undo your own operations in reverse order).

---

## Experiment 3: Rich Text Complexity

**Scenario:**
Beyond plain text, Google Docs supports bold, italic, fonts, colors, bullet lists, tables, images, and more.

**Questions to consider:**

1. How do you represent "Hello **World**" (with World bolded) as operations? Is bold a character property, a range, or something else?

2. User A bolds characters 6-10 ("World"). User B inserts "New " at position 6. What happens to the bold range?

3. Tables: User A adds a row. User B adds a column. User C edits cell (2,3). How do you model this? Is a table a nested document, or flattened into the main document?

4. Embedded images: User A moves an image. User B resizes it. How do you merge these? Is the image a single "character" at a position, or does it have internal structure?

**The insight:** Rich text OT is an order of magnitude more complex than plain text. Each feature (tables, images, comments) requires new operation types and transforms.

---

## Experiment 4: The Latency Fairness Problem

**Scenario:**
User A is in New York (20ms to server). User B is in Tokyo (200ms to server). Both edit simultaneously.

**Questions to consider:**

1. User A and User B type at the same position simultaneously. Due to latency, A's operations always arrive at the server first. B's operations always get transformed. Is this fair?

2. The server decides operation order by arrival time. A's fast connection means A's operations always "win" positional conflicts. How would you make this fairer?

3. Alternative: Use logical clocks (Lamport timestamps) instead of wall-clock time. How would this change fairness?

4. Perception: Even if the merge is fair, A sees their edits immediately while B sees them after 200ms. How does this affect the user experience?

**The insight:** Network latency creates asymmetry in collaborative editing. True fairness might require intentionally delaying fast users, which harms responsiveness.

---

## Experiment 5: The Scale Limit

**Scenario:**
A company wants to use Google Docs for their 10,000-person all-hands meeting notes. Every employee types simultaneously.

**Questions to consider:**

1. 10,000 users × 5 operations/second = 50,000 operations/second for one document. The OT server processes operations serially (to maintain order). Is this feasible?

2. Every operation is broadcast to 10,000 clients. That's 500 million messages per second for this one document. How do you scale the WebSocket layer?

3. The document view shows 10,000 cursors. The UI can't render 10,000 cursor indicators. How do you visualize presence at this scale?

4. A pragmatic solution: "View-only mode" for most users, "Editor mode" for 10. How do you implement this? How do you switch users between modes?

**The insight:** Collaborative editing doesn't scale linearly. Documents have practical limits on concurrent editors, and the product must design around these limits.
