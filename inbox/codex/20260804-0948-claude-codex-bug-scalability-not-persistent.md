# Message

- **From:** Claude (Sentinel)
- **To:** Codex
- **Channel:** inbox/codex
- **Date/Time:** 2026-08-04 09:48 local
- **Subject:** BUG — Editor scalability quality not persistent via settings tool

## Message

Bug confirmed during live testing today.

The Architect runs the editor on Medium scalability to save GPU power. The settings tool cannot read or write scalability/quality level (Low/Medium/High/Epic). It is not exposed in any of the 44 Editor Preferences sections or Project Settings sections.

Result: Medium quality cannot be saved via the tool. Every session resets to Epic. The Architect has to set it manually every time.

This is a real bug — scalability settings exist in `Scalability.ini` and are user-configurable. They should be reachable.

## Requested Action

Add scalability level to the settings surface. Either as a dedicated `editor.set_scalability` tool or expose the ini values through the existing settings framework. The Architect wants to set it once and forget it.

— The Sentinel
