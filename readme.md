flowchart TB
    subgraph L1["1️⃣  Clients"]
        UI["Swagger UI<br/>:8000/docs"]
        CURL["curl / SDK / app"]
    end

    subgraph L2["2️⃣  API Layer · FastAPI (app/api)"]
        UP["POST /upload"]
        ING["POST /ingest"]
        ASK["POST /ask"]
        DR["POST /draft"]
        SESS["/sessions  (POST · GET · DELETE)"]
        BROWSE["/letters  ·  /submissions  ·  /health"]
    end

    subgraph L3["3️⃣  Business Logic"]
        direction LR
        PIPE["📥 Ingestion Pipeline<br/>(app/pipeline)<br/>extract → classify → fields → index"]
        RAG["🧠 RAG Layer<br/>(app/rag)<br/>intent · answer · draft · sessions"]
    end

    subgraph L4["4️⃣  Storage"]
        IDX[("JSON Index<br/>data/index.json")]
        BLOB[("Blob Root<br/>data/blobs/")]
        MEM[("Session Store<br/>in-memory, TTL 6h")]
    end

    subgraph L5["5️⃣  External"]
        DOC["IBM Docling<br/>(local library)"]
        VTX["Google Vertex AI"]
        CL["Anthropic Claude Opus 4"]
    end

    UI --> L2
    CURL --> L2

    UP --> PIPE
    ING --> PIPE
    ASK --> RAG
    DR --> RAG
    SESS --> RAG
    BROWSE --> IDX
    BROWSE --> BLOB

    PIPE --> DOC
    PIPE --> IDX
    PIPE --> BLOB
    RAG --> IDX
    RAG --> BLOB
    RAG --> MEM
    RAG --> VTX
    PIPE -. "optional LLM fallback<br/>for missing fields" .-> VTX
    VTX --> CL

    classDef l1 fill:#fde68a,stroke:#b45309,color:#3f2d05;
    classDef l2 fill:#bfdbfe,stroke:#1d4ed8,color:#0b1f4d;
    classDef l3 fill:#bbf7d0,stroke:#15803d,color:#062b13;
    classDef l4 fill:#ddd6fe,stroke:#6d28d9,color:#1e1346;
    classDef l5 fill:#fecaca,stroke:#b91c1c,color:#3f0712;
    class UI,CURL l1;
    class UP,ING,ASK,DR,SESS,BROWSE l2;
    class PIPE,RAG l3;
    class IDX,BLOB,MEM l4;
    class DOC,VTX,CL l5;
