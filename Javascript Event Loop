# 🔄 Complete JavaScript Event Loop Guide
**Call Stack, Web APIs, Callback Queue, Microtasks — Explained with Moving Diagrams**

---

## 📊 Visual Diagrams

### 1. The Big Picture: JavaScript Runtime Architecture

```mermaid
graph TB
    subgraph "JavaScript Engine (V8/SpiderMonkey)"
        CS[📚 Call Stack<br/>Executes code<br/>one function at a time]
        MH[🧠 Memory Heap<br/>Stores objects,<br/>variables, functions]
    end

    subgraph "Browser / Node.js Environment"
        WA[🌐 Web APIs<br/>setTimeout, fetch,<br/>DOM events, etc.]
        MQ[⚡ Microtask Queue<br/>Promises, queueMicrotask,<br/>MutationObserver]
        CQ[📋 Callback Queue<br/>setTimeout, setInterval,<br/>I/O, UI events]
    end

    EL[🔄 Event Loop<br/>Checks: Stack empty?<br/>→ Microtasks first<br/>→ Then Callbacks]

    CS -->|Calls Web APIs| WA
    WA -->|Promise resolved| MQ
    WA -->|Timer/event done| CQ
    EL -->|Stack empty? Push microtask| CS
    EL -->|No microtasks? Push callback| CS
    MQ -->|Higher Priority| EL
    CQ -->|Lower Priority| EL

    style CS fill:#ff9999
    style MH fill:#99ccff
    style WA fill:#ffcc99
    style MQ fill:#99ff99
    style CQ fill:#ffff99
    style EL fill:#cc99ff
```

---

### 2. Event Loop — Step-by-Step Flow

```mermaid
flowchart TD
    A[📝 Script starts executing] --> B[Push to Call Stack]
    B --> C{Is it synchronous?}
    
    C -->|Yes| D[Execute immediately<br/>on Call Stack]
    C -->|No − async| E{What type?}
    
    E -->|setTimeout/setInterval| F[Send to Web API Timer]
    E -->|fetch/Promise| G[Send to Web API]
    E -->|DOM event listener| H[Send to Web API Events]
    
    F -->|Timer expires| I[Callback → Callback Queue]
    G -->|Promise resolves| J[.then → Microtask Queue]
    H -->|Event fires| I
    
    D --> K{Call Stack empty?}
    K -->|No| B
    K -->|Yes| L{Microtask Queue<br/>has tasks?}
    
    L -->|Yes| M[Execute ALL microtasks<br/>until queue is empty]
    M --> L
    L -->|No| N{Callback Queue<br/>has tasks?}
    
    N -->|Yes| O[Execute ONE callback<br/>push to Call Stack]
    O --> K
    N -->|No| P[🔄 Wait for new tasks<br/>Keep checking...]
    P --> L

    style A fill:#e1f5fe
    style D fill:#c8e6c9
    style J fill:#a5d6a7
    style I fill:#fff9c4
    style M fill:#a5d6a7
    style O fill:#fff9c4
    style P fill:#e1bee7
```

---

### 3. Microtask vs Macrotask Priority

```mermaid
sequenceDiagram
    participant CS as 📚 Call Stack
    participant MT as ⚡ Microtask Queue
    participant CT as 📋 Callback Queue
    participant EL as 🔄 Event Loop

    Note over CS: console.log("Start")
    CS->>CS: Execute: "Start"
    
    Note over CS: setTimeout(() => {...}, 0)
    CS->>CT: Callback → Callback Queue
    
    Note over CS: Promise.resolve().then(...)
    CS->>MT: .then → Microtask Queue
    
    Note over CS: console.log("End")
    CS->>CS: Execute: "End"
    
    Note over CS: ✅ Call Stack is EMPTY
    
    EL->>MT: Any microtasks?
    MT-->>EL: Yes! Promise callback
    EL->>CS: Push Promise callback
    CS->>CS: Execute: "Promise"
    
    EL->>MT: Any more microtasks?
    MT-->>EL: No, empty
    
    EL->>CT: Any callbacks?
    CT-->>EL: Yes! setTimeout callback
    EL->>CS: Push setTimeout callback
    CS->>CS: Execute: "Timeout"

    Note over CS,CT: Output: Start → End → Promise → Timeout
```

