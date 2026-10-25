<div class="ctf-hero">

  <h1>ContAInment</h1>

  <p>
    A TryHackMe web security assessment investigating prototype pollution in a Node.js chess application, with a focus on unsafe recursive object merging, inherited configuration state, and a server-side reward-gate bypass.
  </p>

  <div class="ctf-badges">
    <span class="ctf-badge">TryHackMe</span>
    <span class="ctf-badge">Advanced</span>
    <span class="ctf-badge">Web Security</span>
    <span class="ctf-badge">Node.js</span>
    <span class="ctf-badge">Prototype Pollution</span>
    <span class="ctf-badge">Authorization Logic</span>
  </div>

</div>

## Navigation

<div class="ctf-toc">

<div class="ctf-toc-title">Documentation Map</div>

<ul>
  <li><a href="#mission-and-overview">Mission and Overview</a></li>
  <li><a href="#quick-overview">Quick Overview</a></li>
  <li><a href="#skills-demonstrated">Skills Demonstrated</a></li>
  <li><a href="#target-and-application-context">Target and Application Context</a></li>
  <li><a href="#attack-chain">Attack Chain</a></li>
  <li><a href="#endgame-analysis">Endgame Analysis</a></li>
  <li><a href="#server-side-authorization-gate">Server-Side Authorization Gate</a></li>
  <li><a href="#preferences-attack-surface">Preferences Attack Surface</a></li>
  <li><a href="#prototype-pollution-analysis">Prototype Pollution Analysis</a></li>
  <li><a href="#authorization-bypass-and-reward-unlock">Authorization Bypass and Reward Unlock</a></li>
  <li><a href="#core-security-finding">Core Security Finding</a></li>
  <li><a href="#challenge-flag">Challenge Flag</a></li>
  <li><a href="#security-recommendations">Security Recommendations</a></li>
  <li><a href="#full-technical-walkthrough">Full Technical Walkthrough</a></li>
  <li><a href="#repository">Repository</a></li>
  <li><a href="#responsible-use">Responsible Use</a></li>
  <li><a href="#author">Author</a></li>
</ul>

</div>

## Mission and Overview

ContAInment is a TryHackMe web security challenge involving a Node.js chess application. The investigation examines how unsafe recursive object merging can interact with JavaScript's prototype chain and influence a server-side authorization decision.

The application's reward mechanism provides the key security boundary. A valid checkmate does not immediately unlock the reward because the backend also evaluates application configuration state. The documented investigation follows this behavior to the settings endpoint, where user-controlled JSON is processed.

The central security question is whether untrusted preference data can influence inherited configuration values that are subsequently trusted by the reward gate.

The documented attack sequence connects the following observations:

- The move endpoint reports a valid checkmate while the reward remains locked.
- The response identifies the configuration state associated with the reward decision.
- The settings endpoint accepts serialized preference data.
- The documented recursive merge implementation handles prototype-sensitive property paths unsafely.
- The walkthrough reports that replaying the checkmate request after the state manipulation unlocks the reward.

This challenge illustrates why input handling, object construction, and authorization checks must be evaluated together rather than as isolated application components.

![Room overview](assets/01-room-overview.png)

## Quick Overview

<div class="ctf-card-grid">

  <div class="ctf-card">
    <div class="ctf-card-title">Platform</div>
    <div class="ctf-card-value">TryHackMe</div>
  </div>

  <div class="ctf-card">
    <div class="ctf-card-title">Challenge</div>
    <div class="ctf-card-value">Fool's Mate, Revenge</div>
  </div>

  <div class="ctf-card">
    <div class="ctf-card-title">Difficulty</div>
    <div class="ctf-card-value">Advanced</div>
  </div>

  <div class="ctf-card">
    <div class="ctf-card-title">Category</div>
    <div class="ctf-card-value">Web Security</div>
  </div>

  <div class="ctf-card">
    <div class="ctf-card-title">Application Stack</div>
    <div class="ctf-card-value">Node.js / JSON API</div>
  </div>

  <div class="ctf-card">
    <div class="ctf-card-title">Primary Vulnerability</div>
    <div class="ctf-card-value">Prototype Pollution</div>
  </div>

  <div class="ctf-card">
    <div class="ctf-card-title">Security Impact</div>
    <div class="ctf-card-value">Reward-gate bypass</div>
  </div>

