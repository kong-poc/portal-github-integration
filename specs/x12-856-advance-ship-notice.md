# ANSI X12 856 - Advance Ship Notice (ASN)

## What it is

The X12 856 is the standard EDI transaction set a shipper sends **before** goods arrive, telling
the receiver what's in the shipment, how it's packed, and when to expect it - so the receiving
warehouse can plan labor and match the physical shipment against the order electronically.

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
| Transaction Set | `ST` | Transaction Set Header - marks the start of one 856 document |
| ... | ... | (document-specific segments, see below) |
| Transaction Set | `SE` | Transaction Set Trailer - segment count, control number |
| Functional Group | `GE` | Functional Group Trailer |
| Interchange | `IEA` | Interchange Control Trailer |

## 856-specific segments

Unlike the 850/810, the 856 is built around a **hierarchical loop** (`HL`) that nests
Shipment -> Order -> Pack -> Item, rather than a flat line-item list.

| Segment | Name | Purpose |
|---|---|---|
| `BSN` | Beginning Segment for Ship Notice | Shipment ID, creation date/time, type |
| `DTM` | Date/Time Reference | Estimated delivery date, shipped date |
| `HL` | Hierarchical Level | Marks each level of the Shipment/Order/Pack/Item nesting |
| `TD1`/`TD5` | Carrier Details | Packaging type, carrier, routing |
| `REF` | Reference Identification | PO number, bill of lading number |
| `LIN` | Item Identification | SKU / item identifier at the Item level |
| `SN1` | Item Detail (Shipment) | Quantity shipped for that item |
| `MAN` | Marks and Numbers | Container/carton ID (e.g. SSCC-18 barcode number) |
| `CTT` | Transaction Totals | Count of line items in the transaction |

## Illustrative example (generic, not a real trading partner)

```
ISA*00*          *00*          *ZZ*SENDERID       *ZZ*RECEIVERID     *260811*1200*U*00401*000000003*0*P*>~
GS*SH*SENDERID*RECEIVERID*20260811*1200*3*X*004010~
ST*856*0001~
BSN*00*ASN-77031*20260811*1200~
HL*1**S~
TD5*B*2*FEDX~
HL*2*1*O~
REF*PO*PO-100245~
HL*3*2*P~
MAN*GM*00012345000000012340~
HL*4*3*I~
LIN**VP*SKU-12345~
SN1**100*EA~
CTT*1~
SE*12*0001~
GE*1*3~
IEA*1*000000003~
```

## Mapping to a modern REST equivalent

The `HL` hierarchy maps to nested JSON objects/arrays (`shipment` -> `packages` -> `items`)
instead of X12's flat segment-with-pointer structure, `BSN`/`TD5`/`REF` map to shipment header
fields, and `LIN`/`SN1`/`MAN` map to an `itemDetail` array carrying SKU, quantity, and the
container/tracking identifier for each package.