---

### 4. setTimeout(fn, 0) — Why It's NOT Instant

```mermaid
sequenceDiagram
    participant Code as 📝 Your Code
    participant CS as 📚 Call Stack
    participant WA as 🌐 Web API
    participant CQ as 📋 Callback Queue
    participant EL as 🔄 Event Loop

    Code->>CS: console.log("First")
    CS->>CS: ✅ Print "First"
    
    Code->>CS: setTimeout(fn, 0)
    CS->>WA: Register timer (0ms)
    WA->>WA: Timer fires immediately
    WA->>CQ: Callback added to queue
    Note over CQ: Waiting... Stack not empty yet
    
    Code->>CS: console.log("Second")
    CS->>CS: ✅ Print "Second"
    
    Note over CS: Stack is now empty!
    
    EL->>CQ: Any callbacks?
    CQ-->>EL: Yes!
    EL->>CS: Push callback
    CS->>CS: ✅ Print "Third"

    Note over CS,CQ: Output: First → Second → Third<br/>Even though timeout was 0ms!
```

---

### 5. Nested Promises & Microtask Chaining

```mermaid
sequenceDiagram
    participant CS as 📚 Call Stack
    participant MT as ⚡ Microtask Queue
    participant CT as 📋 Callback Queue
    participant EL as 🔄 Event Loop

    Note over CS: Script starts
    CS->>CS: log("1")
    CS->>CT: setTimeout → log("2")
    CS->>MT: Promise.then → log("3")
    CS->>MT: Promise.then → log("4")
    CS->>CS: log("5")
    
    Note over CS: ✅ Stack empty — Process ALL microtasks
    
    EL->>CS: Microtask: log("3")
    CS->>CS: ✅ Print "3"
    Note over MT: 💡 If this .then returns a<br/>new Promise, its .then goes<br/>BACK to microtask queue
    
    EL->>CS: Microtask: log("4")
    CS->>CS: ✅ Print "4"
    
    Note over MT: Microtask queue empty ✅
    
    EL->>CS: Callback: log("2")
    CS->>CS: ✅ Print "2"

    Note over CS,CT: Output: 1 → 5 → 3 → 4 → 2
```

---

### 6. async/await Under the Hood

```mermaid
sequenceDiagram
    participant CS as 📚 Call Stack
    participant MT as ⚡ Microtask Queue
    participant EL as 🔄 Event Loop

    Note over CS: async function foo()
    CS->>CS: log("foo start")
    
    Note over CS: await bar() hit!<br/>Everything AFTER await<br/>becomes a microtask
    CS->>CS: Execute bar() synchronously
    CS->>CS: log("bar")
    CS->>MT: Rest of foo() → Microtask Queue
    Note over MT: log("foo end") waiting...
    
    Note over CS: Back to main script
    CS->>CS: log("script end")
    
    Note over CS: ✅ Stack empty
    EL->>CS: Microtask: resume foo()
    CS->>CS: log("foo end")

    Note over CS,MT: Output: foo start → bar → script end → foo end
```

---

### 7. Complete Execution Model (Everything Together)

```mermaid
graph TB
    subgraph "EXECUTION ORDER"
        direction TB
        S1["1️⃣ Synchronous Code<br/>Runs first, top to bottom<br/>On the Call Stack"]
        S2["2️⃣ Microtasks<br/>ALL processed before any macrotask<br/>Promise.then, await resume,<br/>queueMicrotask, MutationObserver"]
        S3["3️⃣ ONE Macrotask<br/>Then check microtasks again<br/>setTimeout, setInterval,<br/>I/O, UI rendering, events"]
        S4["4️⃣ Render<br/>Browser may repaint<br/>requestAnimationFrame"]
        S5["🔄 Repeat from Step 2"]
    end

    S1 --> S2
    S2 --> S3
    S3 --> S4
    S4 --> S5
    S5 --> S2

    style S1 fill:#ff9999
    style S2 fill:#99ff99
    style S3 fill:#ffff99
    style S4 fill:#99ccff
    style S5 fill:#e1bee7
```

