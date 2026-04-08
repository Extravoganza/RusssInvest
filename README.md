# Regional Index Pools

**DeFi-протокол для токенизации и инвестирования в реальные региональные активы на Solana.**

🔗 **Live Demo:** https://illustrious-llama-e58e11.netlify.app/

---

## 🎯 Концепция

Regional Index Pools — это инвестиционные пулы, привязанные к экономическим индексам российских регионов. Инвесторы вносят USDC в пулы, получая LP-токены, и зарабатывают APY от реальной экономической деятельности предприятий внутри региона.

```
┌─────────────────────────────────────────────────────────────┐
│              REGIONAL INDEX POOLS — RWA DEFI                   │
│                                                               │
│   USDC ──────▶ Pool ──────▶ LP Tokens                        │
│                        │                                     │
│                        ▼                                     │
│   ┌─────────────────────────────────────────────────────┐    │
│   │            RWA Assets Portfolio                      │    │
│   │  🏢 Real Estate  │  🛢️ Industry  │  💻 Tech IP    │    │
│   └─────────────────────────────────────────────────────┘    │
│                        │                                     │
│                        ▼                                     │
│              📈 APY 8-14% annually                           │
└─────────────────────────────────────────────────────────────┘
```

## ⚡ Доступные регионы

| Регион | APY | TVL | Lock Period | Актива | Риск |
|--------|-----|-----|-------------|--------|------|
| Северо-Кавказский ФО | 12.5% | $890K | 12 мес | Туризм, АПК, Логистика | Low |
| Центральный ФО | 9.8% | $650K | 6 мес | Недвижимость, FinTech, IT | Low |
| Северо-Западный ФО | 10.2% | $420K | 6 мес | Судостроение, Промкластеры | Medium |
| Уральский ФО | 11.5% | $340K | 9 мес | Металлургия, Энергетика | Medium |
| Дальневосточный ФО | 14.2% | $180K | 12 мес | Ресурсы, Логистика, СПГ | High |
| Приволжский ФО | 8.9% | $280K | 6 мес | АПК, Автопром | Low |

## 🏗️ Архитектура

```
┌──────────────────────────────────────────────────────────────┐
│                        FRONTEND LAYER                         │
│   Next.js 14 • Framer Motion • TailwindCSS • Wallet Adapter  │
│                                                                │
│   ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌─────────┐       │
│   │ Landing │  │Dashboard│  │  Pool   │  │Portfolio│       │
│   │  Page   │  │  Stats  │  │ Detail  │  │  View   │       │
│   └────┬────┘  └────┬────┘  └────┬────┘  └────┬────┘       │
└────────┼────────────┼────────────┼────────────┼─────────────┘
         │            │            │            │
         └────────────┴─────┬──────┴────────────┘
                            │ RPC
┌───────────────────────────┴──────────────────────────────────┐
│                    PROGRAM LAYER (Anchor)                     │
│                                                                │
│   ┌────────────────────────────────────────────────────────┐ │
│   │                      RWIPool Program                     │ │
│   │                                                         │ │
│   │   ┌───────────┐  ┌───────────┐  ┌───────────────┐     │ │
│   │   │   Pool    │  │ Investor  │  │ LP Mint (SPL) │     │ │
│   │   │  Account  │  │  Account  │  │               │     │ │
│   │   └───────────┘  └───────────┘  └───────────────┘     │ │
│   │                                                         │ │
│   │   deposit() │ withdraw() │ updatePoolRate()           │ │
│   └────────────────────────────────────────────────────────┘ │
└────────────────────────────┬─────────────────────────────────┘
                             │ Token Accounts
┌────────────────────────────┴─────────────────────────────────┐
│                      SOLANA CLUSTER                            │
│                   Program ID: RwaPooL1111...                   │
│                   USDC Mint (Devnet)                          │
└───────────────────────────────────────────────────────────────┘
```

## 📜 Смарт-контракт

### Account Structures

**Pool** — инвестиционный пул
```rust
authority: Pubkey           // Админ пула
mint: Pubkey                // USDC mint
lp_mint: Pubkey            // LP token mint
total_deposits: u64        // TVL пула
total_shares: u64          // Всего LP токенов
min_deposit: u64           // Мин. депозит
max_deposit: u64           // Макс. депозит
pool_cap: u64              // Вместимость
annualized_rate: u64       // APY в basis points
is_active: bool            // Статус
```

