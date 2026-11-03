# TDS Category Check

A clickable mock of a quarterly review tool for tax deducted at source (TDS) in India. It accompanies my take-home brief for Tresata.

**Live demo:** https://tds-category-check.vercel.app/

**This is a mock.** No AI model is called. The AI output for the sample payments is written in advance, so the results below show how the design behaves, not how accurate a real model would be.

## The problem

Accounts teams give each vendor payment a TDS category, which sets the rate and payment code. My hypothesis, not yet confirmed with a practitioner, is that the category is often set once per vendor and then reused even when the work changes. A wrong category can go unnoticed, and the company may have no record of why it chose it.

Before each quarterly return, the tool reads each invoice without seeing its current tag, then flags where the two disagree. It recommends and never files, and a named person decides every change.

## How to run it

The app is one file, `tds-category-check.html`, with nothing to install or build. It makes no network requests, though the source links on its pages open in your browser.

- **Online:** open the [live demo](https://tds-category-check.vercel.app/).
- **On your computer:** download the file and open it in a browser. GitHub's normal file view shows only the source code.

Nothing is saved. Refreshing the page or clicking **Start over** resets it.

## What to try

1. Click **Check 16 payments**.
2. Open **Vellum Print Studio**. The tag dates from a printing order, but this invoice is for interior decoration, so it is ₹36,000 short. Tick the box and accept the change.
3. Open **Coolbreeze Engineers**. The reader quotes text that isn't in the invoice, so the check rejects its suggestion. Attach the sample contract and check again.
4. Open **Skylink Connect**. The AI agrees with the tag, but this is a contested type, so an accountant can only confirm the facts. Switch **Acting as** to S. Iyer to decide as the CA.
5. Open **Decision log** and click **Check the chain**.
6. Click **Add a payment** to try your own invoice text.

## What is real and what is scripted

| Part | In this mock |
|---|---|
| Choosing the Act from the earlier of the credit and payment dates | Real code |
| Codes, rates, thresholds and tax amounts | Real code, using rates from a secondary source |
| Keeping the tag away from the reader | Real code |
| Checking each quote against the document text | Real code |
| Comparing with the tag and routing to a queue | Real code |
| Limits on what each role can do, and the hash-chained decision log | Real code, but the role switch is not real sign-in |
| Facts, suggestions and confidence for the 16 samples | **Scripted** |
| Reading new or edited text | **A stand-in that looks for fixed phrases**, not a model |

## Examples tested

### 16 scripted samples

All companies and invoices are made up, and any resemblance to a real business is a coincidence. These outcomes were written into the script, so they don't measure accuracy. They do show that the code routes each case and calculates each amount as designed.

| ID | Vendor | Case | Tagged | Result | TDS on file | TDS suggested |
|---|---|---|---|---|---|---|
| PAY-1041 | Alder Housekeeping | Routine site staff | 1024 | Match, spot-check | ₹3,700 | ₹3,700 |
| PAY-1042 | Quillon Legal | Legal opinion | 1027 | Match | ₹24,000 | ₹24,000 |
| PAY-1043 | Tessera Cloud | Wrong code, same rate | 1024 | Mismatch | ₹12,000 | ₹12,000 |
| PAY-1044 | Harbour Advisory | Tax advice tagged as technical | 1026 | Mismatch | ₹6,000 | ₹30,000 |
| PAY-1045 | Vellum Print Studio | Work changed since the tag was set | 1024 | Mismatch | ₹9,000 | ₹45,000 |
| PAY-1046 | Ember Talent | Manpower supply, over-deducted | 1027 | Mismatch | ₹20,000 | ₹4,000 |
| PAY-1047 | Lexford Associates | Vague invoice, reader abstains | 1024 | Need evidence | ₹7,200 | None |
| PAY-1048 | Coolbreeze Engineers | Reader's quote isn't in the invoice | 1024 | Need evidence | ₹1,700 | None |
| PAY-1049 | Forgeline Machine Care | Annual maintenance contract | 1024 | Contested | ₹10,400 | ₹10,400 |
| PAY-1050 | Skylink Connect | Connectivity, AI agrees with the tag | 1024 | Contested | ₹6,400 | ₹6,400 |
| PAY-1051 | Orchard Business Park | Common area charges | 1024 | Contested | ₹3,800 | ₹19,000 |
| PAY-1052 | Swift Referral Partners | Commission | 1006 | Match | ₹1,200 | ₹1,200 |
| PAY-1053 | Banyan Estates | Office rent | 1009 | Match, spot-check | ₹15,000 | ₹15,000 |
| PAY-1054 | Plan Line Architects | Below the threshold | 1027 | Match | ₹0 | ₹0 |
| PAY-1055 | Cedar Consultancy | Credited before 1 April 2026, so old Act | 194J(b) | Match | ₹11,000 | ₹11,000 |
| PAY-1056 | Sunil Paint Works | Contract work by an individual | 1023 | Match, spot-check | ₹1,200 | ₹1,200 |

### 10 harder invoices

I also entered these through **Add a payment**, so the stand-in handled them instead of the script. This tests the mock's routing and the stand-in, not a real model.

| Invoice text | Tagged as | Result | How it went |
|---|---|---|---|
| Tax advisory opinion by chartered accountants, 10 hours | Professional | Match | Correct |
| Security guards for the Pune plant, and legal advice on their contracts | Contract | Need evidence | Reasonable: a mixed invoice shouldn't get one category |
| AMC charges and repairs for CNC machines | Contract | Match | **Failure:** the abbreviation "AMC" missed the contested list, so no CA saw it |
| Bluetooth connectivity kits, purchase of 40 units | Contract | Contested | **False alarm:** a purchase of goods matched the word "connectivity" |
| Anual maintenence contract for lifts, breakdown support | Contract | Need evidence | **Failure:** the misspelling missed the contested list, though the tool still abstained |
| Legal fee for counsel, September | Contract | Need evidence | Too cautious: the phrase isn't in its list |
| Printing of statutory audit reports, 200 copies | Contract | Need evidence | Too cautious: "audit" and "printing" point different ways |
| Technical services: housekeeping and pantry staff for September | Contract | Need evidence | Too cautious: the invoice's label conflicts with the work |
| No commission is payable. Painting of office walls. | Contract | Need evidence | Too cautious: it doesn't understand "no" |
| Consulting | Professional | Need evidence | Reasonable: there isn't enough detail |

## Where it fails

- **The contested list is too literal.** Plain text matching misses abbreviations and misspellings, and it flags unrelated uses of a word. The AMC case above is the worst kind of failure, because a contested payment looked like a clean match.
- **A true quote can still mislead.** The check confirms that a quote exists in the document, not that it supports the suggestion.
- **Documents can misdescribe the real work.** The tool only sees documents, so neither this mock nor a real model could detect that.
- **Vague invoices could flood the evidence queue.** If most invoices are vague and contracts aren't available, the tool would create work instead of removing it. I would test this first, on real invoices.
- **The calculation is simplified.** It applies the rate to the current payment only. It ignores PAN problems, lower-deduction certificates and invoices that need splitting across categories.

## What this does not show

- **Model accuracy.** No model was used, so I have no error or abstention rate for one. A real test would run a model on these invoices and count wrong-and-unflagged cases and abstentions.
- **Practitioner input.** I have not spoken to a CA or an accounts team, so the core hypothesis is untested.
- **Verified law.** The codes, rates and thresholds come from a secondary source and have not been checked against section 393 or the Income-tax Rules 2026. Rulings come from secondary reports, not the orders themselves. The **Rules and sources** page in the app links to them.

This is a design demonstration, not tax advice.

## How it was built

Claude wrote the code, the sample data and this README from my direction. It is a single HTML file using plain JavaScript and CSS, with the Geist typeface embedded.

I checked it with 39 automated browser checks using Playwright, covering routing, the tax arithmetic, role limits, escaping of entered text, downloads and the log chain, and I reviewed the screens by eye. Those checks test the code, not AI quality, and the test script isn't in this repository.

## LLM chat log

The assignment asks for a zip of the LLM chats. I couldn't see a place to upload it on the Google Form, so I've uploaded it to this repository as `tds-assignment-chat-log.zip`.

## Jurisdiction

India, for non-salary payments to residents under the Income Tax Act 2025, with the 1961 Act covering earlier payments. Another country would need a new rule table, crosswalk and rulings, while the pipeline could stay the same.