---

### 8. Blocking vs Non-Blocking

```mermaid
graph LR
    subgraph "❌ BLOCKING (Bad)"
        B1[Start] --> B2[Heavy Loop<br/>5 seconds ⏳]
        B2 --> B3[UI Frozen! 🥶<br/>Can't click anything]
        B3 --> B4[Loop Done]
        B4 --> B5[UI Responsive Again]
    end

    subgraph "✅ NON-BLOCKING (Good)"
        NB1[Start] --> NB2[Start fetch API 🌐]
        NB2 --> NB3[UI Still Works! ✨<br/>User can interact]
        NB2 --> NB4[Response arrives]
        NB4 --> NB5[Process response<br/>via callback/promise]
    end

    style B2 fill:#ff9999
    style B3 fill:#ff9999
    style NB3 fill:#99ff99
    style NB5 fill:#99ff99
```

---

### 9. Real Scenario: fetch + DOM + setTimeout Interaction

```mermaid
sequenceDiagram
    participant CS as 📚 Call Stack
    participant WA as 🌐 Web APIs
    participant MT as ⚡ Microtask Queue
    participant CT as 📋 Callback Queue
    participant EL as 🔄 Event Loop

    Note over CS: === SCRIPT STARTS ===
    CS->>CS: 1. log("🚀 App Start")
    
    CS->>WA: 2. setTimeout(timerCB, 0)
    CS->>WA: 3. fetch("/api/users")
    CS->>WA: 4. button.addEventListener("click", clickCB)
    
    CS->>CS: 5. log("📝 Script Done")
    Note over CS: ✅ Stack empty
    
    WA->>CT: Timer expires → timerCB to Callback Queue
    WA->>MT: fetch resolves → .then to Microtask Queue
    
    Note over EL: Step 1: Process ALL Microtasks
    EL->>CS: fetch .then callback
    CS->>CS: 6. log("📦 Data received")
    
    Note over EL: Step 2: Process ONE Macrotask
    EL->>CS: setTimeout callback
    CS->>CS: 7. log("⏰ Timer fired")
    
    Note over EL: User clicks button later...
    WA->>CT: Click event → clickCB to Callback Queue
    EL->>CS: Click callback
    CS->>CS: 8. log("🖱️ Button clicked")

    Note over CS,CT: Output: 🚀 App Start → 📝 Script Done →<br/>📦 Data received → ⏰ Timer fired →<br/>🖱️ Button clicked (when user clicks)
```

---

### 10. Promise Chain vs setTimeout Race

```mermaid
sequenceDiagram
    participant CS as 📚 Call Stack
    participant MT as ⚡ Microtask Queue
    participant CT as 📋 Callback Queue
    participant EL as 🔄 Event Loop

    Note over CS: Complex nested example
    CS->>CS: log("A")
    CS->>CT: setTimeout → log("B")
    CS->>MT: Promise.then → log("C")
    
    Note over CS: Inside the Promise.then("C"):
    Note over MT: When "C" runs, it creates<br/>ANOTHER Promise.then → log("D")
    
    CS->>CT: setTimeout → log("E")
    CS->>MT: Promise.then → log("F")
    CS->>CS: log("G")

    Note over CS: ✅ Stack empty — Microtasks first!
    EL->>CS: Microtask: log("C")
    CS->>MT: C creates: Promise.then → log("D")
    EL->>CS: Microtask: log("F")
    EL->>CS: Microtask: log("D")
    Note over MT: ✅ All microtasks done
    
    EL->>CS: Callback: log("B")
    EL->>CS: Callback: log("E")

    Note over CS,CT: Output: A → G → C → F → D → B → E
```

---

