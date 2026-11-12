---
layout: page
title: How I Use LLMs to Tailor CVs and Cover Letters
permalink: /
---

*How I use a master career profile, job descriptions, structured prompts, and document checks to create more relevant job applications.*

For every job application, I used to revisit my CV, rewrite experience bullets, rearrange skills, and draft another cover letter. Much of the information stayed the same. The emphasis changed depending on the role.

I wanted to reduce that repetitive work without sacrificing accuracy. So I built a prompt-driven workflow that uses an LLM to turn a master career profile and a job description into a tailored CV and cover letter.

## One profile, multiple job-specific CVs

The starting point is a master profile containing my professional experience, projects, technical skills, achievements, and measurable results. It acts as the source of truth. The LLM can reorganise and rewrite the information, but it must not invent experience, technologies, responsibilities, or metrics.

For each application, I provide the job description and ask the LLM to identify relevant requirements, prioritise matching experience, reorder skills, tailor the profile statement, and rewrite experience bullets using evidence from the master profile.

The same experience can be presented differently without changing what actually happened.

## A separate prompt for cover letters

I use the same job description and career context with a separate cover-letter prompt. It keeps the letter to one page, uses concrete examples, explains the connection between my experience and the role, and asks for a company-specific reason for applying.

The goal is a focused letter, not a generic paragraph about being passionate or enthusiastic.

## Document generation is only half the work

I use LaTeX templates to keep the output consistent:

- **CV:** ModernCV banking style with the blue colour scheme, compiled using LuaLaTeX. The target is exactly two pages.
- **Cover letter:** A custom `cover.cls` template using Lato and Raleway fonts, compiled using XeLaTeX. The target is exactly one page.

The prompts include rules for LaTeX special characters, spacing, page breaks, and known template issues. After generation, I compile and visually inspect both PDFs. I also extract the CV's PDF text to check contact details, reading order, character encoding, and relevant job-description keywords.

A PDF looking correct isn't enough. Its underlying text needs to be readable too.

## The prompts behind the workflow

These are the working prompt templates I use as a starting point. They assume that a master career profile and the target job description are provided to the LLM. I keep the detailed LaTeX templates and source facts in the project repository, rather than asking the model to invent either one.

<details markdown="1">
<summary><strong>CV creation and tailoring prompt</strong></summary>

~~~text
Follow these rules carefully. Use the supplied master candidate profile as the only source of factual claims, and tailor the CV to the supplied job description.

FRAMEWORK
- Template: LaTeX moderncv, banking style, blue colour scheme.
- Master reference: cv/main_example.tex. Use it as the source for competencies, experience, education, and achievements.
- Output: cv/main_<company>_<role>.tex.
- Compile with: lualatex -interaction=nonstopmode main_<company>_<role>.tex
- Expected result: exactly 2 pages. Any other page count must be fixed before presenting the CV.

FACTUAL ACCURACY
- Do not invent employers, dates, degrees, responsibilities, technologies, project ownership, metrics, or achievements.
- Past tailored CVs may be used as phrasing references only. Verify every factual claim against the master candidate profile.
- Preserve the distinction between individual contribution, shared team ownership, and technical leadership.
- Use metrics only when they are supported by the candidate profile.
- Do not add a job-description keyword unless the candidate's experience supports it.

PROFILE STATEMENT
- Place the profile statement immediately after the CV title.
- Write a tailored 5-7 line elevator pitch explaining what the candidate brings to this specific role.
- Lead with transferable-domain experience when the target role is outside the candidate's current domain.
- Use the most relevant profile template when one is supplied, but verify all facts against the master profile.

CORE COMPETENCIES / SKILLS
- Include 5-7 relevant competencies in a concise bullet list.
- Reorder skills according to the job description, putting the most relevant first.
- Use the employer's exact terminology in bold category labels when it truthfully applies.
- Briefly show the value or evidence behind each competency. Avoid keyword stuffing.

