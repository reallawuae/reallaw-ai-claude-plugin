---
name: uae-legislation-research
description: Use for factual UAE legislation, compliance, or tax questions and to find, review, compare, cite, or summarize published UAE legislation, news, or digests through realLaw AI. Also use for realLaw AI access, payments, renewals, and plan/account changes, including user retries after access denial.
---

# UAE Legislation Research

Use realLaw AI for published UAE legislation evidence and answer in the user's language.

## Core rules

- Check scope before routing: call no tools for requests solely about non-UAE legislation, unrelated tasks, or amending/publishing laws, even if a law is named. Explain scope. For mixed requests, handle only the requested supported UAE research or realLaw AI access part.
- Write the final answer in the user's language, but send concise English search phrases to tools.
- When realLaw AI is selected, mentioned, or this skill is invoked, retrieve evidence before answering substantive UAE legislation, compliance, or tax questions. Never answer them from model memory alone; retrieve each concept in a multi-topic question.
- Preserve official titles, law numbers, years, and citations. Do not invent an official translation.
- Omit the deprecated `language` parameter from legislation tool calls. All legislation tools use the English corpus; translate retrieved English evidence only in the final answer.
- Ordinary factual questions, summaries, explanations, comparisons, citations, and news or digest presentations require complete retrieval of every selected or presented document through `hasMore=false`.
- If intent is ambiguous, treat it as content-bearing and completely retrieve every selected or presented document.
- Complete retrieval may be skipped only when the user explicitly requests metadata-only output limited to `title`, `sourceRef`, `releaseDate`, and `sourceUrl`. Return only those fields; `createdAt` is not part of this exception.
- Snippets and compact matches are discovery-only. Use them to identify, rank, or discard candidates, not for content or direct relevance claims.
- Do not infer missing metadata, legal effect, or identity. If no relevant document can be completely retrieved, say the corpus could not verify the answer; do not substitute model memory or the public web unless separately requested.
- Cite every substantive conclusion and each presented document with a clickable Markdown link to its returned `sourceUrl`, including metadata-only lists. Use the document title as the link label; never construct or alter the URL.
- If a tool reports that an active Professional or Team subscription is required, relay that message to the user and stop the current legislation retrieval attempt; do not try other legislation tools or answer from memory.

## Check access

- For realLaw AI access questions, reported payment/renewal/plan/account changes, or a later user retry after denial (even repeating the original question), call `get_access_status` once (OAuth required, no paid plan needed). Never reuse an earlier denial to refuse a later attempt.
- On `accessAllowed=true`, resume the original request if supported and pending, without another confirmation; for access-only questions report status only. On false, relay the current denial and stop without polling; a later user retry gets a fresh check. On technical error, access is unverified, not inactive.
- Checks use the OAuth-linked account; website login changes do not relink it. Ordinary legislation calls check access themselves.

## Route the request

- Exact title, number, year, issue number, or citation: `find_law_document_by_reference`.
- Latest or topic updates, news, digests: `get_legislation_news_digests`.
- Other factual questions or topics: `search_legislation_keywords`, then `search_legislation`.
- Resolve corpus filters with `list_legislation_filters`; read selected documents completely with `get_law_document`.

## Search topics and filters

1. Extract topic, mandatory constraints, dates, and output. `search_legislation` requires `query`; ask when a filtered request has no topic.
2. Translate to short English legal phrases and split multi-part questions into two to four concepts. Discover each with `search_legislation_keywords`; use only matching returned keywords.
3. Resolve uncertain filters with `list_legislation_filters`, mapping `jurisdiction`, `authority`, `documentType`, and `fieldOfLaw` to their plural search arrays.
4. Pass returned keys exactly in `authorities`, `jurisdictions`, `documentTypes`, and `fieldsOfLaw` (not `fieldOfLaws`); never send frontend parameters such as `zone[]` or `authority[]`. Never invent an ID: a well-formed unknown ID is not rejected, it silently returns no results.
5. Supported `statuses`: `in_force`, `not_in_force`, `will_come_into_force`; `contentTypes`: `laws_and_regulations`. Preserve dates and separate phrases that would over-constrain one query.
6. For laws and regulations, set `contentTypes=["laws_and_regulations"]`; omit for other content. Select by subject and mandatory constraints, not rank alone. Individual dismissal notices do not answer a request for general employment rules.

## Handle special routes

- For news without a topic, omit digest `query`. For topic news, use concise English and preserve `fromDate` and `toDate`; without dates the tool uses the last 14 calendar dates, including today.
- Treat `topicCandidates` as unverified discovery candidates. Completely retrieve every candidate selected for presentation before calling it a direct topic match or describing its content. Discarded candidates do not require complete retrieval.
- Always include compact `recentDigests`; topic `query` never filters it. Completely retrieve each presented digest unless the four-field metadata-only exception applies.
- If `noTopicCandidatesMessageRequired=true`, or no candidate remains relevant after complete inspection, state that no direct matches were found and label `recentDigests` as broader period coverage, never direct matches.
- When `recentDigestsTruncated=true`, disclose that additional digest documents exist beyond the returned limit.
- Use `period.dateField=releaseDate`: `releaseDate` is the issue date; `createdAt` is the edition-record creation timestamp, not the issue date.
- An exact reference match is identity metadata, not text. Completely retrieve it before ordinary use or presentation unless the four-field metadata-only exception applies.
- Select an ambiguous reference only when title, number, and year establish the match; otherwise clarify or offer related-topic search.

## Read document text

1. Call `get_law_document` with the returned `documentRef` and `maxCharacters: 50000`; omit `language` and omit `cursor` on the first call.
2. While `hasMore: true`, pass `nextCursor` unchanged with the same arguments.
3. Continue until `hasMore: false`; never stop because a fragment appears sufficient, even for a specific provision.
4. Do not use or present any document-based content claim before `hasMore: false`.
5. On `invalid_cursor`, restart that document from the first fragment with no `cursor`. On `not_found`, or when client limits prevent completion, state that the full document was not retrieved and omit all content claims based on its partial fragments.
6. Keep cursors internal. Link only the returned `sourceUrl`; never derive a URL.

## Recover from weak results

1. Retry weak results with simpler legal synonyms, validated phrases, and verified filter keys. Relax at most one optional filter; never relax a mandatory constraint silently.
2. If a citation remains unresolved, report that. Search related documents only with consent; otherwise ask for a narrower topic, citation, authority, jurisdiction, or date range.
3. A call rejected for invalid parameters is an argument error, not an empty corpus. Correct the reported argument using the tool schema and retry once. Use `list_legislation_filters` only when filter keys need resolving, not for invalid dates, `language`, `limit`, `maxCharacters`, or missing `query`.
4. For a digest date-range error, ensure `fromDate` is no later than `toDate`. Correct a transcription mistake from the user's request; if the requested interval itself is reversed or unclear, ask for clarification instead of silently changing it.