## 🎓 Detailed Explanations with Code Examples

### **Example 1: The Classic — Proving JavaScript is Single-Threaded**

```javascript
console.log("1 - Start");

setTimeout(function() {
    console.log("2 - setTimeout");
}, 0);

Promise.resolve().then(function() {
    console.log("3 - Promise");
});

console.log("4 - End");
```

**Output:**
```
1 - Start
4 - End
3 - Promise
2 - setTimeout
```

**Why this order?**
| Step | What Happens | Where |
|------|-------------|-------|
| 1 | `console.log("1 - Start")` runs | Call Stack (sync) |
| 2 | `setTimeout` registered | Sent to Web API → Callback Queue |
| 3 | `Promise.resolve().then()` registered | → Microtask Queue |
| 4 | `console.log("4 - End")` runs | Call Stack (sync) |
| 5 | Stack empty → process microtasks | `"3 - Promise"` runs |
| 6 | Microtasks empty → process callbacks | `"2 - setTimeout"` runs |

---

### **Example 2: Multiple Promises vs Multiple Timeouts**

```javascript
console.log("Start");

setTimeout(() => console.log("Timeout 1"), 0);
setTimeout(() => console.log("Timeout 2"), 0);

Promise.resolve().then(() => console.log("Promise 1"));
Promise.resolve().then(() => console.log("Promise 2"));

console.log("End");
```

**Output:**
```
Start
End
Promise 1
Promise 2
Timeout 1
Timeout 2
```

**Key Rule:** ALL microtasks run before ANY macrotask. Both promises run before either timeout.

---

### **Example 3: Chained Promises Create More Microtasks**

```javascript
console.log("Start");

setTimeout(() => console.log("Timeout"), 0);

Promise.resolve()
    .then(() => {
        console.log("Promise 1");
        return Promise.resolve();
    })
    .then(() => {
        console.log("Promise 2");
    });

Promise.resolve().then(() => console.log("Promise 3"));

console.log("End");
```

**Output:**
```
Start
End
Promise 1
Promise 3
Promise 2
Timeout
```

**Why Promise 3 comes before Promise 2?**
- `Promise 1` and `Promise 3` are both queued as microtasks initially
- `Promise 1` runs first (queued first), creates a new promise → `Promise 2` goes to *end* of microtask queue  
- `Promise 3` runs next (it was already in queue before `Promise 2`)
- `Promise 2` runs last among microtasks
- `Timeout` runs after all microtasks are done

---

### **Example 4: async/await Execution Order**

```javascript
async function foo() {
    console.log("foo start");
    await bar();
    console.log("foo end");         // This becomes a microtask!
}

async function bar() {
    console.log("bar");
}

console.log("script start");
foo();
console.log("script end");
```

**Output:**
```
script start
foo start
bar
script end
foo end
```

**Step-by-Step Breakdown:**

```
📚 Call Stack                    ⚡ Microtask Queue

1. log("script start")          (empty)
   → prints "script start"

2. foo() called                  (empty)
   → prints "foo start"

3. await bar()                   (empty)
   → bar() runs SYNCHRONOUSLY
   → prints "bar"
   → await pauses foo()
   → rest of foo() → microtask    ["foo end"]

4. log("script end")            ["foo end"]
   → prints "script end"

5. Stack empty! Process microtasks
   → resume foo()               (empty)
   → prints "foo end"
```

---

### **Example 5: The Tricky Nested setTimeout + Promise**

```javascript
console.log("1");

setTimeout(() => {
    console.log("2");
    Promise.resolve().then(() => console.log("3"));
}, 0);

Promise.resolve().then(() => {
    console.log("4");
    setTimeout(() => console.log("5"), 0);
});

console.log("6");
```

**Output:**
```
1
6
4
2
3
5
```

