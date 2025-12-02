# Silice Protocol V3: Practical Usage Guide

**Quick Reference**: How to use `silice-protocol-v3.json` to develop any feature

---

## 📋 What is Silice Protocol V3?

A **9-step development workflow** that treats JSON as the single source of truth, minimizing AI token usage while ensuring quality. Think of it as a strict recipe for adding features, fixing bugs, or making changes.

---

## 🚀 Quick Start: The 9 Steps

Use these steps **in order** for every change:

### **Step 1: Roadmap** ✅ Validate
**Question**: *Is this feature planned?*

```bash
# Check if feature exists in roadmap
- Look at ROADMAP.md or knowledge.json → roadmap
- Confirm it's prioritized
- Note dependencies
```

**Output**: ✅ "Feature X is approved and has no blockers"

---

### **Step 2: Design** 📝 JSON Specification
**Question**: *What exactly are we building?*

```json
// Update _07_config/knowledge.json
// ADD this section (send ONLY the diff):
{
  "api": {
    "YourNewClass": {
      "your_new_method": {
        "args": ["arg1", "arg2"],
        "returns": "bool",
        "purpose": "Does X when Y"
      }
    }
  }
}
```

**Token Tip**: Send only the NEW section (50 tokens), not the entire file (3500 tokens)

**Output**: Updated `knowledge.json` with API contract

---

### **Step 3: Context** 🔍 Analyze Dependencies
**Question**: *What existing code will this affect?*

```bash
# Query codebase_index.json
- Find related classes/methods
- Check for MVC violations
- Map complexity impact
```

**Output**: "This affects BoardState2D.place_stone, calls laser.calculate()"

---

### **Step 4: Spec** 🧪 Write Failing Tests
**Question**: *How do we know it works?*

```python
# _05_tests/test_your_feature.py
def test_new_feature():
    # Given
    board = BoardState2D(grid_size=5)
    
    # When
    result = board.your_new_method(arg1, arg2)
    
    # Then
    assert result == expected_value
```

```bash
# Run test - it should FAIL
python -m pytest _05_tests/test_your_feature.py -v
```

**Output**: ❌ Red phase - test fails as expected

---

### **Step 5: Build** 💻 Implement
**Question**: *Write minimal code to pass tests*

```python
# _01_core_logic/board_state.py
class BoardState2D:
    def your_new_method(self, arg1, arg2):
        # Implement logic
        return result
```

```bash
# Run test again
python -m pytest _05_tests/test_your_feature.py -v
```

**Output**: ✅ Green phase - all tests pass

---

### **Step 6: JSON** 📊 Update Knowledge Base
**Question**: *Does JSON still match reality?*

```json
// Update _07_config/codebase_index.json (ONLY the diff)
{
  "01_core_logic/board_state.py": {
    "BoardState2D": {
      "methods": {
        "your_new_method": {
          "complexity": 5,
          "calls": ["laser.calculate"],
          "called_by": ["ui/game_board.py"]
        }
      }
    }
  }
}
```

**Critical**: Send ONLY the new section, not entire file

**Output**: Synchronized JSON knowledge base

---

### **Step 7: Markdown** 📖 Update Docs
**Question**: *Can humans understand this?*

```markdown
<!-- _06_docs/API_REFERENCE.md -->
## BoardState2D.your_new_method

**Purpose**: Does X when Y

**Parameters**:
- `arg1`: Description
- `arg2`: Description

**Returns**: `bool` - True if successful

**Example**:
\```python
board.your_new_method(arg1, arg2)
\```
```

**Output**: Updated human-readable documentation

---

### **Step 8: Audit** 🛡️ Quality Check
**Question**: *Does this meet quality standards?*

```bash
# Check MVC violations
grep -r "PySide6" _01_core_logic/  # Should be empty

# Check complexity
# Max 15 per method

# Run linters
pylint _01_core_logic/board_state.py
mypy _01_core_logic/board_state.py

# Validate JSON
python -m json.tool _07_config/knowledge.json > /dev/null
```

