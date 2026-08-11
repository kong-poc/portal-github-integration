# ANSI X12 850 - Purchase Order

## What it is

The X12 850 is the standard EDI transaction set used to transmit a purchase order from a buyer
to a seller: what's being ordered, how much, at what price, and where it should ship.

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
| Transaction Set | `ST` | Transaction Set Header - marks the start of one 850 document |
| ... | ... | (document-specific segments, see below) |
| Transaction Set | `SE` | Transaction Set Trailer - segment count, control number |
| Functional Group | `GE` | Functional Group Trailer |
| Interchange | `IEA` | Interchange Control Trailer |

## 850-specific segments

| Segment | Name | Purpose |
|---|---|---|
| `BEG` | Beginning Segment for Purchase Order | PO number, PO type, issue date |
| `REF` | Reference Identification | Additional reference numbers (dept, contract, etc.) |
| `PER` | Administrative Communications Contact | Buyer contact info |
| `FOB` | F.O.B. Related Instructions | Shipping terms |
| `DTM` | Date/Time Reference | Requested ship / delivery dates |
| `N1` | Name | Party identification (Buyer, Vendor, Ship-To), often looped |
| `PO1` | Baseline Item Data | One line item: SKU, quantity, unit price, UOM |
| `PID` | Product/Item Description | Free-text description of the line item |
| `CTT` | Transaction Totals | Count of line items in the transaction |

## Illustrative example (generic, not a real trading partner)

```
ISA*00*          *00*          *ZZ*SENDERID       *ZZ*RECEIVERID     *260811*1200*U*00401*000000001*0*P*>~
GS*PO*SENDERID*RECEIVERID*20260811*1200*1*X*004010~
ST*850*0001~
BEG*00*SA*PO-100245**20260811~
REF*DP*100~
N1*BY*Example Buyer Co~
N1*VN*Example Vendor Inc~
PO1*1*100*EA*12.50**VP*SKU-12345~
PID*F****Blue Widget, Size Medium~
CTT*1~
SE*9*0001~
GE*1*1~
IEA*1*000000001~
```

## Mapping to a modern REST equivalent

This structure maps naturally onto a JSON `PurchaseOrder` payload: `BEG` -> order header fields,
`N1` loop -> a `businessEntity` array (buyer/vendor/ship-to), `PO1`/`PID` loop -> an `itemDetail`
array, `CTT` -> a derived count rather than a transmitted field. That mapping is exactly what the
companion OpenAPI spec in this repo expresses as a callable API.