**Visualization:**
```
PHASE 1: Synchronous
─────────────────────────────────
Call Stack: log("1") → log("6")
Callback Queue: [setTimeout-A]
Microtask Queue: [Promise-A]

PHASE 2: Microtasks (drain ALL)
─────────────────────────────────
Execute Promise-A → log("4")
  └─ Creates setTimeout-B → Callback Queue
Callback Queue: [setTimeout-A, setTimeout-B]
Microtask Queue: (empty)

PHASE 3: ONE Macrotask
─────────────────────────────────
Execute setTimeout-A → log("2")
  └─ Creates Promise-B → Microtask Queue
Microtask Queue: [Promise-B]

PHASE 4: Microtasks again (before next macrotask!)
─────────────────────────────────
Execute Promise-B → log("3")
Microtask Queue: (empty)

PHASE 5: Next Macrotask
─────────────────────────────────
Execute setTimeout-B → log("5")
```

---

### **Example 6: queueMicrotask**

```javascript
console.log("Start");

queueMicrotask(() => console.log("Microtask 1"));

setTimeout(() => console.log("Timeout 1"), 0);

queueMicrotask(() => console.log("Microtask 2"));

Promise.resolve().then(() => console.log("Promise 1"));

console.log("End");
```

**Output:**
```
Start
End
Microtask 1
Microtask 2
Promise 1
Timeout 1
```

**`queueMicrotask`** and **`Promise.then`** both go into the **same microtask queue**. They run in the order they were added, and ALL run before any setTimeout callback.

---

### **Example 7: requestAnimationFrame Position**

```javascript
console.log("Start");

requestAnimationFrame(() => console.log("rAF"));

setTimeout(() => console.log("Timeout"), 0);

Promise.resolve().then(() => console.log("Promise"));

console.log("End");
```

**Output (typical):**
```
Start
End
Promise
Timeout
rAF          ← runs before next repaint, usually after macrotasks
```

> **Note:** `requestAnimationFrame` timing depends on the browser's repaint cycle (~16ms at 60fps). It typically runs after microtasks and before or after the next macrotask, depending on when the browser schedules a paint.

---

### **Example 8: Microtask Starvation (Dangerous!)**

```javascript
// ⚠️ WARNING: This will FREEZE the browser!
function endlessPromise() {
    Promise.resolve().then(() => {
        console.log("microtask");
        endlessPromise(); // Creates another microtask FOREVER
    });
}

endlessPromise();
setTimeout(() => console.log("I will NEVER run"), 0);
```

**What happens?**
- Each microtask creates a new microtask
- The Event Loop ALWAYS drains ALL microtasks before processing macrotasks
- The setTimeout callback will **never** run
- The browser tab will **freeze** because it never gets to render

```
⚡ Microtask Queue: [task1]
→ Execute task1 → creates task2
⚡ Microtask Queue: [task2]
→ Execute task2 → creates task3
⚡ Microtask Queue: [task3]
→ ... INFINITE LOOP! Never reaches Callback Queue or Render
```

---

### **Example 9: The Interview Favorite — Complex Mix**

```javascript
console.log("1");

setTimeout(() => {
    console.log("2");
    new Promise(resolve => {
        console.log("3");
        resolve();
    }).then(() => {
        console.log("4");
    });
}, 0);

new Promise(resolve => {
    console.log("5");
    resolve();
}).then(() => {
    console.log("6");
}).then(() => {
    console.log("7");
});

setTimeout(() => {
    console.log("8");
}, 0);

console.log("9");
```

**Output:**
```
1
5
9
6
7
2
3
4
8
```

**Complete Breakdown:**