**Quality Gates from silice-protocol-v3.json**:
- ✅ No UI imports in logic layers
- ✅ Complexity ≤ 15
- ✅ Test coverage: Core 100%, Integration 80%, UI 60%
- ✅ Valid JSON syntax

**Output**: ✅ All checks pass or 🔴 Violations to fix

---

### **Step 9: Commit** 🎯 Finalize
**Question**: *Is everything clean?*

```bash
# Final full test
python -m pytest _05_tests/ -v

# Commit
git add .
git commit -m "feat: Add YourNewMethod to BoardState2D

- Implements X functionality
- Updates knowledge.json and codebase_index.json
- All tests passing (100% coverage)
- Complexity: 5/15"

# Optional: Generate artifacts
# Output to _08_generated/ if needed
```

**Output**: Clean commit with all changes tracked

---

## 💡 Practical Examples

### Example 1: Adding a New Game Feature

**Feature**: Add "stone rotation limit" (max 2 rotations per stone)

```
Step 1 (Roadmap): ✅ Check ROADMAP.md → "Stone rotation mechanics" is listed
Step 2 (Design):  📝 Update knowledge.json → BoardState2D.rotate_stone → add "rotation_count" tracking
Step 3 (Context): 🔍 Query codebase_index.json → Affects board_state.py, game_board.py
Step 4 (Spec):    🧪 Write test_rotation_limit.py → assert rotation fails after 2nd use
Step 5 (Build):   💻 Add rotation_count dict to BoardState2D
Step 6 (JSON):    📊 Update codebase_index.json → new method, ui_config.json → rotation settings
Step 7 (Markdown):📖 Update GAME_RULES.md → document rotation limit
Step 8 (Audit):   🛡️ Check: no UI in core ✅, complexity 8/15 ✅, tests pass ✅
Step 9 (Commit):  🎯 git commit -m "feat: Add 2-rotation limit per stone"
```

**Time**: 1-2 hours

---

### Example 2: Fixing a Bug

**Bug**: Replayer crashes when loading game with special moves

```
Step 1 (Roadmap): ⏭️ Skip (bug fix, not planned feature)
Step 2 (Design):  📝 Update knowledge.json → replayer.py → add special_moves handling
Step 3 (Context): 🔍 Affects replayer.py, recorder.py
Step 4 (Spec):    🧪 Write test_replayer_special_moves.py → reproduce crash
Step 5 (Build):   💻 Fix replayer.load_game() to handle special_moves key
Step 6 (JSON):    📊 Add to knowledge.json → critical_bugs_fixed
Step 7 (Markdown):⏭️ Skip (no doc change needed)
Step 8 (Audit):   🛡️ Run tests ✅
Step 9 (Commit):  🎯 git commit -m "fix: Handle special_moves in replayer"
```

**Time**: 30 minutes

---

### Example 3: Updating UI Configuration

**Change**: Change stone margin from 50px to 75px

```
Step 1 (Roadmap): ⏭️ Skip (config change)
Step 2 (Design):  📝 Update ui_config.json → tabletop.margin: 75
Step 3 (Context): 🔍 Only affects game_board.py
Step 4 (Spec):    👁️ Visual test (run game, verify spacing)
Step 5 (Build):   💻 Ensure game_board.py reads from ui_config.json
Step 6 (JSON):    📊 Update knowledge.json if pattern changed
Step 7 (Markdown):⏭️ Skip
Step 8 (Audit):   👀 Visual check ✅
Step 9 (Commit):  🎯 git commit -m "style: Increase tabletop margin to 75px"
```

**Time**: 15 minutes

---

## 🎯 Token Efficiency Tips

### ❌ BAD (Wastes Tokens)
```
"Here's the entire knowledge.json file (3500 tokens)..."
"Let me regenerate the whole API_REFERENCE.md (10,000 tokens)..."
```

### ✅ GOOD (Token Efficient)
```
"Update knowledge.json → api → BoardState2D → add:
{
  'new_method': { 'args': [...], 'returns': 'bool' }
}
(50 tokens)"

"Update API_REFERENCE.md line 145-150:
Add new_method documentation
(200 tokens)"
```

