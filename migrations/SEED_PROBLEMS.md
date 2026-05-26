# Adding Dummy Problem to Supabase

## Quick Steps

1. **Go to Supabase Dashboard**
   - Navigate to your project at https://supabase.com
   - Click on "SQL Editor" in the left sidebar

2. **Run Migration Files (if not done yet)**
   - First run: `migrations/001_create_core_tables.sql`
   - Then run: `migrations/002_create_duel_tables.sql`

3. **Seed the Dummy Problem**
   - Copy and paste the contents of: `migrations/003_seed_dummy_problem.sql`
   - Click "Run" or press Ctrl+Enter
   - You should see: "✅ Successfully seeded 3 dummy problems..."

## What Gets Created

The seed file adds **3 versions** of the same problem for different languages:

### Problem: "Find Maximum Number in Array"
- **Difficulty:** Beginner
- **Languages:** Python, JavaScript, Java
- **XP Reward:** 50 points
- **Description:** Write a function that finds the maximum number in an array

### Test Cases Included:
**Public Tests (visible to users):**
1. Simple array: [3, 7, 2, 9, 1] → 9
2. Single element: [5] → 5
3. Negative numbers: [-5, -2, -8, -1] → -1

**Hidden Tests (for evaluation only):**
4. Large array: [100, 500, 250, 750, 300] → 750
5. All same numbers: [7, 7, 7, 7] → 7
6. Mix positive and negative: [-10, 5, -3, 20, 15] → 20

## Verification

After running the seed file, verify by running:

```sql
SELECT id, title, language, difficulty 
FROM public.problems 
WHERE title = 'Find Maximum Number in Array';
```

You should see 3 rows (one for each language).

## Testing Code Duel

Once the problems are seeded:
1. Make sure backend is running: `cd backend && npm run dev:duel`
2. Start frontend: `npm start`
3. Navigate to Code Duel page
4. Select "Beginner" difficulty and any language (Python/JavaScript/Java)
5. Click "Find Opponent"
6. Open another browser/incognito tab and do the same
7. You'll be matched and can test the "Find Maximum Number" problem!

## Problem Structure

Each problem has:
- **Starter Code:** Template with function signature and comments
- **Solution Code:** Working solution (for reference)
- **Public Tests:** Shown to users for testing
- **Hidden Tests:** Used for final evaluation
- **Time Limit:** 5000ms (5 seconds)
- **Memory Limit:** 256MB

## Next Steps

After testing, you can add more problems by:
1. Copying the INSERT statement from `003_seed_dummy_problem.sql`
2. Modifying the title, description, starter_code, solution_code, and tests
3. Running the new INSERT in Supabase SQL Editor

Happy coding! 🎮
