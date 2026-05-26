# Code Duel: Real-Time 1v1 Coding Battles

## Overview

Code Duel is PyScape-Basic's competitive coding feature where users challenge each other in live 1v1 battles. Race to solve coding problems, earn XP, and climb the global leaderboard.

## How It Works

### 1. Joining the Queue

**User Flow:**
```
1. User opens CodeDuel page
2. Selects difficulty (beginner, intermediate, advanced)
3. Selects programming language (Python, JavaScript, Java, C++)
4. Clicks "Join Queue"
5. System searches for opponent with matching preferences
```

**Time to Match:**
- Typical: 5-30 seconds
- If queue is large: may match opponents with slightly different difficulty

### 2. Duel Session

**Duration:** 15 minutes per duel

**During the duel, players can:**
- ✅ See problem statement and test cases (visible ones)
- ✅ Write and submit code in real-time
- ✅ See submission results (pass/fail counts)
- ✅ Watch opponent's progress live
- ✅ Chat with opponent (sportsmanship-focused)
- ✅ Submit multiple times (get feedback on each)

**Hidden from players:**
- ❌ Opponent's actual code
- ❌ Full solution or hidden test cases
- ❌ Exact opponent test results until duel ends

### 3. Winning Condition

**Priority order:**

1. **First to solve all tests** wins immediately
2. **If tied on test counts** → fastest time wins
3. **If neither solves all** → higher test pass count wins
4. **If completely tied** → draw (both earn consolation XP)

### 4. After the Duel

**Winners receive:**
- ✅ +100 XP for win
- ✅ Problem problem mastery
- ✅ Rank points (ELO-based)
- ✅ Streak tracking

**Losers receive:**
- ✅ +25 XP for participation
- ✅ Problem attempt recorded
- ✅ Learning opportunity (see editorial)

---

## Architecture

### Frontend Components

**Main Component:** `src/pages/practice/CodeDuel.js`

**Sub-components:**
- `src/components/codeDuel/DuelChallengeList.js` — Challenge selection UI
- `src/components/codeDuel/DuelLeaderboard.js` — Rankings display
- `src/components/editor/UniversalCodePlayground.js` — Code editor

**State Management:**
- `useContext(AuthContext)` — Current user
- WebSocket connection to backend

### Backend

**WebSocket Server:** `backend/duel-server.js`

**Key Functions:**
- `handleAuthentication()` — Verify user JWT
- `tryMatchmaking()` — Find opponent with similar preferences
- `createDuel()` — Initialize 1v1 session
- `executeCodeWithJudge0()` — Submit code for execution
- `completeDuel()` — Determine winner and award XP
- `awardXP()` — Update gamification table

**Database:**
- `duels` table — Stores duel sessions and results
- `duel_submissions` table — Individual code submissions
- `duel_stats` table — Leaderboard and user statistics

### Code Execution

**External Service:** Judge0 API

**How it works:**
1. User submits code in browser
2. Frontend sends to backend WebSocket
3. Backend submits to Judge0 API (RapidAPI)
4. Judge0 runs code in isolated Docker container
5. Returns: pass/fail, runtime, memory usage
6. Backend updates duel session and broadcasts to both players
7. Frontend receives update and refreshes UI

---

## Matchmaking Algorithm

```
1. User joins queue with (difficulty, language)

2. System looks for opponent:
   - First 30s: Exact difficulty + language match
   - If no match: Expand to ±1 difficulty level
   - If queue > 10 players: Force match to reduce wait

3. When match found:
   - Create duel session
   - Select random problem matching difficulty + language
   - Both players get SAME problem
   - Send "match-found" event to both

4. Duel starts immediately
   - 15-minute timer begins
   - Countdown visible to both players
   - First to solve wins
```

---

## XP & Ranking System

### XP Awards

**Per Duel:**
- Win: +100 XP
- Loss: +25 XP
- Participation bonus: +10 XP

**Based on difficulty:**
- Easy duel: Base XP
- Medium duel: +25% XP
- Hard duel: +50% XP

**Streaks:**
- Win streak 3+: +10 XP per win
- Win streak 5+: +20 XP per win

### Leaderboard Ranking

**Calculation:**
```
Rank = based on Win Rate and Total Wins

Example leaderboard:
#1: pro_coder    — 250 wins, 85% win rate
#2: john_dev     — 180 wins, 72% win rate
#3: alice_ai     — 120 wins, 68% win rate
```

**Reset:** Leaderboard resets monthly with achievements milestone

---

## Real-Time Features

### WebSocket Events

**Client sends:**
- `join-queue` — Enter matchmaking
- `code-submit` — Submit code
- `forfeit` — Abandon duel
- `send-message` — Chat with opponent

**Server sends:**
- `match-found` — Opponent matched, duel starting
- `code-result` — Your submission result
- `opponent-progress` — Opponent test pass count
- `duel-complete` — Duel finished, winner announced
- `receive-message` — Message from opponent

**Example flow:**

```
[User A] submit code
    ↓
WebSocket: {type: 'code-submit', code: '...'}
    ↓
Backend: executeCodeWithJudge0()
    ↓
Backend: {type: 'code-result', passed_tests: 3, total_tests: 5}
    ↓
Frontend A: Show results (3/5 tests passed)
    ↓
Backend: {type: 'opponent-progress', ...}
    ↓
Frontend B: Show "Opponent passed 3 tests"
```

---

## Best Practices for Users

### Before Duel

✅ **Do:**
- Test your environment (code editor working)
- Know the problem language well
- Be familiar with test case format
- Join with good internet connection

❌ **Don't:**
- Join too frequently without breaks
- Use external resources (defeats purpose)
- Alt-tab during duel (focus required)

### During Duel

✅ **Do:**
- Start with the problem statement carefully
- Submit incrementally (get feedback)
- Use sample test cases provided
- Respect opponent (sportsmanship)

❌ **Don't:**
- Panic on first wrong answer
- Submit without testing locally first
- Spam submissions
- Give up before timer ends

### After Duel

✅ **Do:**
- Learn from editorial if provided
- Review opponent approach (if shared)
- Analyze mistakes
- Continue practicing

❌ **Don't:**
- Blame Judge0 without checking code
- Assume loss is unfair (system is tested)
- Skip learning from failures

---

## Troubleshooting

### "Timeout During Duel"
- Check internet connection
- Try lower difficulty first
- Contact support with duel_id

### "Code Execution Error"
- Verify syntax in local IDE first
- Check that language is set correctly
- Look at error message carefully

### "Opponent Not Responding"
- System auto-completes after 15 minutes
- Your result is recorded
- You still earn XP for completion

---

## Future Enhancements

### Planned Features

- 🔄 **Replay system** — Watch duel submission history
- 📊 **Analytics** — Trends in problem difficulty
- 🎯 **Problem suggestions** — Recommended problems to practice
- 🏆 **Achievements** — Special badges for streaks, speed
- ⚔️ **Tournaments** — Large-scale competitive events
- 🌍 **Regional leagues** — Country-based rankings

---

## See Also

- [API.md](../API.md) — WebSocket event reference
- [ARCHITECTURE.md](../ARCHITECTURE.md) — System design
- [DATABASE.md](../DATABASE.md) — Duel data schema