**Savings**: 70x fewer tokens

---

## 📚 Integration with Other JSONs

| JSON File | Role | When to Update |
|-----------|------|----------------|
| `silice-protocol-v3.json` | **The protocol itself** | Never (reference only) |
| `knowledge.json` | **Architectural truth** | Steps 2 (design), 6 (implementation) |
| `codebase_index.json` | **Dependency graph** | Step 6 (after implementation) |
| `ui_config.json` | **UI constants** | Step 2 or 6 (if UI changes) |
| `development_guide.json` | **Process guide** | Rarely (protocol changes only) |

---

## 🤖 How AI Agents Should Use This

### For Any Development Request:

1. **Load protocol**: `Read silice-protocol-v3.json → protocol_steps`
2. **Start at Step 1**: Check roadmap alignment
3. **Follow sequentially**: Don't skip steps
4. **Reference by path**: Use `knowledge.json → api → ClassName → method`
5. **Send diffs only**: Never regenerate entire files
6. **Validate at Step 8**: Run all quality gates

### Example AI Workflow:

```
User: "Add AI difficulty levels"

AI: 
1. Query: silice-protocol-v3.json → protocol_steps → step_1
2. Action: Check knowledge.json → roadmap → "AI features"
3. Query: silice-protocol-v3.json → protocol_steps → step_2
4. Action: Send diff to knowledge.json (50 tokens, not 3500)
... (continue through all 9 steps)
```

---

## ✅ Success Indicators

You're using Silice V3 correctly if:

- ✅ JSON files are always updated **before** or **with** code changes
- ✅ You send **diffs**, not full file regenerations
- ✅ Tests are written **before** implementation (Step 4 before Step 5)
- ✅ All 9 steps are followed, even for small changes
- ✅ Quality gates in Step 8 catch violations automatically
- ✅ Token usage is **under 1K per feature**, not 50K+

---

## 🚫 Common Mistakes

| Mistake | Why It's Bad | Fix |
|---------|--------------|-----|
| Skip Step 4 (tests) | No specification of "done" | Always write failing test first |
| Regenerate entire JSON | Wastes 95% tokens | Send only the diff/new section |
| Update code before JSON | JSON truth becomes stale | Update knowledge.json in Step 2 |
| Skip Step 8 (audit) | Technical debt accumulates | Run quality checks before commit |
| Markdown before JSON | Double work, inconsistency | JSON first, markdown generated/updated after |

---

## 📖 Quick Reference Card

```
┌─────────────────────────────────────────────────┐
│ SILICE PROTOCOL V3 - 9 STEPS                   │
├─────────────────────────────────────────────────┤
│ 1. ✅ Roadmap    → Is this planned?            │
│ 2. 📝 Design     → Update knowledge.json       │
│ 3. 🔍 Context    → Query dependencies          │
│ 4. 🧪 Spec       → Write FAILING tests         │
│ 5. 💻 Build      → Code until tests PASS       │
│ 6. 📊 JSON       → Sync all JSONs              │
│ 7. 📖 Markdown   → Update/generate docs        │
│ 8. 🛡️ Audit      → Quality gates               │
│ 9. 🎯 Commit     → Clean finish                │
├─────────────────────────────────────────────────┤
│ Always: Send diffs, not full files             │
│ Token savings: 10-100x                         │
└─────────────────────────────────────────────────┘
```

---

## 🎓 Summary

**Silice Protocol V3** is a strict, token-efficient workflow that:
- Treats **JSON as truth**, markdown as output
- Requires **tests before code** (TDD)
- Enforces **incremental updates** (diffs only)
- Validates **quality automatically** (Step 8)
- Saves **90%+ AI tokens** through smart referencing

**Use it for**: Every feature, bug fix, or change to maintain consistency and quality.

**Skip it for**: Never. Even small changes benefit from the discipline.

---

**Next**: Reference `silice-protocol-v3.json → protocol_steps → step_X` for detailed actions and success criteria for each step.

