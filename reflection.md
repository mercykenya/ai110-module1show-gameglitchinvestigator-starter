# 💭 Reflection: Game Glitch Investigator

Answer each question in 3 to 5 sentences. Be specific and honest about what actually happened while you worked. This is about your process, not trying to sound perfect.

## 1. What was broken when you started?

- What did the game look like the first time you ran it?
The game looked okay, and the first secret number was 50, so using binary search worked in the first try, but running it in subsequent tries that's when I began noticing the bugs

- List at least two concrete bugs you noticed at the start  
  (for example: "the secret number kept changing" or "the hints were backwards").

  Bug one: The secret number was 99, but upon guessing 50, I got feedback to go lower instead of going higher
  Bug two: The secret number was 22, but guessing 90 gave a go higher feedback
  Bug three: No bounds check still accepts negative numbers, or numbers greater than 100
  Bug four: The new game button does not work really, I have to refresh the entire project, it just resets the secret number and not the attempts or any other game data

  Bug 5: If you to toggle the different modes(easy(range 1-20),normal range 1-100, hard range 1-50), the secret number is not contrained to the ranges, in that for easy we can still get 44 and not strictly a number in that range, same case withe hard mode

  Bug 6: When you refresh and then click on new game, it resets the attempts and add an extra 1, e.g if there wrer 7 attemots they become 6


---

## 2. How did you use AI as a teammate?

- Which AI tools did you use on this project (for example: ChatGPT, Gemini, Copilot)?
Copilot

- Give one example of an AI suggestion that was correct (including what the AI suggested and how you verified the result).
Copilot suggested that the feedback messages were reversed in the `check_guess()` function (lines 36-48). When guess > secret, it was saying "Go HIGHER!" instead of "Go LOWER!". I verified this by testing: guessing 50 when secret was 99 should say "Go HIGHER" but was saying "Go LOWER". After swapping the emoji messages, the feedback became correct.

- Give one example of an AI suggestion that was incorrect or misleading (including what the AI suggested and how you verified the result).
Copilot initially suggested the issue might be with the secret number generation, but the real bug was that the `status` variable wasn't being reset in the "New Game" button handler. The game would show "You already won" even after clicking "New Game". I verified the fix worked by winning, clicking "New Game", and confirming I could play again.

---

## 3. Debugging and testing your fixes

- How did you decide whether a bug was really fixed?
I tested by manually playing the game multiple times: testing the feedback for higher/lower guesses, clicking New Game after winning, switching difficulty modes, and checking that the displayed range matched the actual secret number range. If the game behaved as expected, the bug was fixed.

- Describe at least one test you ran (manual or using pytest)  
  and what it showed you about your code.
I manually tested Bug 4 by: (1) playing a game until I won, (2) clicking "New Game 🔁", (3) verifying the game let me play again. Before the fix, the game would stop with "You already won." This showed me that the `status` wasn't being reset to "playing". After fixing it, the game worked correctly.

- Did AI help you design or understand any tests? How?
Yes, Copilot helped me understand that checking `if st.session_state.status != "playing"` at line 144 was the blocker. It explained that Streamlit apps rerun on every interaction, so session state needs to be reset properly or it persists through reruns.

---

## 4. What did you learn about Streamlit and state?

- In your own words, explain why the secret number kept changing in the original app.
The secret number started changing on subsequent runs because the initial game state used `attempts = 1` instead of `attempts = 0`. When you changed difficulty, it didn't reset the secret or status, so changing games was broken. Also, the "New Game" button hardcoded `random.randint(1, 100)` instead of using the correct difficulty range, so the secret wasn't constrained properly.

- How would you explain Streamlit "reruns" and session state to a friend who has never used Streamlit?
Streamlit reruns the entire script from top to bottom every time a user interacts with it (clicks a button, types input, etc.). Session state is like a memory that persists across reruns—variables stored in `st.session_state` stay alive between interactions. If you don't properly manage session state, bugs happen because old values stick around even when they shouldn't.

- What change did you make that finally gave the game a stable secret number?
I added logic to track the difficulty and auto-reset the game when it changes. In the "New Game" button, I changed from hardcoded `random.randint(1, 100)` to `random.randint(low, high)` to respect the difficulty range. I also reset `status = "playing"` so the game didn't get stuck thinking you already won.

---

## 5. Looking ahead: your developer habits

- What is one habit or strategy from this project that you want to reuse in future labs or projects?
  - This could be a testing habit, a prompting strategy, or a way you used Git.
I want to test edge cases. This project showed me that bugs often hide in state transitions, not just in single functions.

- What is one thing you would do differently next time you work with AI on a coding task?
I would be more skeptical of AI-generated code that has logical conditions I don't understand (like converting to strings on even attempts). Even if code looks plausible, I should ask "why" before assuming it's correct. I'd add more print statements or debug info early on to understand the actual behavior.

- In one or two sentences, describe how this project changed the way you think about AI generated code.
AI-generated code can be confident and look polished, but it can contain subtle, intentional bugs that break functionality. I now realize that using AI means I still need to actively test and debug—AI is a tool that needs verification, not a source of truth.
