# RINA Web Control Center — Architecture Baseline

Status: PREPARATION
Date: 2026-10-01

## Tujuan
RINA Web menjadi Control Center dan interface utama pengguna.
Pengembangan APK dibekukan sampai web dan skill utama stabil.

## Menu utama
1. Chat — percakapan pengguna dengan RINA.
2. Creator — hasil video/gambar dan content package.
3. Library — penyimpanan media dan metadata.
4. Posting Center — status distribusi semua platform.
5. Trading — monitoring hasil analisa; tidak mengubah engine trading.
6. Brain — orkestrasi skill dan agent.
7. System — scheduler, health, worker, error dan recovery.

## Content Package
Setiap hasil Creator nantinya membawa:
- content_id
- title / hook / script
- media_url / thumbnail_url
- description / hashtags
- platform_targets
- created_at
- render_status / qc_status / posting_status

Lifecycle:
DRAFT → RENDERING → QC → READY_TO_POST → QUEUED → POSTING → POSTED → VERIFIED
Failure:
ANY_STATE → FAILED → RETRY / NEEDS_REVIEW
## RINA Distribution Agent
Konsep TikTok Integration diubah menjadi Distribution Agent.
Agent menerima content package READY_TO_POST dari Creator.

Tugas agent:
1. preflight
2. upload/publish
3. verify
4. simpan post ID
5. simpan status/error
6. retry bila aman

Platform dibuat sebagai adapter:
- TikTok
- Instagram
- YouTube
- Facebook
- platform berikutnya

Agent tidak membuat konten.

## Batasan
- Jangan hapus backend TikTok lama.
- Jangan bangun auto-post semua platform sekaligus.
- Jangan menyentuh logika trading untuk migrasi web.
- Landing page dan flow TikTok lama tetap dijaga.
- APK tetap freeze.

## Control Plane Web → Brain
Web tidak mengeksekusi skill secara langsung.
1. User login melalui Supabase Auth.
2. Web mengirim command terotorisasi ke `rina-control-api`.
3. API memvalidasi command dan menyimpan ke `rina_control_commands`.
4. Brain/worker mengambil command QUEUED, mengeksekusi sesuai registry/policy, lalu menulis hasil.
5. Web membaca status/result command.

Command awal yang diizinkan:
- CHAT_MESSAGE
- HEALTH_CHECK
- CREATOR_STATUS
- SYSTEM_STATUS
- REQUEST_DAILY_CONTENT

Publisher, trading execution, perubahan security/schema, dan perubahan source code tetap di luar jalur kontrol otomatis.

## Tahapan
1. Web shell + navigation
2. Auth/session
3. Control Plane Web → Brain
4. Storage contract
5. Creator library/media preview
5. Chat
6. Monitoring Brain/System
7. Posting Center
8. Distribution Agent API
9. Platform adapters
10. End-to-end verification
11. Evaluasi APK hanya setelah stabil

Dokumen ini adalah kontrak arsitektur, bukan klaim bahwa seluruh backend sudah terhubung.
