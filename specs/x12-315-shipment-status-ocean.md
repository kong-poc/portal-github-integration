---
api_name: "X12 315 - Shipment Status (Ocean)"
category: "Transportation"
mode: "Ocean"
---

# ANSI X12 315 - Shipment Status (Ocean)

## What it is

The X12 315 (Status Details, Ocean Transportation) is the standard EDI transaction set an ocean
carrier sends to report shipment status milestones - vessel departure, arrival, customs release,
container availability - as an ocean shipment moves through its journey. It provides the event
milestones customers use to track an ocean shipment in transit.

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
| Transaction Set | `ST` | Transaction Set Header - marks the start of one 315 document |
| ... | ... | (document-specific segments, see below) |
| Transaction Set | `SE` | Transaction Set Trailer - segment count, control number |
| Functional Group | `GE` | Functional Group Trailer |
| Interchange | `IEA` | Interchange Control Trailer |

## 315-specific segments

| Segment | Name | Purpose |
|---|---|---|
| `B4` | Beginning Segment for Inquiry or Reply | Reference numbers (booking number, bill of lading) |
| `N1` | Name | Party identification (Carrier, Shipper, Consignee), often looped |
| `Q2` | Status Details (Ocean) | Status/event code, date/time, and location - the core repeating segment carrying each milestone event |
| `R4` | Port or Terminal | Port relevant to the reported event |
| `V1` | Vessel Identification | Vessel name and voyage number |

## Illustrative example (generic, not a real trading partner)

```
ISA*00*          *00*          *ZZ*SENDERID       *ZZ*RECEIVERID     *260811*1200*U*00401*000000008*0*P*>~
GS*QO*SENDERID*RECEIVERID*20260811*1200*8*X*004010~
ST*315*0001~
B4**BKG-88010~
N1*CA*Example Ocean Carrier Line~
Q2***CNSHA*USLAX*9876543*99*L~
R4*L*PT*CNSHA*20260812~
V1*9876543*Example Voyager*99*L~
SE*7*0001~
GE*1*8~
IEA*1*000000008~
```

## Mapping to a modern REST equivalent

`B4` maps to reference identifiers on a `shipmentStatus` header, the repeating `Q2` segment maps
to a `statusEvents` array (each entry carrying an `eventCode`, `dateTime`, and `location`), and
`R4`/`V1` add vessel and port context to each event - much like a modern tracking API's list of
milestone events.
