# Flow Diagrams (Mermaid)

These diagrams can be rendered in any Mermaid-compatible viewer (GitHub, VS Code with extension, mermaid.live).

---

## 1. Complete Ticket Lifecycle (State Machine)

```mermaid
stateDiagram-v2
    [*] --> RECEIVED: Front desk creates ticket
    RECEIVED --> ASSESSING: Technician starts assessment
    ASSESSING --> ASSESSED: Technician saves diagnosis
    ASSESSED --> APPROVED: Customer replies YES (WhatsApp)
    ASSESSED --> CANCELLED: Customer replies NO or staff cancels
    RECEIVED --> CANCELLED: Customer changes mind
    ASSESSING --> CANCELLED: Device beyond repair
    APPROVED --> IN_PROGRESS: Technician starts repair
    IN_PROGRESS --> COMPLETED: Repair done
    COMPLETED --> PICKED_UP: Customer collects device
    COMPLETED --> [*]: Auto-close after 7 days
    CANCELLED --> [*]: Device returned
    PICKED_UP --> [*]: Ticket archived
```

---

## 2. Two-Person Flow (Front Desk + Technician)

```mermaid
sequenceDiagram
    actor C as Customer
    actor F as Front Staff
    participant A as Mobile App
    participant DB as Database
    actor T as Technician
    participant AI as OpenAI
    participant WA as WhatsApp API

    C->>F: "My phone screen is cracked"
    F->>A: Open app, tap "New Ticket"
    F->>A: Snap photo of device back
    A->>AI: POST /ai/identify-device (photo)
    AI-->>A: { device: "iPhone 14 Pro", confidence: 0.94 }
    F->>A: Confirm device + enter customer info + notes
    F->>A: Submit ticket
    A->>DB: INSERT ticket #D1-042 (status: RECEIVED)
    DB-->>A: Ticket created
    A->>WA: Send notification: "Ticket #D1-042 created"
    WA-->>C: WhatsApp: "Your ticket #D1-042 is created"

    Note over DB,T: Device sits on bench, technician sees it in queue

    T->>A: Open queue, tap ticket #D1-042
    A->>DB: SELECT ticket #D1-042 with device + customer
    DB-->>A: Ticket details
    T->>A: Tap "Start Assessment"
    A->>DB: UPDATE status to ASSESSING
    T->>A: Inspect device
    A->>AI: POST /ai/suggest-diagnosis (device + symptoms)
    AI-->>A: { suggestions: [Screen (95%), Digitizer (4%), MB (1%)] }
    T->>A: Select diagnosis, confirm parts & price
    A->>DB: UPDATE assessment + status to ASSESSED
    A->>WA: Send assessment notification with quote
    WA-->>C: WhatsApp: "Assessment complete. RM132. Reply YES"

    C-->>WA: Reply: "YA"
    WA->>A: POST /webhooks/whatsapp (customer reply)
    A->>DB: UPDATE status to APPROVED
    A->>WA: Send confirmation
    WA-->>C: "Approved! Repair in progress"

    T->>A: Start repair, update status IN_PROGRESS
    T->>A: Complete repair, update status COMPLETED
    A->>WA: Send pickup notification
    WA-->>C: "Your device is ready for pickup!"

    C->>F: Collects device
    F->>A: Mark as PICKED_UP
    A->>DB: UPDATE status to PICKED_UP
```

---

## 3. Single-Person Flow (Technician Does Everything)

```mermaid
sequenceDiagram
    actor C as Customer
    actor T as Technician
    participant A as Mobile App
    participant AI as OpenAI
    participant DB as Database
    participant WA as WhatsApp API

    C->>T: "My phone won't turn on"
    T->>A: Open app, tap "New Ticket"
    T->>A: Snap photo
    A->>AI: Identify device from photo
    AI-->>A: { device: "iPhone 12", confidence: 0.91 }
    T->>A: Enter customer info + notes ("won't turn on, says battery")
    T->>A: Submit (status: RECEIVED)

    Note over A: Same person, no handoff needed

    T->>A: App shows: "Assess now or later?"
    T->>A: Tap "Assess Now"
    A->>DB: UPDATE status ASSESSING

    Note over T: Inspects device physically

    A->>AI: POST /ai/suggest-diagnosis (device + symptoms)
    AI-->>A: { suggestions: [Battery (40%), Charging (25%), MB (20%), PowerBtn (15%)] }
    T->>A: Physical inspection reveals burnt capacitor
    T->>A: Type actual diagnosis: "Motherboard short, capacitor C321 burnt"
    T->>A: Select parts: capacitor pack + thermal paste
    A->>DB: Calculate price: RM289
    T->>A: Confirm, save assessment
    A->>DB: UPDATE status ASSESSED
    A->>WA: Send quote to customer
    WA-->>C: WhatsApp: "Quote: RM289. Reply YES"

    C-->>WA: "YA"
    WA->>A: Webhook received
    A->>DB: UPDATE status APPROVED
    A->>WA: Confirmation sent
    WA-->>C: "Approved. 3-5 days."

    T->>A: Proceed with repair
    A->>DB: UPDATE status IN_PROGRESS
    T->>A: Complete repair
    A->>DB: UPDATE status COMPLETED
    A->>WA: Pickup notification
    WA-->>C: "Ready for pickup!"
```