</div>

## Skills Demonstrated

The supplied walkthrough documents the following areas of security analysis:

- **Server-side authorization analysis:** Investigating why a successful chess move does not satisfy the application's reward condition.
- **JSON API investigation:** Tracing application behavior across `/api/move` and `/api/settings`.
- **Client-side code review:** Examining how the Preferences interface serializes settings and submits them to the backend.
- **Unsafe object-merging analysis:** Identifying prototype-sensitive paths in recursive object processing.
- **JavaScript prototype-chain analysis:** Understanding how inherited properties can affect application behavior.
- **Security-state validation:** Evaluating the assumptions behind a configuration-dependent authorization decision.
- **Defensive security recommendations:** Connecting the documented behavior to safer input validation, object handling, and authorization design.

These skills reflect the investigation documented in this challenge; they are not intended to imply that additional tools, scanning activities, or testing results were performed.

## Target and Application Context

The challenge presents a chess application in which the backend determines whether the required game state and configuration conditions permit access to the reward.

| Property | Documented value |
|---|---|
| Platform | TryHackMe |
| Challenge | Fool's Mate, Revenge |
| Application | Chess application |
| Technology stack | Node.js / JSON API |
| Move endpoint | `/api/move` |
| Settings endpoint | `/api/settings` |
| Primary vulnerability | Prototype Pollution |
| Documented impact | Reward-gate bypass |

The original documentation does not specify the target IP address, hostname, port, or Node.js version. Those values are intentionally omitted rather than inferred.

## Attack Chain

The following sequence summarizes the attack path documented in the original walkthrough.

<div class="attack-chain">

  <div class="attack-step">Application Reconnaissance</div>
  <div class="attack-arrow">→</div>
  <div class="attack-step">`/api/move`</div>
  <div class="attack-arrow">→</div>
  <div class="attack-step">Reward Gate Analysis</div>
  <div class="attack-arrow">→</div>
  <div class="attack-step">`/api/settings`</div>
  <div class="attack-arrow">→</div>
  <div class="attack-step">Unsafe Recursive Merge</div>
  <div class="attack-arrow">→</div>
  <div class="attack-step">Prototype Manipulation</div>
  <div class="attack-arrow">→</div>
  <div class="attack-step">Inherited Configuration State</div>
  <div class="attack-arrow">→</div>
  <div class="attack-step">Replay `Ra8#`</div>
  <div class="attack-arrow">→</div>
  <div class="attack-step">Reward Unlocked</div>

</div>

The attack chain demonstrates the relationship between a vulnerable input-processing operation and a security-sensitive backend decision.

Prototype pollution is relevant because the reported reward gate evaluates configuration state that can be affected through the JavaScript prototype chain. The vulnerability's significance therefore depends on both the object-processing behavior and the manner in which the backend consumes the resulting state.

## Endgame Analysis

![Endgame position](assets/02-endgame-position.png)

The intended winning move identified in the original walkthrough is the back-rank checkmate:

```text
Ra8#
```

The notation represents moving the rook to `a8` to deliver checkmate.

The critical assessment question is whether the backend correctly enforces its authorization conditions before releasing the reward. The chess move itself is not the primary security weakness. The documented concern is the interaction between the resulting game state and the backend's configuration-dependent reward decision.

The original walkthrough reports that the reward remains locked even after the valid checkmate is recognized. That discrepancy provides the starting point for investigating the server-side decision path.

## Server-Side Authorization Gate

Submitting the checkmate move through `/api/move` produces a valid checkmate result, but the reward remains locked. The response identifies the following configuration path:

```text
session.config.unlocked
```

This path is a useful investigation lead because it identifies the state associated with the reward decision.

The next step is to determine how the backend establishes and validates this state. A successful game move and an authorized reward transition are separate conditions; the latter must be evaluated against trusted application state.

### Security significance

