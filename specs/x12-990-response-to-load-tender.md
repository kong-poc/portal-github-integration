---
api_name: "X12 990 - Response to a Load Tender (Air/Truck)"
category: "Transportation"
---

# ANSI X12 990 - Response to a Load Tender (Air/Truck)

## What it is

The X12 990 is the standard EDI transaction set a motor or air carrier sends back in response
to a load tender - accepting, declining, or placing on hold a proposed shipment. It is the
carrier's answer to an X12 204 Booking Tender, similar in role to the 301 for ocean shipments.

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
| Transaction Set | `ST` | Transaction Set Header - marks the start of one 990 document |
| ... | ... | (document-specific segments, see below) |
| Transaction Set | `SE` | Transaction Set Trailer - segment count, control number |
| Functional Group | `GE` | Functional Group Trailer |
| Interchange | `IEA` | Interchange Control Trailer |

## 990-specific segments

| Segment | Name | Purpose |
|---|---|---|
| `B1` | Beginning Segment for Response to Load Tender | Shipment ID, response status code (accept/decline/hold) |
| `N1` | Name | Party identification (Carrier, Shipper), often looped |
| `G62` | Date/Time | Estimated pickup date, if the tender was accepted |
| `MS3` | Interline Information | Interline carrier details, if the shipment is handed off to another carrier |

## Illustrative example (generic, not a real trading partner)

```
ISA*00*          *00*          *ZZ*SENDERID       *ZZ*RECEIVERID     *260811*1200*U*00401*000000007*0*P*>~
GS*AR*SENDERID*RECEIVERID*20260811*1200*7*X*004010~
ST*990*0001~
B1*TL-330991*EXLC*A~
N1*CA*Example Motor Carrier Inc~
N1*SH*Example Shipper Co~
G62*10*20260815~
SE*7*0001~
GE*1*7~
IEA*1*000000007~
```

## Mapping to a modern REST equivalent

`B1` maps to a `loadTenderResponse` header carrying a `responseStatus` field
(`accepted`/`declined`/`hold`), the `N1` loop maps to a `businessEntity` array, `G62` maps to an
`estimatedPickupDate` field, and `MS3` maps to an optional `interlineCarrier` object when the
shipment is being handed off.