```
═══════════════════════════════════════════════════════
SYNCHRONOUS EXECUTION (Call Stack)
═══════════════════════════════════════════════════════
• log("1")              → prints 1
• setTimeout(cbA, 0)    → cbA → Callback Queue
• new Promise(executor) → executor runs SYNCHRONOUSLY!
  └─ log("5")           → prints 5
  └─ resolve()          → .then(log "6") → Microtask Queue
• setTimeout(cbB, 0)    → cbB → Callback Queue
• log("9")              → prints 9

Stack: EMPTY
Microtask Queue: [ log("6") ]
Callback Queue:  [ cbA, cbB ]

═══════════════════════════════════════════════════════
MICROTASK PHASE — Drain ALL
═══════════════════════════════════════════════════════
• Execute log("6")      → prints 6
  └─ .then(log "7")     → Microtask Queue
• Execute log("7")      → prints 7

Microtask Queue: EMPTY ✅

═══════════════════════════════════════════════════════
MACROTASK PHASE — ONE at a time
═══════════════════════════════════════════════════════
• Execute cbA:
  └─ log("2")           → prints 2
  └─ new Promise:
     └─ log("3")        → prints 3 (sync inside Promise constructor!)
     └─ resolve()       → .then(log "4") → Microtask Queue

── Microtask check! ──
  └─ log("4")           → prints 4

• Execute cbB:
  └─ log("8")           → prints 8

═══════════════════════════════════════════════════════
FINAL OUTPUT:  1 → 5 → 9 → 6 → 7 → 2 → 3 → 4 → 8
═══════════════════════════════════════════════════════
```

---

### **Example 10: async/await + setTimeout + Promise — Ultimate Challenge**

```javascript
async function async1() {
    console.log("async1 start");
    await async2();
    console.log("async1 end");
}

async function async2() {
    console.log("async2");
}

console.log("script start");

setTimeout(() => console.log("setTimeout"), 0);

async1();

new Promise(resolve => {
    console.log("promise1");
    resolve();
}).then(() => {
    console.log("promise2");
});

console.log("script end");
```

**Output:**
```
script start
async1 start
async2
promise1
script end
async1 end
promise2
setTimeout
```

**Step-by-step:**

```
SYNCHRONOUS:
─────────────────────────────────
1. log("script start")           → prints "script start"
2. setTimeout registered          → to Callback Queue
3. async1() called:
   a. log("async1 start")        → prints "async1 start"
   b. await async2():
      - async2() runs sync       → prints "async2"
      - rest of async1 → Microtask Queue  ["async1 end"]
4. new Promise constructor:
   a. log("promise1")            → prints "promise1" (sync!)
   b. resolve() → .then          → Microtask Queue  ["async1 end", "promise2"]
5. log("script end")             → prints "script end"

MICROTASKS:
─────────────────────────────────
6. "async1 end"                  → prints "async1 end"
7. "promise2"                    → prints "promise2"

MACROTASKS:
─────────────────────────────────
8. "setTimeout"                  → prints "setTimeout"
```

---

## 🧠 The Golden Rules

### Rule 1: Synchronous Code Always Runs First
```
✅ All synchronous code completes before ANY async callback
```

### Rule 2: Promise Constructor is Synchronous
```javascript
new Promise(resolve => {
    console.log("I run IMMEDIATELY!");  // ← Synchronous!
    resolve();
}).then(() => {
    console.log("I'm async");           // ← Microtask
});
```

### Rule 3: ALL Microtasks Before ANY Macrotask
```
⚡ Microtask Queue is FULLY drained before 📋 Callback Queue is touched
```

### Rule 4: After Each Macrotask, Check Microtasks Again
```
Sync → ALL Microtasks → ONE Macrotask → ALL Microtasks → ONE Macrotask → ...
```

### Rule 5: await Splits the Function
```javascript
async function example() {
    // BEFORE await = synchronous
    console.log("sync part");
    
    await something();
    
    // AFTER await = microtask (like .then)
    console.log("async part");
}
```

---

## 📋 Microtask vs Macrotask Cheat Sheet

```mermaid
graph LR
    subgraph "⚡ MICROTASKS (Higher Priority)"
        M1[Promise.then / catch / finally]
        M2[await resume]
        M3[queueMicrotask]
        M4[MutationObserver]
    end

    subgraph "📋 MACROTASKS (Lower Priority)"
        C1[setTimeout]
        C2[setInterval]
        C3[setImmediate − Node.js]
        C4[I/O operations]
        C5[UI rendering events]
        C6[DOM events − click, input]
        C7[MessageChannel]
        C8[requestAnimationFrame *]
    end

    style M1 fill:#a5d6a7
    style M2 fill:#a5d6a7
    style M3 fill:#a5d6a7
    style M4 fill:#a5d6a7
    style C1 fill:#fff9c4
    style C2 fill:#fff9c4
    style C3 fill:#fff9c4
    style C4 fill:#fff9c4
    style C5 fill:#fff9c4
    style C6 fill:#fff9c4
    style C7 fill:#fff9c4
    style C8 fill:#fff9c4
```

