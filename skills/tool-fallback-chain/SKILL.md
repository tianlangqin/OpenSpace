---
name: tool-fallback-chain
description: Resilience pattern for recovering from tool failures using a cascading fallback chain (write_file → heredoc → python3 -c)
---

# Tool Fallback Chain

When infrastructure tools fail intermittently (e.g., "unknown error" on write_file, or run_shell refusing to execute scripts), use a cascading fallback strategy to accomplish the same goal through alternative means.

## Core Pattern

Always attempt the primary tool first. If it fails, immediately fall through to the next level — do not retry the same failing tool more than once.

```
Level 1: write_file(path, content)
   ↓ (on failure)
Level 2: run_shell("cat > path << 'EOF'\n...\nEOF")
   ↓ (on failure)
Level 3: python3 -c "with open('path','w') as f: f.write(...)"
```

For script execution:

```
Level 1: run_shell(script_path)  or  run_shell("bash script.sh")
   ↓ (on failure)
Level 2: python3 -c "exec(open('script.sh').read())"  (for simple shell logic)
   ↓ (on failure)
Level 3: rewrite the logic entirely as a python3 -c one-liner
```

## When to Activate This Pattern

- `write_file` returns an "unknown error" or generic failure (not a permission/content issue).
- `run_shell` returns an "unknown error" when executing a script file.
- The error is clearly infrastructure-level, not logic-level (i.e., the content or command itself is valid).
- You have already confirmed the target directory exists and the content is correct.

## Important Rules

1. **Do not retry the same failing tool blindly.** If `write_file` fails once on valid content, immediately go to Level 2.
2. **Preserve content exactly.** When switching from `write_file` to heredoc or Python, ensure no content is altered (watch for shell interpolation in heredocs — always use quoted `'EOF'` to prevent variable expansion).
3. **Check the result.** After using a fallback, verify the file was written correctly (e.g., `cat file | head -5` or check file size).
4. **If all levels fail, report clearly.** State which levels were attempted and what errors were returned — this indicates a deeper infrastructure problem that the agent cannot resolve alone.

## Examples

### Fallback from write_file

Primary attempt:
```
write_file(path="/tmp/output.txt", content="line1\nline2\n")
```

If that fails, immediate fallback:
```
run_shell("cat > /tmp/output.txt << 'EOF'\nline1\nline2\nEOF")
```

If that also fails, final fallback:
```
run_shell("python3 -c \"open('/tmp/output.txt','w').write('line1\\nline2\\n')\"")
```

### Fallback from run_shell script execution

Primary attempt:
```
run_shell("bash /tmp/myscript.sh")
```

If that fails, inline via Python:
```
run_shell("python3 -c \"\nimport subprocess\nresult = subprocess.run(['curl', '-s', 'https://example.com'], capture_output=True, text=True)\nprint(result.stdout)\n\"")
```

### Multi-line heredoc with special characters

When content contains backticks, dollar signs, or other shell-sensitive characters, always quote the EOF delimiter:
```bash
cat > /tmp/config.yaml << 'EOF'
api_key: $SECRET_VAR
command: `echo hello`
EOF
```