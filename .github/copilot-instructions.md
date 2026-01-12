# Development Instructions for StrongSwan

**LANGUAGE: C** - Pure C for maximum portability and OpenSSL 3.5 integration.

**VERSIONING: Follow major.minor.patch semantic versioning strictly, for every binary.  Every binary must have its own version.  Every binary that is used as a systemd process must output its version on startup.**

***NO SUMMARY DOCUMENTS WITHOUT EXPLICIT INSTRUCTION***
***NO SUMMARY DOCUMENTS WITHOUT EXPLICIT INSTRUCTION***
***NO SUMMARY DOCUMENTS WITHOUT EXPLICIT INSTRUCTION***
***NO SUMMARY DOCUMENTS WITHOUT EXPLICIT INSTRUCTION***
***NO SUMMARY DOCUMENTS WITHOUT EXPLICIT INSTRUCTION***

---

## ⚠️ MANDATORY: PROJECT PHASES TRACKING ⚠️

***THIS IS NON-NEGOTIABLE. FAILURE TO COMPLY WILL RESULT IN WASTED EFFORT AND PROJECT CHAOS.***

### BEFORE EVERY SINGLE RESPONSE, YOU MUST:

**1. OUTPUT THE FOLLOWING HEADER AT THE START OF EVERY RESPONSE:**

```
══════════════════════════════════════════
🤖 MODEL: [Model name and version]
📊 CONTEXT: [Context window size, e.g., 200K tokens]
══════════════════════════════════════════
```

**2. CONSULT THE PROJECT PHASES DIRECTORY:**

The `Project Phases/` directory is the **SINGLE SOURCE OF TRUTH** for all development work. These files contain:
- Detailed task breakdowns for each phase
- Acceptance criteria that MUST be met
- Dependencies between components
- Quality gates that MUST pass before proceeding


### ABSOLUTE RULES FOR PHASE-BASED DEVELOPMENT:

1. **NEVER** start work on a later phase until all prior phases are complete
2. **NEVER** skip tasks within a phase - complete them in order
3. **NEVER** assume a task is complete - verify against acceptance criteria
4. **ALWAYS** check off completed tasks in the phase documents
5. **ALWAYS** run required tests before marking tasks complete
6. **ALWAYS** cite the specific phase/task group/task you are working on

### IF YOU DO NOT KNOW WHICH PHASE/TASK YOU ARE ON:

**STOP. DO NOT PROCEED.** Ask the user to clarify:
- What phase are we currently in?
- What task group within that phase?
- What specific task needs to be completed?

Development without phase tracking is **PROHIBITED**.


## IMPORTANT RULES

1. **Never create summary documents** without explicit instruction. Document changes only in `tmp/update-summary.md`.
2. **Refer to README.md and docs/architecture.md** before platform-specific development.
3. **All temporary files** (to-do lists, notes, debug scripts) go in `/tmp/` and are never committed.
4. **No netlink/kernel driver usage** - all MACsec processing is userspace via raw sockets.



## Cryptographic Requirements (CNSA 2.0)

### Mandatory Algorithms
| Algorithm   | Requirement  | Implementation             |
|-------------|--------------|----------------------------|
| AES-256-GCM | CNSA 1.0/2.0 | Only cipher suite (0x0080) |
| SHA-384     | CNSA 1.0/2.0 | All hashing, HMAC, KDF     |
| P-384 ECDH  | CNSA 2.0     | Key agreement              |
| ML-KEM      | CNSA 2.0     | Future (Phase 3+)          |



## Testing Requirements

### Unit Tests
- **Coverage**: 90% line, 80% branch minimum
- **Crypto**: NIST CAVP vectors (AES-256-GCM, SHA-384)
- **Frames**: IEEE 802.1AE test vectors
- **Protocol**: MKA/EAPOL state machine transitions

### Integration Tests
- Client-gateway session establishment
- Multi-client concurrent (100+ sessions)
- Rekey under traffic
- Timeout and recovery

### Negative Tests
- Malformed frames (truncated, invalid fields)
- Replay attacks (duplicate PN)
- ICV failures (bit flips)
- Resource exhaustion
- Invalid certificates

---

### Review Checklist
- [ ] Buffer bounds validated
- [ ] All error paths handle cleanup
- [ ] Keys cleared before free
- [ ] No prohibited functions
- [ ] Input validation at trust boundaries

---

## Version Control

- Commit messages: Conventional commits format
- Merge to main only after:
  - All tests pass
  - Static analysis clean
  - Code review approved

---

## References

- NSA CNSA 1.0/2.0: Cryptographic requirements
- NIST SSDF v1.1: Secure development framework
- FIPS 140-3: Cryptographic module requirements
