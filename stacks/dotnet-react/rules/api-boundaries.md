---
id: api-boundaries
type: rule
title: API boundaries in .NET and React
summary: >-
  How data crosses between an ASP.NET Core API and a React app: generated contract, validated requests,
  parsed responses, ProblemDetails errors, explicit nulls.
layer: stack
stacks: [dotnet, react]
paths: ["**/*.cs", "**/*.ts", "**/*.tsx"]
owner: "[NAME]"
version: 2
status: active
requires: [working-agreement]
---

# API boundaries in .NET and React

## Essentials {#essentials}

- The OpenAPI document is the contract; the React client is generated from it.
- Validate requests on the server. Parse every response on the client before components see it.

## Purpose {#purpose}

Most bugs in a .NET and React system happen where the two meet. This document makes the boundary explicit and checked on both sides.

## Principles {#principles}

1. One contract, generated both ways: the C# DTOs produce the OpenAPI document and Orval generates the TypeScript client from it.
2. Types are claims and parsing is evidence.
3. Null is a value, not an accident.

## Conventions {#conventions}

Name every endpoint, return typed results, and keep DTOs separate from domain and database entities.

## Request validation {#request-validation}

The server checks every request body and parameter before it does any work. Use the built-in DataAnnotations validation of minimal APIs
and return a validation problem with an errors dictionary, so the client can show each field's message. Never trust the client to have checked.

## Response validation {#response-validation}

The client must validate every API response before a component sees it. Parse the response with the generated Zod schema, once, inside a
data-access hook, and let components use only the parsed result. A TypeScript type disappears at runtime, so a response that does not match
the schema fails loudly at the boundary instead of leaking a wrong value into the UI. Do not cast API data with `as`.

## Error handling {#error-handling}

Every error response from the API is ProblemDetails with the same shape, including a trace id. The generated fetcher throws an `ApiError`
that carries it, and forms map the errors dictionary onto their fields.

## Null handling {#null-handling}

A field that can be missing is nullable in the contract and stays nullable in every consumer. Do not hide it behind a default such as `?? 0`;
decide what missing means for the business and handle that case explicitly.

## Exceptions {#exceptions}

Streamed and binary downloads are not parsed with a schema.

## Verification {#verification}

- [ ] Every new request DTO has server-side validation.
- [ ] Every response is parsed in a data-access hook.

## References {#references}

- ASP.NET Core minimal APIs: validation and ProblemDetails.