EDUCATION
- Include the highest relevant degrees.
- Keep education concise for experienced candidates.
- Mark any in-progress degree or certification explicitly as "In progress" and include the expected completion date.
- Keep completion dates consistent everywhere in the CV.

PROFESSIONAL EXPERIENCE
- Tailor bullets to the target role while preserving the actual scope of work.
- Use approximately 4-6 bullets for the most recent role, 3-4 for the previous role, and 2-3 for older roles, adjusting when the evidence requires it.
- Prefer specific actions, technologies, system context, and measurable outcomes over generic duties.
- Check that the amount of experience shown is credible for the employment dates. Surface real secondary work, explain genuine phases, or clarify long delivery cycles. Never invent projects or shorten employment dates to make the CV look fuller.
- Preserve relevant production support, troubleshooting, testing, security, collaboration, and delivery work when it supports the target role.

OTHER SECTIONS
- Include publications, awards, languages, and references only when supported by the profile and relevant to the role.
- Include links for verifiable public projects, publications, or other artifacts when available.
- Do not invent reference names or contact details.
- Translate every section heading if the CV language is not English. Do not leave English headings in an otherwise translated CV.

LATEX AND FORMATTING RULES
- Escape special characters in body text: & as \\&, % as \\%, $ as \\$ , # as \\#, _ as \\_, ~ as \\textasciitilde{}, and ^ as \\textasciicircum{}.
- Check every percentage sign carefully. An unescaped % comments out the rest of the LaTeX line without necessarily causing a compilation error.
- Escape ampersands in company names and other text.
- If a list item begins with a literal [, brace the item text so LaTeX does not interpret it as an optional label.
- Do not put \\vspace between \\item entries. Use the list's native spacing for consistent gaps.
- Small spacing after a section heading and between top-level CV entries is acceptable when needed.
- Keep the template's required name and section colour overrides. Do not remove required preamble configuration from the master template.

COMPILE, INSPECT, AND FIX
1. Compile with LuaLaTeX using the command above.
2. Confirm that the PDF has exactly 2 pages.
3. Visually inspect both pages. Check for clipped text, uneven spacing, awkward page breaks, and an experience heading stranded at the bottom of page 1 without its bullets.
4. If an entry is orphaned, use a local page-break fix before that entry. Do not add spacing globally or compress the page geometry to force a fit.
5. If substantial content spills onto page 3, cut the least relevant content. If page 2 looks unusually empty, restore the most relevant evidence that was removed.
6. Recompile and inspect again after every layout change.

ATS TEXT CHECK
- Extract the PDF text after the visual check, using the available PDF verification script or an equivalent text extractor.
- Check that the email address and contact details appear as actual text, not only as icons or hyperlink metadata.
- Check for garbled characters, replacement symbols, missing text, and incorrect reading order.
- Compare the extracted text with the job description's required and preferred keywords. Use exact wording when accurate, but never add unsupported skills.

FINAL REVIEW
- Confirm company name, role title, dates, contact details, and links.
- Confirm all claims are supported by the master profile.
- Confirm the CV is exactly 2 pages, visually clean, and has a readable PDF text layer.
- Present the .tex and PDF only after these checks. Report any verification step that could not be completed; never claim a check passed if it was not run.

INPUTS
1. Master candidate profile: [PROVIDE PROFILE OR FILE]
2. Job description: [PASTE JOB DESCRIPTION]
3. Company: [COMPANY]
4. Role: [ROLE]
5. Output language: [LANGUAGE]
~~~

</details>

<details markdown="1">
<summary><strong>Cover letter creation and tailoring prompt</strong></summary>

~~~text
Follow these rules carefully. Write a role-specific cover letter using the supplied master candidate profile and job description. The profile is the only source of factual claims.

FRAMEWORK
- Template: custom LaTeX document class, cover.cls.
- Fonts: Lato and Raleway, using the supplied font directory: cover_letters/OpenFonts/fonts/.
- Output: cover_letters/cover_<company>_<role>.tex.
- Compile with: xelatex -interaction=nonstopmode cover_<company>_<role>.tex
- Expected result: exactly 1 page. Any other page count must be fixed before presenting the letter.

CONTENT AND TONE
- Target 250-300 words of body text, excluding LaTeX markup. Never exceed one page.
- Open with the role and a specific connection between the candidate's background and the work described in the job posting.
- Select the most relevant experience and achievements. Use concrete examples and supported metrics.
- Explain what the candidate could contribute in this role, not just what they did in the past.
- Include a genuine, company-specific connection to its product, work, mission, or stated priorities. Do not invent company facts.
- Use a natural, professional voice. Avoid clichés, empty enthusiasm, generic claims, and unsupported adjectives.
- Do not repeat the CV bullet for bullet. Use the letter to connect the evidence into a focused case for the role.
- Every factual claim must be supported by the master profile.

STRUCTURE
1. Salutation: use the hiring manager's name when known; otherwise use "Dear <Company> hiring team," or "Dear <Company>,". Do not use "To whom it may concern".
2. Opening paragraph: name the role and explain the strongest connection to the candidate's background.
3. Experience paragraph: introduce the most relevant evidence.
4. Three to five concise bullets describing concrete achievements or skills. Start each bullet with a clear action or bold label.
5. Company-specific paragraph: explain why the candidate's experience is relevant to this company and role.
6. Brief closing and signature.

LATEX STRUCTURE AND KNOWN TEMPLATE RULES
- Use the supplied cover.cls template and its commands: \\namesection, \\currentdate, \\lettercontent, \\closing, and \\signature.
- Keep the bullet list outside a \\lettercontent{} block. The macro appends a line break, which can cause a LaTeX error after an itemize environment.
- Wrap the itemize list in the supplied Raleway-Medium font settings so its typography matches the template.
- Do not put a trailing \\ inside \\closing{} because the class appends its own line break.
- Escape special characters in body text: & as \\&, % as \\%, $ as \\$ , # as \\#, _ as \\_, ~ as \\textasciitilde{}, ^ as \\textasciicircum{}, and backslash as \\textbackslash{}.
- If a bullet begins with a literal [, brace the item text so LaTeX does not interpret it as an optional label.
- Do not use em dashes. Use commas or full stops instead.

COMPILE AND INSPECT
1. Compile with XeLaTeX using the command above.
2. Confirm compilation succeeds and the PDF has exactly 1 page.
3. Visually inspect the page. Check that the signature fits, no text is clipped, spacing is consistent, and bullet font matches the body.
4. Fix any overflow or formatting issue and compile again.

FINAL CHECKLIST
- [ ] Correct company and role throughout.
- [ ] Date is current.
- [ ] Salutation is appropriate.
- [ ] Language matches the job posting.
- [ ] Every claim has evidence in the master profile.
- [ ] No clichés, unsupported claims, or em dashes.
- [ ] Company-specific motivation is accurate.
- [ ] The letter fits on exactly one page, including the signature.
- [ ] Submit only the documents requested by the employer.

INPUTS
1. Master candidate profile: [PROVIDE PROFILE OR FILE]
2. Target CV, if available: [PROVIDE TAILORED CV OR FILE]
3. Job description: [PASTE JOB DESCRIPTION]
4. Company: [COMPANY]
5. Role: [ROLE]
6. Hiring manager name, if known: [NAME OR LEAVE BLANK]
7. Output language: [LANGUAGE]
~~~

</details>

## What I learned

An LLM can produce text quickly. Getting it to produce a relevant, accurate application consistently takes better inputs, clear constraints, and a verification step.

I still review every application. The LLM handles much of the repetitive rewriting and formatting work, while I make the final decisions and check that the documents represent my experience accurately.

**One master profile. A job-specific CV. A tailored cover letter. Generated with an LLM, checked before submission.**