> \* `requestAnimationFrame` runs before repaint, between macrotasks

---

## 💡 Easy Memory Tricks

- **Call Stack** = "**Chef's Kitchen**" — only one dish at a time, LIFO (last order prepared first)
- **Web APIs** = "**Delivery Services**" — send orders out, they come back when ready
- **Microtask Queue** = "**VIP Line**" — always served before regular queue, ALL VIPs first
- **Callback Queue** = "**Regular Line**" — served one at a time, only when VIP line is empty
- **Event Loop** = "**Doorman**" — checks: Kitchen empty? → VIP line first → then Regular line

---

## 🎯 Quick Decision Guide: When Does My Code Run?

| Code | Type | Queue | Priority |
|------|------|-------|----------|
| `console.log()` | Synchronous | Call Stack | 🔴 Immediate |
| `Promise.then()` | Microtask | Microtask Queue | 🟢 After sync, before macrotask |
| `await` resume | Microtask | Microtask Queue | 🟢 After sync, before macrotask |
| `queueMicrotask()` | Microtask | Microtask Queue | 🟢 After sync, before macrotask |
| `setTimeout(fn, 0)` | Macrotask | Callback Queue | 🟡 After ALL microtasks |
| `setInterval()` | Macrotask | Callback Queue | 🟡 After ALL microtasks |
| `fetch().then()` | Microtask | Microtask Queue | 🟢 When response arrives |
| DOM event callback | Macrotask | Callback Queue | 🟡 When event fires |
| `requestAnimationFrame` | Special | Before repaint | 🔵 Before next paint |
| `new Promise(executor)` | **Synchronous!** | Call Stack | 🔴 Immediate |

---

## 🔬 Node.js Event Loop (Bonus)

```mermaid
graph TD
    subgraph "Node.js Event Loop Phases"
        T[Timers<br/>setTimeout, setInterval]
        PI[Pending I/O Callbacks<br/>System-level callbacks]
        IP[Idle / Prepare<br/>Internal use]
        PO[Poll<br/>Retrieve I/O events,<br/>execute callbacks]
        CH[Check<br/>setImmediate]
        CL[Close Callbacks<br/>socket.on close]
    end

    subgraph "Between EVERY phase"
        MQ[⚡ process.nextTick queue<br/>⚡ Promise microtask queue]
    end

    T --> PI --> IP --> PO --> CH --> CL --> T
    MQ -.->|Runs between<br/>every phase| T

    style T fill:#ff9999
    style PO fill:#99ccff
    style CH fill:#99ff99
    style MQ fill:#c8e6c9
```

### Node.js Priority Order:
```
1. process.nextTick()      ← Highest priority (even above Promise)
2. Promise.then()          ← Microtask
3. setTimeout(fn, 0)       ← Timers phase
4. setImmediate()          ← Check phase
5. I/O callbacks           ← Poll phase
```

```javascript
// Node.js specific example
setTimeout(() => console.log("setTimeout"), 0);
setImmediate(() => console.log("setImmediate"));
process.nextTick(() => console.log("nextTick"));
Promise.resolve().then(() => console.log("Promise"));
console.log("sync");

// Output:
// sync
// nextTick
// Promise
// setTimeout (or setImmediate — order varies here!)
// setImmediate (or setTimeout)
```

---

## 🧪 Test Yourself — Predict the Output!

### Challenge 1: Warm Up
```javascript
setTimeout(() => console.log("A"), 0);
Promise.resolve().then(() => console.log("B"));
console.log("C");
```
<details>
<summary>Click for answer</summary>

