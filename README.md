# games-collector

## Steps

* Create an initial version of REQUIREMENTS.md
    * No AI used in this part

* Review the initial version of REQUIREMENTS.md. Prompt using Claude desktop app:

```
I want to build an HTTP service using AI. Right now I have a requirements specification (see REQUIREMENTS.md), 
so my first task would be to review those requirements and reduce any ambiguity that they might contain.
Could you please try to understand the requirements and rewrite them to be more formally correct?
Please ask me any question you may have
```

* Create an OpenAPI spec out of the requirements. Prompts using Claude Code Visual Code extension:

```
given the requirements in `REQUIREMENTS_v2.md`, could you create an OpenAPI specification for the HTTP service?
```

```
the API spec will be public, could you remove any reference to internal implementation details (like requirement IDs or how the admin user and password is configured)?
```