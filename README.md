# Unnamed_Inviolable_Secure_Handshake
A self-contained HTML app enabling secure peer-to-peer file transfer between browser tabs via WebRTC DataChannels. It uses AES-GCM-256 with per-chunk encryption, HKDF-derived keys from a passphrase, a symmetric ratchet for forward secrecy, and 4-channel SCTP striping. Encrypted signaling; no persistent storage.

UISH  ·  Unnamed Inviolable Secure Handshake
─────────────────────────────────────────────
A single HTML file. Open it. Transfer files.
Nothing persists. Nothing phones home.

HOW TO USE
──────────
1. Both parties open uish.html in a browser
2. Sender clicks SEND tab, generates or enters a passphrase
3. Sender clicks CREATE SESSION → copies the encrypted offer
4. Sender transmits the offer blob via any channel (Signal, email, chat)
5. Receiver clicks RECEIVE tab, enters the same passphrase
6. Receiver pastes the offer → copies the encrypted answer back
7. Sender pastes the answer → CONNECT
8. Both verbally confirm the session fingerprint matches
9. Sender drops a file. Transfer begins. Receiver saves.
10. WIPE when done.

SECURITY MODEL
──────────────
Keys derived from passphrase via HKDF-SHA256 with domain separation.
SDP offer/answer encrypted with signaling key — safe on any channel.
Per-chunk AES-GCM-256 with symmetric ratchet: K_{n+1} = SHA256(K_n ∥ ct).
Hash-chain AAD: each chunk authenticated against its position in sequence.
Session fingerprint allows manual MITM verification.
All sensitive Uint8Arrays zeroed explicitly before GC can see them.

ARCHITECTURE
────────────
Transport  · WebRTC DataChannel (4 streams, negotiated:true, SCTP)
Crypto     · WebCrypto API (AES-GCM-256, HKDF-SHA256)
Reading    · BYOB stream (zero-copy where available, fallback safe)
Receiving  · Pre-allocated Uint8Array slab (one allocation for entire file)
Backpressure · Event-driven via bufferedamountlow (no polling)
Pipeline   · Lookahead encryption (N+1 encrypted while N is in flight)
Signaling  · Manual encrypted blob exchange (no relay server)

REQUIREMENTS
────────────
A modern browser (Chrome 111+, Firefox 113+, Safari 16.4+)
Network that allows WebRTC (most do; corporate symmetric NAT may fail)
Internet access for Google Fonts (cosmetic only — works without them)

SETTINGS
────────
Gear icon → slide-in panel
· 8 themes (7 standard + SOVEREIGN, unlocked by typing 'dakari')
· 3 layouts: INSTRUMENT / MINIMAL / COMPACT
· 3 typefaces: Fira Code / JetBrains Mono / Courier Prime
· Effect toggles: grid, vignette, glow
· Density slider: COMPACT / BALANCED / SPACIOUS
Settings survive within the session only.

THREAT MODEL
────────────
Protected against: passive surveillance, server seizure,
  relay visibility, SDP interception, post-facto forensics
Not protected against: compromised endpoint, keylogger,
  screen capture, symmetric NAT failure (silent drop)
