---
id: US-AI-074
prd: PRD-AI-003
epic: EPIC-AI-003
feature: FEAT-AI-013
title: Mengelola Perubahan Outlet
status: Draft
priority: High
uix_status: 0%
created_at: 2026-07-21
updated_at: 2026-07-21
---

# US-AI-074 - Mengelola Perubahan Outlet

## 1. Parent Reference

- PRD: PRD-AI-003 - Customer Self-Service Capabilities
- Epic: EPIC-AI-003 - Intent Capabilities
- Feature: FEAT-AI-013 - Orkestrasi Self-Service

## 2. User Story

Sebagai customer yang dapat mengenal lebih dari satu outlet,  
Saya ingin bot memastikan outlet yang sedang dibahas,  
Agar informasi jadwal, promo, membership, dan layanan tidak berasal dari outlet yang salah.

## 3. Goal

Bot menggunakan satu outlet yang jelas per jawaban, memvalidasi outlet yang disebut customer, dan meminta klarifikasi ketika nama outlet tidak unik.

## 4. Acceptance Criteria

- Default outlet berasal dari percakapan Omnichannel.
- Nama outlet yang disebut secara eksplisit mengganti konteks hanya setelah ditemukan secara tunggal dan tersedia pada WABA terkait.
- Jika beberapa outlet memiliki nama serupa, bot menampilkan maksimal lima pilihan outlet.
- Jika outlet tidak dapat dilayani pada percakapan tersebut, bot tetap memakai outlet awal dan menjelaskan keterbatasan.
- Pergantian outlet menghapus data capability yang spesifik terhadap outlet sebelumnya.
- Bot tidak menggabungkan data dari dua outlet dalam satu jawaban sesuai BR-AI-375.
- Permintaan membandingkan outlet diarahkan untuk memilih satu outlet terlebih dahulu karena perbandingan belum termasuk MVP.

## 5. Form Rules

| Label | Field Key | Type | Required | Unique | Max Length | Description |
|---|---|---|---:|---:|---:|---|
| Outlet | outlet_id | selection/context | yes | no | - | Default dari percakapan; berubah jika pilihan customer valid |

## 6. Form Notes

- Customer-facing menggunakan nama outlet; identifier internal tidak ditampilkan.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-074-01 | Percakapan terhubung ke Outlet Tebet | Customer bertanya promo tanpa menyebut outlet | Bot menggunakan data Outlet Tebet |
| SC-AI-074-02 | Percakapan di Outlet Tebet dan Outlet Menteng valid pada WABA yang sama | Customer bertanya "kalau Outlet Menteng?" | Bot mengganti konteks ke Outlet Menteng dan menjawab untuk outlet tersebut |
| SC-AI-074-03 | Terdapat beberapa outlet bernama "Kemang" | Customer menyebut "Outlet Kemang" | Bot meminta customer memilih outlet yang dimaksud |
| SC-AI-074-04 | Customer meminta perbandingan dua outlet | Bot mengenali dua outlet | Bot meminta customer memilih satu outlet yang ingin dicek lebih dahulu |

### Detail SC-AI-074-03 - Nama Outlet Ambigu

#### Given

WABA memiliki Outlet Kemang Utara dan Outlet Kemang Selatan.

#### When

Customer mengirim "promo Outlet Kemang apa saja?".

#### Then

Bot tidak mengambil promo dan meminta customer memilih salah satu outlet.

#### Error Message

> Ada lebih dari satu outlet Kemang, Kak. Pilih salah satu: 1. Kemang Utara, 2. Kemang Selatan.

## 8. Supporting Information

- Effective state bot tetap dievaluasi untuk outlet tujuan sesuai PRD-AI-001.
