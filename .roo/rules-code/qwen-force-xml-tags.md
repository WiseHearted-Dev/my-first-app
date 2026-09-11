# qwen-force-xml-tags

CRITICAL OVERRIDE FOR LOCAL MODELS:
1. Do not output raw JSON tools like {"name": ...}.
2. Always format tool actions using XML tags:

<write_to_file>
<path>index.html</path>
<content>
<!DOCTYPE html>
<html>
<body style="background:#121212; color:white;">
  <h1>Dark Mode App</h1>
  <button onclick="alert('Hello!')">Click Me</button>
</body>
</html>
</content>
</write_to_file>

3. NEVER call switch_mode, ask_followup_question, or read non-existent files.
4. When done, call:
<attempt_completion>
<result>Task finished.</result>
</attempt_completion>
