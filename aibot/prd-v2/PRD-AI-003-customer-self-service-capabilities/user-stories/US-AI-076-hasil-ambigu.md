---
id: US-AI-076
prd: PRD-AI-003
epic: EPIC-AI-003
feature: FEAT-AI-013
title: Memilih Hasil yang Ambigu
status: Draft
priority: Medium
uix_status: 0%
created_at: 2026-07-21
updated_at: 2026-07-21
---

# US-AI-076 - Memilih Hasil yang Ambigu

## 1. Parent Reference

- PRD: PRD-AI-003 - Customer Self-Service Capabilities
- Epic: EPIC-AI-003 - Intent Capabilities
- Feature: FEAT-AI-013 - Orkestrasi Self-Service

## 2. User Story

Sebagai customer,  
Saya ingin memilih data yang dimaksud ketika nama promo, membership, atau layanan mirip,  
Agar bot tidak memberikan detail dari hasil yang salah.

## 3. Goal

Bot menampilkan pilihan ringkas dan bernomor ketika pencarian nama menghasilkan lebih dari satu kecocokan yang layak.

## 4. Acceptance Criteria

- Exact match tunggal langsung dipilih.
- Lebih dari satu kecocokan menampilkan maksimal lima pilihan bernomor.
- Pilihan hanya memuat pembeda yang aman dan relevan, seperti nama lengkap atau jenis layanan.
- Customer dapat memilih menggunakan nomor atau nama lengkap.
- Input yang tidak cocok dengan pilihan memicu satu permintaan ulang.
- Setelah dua pilihan tidak valid berturut-turut, bot menawarkan menu atau agent.
- Bot tidak menampilkan detail lengkap sebelum customer memilih.

## 5. Form Rules

| Label | Field Key | Type | Required | Unique | Max Length | Description |
|---|---|---|---:|---:|---:|---|
| Pilihan Hasil | selected_result | number/string | yes | no | 150 | Nomor atau nama lengkap dari pilihan yang ditampilkan |

## 6. Form Notes

- Nomor pilihan hanya berlaku pada daftar terakhir yang ditampilkan bot.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-076-01 | Terdapat dua promo dengan nama mirip | Customer meminta detail menggunakan nama umum | Bot menampilkan dua pilihan bernomor |
| SC-AI-076-02 | Bot menampilkan pilihan | Customer mengirim "2" | Bot menampilkan detail hasil nomor 2 |
| SC-AI-076-03 | Bot menampilkan pilihan | Customer mengirim nilai di luar pilihan | Bot meminta customer memilih ulang |
| SC-AI-076-04 | Customer dua kali memberi pilihan tidak valid | Bot tidak dapat menentukan hasil | Bot menawarkan kembali ke menu atau handoff |

### Detail SC-AI-076-01 - Beberapa Hasil Cocok

#### Given

Outlet memiliki promo "HEMAT MEMBER" dan "HEMAT MEMBER PLUS" yang sama-sama aktif.

#### When

Customer mengirim "detail promo hemat member" dan pencocokan tidak menghasilkan satu pilihan yang pasti.

#### Then

Bot meminta customer memilih sebelum menampilkan benefit atau syarat promo.

#### UI Behavior

- Pilihan ditampilkan sebagai daftar bernomor.
- Maksimal lima pilihan per respons.

## 8. Supporting Information

- Berlaku pada promo, membership, dan layanan; tidak berlaku pada ID transaksi atau nomor ticket.
