# Vaultline Documentation Style Guide

*Sample documentation by Kumar Gaurav. Vaultline is a fictional product created for portfolio purposes.*

| Field | Value |
| --- | --- |
| Version | 1.0 |
| Owner | Documentation lead |
| Applies to | All Vaultline developer documentation, release notes and in-product text |
| Spelling reference | Macquarie Dictionary (Australian English). Use the Vaultline glossary for product terms. |

## How to use this guide

This guide helps writers and reviewers make consistent choices quickly. It is short on purpose. When a rule doesn't cover your case, choose the option that is clearest for a developer who is busy and reading in a second language, and then tell the documentation lead so we can add a rule.

**Principles**

1. **Help readers finish a task.** Start with what they want to do, not with how the product is built.
2. **Be accurate before being clever.** Test every step and run every code sample.
3. **Be brief and plain.** Use the simplest word that is correct.
4. **Be consistent.** Use the same word for the same thing, every time.

## Choose the right page type

Each page does one job. Pick a template from the table, and don't mix purposes on one page.

| If the reader wants to... | Write a... | Template |
| --- | --- | --- |
| Learn by following a guided exercise | Tutorial | [tutorial-template.md](templates/tutorial-template.md) |
| Complete a specific task | How-to guide | [how-to-template.md](templates/how-to-template.md) |
| Look up a fact, such as a parameter | Reference | [api-endpoint-template.md](templates/api-endpoint-template.md) |
| Fix a problem | Troubleshooting entry | [troubleshooting-template.md](templates/troubleshooting-template.md) |
| Learn what changed in a release | Release notes | [release-notes-template.md](templates/release-notes-template.md) |

## Voice and tone

Write in a **clear, direct and respectful** voice. We are experienced colleagues explaining something, not a salesperson or a lecturer.

- Address the reader as **you**. Use **we** only for the Vaultline team.
- Use the **active voice**. Write "Vaultline encrypts the key", not "The key is encrypted by Vaultline".
- Use the **present tense** for how the product works. Use the future tense only for things that haven't happened.
- Don't use marketing language. Avoid *powerful*, *seamless*, *robust*, *easy* and *simply*. If something is easy, the steps will show it.
- Don't blame the reader. Write "The token has expired", not "You used an expired token".
- Keep a calm tone for security warnings. State the risk and the action, without alarm.

| Instead of | Write |
| --- | --- |
| Simply paste your token into the config file. | Add your token to the `VAULTLINE_TOKEN` environment variable. |
| Our robust platform seamlessly encrypts your data. | Vaultline encrypts your data with AES-256-GCM. |
| The request must be made by you with a valid token. | Send a valid token with the request. |

## Language and grammar

- **Use Australian English.** Write *organisation*, *authorise* and *behaviour*. Exception: use the exact spelling that appears in code, UI labels and API names.
- **Keep sentences short.** Aim for 25 words or fewer. Put one idea in each sentence.
- **Keep paragraphs short.** Use three or four sentences at most.
- **Put conditions first.** Write "If the key is disabled, enable it", not "Enable the key if it is disabled".
- **Use sentence case for headings.** Capitalise only the first word and proper nouns.
- **Use the serial comma only when it prevents confusion.** Be consistent within a page.
- **Write dates as** *31 March 2027*. Use ISO format (`2027-03-31`) only in code and data.
- **Write numbers** as numerals for 10 and above, and as words for one to nine, except with units (3 days, 5 MB), steps and versions.
- **Don't use Latin abbreviations** such as *e.g.*, *i.e.* and *etc.* Write *for example* and *that is*.
- **Spell out acronyms** on first use on each page, for example "hardware security module (HSM)".
- **Use plain verbs.** Write *use* instead of *utilise*, and *start* instead of *initiate*.

## Inclusive and accessible writing

- Use **allowlist** and **blocklist**, **primary** and **replica**, and **main** for the default branch.
- Don't use idioms, sports metaphors or humour that depends on culture.
- Use *they* for a person whose gender is unknown.
- Describe images in alt text. State what the image shows and why it matters.
- Don't rely on colour alone to carry meaning.
- Write link text that makes sense out of context. Write "read the [error code reference]", not "click [here]".
- Use real headings, not bold text, to create structure, so screen readers can navigate.

## Formatting conventions

