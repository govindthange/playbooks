# Mermaid Synxtax

Ref: [Mermaid Synxtax](https://mermaid.ai/open-source/syntax/flowchart.html)

### New Node Synxtax

```mmd
node_name@{ img: "https://example.com/image.png", label: "Image Label", pos: "t", w: 60, h: 60, constraint: "off" }
```

### New Edge Synxtax

```mmd
  A edge_name@==> B
  edge_name@{ curve: linear, animate: true, animation: fast }
```

### Directions in Graph

```mmd
flowchart LR
  subgraph TOP
    direction TB
    subgraph B1
        direction RL
        i1 -->f1
    end
    subgraph B2
        direction BT
        i2 -->f2
    end
  end
  A --> TOP --> B
  B1 --> B2
```

> **Limitation:** If any of a subgraph's nodes are linked to the outside, subgraph direction will be ignored. Instead the subgraph will inherit the direction of the parent graph:

```mermaid
flowchart LR
  subgraph TOP
    direction TB
    subgraph B1
        direction RL
        i1 -->f1
    end
    subgraph B2
        direction BT
        i2 -->f2
    end
  end
  A --> TOP --> B
  B1 --> B2
```

### Mermaid Font Support

Supported prefixes: fa, fab, fas, far, fal, fad.

```mmd
flowchart TD
    B["fa:fa-twitter for peace"]
    B-->C[fa:fa-ban forbidden]
    B-->D(fa:fa-spinner)
    B-->E(A fa:fa-camera-retro perhaps?)
```

# Mermaid Shapes

Ref: [Mermaid Document](https://github.com/mermaid-js/mermaid/blob/develop/README.md) 

### 1. Standard & Process Shapes (Basic)

```mermaid
flowchart TD
    rect@{ shape: rect, label: "Process (rect)" }
    --> rounded@{ shape: rounded, label: "Event (rounded)" }
    --> stadium@{ shape: stadium, label: "Terminal Point (stadium)" }
    --> subproc@{ shape: fr-rect, label: "Subprocess (fr-rect)" }
    --> div_rect@{ shape: div-rect, label: "Divided Process (div-rect)" }
    --> lin_rect@{ shape: lin-rect, label: "Lined Process (lin-rect)" }
    --> tag_rect@{ shape: tag-rect, label: "Tagged Process (tag-rect)" }
    --> odd@{ shape: odd, label: "Odd Shape (odd)" }

```

### 2. Data & Input/Output Shapes (Data)

```mermaid
flowchart TD
    lean_r@{ shape: lean-r, label: "Data Input (lean-r)" }
    --> lean_l@{ shape: lean-l, label: "Data Output (lean-l)" }
    --> sl_rect@{ shape: sl-rect, label: "Manual Input (sl-rect)" }
    --> doc@{ shape: doc, label: "Document (doc)" }
    --> lin_doc@{ shape: lin-doc, label: "Lined Document (lin-doc)" }
    --> tag_doc@{ shape: tag-doc, label: "Tagged Document (tag-doc)" }
    --> docs@{ shape: docs, label: "Multi-Document (docs)" }
    --> st_rect@{ shape: st-rect, label: "Multi-Process (st-rect)" }
    --> flag@{ shape: flag, label: "Paper Tape (flag)" }
    --> curv_trap@{ shape: curv-trap, label: "Display (curv-trap)" }

```

### 3. Storage & Database Shapes (Storage)

```mermaid
flowchart TD
    cyl@{ shape: cyl, label: "Database (cyl)" }
    --> datastore@{ shape: datastore, label: "Data Store (datastore)" }
    --> h_cyl@{ shape: h-cyl, label: "Direct Access (h-cyl)" }
    --> lin_cyl@{ shape: lin-cyl, label: "Disk Storage (lin-cyl)" }
    --> win_pane@{ shape: win-pane, label: "Internal Storage (win-pane)" }
    --> bow_rect@{ shape: bow-rect, label: "Stored Data (bow-rect)" }
    --> bucket@{ shape: bucket, label: "Bucket (bucket)" }
    --> folder@{ shape: folder, label: "Folder (folder)" }
    --> flip_tri@{ shape: flip-tri, label: "Manual File (flip-tri)" }

```

### 4. Control, Flow & Special Shapes (Control)

```mermaid
flowchart TD
    diam@{ shape: diam, label: "Decision (diam)" }
    --> hex@{ shape: hex, label: "Prepare / Condition (hex)" }
    --> circle@{ shape: circle, label: "Start (circle)" }
    --> sm_circ@{ shape: sm-circ, label: "Small Start (sm-circ)" }
    --> dbl_circ@{ shape: dbl-circ, label: "Stop (dbl-circ)" }
    --> fr_circ@{ shape: fr-circ, label: "Framed Circle (fr-circ)" }
    --> cross_circ@{ shape: cross-circ, label: "Summary (cross-circ)" }
    --> trap_b@{ shape: trap-b, label: "Priority Action (trap-b)" }
    --> trap_t@{ shape: trap-t, label: "Manual Operation (trap-t)" }
    --> notch_pent@{ shape: notch-pent, label: "Loop Limit (notch-pent)" }
    --> delay@{ shape: delay, label: "Delay (delay)" }
    --> fork@{ shape: fork, label: "Fork/Join (fork)" }
    --> f_circ@{ shape: f-circ, label: "Junction (f-circ)" }
    --> hourglass@{ shape: hourglass, label: "Collate (hourglass)" }
    --> tri@{ shape: tri, label: "Extract (tri)" }
    --> notch_rect@{ shape: notch-rect, label: "Card (notch-rect)" }
    --> bang@{ shape: bang, label: "Bang (bang)" }
    --> browser@{ shape: browser, label: "Browser (browser)" }
    --> cloud@{ shape: cloud, label: "Cloud (cloud)" }
    --> console@{ shape: console, label: "Console (console)" }
    --> person@{ shape: person, label: "Person (person)" }
    --> bolt@{ shape: bolt, label: "Com Link (bolt)" }
    --> text@{ shape: text, label: "Text Block (text)" }
    --> brace@{ shape: brace, label: "Comment (brace)" }
    --> brace_r@{ shape: brace-r, label: "Comment Right (brace-r)" }
    --> braces@{ shape: braces, label: "Comment Both (braces)" }

```
### 5. Link & Arrow Types

```mermaid
flowchart TD
    link1["Standard Arrow: A --> B"] --> target1[Target]
    link2["Open Link: A --- B"] --- target2[Target]
    link3["Text on Arrow: A -->|text| B"] -->|Label Text| target3[Target]
    link4["Dotted Arrow: A -.-> B"] -.-> target4[Target]
```
```mermaid
flowchart TD
    link5["Dotted with Text: A -.->|text| B"] -.->|Label Text| target5[Target]
    link6["Thick Arrow: A ==> B"] ==> target6[Target]
    link7["Thick with Text: A ==>|text| B"] ==>|Label Text| target7[Target]
    link8["Invisible Link: A ~~~ B"] ~~~ target8[Target]
```
