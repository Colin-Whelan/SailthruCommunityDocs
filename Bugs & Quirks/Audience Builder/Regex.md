# Regular Expressions in Sailthru Audience Builder

Sailthru's Audience Builder supports regular expressions in the "Email address contains" criteria, allowing for powerful and flexible email address filtering beyond simple wildcards.

## Basic Usage

You can write regular expressions directly in the "Email address contains" field to create sophisticated audience segments based on email address patterns. This searches the complete email address including both the local part and domain.

## Supported Regex Features

### Character Classes `[]` ⚠️ **CRITICAL LIMITATION**

**Sailthru's regex implementation does NOT support character ranges in square brackets.** This is a major difference from standard regex - instead, they work as literal character sets only.

```regex
[abc]     # Matches literal 'a', 'b', or 'c' ✓
[a-z]     # Does NOT match a-z range ✗ - matches 'a', '-', or 'z' only!
[0-9]     # Does NOT match digits ✗ - matches '0', '-', or '9' only!
[A-Z]     # Does NOT match uppercase letters ✗ - matches 'A', '-', or 'Z' only!
[d-y]     # Matches 'd', '-', or 'y' only
```

**This explains unexpected behavior:**
- `*[A-Z]{1,20}*` matches emails with hyphens because it looks for 'A', '-', or 'Z'
- `*[d-y]{5,20}*` finds fewer results than `*[e-y]{5,20}*` because 'e' is more common than 'd'
- Patterns like `*[A-Z]*` will match emails with hyphens, not uppercase letters
- Many standard regex patterns will behave unexpectedly

**Workaround:** You must write out full character sets: `[abcdefghijklmnopqrstuvwxyz]` instead of `[a-z]` (though this may be impractical for long ranges).

### Quantifiers `{}`
Use curly braces to specify exact counts or ranges:

```regex
{1}       # Exactly 1 occurrence
{1,10}    # Between 1 and 10 occurrences
{3,}      # 3 or more occurrences
```

**Important Note:** Both `{1}` and `{1,1}` produce the same results - any apparent differences are due to unreliable preview counts in the interface.

### Wildcards `*`
The traditional wildcard `*` continues to work and can be combined with regex patterns:

```regex
*@domain.com    # Any characters before @domain.com
user*@*         # Addresses starting with "user"
*gmail*         # Any address containing "gmail"
```

## Common Use Cases

### Finding Letter + Number Patterns
**Note: Standard character ranges don't work in Sailthru.** The original pattern `[a-z]{1}[0-9]{1,10}*` will NOT work as expected.

Instead, you would need to write out full character sets (which may be impractical):

```regex
[abcdefghijklmnopqrstuvwxyz]{1}[0123456789]{1,10}*
```

**Alternative approach:** Use wildcards for simpler patterns:
```regex
*[a-z]*     # Will match emails containing 'a', '-', or 'z' (often hyphens)
*1*         # Emails containing the digit '1'
*test*      # Emails containing 'test'
```

### Identifying Gmail Addresses with Multiple Dots
Gmail addresses often contain multiple periods scattered throughout. To find these:

```regex
*.*.*.*.*.*@*
```

This finds addresses with 6 or more periods, which often indicates the same base Gmail address registered multiple times with varying dot placements.

## Examples in Action

| Pattern | What it ACTUALLY matches | Description |
|---------|-------------|---------|
| `[a-z]{1}*@gmail.com` | Emails with 'a', '-', or 'z' + @gmail.com | NOT lowercase letters (common mistake) |
| `*[0-9]*` | Emails containing '0', '-', or '9' | NOT all digits (often matches hyphens) |
| `test[0-9]{1,3}@*` | "test" + one instance of '0', '-', or '9' | Limited digit matching |
| `*[+]*` | Emails containing '+' symbol | This works as expected |

**Recommendation:** Use wildcards and literal character matching instead of character ranges.

## What Actually Works Reliably

Given Sailthru's limitations, focus on these proven patterns:

### ✅ **Wildcards** 
```regex
*gmail*         # Contains "gmail"
*test*          # Contains "test"  
user*@*         # Starts with "user"
*@company.com   # Ends with specific domain
```

### ✅ **Literal Character Sets**
```regex
[+]             # Contains plus sign
[.]             # Contains period
[abc]           # Contains 'a', 'b', or 'c' (literal only)
```

### ✅ **Quantifiers** 
```regex
{1}             # Exactly 1 occurrence
{1,10}          # Between 1 and 10 occurrences  
{3,}            # 3 or more occurrences
```

### ❌ **What Doesn't Work**
- `[a-z]` (character ranges)
- `[0-9]` (digit ranges) 
- `[A-Z]` (uppercase ranges)
- Complex regex features beyond basic patterns

## Known Limitations and Quirks

### Character Range Limitation
**Most important limitation:** Square brackets do NOT support character ranges. `[a-z]` matches only 'a', '-', or 'z' literally.

### Preview Count Issues
- Preview counts in the audience builder interface may show inconsistent numbers
- The actual downloaded results are typically accurate and reliable
- Always verify your segments by downloading a sample rather than relying solely on preview counts

### Regex Support
- Not all regular expression patterns are supported
- Character classes `[]` and quantifiers `{}` work reliably
- These can be effectively combined with wildcard `*` searches
- More complex regex features may not be available

### Best Practices
- Test your patterns with small segments first
- Download samples to verify results match your expectations
- Use `{1,1}` instead of `{1}` for single character quantification
- Combine regex patterns with wildcards for maximum flexibility

## Troubleshooting

**Different results between `{1}` and `{1,1}`?**
These patterns actually produce identical results. Any perceived differences are due to unreliable preview counts in the interface - the downloaded results will be the same for both formats.

**Preview count doesn't match downloaded results?**
This is a known interface issue. The downloaded results are typically correct, so rely on those for accurate counts.

**Pattern not working as expected?**
- **Check for character ranges**: `[a-z]`, `[0-9]`, `[A-Z]` don't work as ranges - they match literal characters only
- **Hyphen matches**: Patterns like `[a-z]` will often match emails with hyphens because '-' is treated literally
- Try breaking complex patterns into simpler wildcard components
- Test with small audiences first to verify behavior

## Advanced Tips

### Working with Sailthru's Limitations
Since character ranges don't work, focus on patterns that DO work effectively:

```regex
*+*              # Emails with plus signs (for alias detection)
*.*.*.*@*        # Multiple dots (Gmail variations)
*test*           # Contains "test" anywhere
*[abc]*          # Contains 'a', 'b', or 'c' (literal characters only)
```

### Email Validation Cleanup
Use regex patterns to identify problematic email formats:

```regex
*.*.*.*@*    # Multiple consecutive dots
*..@*        # Double dots  
*@*..*       # Dots in domain
*--*         # Double hyphens
```

---

*Note: This feature provides powerful segmentation capabilities but should be used carefully. Always test patterns thoroughly before applying them to large audiences.*