**Investor** — позиция инвестора
```rust
wallet: Pubkey             // Кошелёк
shares: u64                // LP токены
total_deposited: u64       // Всего внесено
kyc_verified: bool         // KYC статус
whitelisted: bool          // В whitelist
```

### Instructions

| Метод | Параметры | Описание |
|-------|-----------|---------|
| `initializePool` | name, region, min, max, cap, rate | Создание пула |
| `initializeInvestor` | kyc_verified | Регистрация инвестора |
| `deposit` | amount | Внос USDC → LP tokens |
| `withdraw` | shares | Вывод: LP → USDC + yield |
| `updatePoolRate` | new_rate | Обновление APY (админ) |
| `togglePoolActive` | — | Пауза/возобновление |
| `whitelistInvestor` | investor, status | whitelist управление |
| `distributeYield` | amount | Распределение дохода |

### Расчёт LP-токенов

```javascript
// При первом депозите: 1 токен = 1 LP
shares = depositAmount

// Далее: пропорционально текущей цене
shares = (depositAmount * totalShares) / totalDeposits

// При выводе
withdrawal = (shares * totalDeposits) / totalShares
```

## 🛠️ Технологии

| Слой | Стек |
|------|------|
| **Blockchain** | Solana 1.18+, Anchor 0.30, Rust, SPL Token |
| **Frontend** | Next.js 14, React 18, TypeScript, TailwindCSS |
| **Анимации** | Framer Motion 11 |
| **Кошельки** | Phantom, Solflare, Backpack, Ledger |
| **RPC** | Solana Devnet |
| **Деплой** | Vercel |

## 🔐 Безопасность

- [x] Anchor-верификация счетов
- [x] KYC mock для compliance
- [x] Whitelist инвесторов
- [x] Лимиты депозитов (min/max)
- [x] Pool caps
- [x] Пауза/возобновление пула
- [ ] Профессиональный аудит
- [ ] Multi-sig governance
- [ ] Страховой фонд

## 📊 Финансовые метрики

### Формулы

```javascript
// ROI
ROI = ((currentValue - initialValue) / initialValue) * 100

// APY
APY = ((1 + rate / 365) ^ 365 - 1) * 100

// Доля инвестора
sharePercent = (userShares / totalShares) * 100
```

### Примеры доходности

| Инвестиция | APY | 1 мес | 6 мес | 1 год |
|------------|-----|-------|-------|-------|
| $100 | 12.5% | $1.04 | $6.25 | $12.50 |
| $1,000 | 12.5% | $10.42 | $62.50 | $125.00 |
| $10,000 | 12.5% | $104.17 | $625.00 | $1,250.00 |

## 🚀 Разработка

### Требования

```
Node.js 18+
Rust 1.75+
Anchor CLI 0.30+
Solana CLI 1.18+
```

### Билд

```bash
# Зависимости
npm install

# Билд Anchor
anchor build

# Тесты
anchor test

# Frontend
npm run dev
```

### Конфигурация

```env
SOLANA_RPC_URL=https://api.devnet.solana.com
PROGRAM_ID=RwaPooL111111111111111111111111111111111111
NETWORK=devnet
```

## 📂 Структура

```
regional-index-pools/
├── programs/
│   └── rwipool/
│       ├── src/lib.rs          # Program + Instructions
│       └── Cargo.toml
├── tests/
│   └── rwipool.ts              # Integration tests
├── app/
│   ├── api/                    # API routes
│   ├── components/             # WalletProvider
│   ├── hooks/                  # usePool, useBlockchainData
│   └── lib/idl.ts             # IDL + constants
├── docs/
│   └── API.md                 # API documentation
└── README.md
```

## 📡 API

| Endpoint | Описание |
|----------|----------|
| `GET /api/pools` | Список всех пулов |
| `GET /api/pools/:address` | Детали пула |
| `GET /api/investor/:wallet` | Портфель инвестора |

## 🔗 Ресурсы

- [Solana Docs](https://docs.solana.com)
- [Anchor Framework](https://anchor-lang.com)
- [Solana Explorer](https://explorer.solana.com)
- [Live Demo](https://regional-index-pools.vercel.app)

## 👥 Команда

| Участник | Роль |
|----------|------|
| Kulakova Kate | Founder, Product Lead |
| Zaytseva Daria | Founder, Lead Engineer |

## 📄 Лицензия

MIT

---

*Токенизация реальных активов • Solana 2026*