| Element | Rule |
| --- | --- |
| Page title | One H1 per page. Use a task-based phrase that starts with a verb for tutorials and how-to pages (for example, "Rotate a master key"). |
| Headings | Use H2 and H3 only below the H1. Don't skip levels. |
| Procedures | Use a numbered list. Put one action in each step and start with a verb. |
| Unordered lists | Use for items with no order. Make items parallel. |
| UI labels | Write in **bold**, exactly as they appear in the product. |
| Code, commands and file names | Write in `code font`. |
| Emphasis | Use **bold** sparingly. Don't use italics for emphasis in procedures. |
| Tables | Use for comparisons and reference data. Always add a header row. |
| Notes | Use a blockquote with a bold label (see the next section). |

### Notes, warnings and tips

Use only these three labels, and use them rarely. If every page has several notes, readers stop reading them.

> **Note:** Extra information that helps but isn't essential.

> **Warning:** An action that could cause data loss, a security risk or an outage. Put the warning before the step it applies to.

> **Tip:** An optional shortcut or best practice.

## Procedures

1. Start each step with a verb: *Select*, *Run*, *Enter*.
2. Put one action in each step. Split steps that contain "and then".
3. State the result when it isn't obvious. For example, "The page shows a green **Active** label."
4. Put the location before the action. Write "In the **Keys** page, select **Rotate**".
5. Keep a procedure to ten steps or fewer. Split longer ones into stages.

## Code samples

- **Test every sample** before you publish. Run it against the current version of the product.
- **Make samples complete enough to run.** Show imports and the variables that the code needs.
- **Use placeholders** that are obvious and consistent: `<YOUR_KEY_ID>` for values the reader replaces. Never use a real secret, token or account number.
- **Read secrets from the environment.** Don't show a hard-coded token in any sample.
- **Add a comment** only to explain why, not what.
- **Show the expected output** after commands and API calls.
- **State the language and version** the sample needs (for example, Python 3.9 or later).
- **Use the same example names** across the documentation: master key `payments-prod`, file `invoice.pdf`.

## API reference conventions

- Generate reference pages from the OpenAPI spec. Edit the spec, not the generated file.
- Write each operation summary as a verb phrase: "Generate a data key".
- Start each description with what the operation does, then give the conditions that matter.
- Always include the required permission, a request example, a response example and every possible error.
- Name each parameter in `code font`, and give its type, whether it is required and its allowed values.

## Error messages and in-product text

An error message should say **what happened**, **why** (if known) and **what to do**.

| Weak | Better |
| --- | --- |
| Error 409. | The master key `payments-prod` is disabled. Enable the key, then retry. |
| Invalid request. | `key_spec` must be `AES_128` or `AES_256`. |
| Something went wrong. | The service is temporarily unavailable. Retry in a few seconds. |

Don't use *please*, exclamation marks or *oops*. Don't blame the user.

## Terminology

Use these terms consistently. Don't use the alternatives.

| Use | Don't use | Definition |
| --- | --- | --- |
| master key | root key, CMK, key encryption key | The key that never leaves Vaultline and wraps data keys. |
| data key | DEK, content key | The key you use to encrypt your data locally. |
| wrapped key | encrypted key, sealed key | A data key that is encrypted under a master key. |
| plaintext key | raw key, clear key | A data key in unencrypted form. |
| wrap, unwrap | encrypt, decrypt (when you mean the data key) | Protect or recover a data key under a master key. Use *encrypt* and *decrypt* only for your data. |
| rotate | renew, refresh | Create a new version of a master key. |
| API token | access key, secret, password | The credential that authenticates API requests. |
| key version | key generation, revision | A numbered version of a master key. |
| disable | suspend, deactivate | Stop a master key being used. The key can be enabled again. |

## Review checklist

Check each item before you publish.

**Content**
- [ ] The page has one purpose and uses the right template.
- [ ] The title says what the reader will do or learn.
- [ ] Every step, command and code sample was tested on the current product version.
- [ ] Prerequisites, expected results and next steps are present.

**Language**
- [ ] The voice is active and the sentences are short.
- [ ] Product terms match the terminology table.
- [ ] No marketing language, idioms or Latin abbreviations.

**Format and accessibility**
- [ ] Headings use sentence case and don't skip levels.
- [ ] Links have meaningful text. Images have alt text.
- [ ] Notes and warnings are necessary and placed before the step they apply to.

**Security**
- [ ] No real token, key, password or personal data appears anywhere.
- [ ] Samples read secrets from the environment.

**Governance**
- [ ] The page has an owner and a review date.
- [ ] A subject matter expert has reviewed it for accuracy.
