---
name: python-cli-tool
description: Build command-line tools and utility scripts in Python with argparse, file I/O, and proper error handling.
---

# Python CLI Tool

Build well-structured command-line scripts with argument parsing, file I/O, and clear output.

## Pattern

```python
#!/usr/bin/env python3
"""Brief description of what this tool does."""

import argparse
import sys
import json
from pathlib import Path


def main():
    parser = argparse.ArgumentParser(description=__doc__)
    parser.add_argument("input", help="Input file path")
    parser.add_argument("-o", "--output", default="output.txt", help="Output file")
    parser.add_argument("-v", "--verbose", action="store_true")
    args = parser.parse_args()

    input_path = Path(args.input)
    if not input_path.exists():
        print(f"Error: {input_path} not found", file=sys.stderr)
        sys.exit(1)

    # Process
    content = input_path.read_text()
    result = process(content)

    # Output
    Path(args.output).write_text(result)
    print(f"Written to {args.output}")


def process(content: str) -> str:
    """Core logic separated from CLI handling."""
    # Transform content here
    return content.upper()


if __name__ == "__main__":
    main()
```

## File Operations

```python
from pathlib import Path
import json

# Read
data = Path("input.json").read_text()
parsed = json.loads(data)

# Write
output = json.dumps(parsed, indent=2)
Path("output.json").write_text(output)

# Directory operations
out_dir = Path("output")
out_dir.mkdir(parents=True, exist_ok=True)
(out_dir / "result.txt").write_text("done")
```

## Best Practices

- Use `pathlib.Path` over `os.path` for file operations
- Separate CLI parsing from core logic for testability
- Print errors to stderr: `print(..., file=sys.stderr)`
- Use `sys.exit(1)` for error conditions
- Make scripts executable: add shebang + `chmod +x`
- Use `if __name__ == "__main__":` guard
