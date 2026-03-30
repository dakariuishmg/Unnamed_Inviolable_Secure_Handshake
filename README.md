# Unnamed_Inviolable_Secure_Handshake
A self-contained HTML app enabling secure peer-to-peer file transfer between browser tabs via WebRTC DataChannels. It uses AES-GCM-256 with per-chunk encryption, HKDF-derived keys from a passphrase, a symmetric ratchet for forward secrecy, and 4-channel SCTP striping. Encrypted signaling; no persistent storage.