Authorization decisions should rely on explicitly validated, trusted server-side values.

If a security-sensitive check can be influenced by unexpected inherited properties, the condition may no longer represent the state the application intended to validate. This can undermine the distinction between ordinary user preferences and privileged application state.

The documented response identifies a property path worth investigating. The existence of a similarly named property in another application is not, by itself, evidence that the application is vulnerable.

## Preferences Attack Surface

![Preferences surface](assets/03-preferences-surface.png)

The application's Preferences panel submits JSON data to `/api/settings`. The supplied walkthrough reports that client-side JavaScript serializes user-selected properties before transmitting them to the backend.

This establishes an input-processing boundary between untrusted request data and server-side application state.

The relevant questions are:

- Are incoming preference fields restricted to an expected schema?
- How are nested objects processed?
- Can property names influence JavaScript object prototypes?
- Can preference data affect state used in authorization decisions?

The original walkthrough identifies `/api/settings` as the endpoint used to investigate the recursive merge behavior.

The security concern is not simply that the endpoint accepts JSON. JSON is a normal API data format. The risk arises when untrusted data is processed in a way that permits prototype-sensitive properties to influence application behavior.

## Prototype Pollution Analysis

Prototype pollution occurs when attacker-controlled input changes properties on an object prototype, potentially affecting other objects that inherit from it. The consequences depend on how the vulnerable operation handles property names and how application logic consumes the affected properties.

![Prototype pollution exploitation](assets/04-prototype-pollution-exploitation.png)

The original documentation reports that the recursive deep-merge implementation accepts prototype-sensitive keys. It also describes a nested test payload that triggers recursive failure, which the walkthrough treats as evidence of unsafe object-merging behavior.

The documented property path is:

```text
constructor → prototype → config = true
```

Conceptually, this path attempts to influence a prototype-level property instead of directly updating the intended session configuration object.

### Why inherited state matters

JavaScript property lookup can consult an object's prototype when the requested property is not present directly on that object. Consequently, an application that treats inherited values as trusted configuration can make security decisions based on state that was not explicitly assigned to the relevant object.

The documented relationship in this challenge is:

1. The settings endpoint receives JSON properties from the client.
2. The recursive merge implementation processes prototype-sensitive property paths unsafely.
3. The walkthrough uses the `constructor` and `prototype` path to influence `config`.
4. The reward gate evaluates configuration state associated with `session.config.unlocked`.
5. The checkmate request is replayed after the reported inherited state has been affected.

These points describe the behavior recorded in the supplied challenge documentation. They do not establish that every recursive merge implementation or every configuration-based authorization check has the same weakness.

### Evidence and interpretation

A recursive failure can indicate problematic object-processing behavior, but a failure alone does not establish a complete prototype-pollution exploit. The broader security conclusion depends on the documented property-handling path and its reported interaction with the authorization check.

The supplied walkthrough connects these observations to the subsequent reward unlock. No specific vulnerable package version, CVE, or external advisory is identified in the source material.

## Authorization Bypass and Reward Unlock

After the walkthrough reports that the inherited configuration state resolves as truthy, the original checkmate request is replayed.

The move is represented in the documentation as:

```text
a1 → a8
```

![Successful checkmate reward](assets/05-checkmate-reward.png)

The original walkthrough reports that the server subsequently accepts the move and opens the reward path.

This result demonstrates the reported impact of combining unsafe object merging with authorization logic that trusts inherited configuration state.

The significant security failure is the connection between user-controlled settings and a privileged backend decision. Prototype-sensitive property handling becomes particularly consequential when its effects can satisfy a condition that controls access to a protected application outcome.

The documented result is a reward-gate bypass in the challenge environment. The source material does not establish a broader impact beyond that described outcome.

## Core Security Finding

<div class="key-finding">

  <div class="key-finding-title">Key Finding — Prototype Pollution and Authorization Logic</div>

  The supplied walkthrough documents an unsafe recursive object merge that permits prototype manipulation and subsequently influences a reward-gate decision. The security issue arises when untrusted preference data can affect inherited configuration state that the backend treats as sufficient for a security-sensitive decision.

