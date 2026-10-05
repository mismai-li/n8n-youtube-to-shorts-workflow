# API migration acceptance checklist

Use this checklist when adapting the template to a provider API or upgrading an existing deployment. It is based on the request bodies and routing in revision `7cd32c2`; all execution results below remain **Not run**.

## Prepare an isolated copy

1. Import a separate copy and leave its trigger inactive until you deliberately start a test.
2. Replace external request/upload destinations with controlled mocks or sandbox endpoints. Keep real upload credentials out of this copy. Mock every side-effecting node on the exercised path, including render creation and YouTube upload.
3. Record the n8n version, workflow revision, test owner, and the intended retry limit/timeout. Use an explicit execution timeout or stop condition while exercising loops.
4. Capture evaluated request bodies and request counts. A green execution alone cannot establish correct item pairing or the number of external writes.

## Checks

| Check | Fixture | Observation to record | Result |
|---|---|---|---|
| Analysis request | A known video ID | `generateShorts` sends that ID under `options.youtubeVideoId`, with the intended `functionName` and `webhook`. Compare with the provider contract you are deploying against. | Not run |
| Render item pairing | Two shorts with distinct IDs and distinct styling | Each evaluated `renderShort` body has its own `shortId` and `renderOptions`. Verify JSON types and styling serialization, including missing styling and string IDs if your provider supports them. | Not run |
| Completed | `{"status":"COMPLETED"}` | `isError ?` takes false, then `iscompleted ?` takes true toward metadata generation. No extra render or status request for that completed item. | Not run |
| Repeated failure | Repeated `{"status":"FAILED"}` | `isError ?` takes true **back to the render POST**. Record how many new requests/jobs occur and verify the chosen stop/retry policy. | Not run |
| Poll then complete | `{"status":"PENDING"}`, then `{"status":"COMPLETED"}` | First response follows the wait/poll path; second exits. Record poll count and delay. `PENDING` is a synthetic nonmatching test value here. | Not run |
| Malformed or legacy status | `{}`, `{"status":null}`, `{"status":"completed"}`, `{"type":"done"}` | Record validation errors or actual branch choices under strict, case-sensitive matching. Verify a defined termination/error outcome rather than endless polling. | Not run |
| Final outputs | Two completed short fixtures and controlled upload responses | Check metadata/file pairing, publication dates, and one intended upload per short. | Not run |

The FAILED route resubmits rendering, while a nonfailed/noncompleted response returns to status polling. Decide whether that is the retry policy you want before accepting the release. This checklist describes static routing; it does not establish observed provider behavior or successful execution.

## Handoff record

- n8n version / workflow revision:
- Test owner / date:
- Retry limit / timeout:
- Request counts and observed outputs:
- Failed checks and remaining decisions:
- Next maintainer:
