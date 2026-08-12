---
api_name: "X12 204 - Booking Tender (Air/Truck)"
category: "Transportation"
mode: "Air, Truck"
---

# ANSI X12 204 - Booking Tender (Air/Truck)

## What it is

The X12 204 is the standard EDI transaction set a shipper or 3PL sends to a motor or air
carrier to tender (offer) a shipment for transport - specifying pickup/delivery locations,
equipment needed, and commodity details. It's the EDI equivalent of a truckload or air freight
tender request.

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
| Transaction Set | `ST` | Transaction Set Header - marks the start of one 204 document |
| ... | ... | (document-specific segments, see below) |
| Transaction Set | `SE` | Transaction Set Trailer - segment count, control number |
| Functional Group | `GE` | Functional Group Trailer |
| Interchange | `IEA` | Interchange Control Trailer |

## 204-specific segments

| Segment | Name | Purpose |
|---|---|---|
| `B2` | Beginning Segment for Shipment Information Transaction | Shipment ID, carrier SCAC code, payment method |
| `B2A` | Set Purpose | Original / cancellation / change indicator |
| `N1` | Name | Party identification (Shipper, Consignee, Bill-To), often looped |
| `G62` | Date/Time | Requested pickup / delivery dates |
| `S5` | Stop-off Details | Sequenced pickup and delivery stops |
| `L5` | Description, Marks and Numbers | Commodity description |
| `L0` | Line Item (Quantity and Weight) | Weight, quantity, and packaging of the shipment |
| `AT5` | Bill of Lading Handling Requirements | Special handling / hazmat codes |

## Illustrative example (generic, not a real trading partner)

```
ISA*00*          *00*          *ZZ*SENDERID       *ZZ*RECEIVERID     *260811*1200*U*00401*000000005*0*P*>~
GS*SR*SENDERID*RECEIVERID*20260811*1200*5*X*004010~
ST*204*0001~
B2**EXLC*TL-330991***PP~
B2A*00~
N1*SH*Example Shipper Co~
N1*CN*Example Consignee Inc~
G62*10*20260815~
S5*1*PU~
S5*2*CN~
L5*1*Palletized General Freight~
L0*1*4000*L*2*PLT~
SE*11*0001~
GE*1*5~
IEA*1*000000005~
```

## Mapping to a modern REST equivalent

`B2`/`B2A` map to a load tender header (shipment ID, carrier, purpose), the `N1` loop maps to a
`businessEntity` array, `S5` maps to a `stops` array (ordered pickup/delivery locations), and
`L5`/`L0`/`AT5` map to a `cargoDetail` array with commodity description, weight, and any special
handling instructions.
