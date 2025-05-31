
**Video:** https://www.youtube.com/watch?v=N0Tz4Qojnno  
**Speaker:** Ben Sadeghipour  
**Role:** Ethical hacker, bug bounty hunter, founder of Hacking Hub

---

## Introduction

Ben shares how AI has fundamentally changed the way he approaches bug hunting. His talk focuses on practical examples of using LLMs (Large Language Models) to speed up reconnaissance, exploit development, and vulnerability discovery.

---

## Bug Bounties Overview

- Bug bounties pay hackers to ethically find and report vulnerabilities.
- Payouts have grown significantly — up to **$2M per bug**, especially in crypto and fintech.
- Ben has earned 7 figures since going full-time in 2022.
- His notable targets include TikTok, Epic Games, Apple, Facebook, Uber, PayPal, and NASA.

---

## AI for Hackers – “Applied AI”

Ben uses LLMs like ChatGPT, Claude, and WhiteRabbit Neo as assistants across multiple tasks. His key points:

### Why AI Is Better Than Google/Stack Overflow:
- Direct answers vs. link surfing.
- Context-aware analysis and suggestions.
- Helps overcome language barriers and documentation gaps.
- Acts as a second brain for experimentation and learning.

---

## LLM Usage Patterns in Bug Hunting

### 1. **Recon/Asset Discovery**
- Subdomain enumeration yields huge lists (e.g. 27,000 NASA domains).
- AI is used to:
  - Identify interesting patterns (e.g., internal tools, staging envs, devops keywords).
  - Filter targets (e.g., Jenkins, GitLab, Grafana) from asset lists.
  - Suggest misconfigured assets (e.g., exposed dashboards).

### 2. **Security Model Workarounds**
- Tools like ChatGPT and Claude increasingly refuse hacking queries.
- WhiteRabbit Neo (or self-hosted LLMs) are preferable — no filters, domain-specific knowledge.

---

## AI-Assisted Vulnerability Discovery

### Case Study 1: **GitLab Misconfiguration**
- Many self-hosted GitLab instances expose the `/explore` section or API endpoints unintentionally.
- LLM helped find public project links without login.
- Ben found internal NASA tools and dashboards (e.g. NetBox) just by exploring unauthenticated GitLab endpoints.

---

### Case Study 2: **IDOR (Insecure Direct Object Reference)**
- API exposes personal data based on usernames.
- Vendor initially rejected report: “must know the exact username.”
- AI helped generate a list of South American last names + first initials to brute-force usernames.
- Found dozens of valid combinations → received a $3K bounty.

### Takeaway:
- AI can assist with social engineering, cultural context, and brute-force heuristics (e.g., guessing localized usernames).

---

## LLM as an Enumeration Assistant

### Case Study 3: **IIS Shortname Enumeration**
- Exploits legacy Windows/IIS behavior to reveal file/directory names (first 6 characters).
- Traditionally brute-forced; AI speeds it up by suggesting common directory/file suffixes based on context.
- Helped find sensitive `.dll`, `.zip`, and `.bak` files on a government web server.

---

## Case Study 4: **DLL Analysis with AI**
- After downloading a `.dll` file from a misconfigured endpoint, AI helped:
  - Suggest reverse engineering tools.
  - Extract sensitive info like hardcoded passwords.
  - Generate proof-of-concept payloads.
- Found login credentials and used the API to upload a webshell (leading to full access).

---

## Case Study 5: **Zip Slip**
- Vulnerability where zip files are crafted to overwrite files on the server using directory traversal.
- AI wrote a script with arguments to automate malicious zip file generation.
- AI helped enumerate common deployment paths for Tomcat and Java webapps (e.g., `/usr/local/tomcat/webapps/`).
- Ben brute-forced zip payloads targeting known files in these directories (e.g., `examples/book.jsp`).
- Demonstrated RCE (remote code execution) by overwriting JSP examples on a test server.

---

## Final Thoughts

### Key Benefits of AI in Bug Hunting:
- **Speed**: Automates common recon, scripting, and enumeration steps.
- **Context Awareness**: Understands web app structures, API behaviors, naming conventions.
- **Non-Coder Friendly**: Ben doesn't write much code himself; he uses LLMs to scaffold and complete scripts.
- **Language & Locale**: Assists in understanding foreign-language apps or culturally localized input patterns.

### Risks:
- Avoid blindly copying AI-generated code into production.
- AI is a tool, not a replacement — it augments creativity and productivity.

---

## Recommendations for Hackers

- Use models that don’t block infosec workflows (e.g. WhiteRabbit Neo, self-hosted LLMs).
- Build prompt pipelines (e.g., asset filter, pattern matcher, fuzzing assistant).
- Use LLMs for brainstorming and recon, not just code.
- Collect known vulnerable patterns and let the model look for them in new targets.

---

## Summary

Ben’s thesis: **LLMs are revolutionizing offensive security**. They reduce the cost of experimentation, automate the boring parts of bug hunting, and help non-developers exploit vulnerabilities at scale. By training your model with the right context, you gain an edge over traditional methods — especially in reconnaissance and discovery.

**Ben’s work + LLM = high-velocity, AI-augmented ethical hacking.**
