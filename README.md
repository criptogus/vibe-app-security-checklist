# Vibe app security checklist

A short, practical checklist for founders shipping apps built with AI coding tools (Lovable, Bolt, Cursor and similar) on a Supabase or similar backend. Each item says what to check and links to the official documentation behind it.

This checklist helps you find common problems. It is not a security audit, and completing it does not mean an app has no vulnerabilities.

Maintained by [Liquid Cybersecurity](https://liquidplatform.org/?utm_source=github&utm_medium=organic&utm_campaign=nov26). Items 1 to 7 are adapted from Liquid's [pre-production code security checklist](https://liquidplatform.org/code-security-checklist.html?utm_source=github&utm_medium=organic&utm_campaign=nov26).

## Code checks

### 1. Prove authorization on the server
- [ ] Every route or server function that reads or changes data checks who is calling and whether that user may act on that specific record.
- [ ] Tested with two accounts: user A cannot read or change user B's data by changing an ID.
- Docs: [Lovable security best practices](https://docs.lovable.dev/tips-tricks/security-best-practices), [Supabase: Securing Edge Functions](https://supabase.com/docs/guides/functions/auth)

### 2. Keep credentials out of source and logs
- [ ] No secret keys in frontend code, in `VITE_` (or other public-prefix) variables, in the repository, or in logs.
- [ ] Any credential that was exposed has been revoked or rotated at the issuer. Deleting it from the code is not enough.
- Docs: [Vite env variables](https://vite.dev/guide/env-and-mode), [Supabase API keys](https://supabase.com/docs/guides/getting-started/api-keys), [GitHub: removing sensitive data](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/removing-sensitive-data-from-a-repository)

### 3. Trace dynamic code execution
- [ ] No request data reaches `eval`, `new Function` or a shell command.

### 4. Preserve TLS certificate validation
- [ ] No `rejectUnauthorized: false` or equivalent outside local test code.

### 5. Validate at boundaries and encode at sinks
- [ ] Input is validated on the server.
- [ ] Database queries use parameters, not string building.
- [ ] Output to HTML is encoded.

### 6. Review what you ship
- [ ] Lockfile committed, dependency advisories reviewed, permissions and integrations limited to what the app needs.

### 7. Verify the fix and keep the limits visible
- [ ] Before and after evidence for each fix.
- [ ] A written note of what was not tested.

## Supabase and Lovable checks

### 8. Row Level Security on every exposed table
- [ ] RLS enabled on every table in an exposed schema, with policies that match your intent.
- [ ] Supabase Security Advisor run, findings reviewed.
- [ ] Views that expose user data are created with `security_invoker`, or are not exposed.
- Docs: [Supabase Row Level Security](https://supabase.com/docs/guides/database/postgres/row-level-security), [Supabase database advisors](https://supabase.com/docs/guides/observability/advisors)

### 9. Secret keys only in server code
- [ ] The Supabase secret or `service_role` key is used only in server code, and that code checks permissions itself, because the key bypasses RLS.
- Docs: [Supabase API keys](https://supabase.com/docs/guides/getting-started/api-keys), [Lovable Secrets](https://docs.lovable.dev/features/secrets)

### 10. Platform scans
- [ ] On Lovable: Deep scan run, critical findings resolved.
- Docs: [Lovable security overview](https://docs.lovable.dev/features/security)

### 11. Login and storage
- [ ] Leaked password protection enabled (Supabase: available on the Pro Plan and above; Lovable built-in backend: Password HIBP check).
- [ ] Private files are in private buckets, with `storage.objects` policies limiting each user to their own files.
- Docs: [Supabase password security](https://supabase.com/docs/guides/auth/password-security), [Supabase Storage access control](https://supabase.com/docs/guides/storage/security/access-control)

## AI features

### 12. The assistant never has more access than the user
- [ ] AI tools run with the signed-in user's permissions; authorization is enforced in code and the database, not in the prompt.
- [ ] High-impact actions need human confirmation.
- [ ] Model output is treated as untrusted (no raw HTML or external images rendered).
- Docs: [OWASP LLM01:2025](https://genai.owasp.org/llmrisk/llm01-prompt-injection/), [OWASP LLM06:2025](https://genai.owasp.org/llmrisk/llm062025-excessive-agency/), [OWASP Prompt Injection Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/LLM_Prompt_Injection_Prevention_Cheat_Sheet.html)

## Email

### 13. Sending domain is authenticated
- [ ] One SPF record, DKIM on at each sender, DMARC record starting at `p=none` with reports.
- Docs: [Gmail sender guidelines](https://support.google.com/a/answer/81126), [Google: Set up DMARC](https://support.google.com/a/answer/2466580), [RFC 9989](https://www.rfc-editor.org/rfc/rfc9989.html)

## Accounts

### 14. Protect the accounts behind the app
- [ ] Multi-factor authentication on your Supabase and GitHub accounts, your app builder, and your domain registrar.
- Docs: [Supabase account MFA](https://supabase.com/docs/guides/platform/multi-factor-authentication), [GitHub 2FA](https://docs.github.com/en/authentication/securing-your-account-with-two-factor-authentication-2fa/configuring-two-factor-authentication), [Lovable 2FA](https://docs.lovable.dev/introduction/two-factor-authentication-2-fa)

## Guides

Step-by-step guides for several of these items ([all guides](https://liquidplatform.org/guides?utm_source=github&utm_medium=organic&utm_campaign=nov26)):

- [How to check Supabase Row Level Security before launch](https://liquidplatform.org/guides/check-supabase-row-level-security-before-launch?utm_source=github&utm_medium=organic&utm_campaign=nov26)
- [VITE_ environment variables are public](https://liquidplatform.org/guides/vite-environment-variables-are-public?utm_source=github&utm_medium=organic&utm_campaign=nov26)
- [Free website security check: HTTPS, headers, SPF and DMARC](https://liquidplatform.org/guides/free-website-security-check-https-headers-spf-dmarc?utm_source=github&utm_medium=organic&utm_campaign=nov26)

## Try a free code review

You can paste JavaScript or TypeScript code you own, including JSX and TSX, up to 12,000 bytes and with no real keys or personal data, into the [Liquid code review sandbox](https://liquidplatform.org/sandbox?utm_source=github&utm_medium=organic&utm_campaign=nov26). It reads the code in your browser without running it and without sending it to a server, with no signup, no upload and no saved history. It checks 8 fixed rules: `eval` or other dynamic code, shell commands, disabled TLS certificate checks (`rejectUnauthorized: false`), MD5 or SHA-1 (for information only), and request data reaching a network call, a SQL string, HTML output, or the choice of which database record to load. It does not look for secrets, check Supabase RLS, buckets or settings, check dependencies, read a repository or a live site, or confirm a fix. A result is a starting point for review and never approves code for production.

## Contributing

Suggestions are welcome through issues. We are not accepting pull requests from third parties for now. Please link to official documentation for any item you suggest. A maintainer reviews every link, and we do not add links to tools that ask you to paste keys, tokens or code.

## License

This checklist is licensed under [Creative Commons Attribution-NonCommercial 4.0 International (CC BY-NC 4.0)](https://creativecommons.org/licenses/by-nc/4.0/). You may share and adapt it for non-commercial purposes, as long as you give appropriate credit to Liquid Cybersecurity, link to the license and indicate if changes were made.
