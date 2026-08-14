---
api_name: "X12 214 - Shipment Status (Air/Truck)"
category: "Transportation"
mode: "Air, Truck"
---

# ANSI X12 214 - Shipment Status (Air/Truck)

## What it is

The X12 214 (Transportation Carrier Shipment Status Message) is the standard EDI transaction
set a motor or air carrier sends to report shipment tracking milestones - picked up, in transit,
out for delivery, delivered, or exception - as a truck or air shipment moves toward its
destination. It's the common EDI "tracking update" message for truck/air freight.

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
| Transaction Set | `ST` | Transaction Set Header - marks the start of one 214 document |
| ... | ... | (document-specific segments, see below) |
| Transaction Set | `SE` | Transaction Set Trailer - segment count, control number |
| Functional Group | `GE` | Functional Group Trailer |
| Interchange | `IEA` | Interchange Control Trailer |

## 214-specific segments

| Segment | Name | Purpose |
|---|---|---|
| `B10` | Beginning Segment for Transportation Carrier Shipment Status Message | Shipment ID / PRO number |
| `L11` | Business Instructions Reference | PO number / customer reference numbers |
| `N1` | Name | Party identification (Shipper, Consignee), often looped |
| `AT7` | Shipment Status Details | Status code, reason code, and date/time - the core repeating segment carrying each tracking event |
| `MS1`/`MS2` | Equipment / Tag Identification | Current city/state and equipment ID for the shipment |

## Illustrative example (generic, not a real trading partner)

```
ISA*00*          *00*          *ZZ*SENDERID       *ZZ*RECEIVERID     *260811*1200*U*00401*000000009*0*P*>~
GS*QM*SENDERID*RECEIVERID*20260811*1200*9*X*004010~
ST*214*0001~
B10*TL-330991*PRO-556677*EXLC~
L11*PO-100245*PO~
N1*SH*Example Shipper Co~
N1*CN*Example Consignee Inc~
AT7*X6*NS***20260815*1400~
MS1*Los Angeles*CA*US~
SE*8*0001~
GE*1*9~
IEA*1*000000009~
```

## Mapping to a modern REST equivalent

`B10` maps to shipment identifiers on a `shipmentStatus` header, `L11` maps to a
`referenceNumbers` array, the repeating `AT7` segment maps to a `statusEvents` array (each entry
carrying a `statusCode`, `reasonCode`, and `dateTime`), and `MS1`/`MS2` map to a `currentLocation`
object - the same shape as a typical parcel/freight tracking API response.