```
C → B → A
```
Sync first (C), then microtask (B), then macrotask (A).
</details>

---

### Challenge 2: Medium
```javascript
console.log("1");
setTimeout(() => console.log("2"), 0);
Promise.resolve().then(() => {
    console.log("3");
    setTimeout(() => console.log("4"), 0);
});
Promise.resolve().then(() => console.log("5"));
console.log("6");
```
<details>
<summary>Click for answer</summary>

```
1 → 6 → 3 → 5 → 2 → 4
```
Sync: 1, 6. Microtasks: 3 (creates timeout for "4"), 5. Macrotasks: 2, 4.
</details>

---

### Challenge 3: Hard
```javascript
async function a() {
    console.log("a1");
    await b();
    console.log("a2");
}
async function b() {
    console.log("b1");
    await c();
    console.log("b2");
}
async function c() {
    console.log("c1");
}
console.log("start");
a();
console.log("end");
```
<details>
<summary>Click for answer</summary>

```
start → a1 → b1 → c1 → end → b2 → a2
```
- `start` (sync), `a1` (sync entering a), `b1` (sync entering b), `c1` (sync in c)
- `await c()` pauses b → `b2` becomes microtask
- `await b()` pauses a → `a2` queued after `b2` resolves
- `end` (sync), then microtasks: `b2`, then `a2`
</details>

---

### Challenge 4: Expert
```javascript
console.log("1");

setTimeout(() => {
    console.log("2");
    Promise.resolve().then(() => console.log("3"));
});

new Promise(resolve => {
    console.log("4");
    setTimeout(() => console.log("5"));
    resolve();
}).then(() => {
    console.log("6");
    return new Promise(resolve => {
        console.log("7");
        resolve();
    }).then(() => console.log("8"));
}).then(() => console.log("9"));

console.log("10");
```
<details>
<summary>Click for answer</summary>

```
1 → 4 → 10 → 6 → 7 → 8 → 9 → 2 → 3 → 5
```
**Sync:** 1, 4 (Promise constructor), 10  
**Microtasks:** 6 (first .then) → 7 (sync in new Promise) → 8 (.then of inner) → 9 (.then of outer chain)  
**Macrotasks:** 2 → 3 (microtask created inside) → 5
</details>

---

## 📝 Summary

| Concept | Key Point |
|---------|-----------|
| **Call Stack** | LIFO — executes one function at a time, sync only |
| **Web APIs** | Browser handles timers, fetch, events in background threads |
| **Microtask Queue** | Promises, await, queueMicrotask — ALL drain before macrotasks |
| **Callback Queue** | setTimeout, events — ONE processed, then check microtasks |
| **Event Loop** | Orchestrates everything: Sync → ALL Microtasks → ONE Macrotask → repeat |
| **Promise constructor** | The executor function runs SYNCHRONOUSLY |
| **await** | Everything after `await` becomes a microtask |
| **setTimeout(fn, 0)** | Not instant — goes to Callback Queue, waits for stack + microtasks |
| **Microtask starvation** | Infinite microtasks block macrotasks and rendering forever |

---

## 🎓 Key Takeaways

1. **JavaScript is single-threaded** — one thing at a time on the Call Stack
2. **Asynchronous ≠ Parallel** — it's concurrent via the Event Loop, not truly parallel
3. **Microtasks are VIP** — they ALWAYS cut the line ahead of macrotasks
4. **After EVERY macrotask**, the Event Loop checks for microtasks before the next macrotask
5. **Promise constructors are sync** — only `.then`/`.catch`/`.finally` are async
6. **`await` is just sugar** — it splits the function into sync (before) and microtask (after)
7. **`setTimeout(fn, 0)` isn't 0ms** — it means "as soon as possible after sync + microtasks"
8. **Don't starve the Event Loop** — avoid infinite microtask chains

---

*Created: February 17, 2026*
*Diagrams use Mermaid syntax — view in VS Code with a Mermaid preview extension or on GitHub.*
