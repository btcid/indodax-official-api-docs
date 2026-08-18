# TAPI v2: Supported Banks for IDR Withdrawal

The following banks are supported for [**fiat (IDR) withdrawal via API**](../INDODAX-TradeAPI-2.md#withdraw-idr):

|  | `bankName` | `bankCodeForPix` |
| --- | --- | --- |
| 1 | Bank BPD Aceh | 116 |
| 2 | Bank Artha Graha | 037 |
| 3 | Bank Mestika | 151 |
| 4 | BCA | 014 |
| 5 | BCA Blu | 501 |
| 6 | Bank BCA Syariah | 536 |
| 7 | Bank Neo Commerce | 490 |
| 8 | BNI 46 | 009 |
| 9 | BPD Bali | 129 |
| 10 | Bank Banten | 137 |
| 11 | Bank Bengkulu | 133 |
| 12 | BPD DIY | 112 |
| 13 | Bank Jambi | 115 |
| 14 | Bank Jateng | 113 |
| 15 | Bank Jatim | 114 |
| 16 | Bank Kalbar | 123 |
| 17 | Bank BPD Kalsel | 122 |
| 18 | BPD Kalteng | 125 |
| 19 | Bank BPD Kaltim | 124 |
| 20 | Bank Maluku | 131 |
| 21 | Bank NTB | 128 |
| 22 | Bank NTT | 130 |
| 23 | Bank Papua | 132 |
| 24 | Bank Kepulauan Riau | 119 |
| 25 | Bank Sulsel | 126 |
| 26 | Bank Sulteng | 134 |
| 27 | Bank Sultra | 135 |
| 28 | Bank Sulut | 127 |
| 29 | Bank Nagari | 118 |
| 30 | Bank Sumsel Babel | 120 |
| 31 | Bank Sumut | 117 |
| 32 | Bank Bumi Arta | 076 |
| 33 | BRI | 002 |
| 34 | Bank Syariah Indonesia | 451 |
| 35 | BTN | 200 |
| 36 | BTPN | 213 |
| 37 | Bank Capital | 054 |
| 38 | CIMB Niaga | 022 |
| 39 | Citibank | 031 |
| 40 | Bank Chinatrust Indonesia | 949 |
| 41 | DANAMON | 011 |
| 42 | DBS | 046 |
| 43 | Bank DKI | 111 |
| 44 | Bank Ganesha | 161 |
| 45 | Hibank (Bank Mayora) | 553 |
| 46 | Bank HSBC Indonesia | 087 |
| 47 | Bank IBK Indonesia (BANK AGRIS) | 945 |
| 48 | Bank Ina Perdana | 513 |
| 49 | Bank Index | 555 |
| 50 | Bank BJB | 110 |
| 51 | Bank Jabar Banten Syariah | 425 |
| 52 | Bank Jago | 542 |
| 53 | Bank Jasa Jakarta | 472 |
| 54 | Bank Bukopin | 441 |
| 55 | Bank Hana | 484 |
| 56 | Bank Lampung | 121 |
| 57 | Mandiri | 008 |
| 58 | Bank Maspion | 157 |
| 59 | Bank Mayapada Internasional | 097 |
| 60 | BII Maybank | 016 |
| 61 | Bank Mega | 426 |
| 62 | Bank Syariah Mega Indonesia | 506 |
| 63 | Bank MNC Internasional | 485 |
| 64 | Bank Muamalat | 147 |
| 65 | Bank Nobu | 503 |
| 66 | OCBC NISP | 028 |
| 67 | Bank Panin | 019 |
| 68 | Permata Bank | 013 |
| 69 | Bank QNB (Bank Kesawan) | 167 |
| 70 | Bank Rakyat Indonesia AGRONIAGA | 494 |
| 71 | Bank Sahabat Sampoerna | 523 |
| 72 | SBI | 498 |
| 73 | Sea Bank | 535 |
| 74 | Bank Sinarmas | 153 |
| 75 | Standard Chartered Bank | 050 |
| 76 | UOB Indonesia | 023 |
| 77 | Bank Victoria | 566 |
| 78 | Bank Saudara | 212 |

> ℹ️ **Notes**
>
> - `bankCodeForPix` is mandatory. If not specified, it defaults to `014` (BCA).
> - `bankCodeForPix` must be provided as a **3-digit code**. For codes with fewer than three digits, prepend `0` (e.g., `9` → `009` for BNI and `14` → `014` for BCA).
> - Fiat (IDR) withdrawal must be made to a bank account registered under the same name as the KYC-verified account holder.
> - Fiat (IDR) withdrawals currently support **Indonesian banks only**. Withdrawals to banks outside Indonesia are not supported.
