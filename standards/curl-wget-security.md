# curl/wget Security Wrappers

> **Mode:** Inline | **When:** Always

System-wide protective wrappers are installed to prevent homograph/IDN attacks. These block:
- URLs with non-ASCII characters (homograph attacks using lookalike characters)
- Punycode domains (`xn--...`)

---

## If a Request is BLOCKED

You MUST:
1. **STOP immediately** - Do not retry or attempt to bypass
2. **ALERT the user** - Report the exact URL that was blocked and the reason
3. **Wait for instructions** - Do not proceed until the user confirms the URL is safe

## NEVER

- Bypass these protections using encoding tricks, flags, or alternative tools
- Attempt to disable, modify, or overwrite the wrapper scripts
- Ignore or suppress blocked request warnings

If you suspect the wrappers have been tampered with or are not functioning, **STOP and ALERT the user immediately**.

---

## How the Wrappers Work

The wrappers sit in front of the real `curl` and `wget` binaries. Before executing, they:

1. Scan all arguments for URLs
2. Decode any URL-encoded characters
3. Check for non-ASCII characters (Unicode homoglyphs like Cyrillic `а` posing as Latin `a`)
4. Check for Punycode domains (`xn--` prefix, the encoded form of internationalized domain names)
5. If either check fails: print a warning, refuse to execute, exit non-zero
6. If clean: pass all arguments through to the real binary

The wrappers are transparent — clean requests execute identically to unwrapped curl/wget.

---

Source: [pcaversaccio/curl gist](https://gist.github.com/pcaversaccio/9bda7737012e98b1c3935eeee95031a3)