---

## 4. WhatsApp Notification Flow

```mermaid
flowchart TD
    A[Customer gives phone number at intake] --> B[System stores number in ticket]
    B --> C{Ticket status change triggers notification?}
    
    C -->|RECEIVED| D[Send: ticket_created<br/>"Tiket #D1-042 dicipta"]
    C -->|ASSESSED| E[Send: assessment_complete<br/>with quote, ask YES/NO]
    C -->|APPROVED| F[Send: approval_confirmed<br/>"Pembaikan dijalankan"]
    C -->|COMPLETED| G[Send: ready_for_pickup<br/>"Sedia untuk diambil"]
    C -->|3 days after COMPLETED| H[Send: pickup_reminder<br/>"Peringatan: belum diambil"]
    
    D --> I[Customer receives WhatsApp]
    E --> J{Customer replies?}
    F --> I
    G --> I
    H --> I
    
    J -->|YES / YA| K[Auto-approve ticket<br/>Update DB: APPROVED]
    J -->|NO / TIDAK| L[Mark declined<br/>Update DB: CANCELLED<br/>Notify staff]
    J -->|Question (contains ?)| M[Forward to staff<br/>app notification<br/>for manual reply]
    J -->|Unrecognized text| N[Forward to staff<br/>for manual review]
    
    K --> O[Send confirmation<br/>"Diluluskan!"]
    L --> P[Send cancellation<br/>"Dibatalkan"]
```

---

## 5. AI Vision + Diagnosis Pipeline

```mermaid
flowchart LR
    A[Staff takes photo] --> B[Compress to 800px max edge]
    B --> C[Upload to Supabase Storage]
    C --> D[Get public URL]
    D --> E[POST /api/ai/identify-device]
    E --> F[OpenAI GPT-4o Vision<br/>"What device is in this photo?"]
    F --> G{Confidence > 0.8?}
    
    G -->|Yes| H[Match to device catalog<br/>by brand + model]
    G -->|No| I[Return "uncertain"<br/>with best guess]
    
    H --> J{Found in catalog?}
    J -->|Yes| K[Return device_id + model + confidence]
    J -->|No| L[Return suggestions +<br/>prompt "add to catalog"]
    I --> M[App shows manual search<br/>with AI guess as hint]
    
    K --> N[Staff confirms or edits device]
    L --> N
    M --> N
    
    N --> O[Staff enters customer notes]
    O --> P[POST /api/ai/suggest-diagnosis]
    P --> Q[OpenAI GPT-4o<br/>"Based on device X and symptoms Y,<br/>suggest likely diagnoses with parts and time"]
    Q --> R[Return ranked suggestions<br/>with parts and labor estimates]
    R --> S[Technician selects + refines]
    S --> T[System auto-fills parts list<br/>and calculates total price]
    T --> U[Technician approves final quote]
    U --> V[WhatsApp notification to customer]
```

---

## 6. Offline Sync Flow (Month 5)

```mermaid
flowchart TD
    A[Staff creates ticket] --> B{Internet available?}
    
    B -->|Yes| C[Normal flow:<br/>POST /api/tickets<br/>Insert to DB]
    B -->|No| D[Store in AsyncStorage<br/>Ticket ID: TMP-001]
    
    D --> E[Show pending sync badge<br/>"4 tiket menunggu sync"]
    
    E --> F{Internet restored?}
    F -->|Yes| G[Background sync process starts]
    F -->|No| E
    
    G --> H[Process TMP tickets<br/>in FIFO order]
    H --> I{Conflict?}
    
    I -->|No| J[POST to API,<br/>convert TMP to real ticket]
    I -->|Yes| K[Show conflict<br/>resolution dialog]
    
    J --> L[Clear from AsyncStorage]
    K --> M[User resolves,<br/>then sync]
    M --> J
    
    L --> N{More TMP tickets?}
    N -->|Yes| H
    N -->|No| O[Sync complete<br/>Hidden badge]
```

---

## 7. Role-Based Authorization

```mermaid
flowchart TD
    A[API Request with JWT] --> B{Valid token?}
    B -->|No| C1[401 Unauthorized]
    B -->|Yes| D{Extract role + tenant + outlet}
    
    D --> E{Endpoint requires role?}
    E -->|admin| F{role = owner OR manager?}
    E -->|technician| G{role = technician?}
    E -->|staff| H{role = front_desk OR technician?}
    E -->|owner| I{role = owner?}
    E -->|any| J[Allow]
    
    F -->|No| C2[403 Forbidden]
    F -->|Yes| K{Tenant isolation check}
    
    G -->|No| C2
    G -->|Yes| K
    
    H -->|No| C2
    H -->|Yes| K
    
    I -->|No| C2
    I -->|Yes| K
    
    K --> L{Data belongs to user's tenant?}
    L -->|No| C2
    L -->|Yes| M{Outlet restriction?}
    
    M -->|owner/manager| N[Allow — full tenant access]
    M -->|front_desk| O{Outlet matches user's outlet?}
    M -->|technician| P{Ticket assigned or unassigned?}
    
    O -->|No| C2
    O -->|Yes| N
    P -->|No| C2
    P -->|Yes| N
```
