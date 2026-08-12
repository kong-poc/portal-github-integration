---
api_name: "X12 810 - Invoice"
category: "Accounting"
---

# ANSI X12 810 - Invoice

## What it is

The X12 810 is the standard EDI transaction set a seller sends to a buyer to request payment
for goods or services already shipped or rendered - the EDI equivalent of a paper invoice.

> **Note:** This is an original educational summary of the transaction set's structure, written
> from publicly known segment names/purposes. It is **not** a reproduction of the official ASC
> X12 standard, which is licensed and sold by [X12.org](https://x12.org/). For a certified
> implementation guide, purchase the official standard from X12 or your trading partner's EDI
> documentation.

## Envelope structure (shared by all X12 transaction sets)

| Level | Segment | Purpose |
|---|---|---|
| Interchange | `ISA` | Interchange Control Header - sender/receiver IDs, delimiters |
| Functional Group | `GS` | Functional Group Header - groups transactions of the same type |
| Transaction Set | `ST` | Transaction Set Header - marks the start of one 810 document |
| ... | ... | (document-specific segments, see below) |
| Transaction Set | `SE` | Transaction Set Trailer - segment count, control number |
| Functional Group | `GE` | Functional Group Trailer |
| Interchange | `IEA` | Interchange Control Trailer |

## 810-specific segments

| Segment | Name | Purpose |
|---|---|---|
| `BIG` | Beginning Segment for Invoice | Invoice number, invoice date, related PO number |
| `REF` | Reference Identification | Additional reference numbers |
| `N1` | Name | Party identification (Buyer, Remit-To, Ship-From), often looped |
| `ITD` | Terms of Sale/Deferred Terms | Payment terms (e.g. net 30, discount %) |
| `DTM` | Date/Time Reference | Shipped date, terms discount due date |
| `IT1` | Baseline Item Data (Invoice) | One line item: SKU, quantity, unit price |
| `PID` | Product/Item Description | Free-text description of the line item |
| `SAC` | Service, Promotion, Allowance, or Charge | Line-level or invoice-level charges/discounts |
| `TDS` | Total Monetary Value Summary | Total invoice amount |
| `CTT` | Transaction Totals | Count of line items in the transaction |

## Illustrative example (generic, not a real trading partner)

```
ISA*00*          *00*          *ZZ*SENDERID       *ZZ*RECEIVERID     *260811*1200*U*00401*000000002*0*P*>~
GS*IN*SENDERID*RECEIVERID*20260811*1200*2*X*004010~
ST*810*0001~
BIG*20260811*INV-55021*20260805*PO-100245~
N1*RE*Example Vendor Inc~
N1*BT*Example Buyer Co~
ITD*01*3*2**30*****15~
IT1*1*100*EA*12.50**VP*SKU-12345~
PID*F****Blue Widget, Size Medium~
TDS*125000~
CTT*1~
SE*10*0001~
GE*1*2~
IEA*1*000000002~
```

## Mapping to a modern REST equivalent

`BIG` maps to invoice header fields (invoice number/date, referenced PO), the `N1` loop maps to
a `businessEntity` array, `IT1`/`PID`/`SAC` map to an `itemDetail` array with pricing and
adjustments, and `TDS` maps to a computed `invoiceTotal` field rather than a segment that must be
transmitted separately.
