# Regular Expressions

### Flags
- `g` - global, match more than one occurance
- `i` - case insensitive
- `m` - multi-line match

### Metacharactors
- `.` - wildcard. match to any caractor
    - `h.t` for `hat`, `hot`
- `\` - scape charactor

### Charactor groups
- `\d` - for any number (opposite `\D`)
- `\w` - word (opposit `\W`)
- `\d` - digits (opposit `\D`)
- `\s` - white space `= [ \s\t\n]` (opposit `\s`)
- `[abc]` - any of a, b, or c
- `[^abc]` - not any of a, b, or c
- `[a-g]` - a to g

### Anchors
- `^abc$` - start / end of the string
- `\b, \B` - word, not-word boundary