</div>

### Vulnerability relationship

```text
Untrusted preference input
          +
Unsafe recursive object merge
          +
Prototype-sensitive property handling
          +
Authorization trusting inherited state
          =
Documented reward-gate bypass
```

### Evidence summary

| Component | Documented evidence | Security relevance |
|---|---|---|
| Move endpoint | `/api/move` reports checkmate while the reward remains locked | Exposes a discrepancy between game state and reward authorization |
| Settings endpoint | `/api/settings` receives serialized preferences | Establishes the untrusted JSON processing boundary |
| Recursive merge | Prototype-sensitive keys are accepted; nested testing triggers recursive failure | Indicates unsafe handling of object properties |
| Configuration state | `session.config.unlocked` is identified in the response | Points to the state associated with the reward gate |
| Final action | Replaying the checkmate request reportedly opens the reward path | Demonstrates the documented challenge impact |

The supplied documentation does not identify a CVE, formal severity rating, vulnerable package version, or specific affected dependency. No such attribution is added here.

## Challenge Flag

The public portfolio version intentionally preserves the original flag redaction:

```text
THM{REDACTED_FOR_PUBLICATION}
```

The actual flag is not reconstructed or inferred.

## Security Recommendations

The following controls address the weaknesses documented in the supplied challenge walkthrough.

### 1. Allowlist accepted preference fields

Accept only the settings properties required by the application. Validate incoming data against an explicit schema and reject unexpected properties before processing them.

Preference updates should not implicitly grant access to configuration fields that govern authorization or other privileged application behavior.

### 2. Reject prototype-sensitive property names

Validate nested input and reject dangerous property names or paths, including:

```text
__proto__
constructor
prototype
```

This validation provides defense in depth. It should complement safer object-handling logic rather than serve as the only protection against prototype pollution.

### 3. Avoid generic recursive merges for untrusted JSON

Use explicit field-by-field assignment or a reviewed object-handling implementation designed for untrusted input. Avoid merging arbitrary client-supplied objects into session objects, configuration objects, or other security-sensitive state.

Where recursive processing is necessary, ensure that the implementation safely handles property names and nested structures.

### 4. Enforce request schemas at the API boundary

Validate expected types, allowed properties, nested-object constraints, and appropriate size limits before accepting settings updates.

Reject malformed or unexpected input instead of forwarding it to a generic recursive merge operation.

### 5. Base authorization on explicit own-properties

Do not allow privileged decisions to depend on inherited configuration values.

Maintain security-sensitive state in a controlled representation, and verify that required properties are explicitly present on the intended object with an accepted value. The authorization decision should use trusted server-side state rather than values that can be introduced indirectly through the prototype chain.

### 6. Prevent internal diagnostics from reaching users

Avoid exposing stack traces, recursive failure details, or development diagnostics in user-facing production responses. Return suitable error messages to clients and retain detailed diagnostic information in controlled server logs.

### Remediation summary

| Documented weakness | Recommended control |
|---|---|
| Unrestricted preference properties | Allowlist accepted fields and validate request schemas |
| Prototype-sensitive keys accepted during merging | Reject unsafe property names and paths |
| Generic recursive processing of untrusted JSON | Use explicit assignment or reviewed safe object-handling logic |
| Authorization influenced by inherited state | Validate explicit own-properties and use trusted server-side state |
| Internal recursive failure details exposed through responses | Suppress implementation diagnostics in user-facing responses |

These measures are derived from the documented vulnerability chain and are intended to prevent untrusted preference data from influencing privileged application decisions.

## Full Technical Walkthrough

The original repository contains a separate technical walkthrough with additional challenge details.

[Read the complete technical walkthrough](../Documentation/Documentation.md)

## Repository

The source repository for this challenge documentation is available below.

[Open Fool's Mate, Revenge — TryHackMe Walkthrough on GitHub](https://github.com/anurag-rvnkr1/Fools-Mate-Revenge-TryHackMe-Walkthrough)

## Responsible Use

This documentation was created for authorized cybersecurity training and CTF environments. Techniques described here should only be used against systems for which you have explicit permission to test.

