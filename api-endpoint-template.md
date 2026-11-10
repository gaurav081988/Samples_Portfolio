<!-- GUIDANCE
Purpose: give accurate, scannable facts about one API operation.
Rules: generate this page from the OpenAPI spec wherever possible, and edit the spec
rather than this file. Describe, don't teach. Link to a how-to for procedures.
Every operation needs a permission, parameters, request, response and all errors.
-->

### <Verb phrase, for example "Generate a data key">

`<METHOD> /<version>/<path>`

<One or two sentences that say what the operation does. Add the condition or side effect that matters most.>

**Required permission:** `<permission>`

**Parameters**

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `<name>` | <type> (<path, query or body>) | <Yes or No> | <What it is. Include allowed values and defaults.> |

**Example request**

```bash
curl -X <METHOD> https://api.example.com/<version>/<path> \
  -H "Authorization: Bearer $TOKEN"
```

**Response: <status code>**

<One sentence that describes a successful result.>

```json
{
  "<field>": "<example value>"
}
```

**Errors**

| HTTP status | Error code | Meaning and action |
| --- | --- | --- |
| <400> | `<error_code>` | <Why it happens, and what the reader should do.> |

**Related**

- <How-to guide that uses this operation.>
