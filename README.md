# Clean Code AI Skill & Standard

[![Clean Code Standard](https://img.shields.io/badge/Standard-Clean%20Code-brightgreen.svg)](#)
[![Language Agnostic](https://img.shields.io/badge/Language-Agnostic-blue.svg)](#)
[![AI Agent Ready](https://img.shields.io/badge/AI%20Agent-Skill%20%26%20Rules-orange.svg)](#)

Panduan standar rekayasa perangkat lunak (*software craftsmanship*) universal dan **AI Agent Skill** untuk menerapkan prinsip **Clean Code**, refactoring, serta **65 code smells & heuristics** di berbagai bahasa pemrograman (TypeScript/JavaScript, Python, Go, Java, C#, Rust, PHP, Dart, dll.).

---
## 🌟 Prinsip Inti yang Diterapkan

1. **The Boy Scout Rule**: Selalu tinggalkan kode dalam keadaan lebih bersih dari saat pertama kali ditemukan.
2. **Readability First (10:1 Ratio)**: Kode dibaca 10 kali lebih sering daripada ditulis. Utamakan kejelasan dibanding trik rumit.
3. **Four Rules of Simple Design**:
   1. Lolos semua tes (*Runs all tests*).
   2. Tanpa duplikasi (*DRY - Don't Repeat Yourself*).
   3. Mengungkapkan niat programmer (*Expresses intent*).
   4. Meminimalkan jumlah class dan method (*No premature abstraction*).
4. **Fungsi Kecil & Fokus**: 5–15 baris, maksimal 1–2 tingkat indentasi, melakukan satu hal (*Single Responsibility*), serta bebas *side effects*.
5. **Penanganan Error Bersih**: Mengutamakan exception/result type di atas error codes, dan **tidak me-return atau mem-passing `null`**.

---

## 🚀 Cara Pemasangan & Penggunaan

### 1. Digunakan sebagai Custom Skill AI Agent
Salin direktori ini ke folder skill AI agent Anda (misalnya Antigravity / Gemini CLI):
```bash
# Contoh direktori skill Antigravity / Gemini
~/.gemini/config/skills/clean-code/
```

### 2. Standar Panduan Tim Pengembang
Gunakan file dalam folder `references/` sebagai bahan referensi code review (*pull request checklist*) tim Anda.

## 📄 Lisensi
Didistribusikan untuk keperluan pembelajaran dan peningkatan standar kualitas rekayasa perangkat lunak. Bebas digunakan dan dimodifikasi.
