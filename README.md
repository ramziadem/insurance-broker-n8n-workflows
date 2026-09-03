# insurance-broker-n8n-workflows

graph TD
    subgraph Core System Architecture
        GS[(Google Sheets DB)]
        N8N_W2[Workflow 2: Renewal Engine]
        N8N_W3[Workflow 3: Inbound Reply Router]
        GEMINI[Gemini 1.5 LLM]
        ZOHO[Zoho SMTP / IMAP]
        SLACK[Slack Telemetry]
        ERR[Workflow 00: Error Handler]
    end

    %% Workflow 2
    GS -->|1. Fetch Expiring Policies| N8N_W2
    N8N_W2 -->|2. Generate Custom HTML| GEMINI
    N8N_W2 -->|3. Dispatch Renewal Notice| ZOHO
    N8N_W2 -->|4. Update Followup Count & Date| GS

    %% Workflow 3
    ZOHO -->|5. Read Unseen Replies| N8N_W3
    N8N_W3 -->|6. Classify Reply Intent| GEMINI
    N8N_W3 -->|7. Route Action & Update Status| GS
    N8N_W3 -->|8. Flag Complex Inquiry| SLACK

    %% Error Handler
    N8N_W2 -.->|On Failure| ERR
    N8N_W3 -.->|On Failure| ERR
    ERR -->|Instant Alert & Log| SLACK
