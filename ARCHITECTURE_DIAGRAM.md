# GitDiagram System Architecture Diagrams

## 1. High-Level System Architecture

```mermaid
flowchart TD
    %% External entities
    User["👤 User"]:::user
    GitHub["📦 GitHub API"]:::external
    OpenAI["🤖 OpenAI o4-mini"]:::external
    
    %% Frontend Layer
    subgraph "Frontend Layer (Next.js)"
        UI["🌐 User Interface"]:::frontend
        MainCard["📝 Main Input Card"]:::frontend
        MermaidChart["📊 Mermaid Renderer"]:::frontend
        Hooks["🔗 React Hooks"]:::frontend
        Actions["⚡ Server Actions"]:::frontend
    end
    
    %% Backend Layer
    subgraph "Backend Layer (FastAPI)"
        API["🚀 API Gateway"]:::backend
        GenerateRouter["📋 Generate Router"]:::backend
        ModifyRouter["✏️ Modify Router"]:::backend
        GitHubService["📦 GitHub Service"]:::backend
        AIService["🤖 AI Service"]:::backend
        Limiter["🛡️ Rate Limiter"]:::backend
    end
    
    %% Data Layer
    subgraph "Data Layer"
        Database["🗄️ PostgreSQL"]:::database
        Cache["💾 Diagram Cache"]:::database
        Analytics["📈 Usage Analytics"]:::database
    end
    
    %% Connections
    User -->|"Input GitHub URL"| UI
    UI --> MainCard
    MainCard --> Hooks
    Hooks -->|"API Calls"| Actions
    Actions -->|"HTTP Requests"| API
    
    API --> Limiter
    Limiter --> GenerateRouter
    Limiter --> ModifyRouter
    
    GenerateRouter --> GitHubService
    GenerateRouter --> AIService
    ModifyRouter --> AIService
    
    GitHubService -->|"Fetch Repo Data"| GitHub
    AIService -->|"Generate Diagrams"| OpenAI
    
    GenerateRouter --> Cache
    ModifyRouter --> Cache
    API --> Analytics
    
    Cache --> Database
    Analytics --> Database
    
    Actions -->|"Stream Response"| Hooks
    Hooks --> MermaidChart
    MermaidChart -->|"Interactive Diagram"| User
    
    %% Styles
    classDef user fill:#e1f5fe,stroke:#01579b,stroke-width:2px
    classDef external fill:#fff3e0,stroke:#e65100,stroke-width:2px
    classDef frontend fill:#e8f5e8,stroke:#2e7d32,stroke-width:2px
    classDef backend fill:#fff8e1,stroke:#f57c00,stroke-width:2px
    classDef database fill:#fce4ec,stroke:#c2185b,stroke-width:2px
```

## 2. AI Processing Pipeline

```mermaid
flowchart TD
    %% Input
    Input["📥 GitHub Repository"]:::input
    
    %% Data Extraction
    subgraph "Data Extraction"
        FileTree["📁 File Tree"]:::data
        README["📖 README Content"]:::data
        RepoMeta["ℹ️ Repository Metadata"]:::data
    end
    
    %% AI Pipeline Stages
    subgraph "AI Processing Pipeline"
        Stage1["🔍 Stage 1: System Analysis"]:::ai
        Stage2["🗺️ Stage 2: Component Mapping"]:::ai
        Stage3["🎨 Stage 3: Diagram Generation"]:::ai
    end
    
    %% Outputs
    subgraph "Generated Outputs"
        Explanation["📝 System Explanation"]:::output
        Mapping["🔗 Component Mapping"]:::output
        Diagram["📊 Mermaid Diagram"]:::output
    end
    
    %% Processing Flow
    Input --> FileTree
    Input --> README
    Input --> RepoMeta
    
    FileTree --> Stage1
    README --> Stage1
    Stage1 --> Explanation
    
    Explanation --> Stage2
    FileTree --> Stage2
    Stage2 --> Mapping
    
    Explanation --> Stage3
    Mapping --> Stage3
    Stage3 --> Diagram
    
    %% Feedback Loop
    Diagram -.->|"Quality Check"| Stage3
    
    %% Styles
    classDef input fill:#e3f2fd,stroke:#1976d2,stroke-width:2px
    classDef data fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px
    classDef ai fill:#fff3e0,stroke:#f57c00,stroke-width:3px
    classDef output fill:#e8f5e8,stroke:#388e3c,stroke-width:2px
```

## 3. Data Flow Sequence

```mermaid
sequenceDiagram
    participant U as 👤 User
    participant F as 🌐 Frontend
    participant A as 🚀 API Gateway
    participant G as 📦 GitHub Service
    participant AI as 🤖 AI Service
    participant DB as 🗄️ Database
    participant GH as 📦 GitHub API
    participant OAI as 🤖 OpenAI
    
    Note over U,OAI: Diagram Generation Flow
    
    U->>F: Enter GitHub URL
    F->>F: Validate URL format
    F->>A: POST /generate/stream
    
    A->>G: Fetch repository data
    G->>GH: Get file tree
    GH-->>G: File structure
    G->>GH: Get README
    GH-->>G: README content
    G-->>A: Repository data
    
    Note over A,OAI: Multi-Stage AI Processing
    
    A->>AI: Stage 1: Analyze structure
    AI->>OAI: System analysis prompt
    OAI-->>AI: System explanation
    AI-->>A: Stream: explanation
    A-->>F: SSE: explanation chunk
    F-->>U: Update UI: explanation
    
    A->>AI: Stage 2: Map components
    AI->>OAI: Component mapping prompt
    OAI-->>AI: Component mappings
    AI-->>A: Stream: mapping
    A-->>F: SSE: mapping chunk
    F-->>U: Update UI: mapping
    
    A->>AI: Stage 3: Generate diagram
    AI->>OAI: Diagram generation prompt
    OAI-->>AI: Mermaid diagram code
    AI-->>A: Stream: diagram
    A-->>F: SSE: diagram chunk
    F-->>U: Update UI: diagram
    
    A->>DB: Cache results
    DB-->>A: Confirmation
    
    A-->>F: Stream: complete
    F->>F: Render Mermaid diagram
    F-->>U: Interactive diagram
    
    Note over U,F: User Interaction
    U->>F: Click diagram element
    F->>F: Navigate to GitHub file
```

## 4. Component Architecture

```mermaid
flowchart LR
    %% Frontend Components
    subgraph "Frontend Components"
        direction TB
        Page["📄 Page Component"]:::component
        MainCard["📝 MainCard"]:::component
        MermaidChart["📊 MermaidChart"]:::component
        Loading["⏳ Loading"]:::component
        Dialogs["💬 Dialogs"]:::component
        
        Page --> MainCard
        Page --> MermaidChart
        Page --> Loading
        Page --> Dialogs
    end
    
    %% Hooks Layer
    subgraph "React Hooks"
        direction TB
        useDiagram["🔗 useDiagram"]:::hook
        useStarReminder["⭐ useStarReminder"]:::hook
        
        Page --> useDiagram
        Page --> useStarReminder
    end
    
    %% Server Actions
    subgraph "Server Actions"
        direction TB
        CacheActions["💾 Cache Actions"]:::action
        RepoActions["📦 Repo Actions"]:::action
        
        useDiagram --> CacheActions
        useDiagram --> RepoActions
    end
    
    %% Backend Services
    subgraph "Backend Services"
        direction TB
        GitHubSvc["📦 GitHub Service"]:::service
        AISvc["🤖 AI Service"]:::service
        AuthSvc["🔐 Auth Service"]:::service
        
        GitHubSvc --> AuthSvc
        AISvc --> AuthSvc
    end
    
    %% External APIs
    subgraph "External APIs"
        direction TB
        GitHubAPI["📦 GitHub API"]:::external
        OpenAIAPI["🤖 OpenAI API"]:::external
        
        GitHubSvc --> GitHubAPI
        AISvc --> OpenAIAPI
    end
    
    %% Database
    subgraph "Database Layer"
        direction TB
        Schema["📋 Drizzle Schema"]:::db
        Migrations["🔄 Migrations"]:::db
        
        CacheActions --> Schema
        RepoActions --> Schema
    end
    
    %% Styles
    classDef component fill:#e3f2fd,stroke:#1976d2,stroke-width:2px
    classDef hook fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px
    classDef action fill:#fff3e0,stroke:#f57c00,stroke-width:2px
    classDef service fill:#e8f5e8,stroke:#388e3c,stroke-width:2px
    classDef external fill:#ffebee,stroke:#d32f2f,stroke-width:2px
    classDef db fill:#fce4ec,stroke:#c2185b,stroke-width:2px
```

## 5. Authentication Flow

```mermaid
flowchart TD
    %% User Authentication Options
    User["👤 User"]:::user
    
    %% Authentication Methods
    subgraph "Authentication Methods"
        NoAuth["🔓 No Authentication"]:::auth
        PAT["🔑 Personal Access Token"]:::auth
        GitHubApp["🏢 GitHub App"]:::auth
    end
    
    %% Rate Limits
    subgraph "Rate Limits"
        Limit60["⏱️ 60 req/hour"]:::limit
        Limit5000["⏱️ 5000 req/hour"]:::limit
        LimitApp["⏱️ App limits"]:::limit
    end
    
    %% Repository Access
    subgraph "Repository Access"
        PublicOnly["📖 Public repos only"]:::access
        PublicPrivate["📖📕 Public + Private"]:::access
        OrgRepos["🏢 Organization repos"]:::access
    end
    
    %% Flow
    User --> NoAuth
    User --> PAT
    User --> GitHubApp
    
    NoAuth --> Limit60
    PAT --> Limit5000
    GitHubApp --> LimitApp
    
    NoAuth --> PublicOnly
    PAT --> PublicPrivate
    GitHubApp --> OrgRepos
    
    %% Token Management
    subgraph "Token Management"
        JWTGen["🔐 JWT Generation"]:::token
        TokenRefresh["🔄 Token Refresh"]:::token
        TokenCache["💾 Token Cache"]:::token
    end
    
    GitHubApp --> JWTGen
    JWTGen --> TokenRefresh
    TokenRefresh --> TokenCache
    
    %% Styles
    classDef user fill:#e1f5fe,stroke:#01579b,stroke-width:2px
    classDef auth fill:#fff3e0,stroke:#e65100,stroke-width:2px
    classDef limit fill:#ffebee,stroke:#d32f2f,stroke-width:2px
    classDef access fill:#e8f5e8,stroke:#2e7d32,stroke-width:2px
    classDef token fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px
```

## 6. Error Handling & Recovery

```mermaid
flowchart TD
    %% Error Sources
    subgraph "Error Sources"
        GitHubError["📦 GitHub API Error"]:::error
        AIError["🤖 AI Service Error"]:::error
        NetworkError["🌐 Network Error"]:::error
        ValidationError["✅ Validation Error"]:::error
    end
    
    %% Error Handling
    subgraph "Error Handling"
        ErrorBoundary["🛡️ Error Boundary"]:::handler
        RetryLogic["🔄 Retry Logic"]:::handler
        Fallback["🔄 Fallback Strategy"]:::handler
        UserFeedback["💬 User Feedback"]:::handler
    end
    
    %% Recovery Strategies
    subgraph "Recovery Strategies"
        CacheCheck["💾 Check Cache"]:::recovery
        AuthRetry["🔐 Retry with Auth"]:::recovery
        ManualInput["✏️ Manual Input"]:::recovery
        SupportContact["📞 Contact Support"]:::recovery
    end
    
    %% Flow
    GitHubError --> ErrorBoundary
    AIError --> ErrorBoundary
    NetworkError --> ErrorBoundary
    ValidationError --> ErrorBoundary
    
    ErrorBoundary --> RetryLogic
    ErrorBoundary --> Fallback
    ErrorBoundary --> UserFeedback
    
    RetryLogic --> CacheCheck
    Fallback --> AuthRetry
    UserFeedback --> ManualInput
    UserFeedback --> SupportContact
    
    %% Success Path
    CacheCheck -->|"Success"| Success["✅ Success"]:::success
    AuthRetry -->|"Success"| Success
    ManualInput -->|"Success"| Success
    
    %% Styles
    classDef error fill:#ffebee,stroke:#d32f2f,stroke-width:2px
    classDef handler fill:#fff3e0,stroke:#f57c00,stroke-width:2px
    classDef recovery fill:#e8f5e8,stroke:#388e3c,stroke-width:2px
    classDef success fill:#e1f5fe,stroke:#01579b,stroke-width:2px
```

## 7. Deployment Architecture

```mermaid
flowchart TD
    %% Development
    subgraph "Development Environment"
        DevFE["💻 Next.js Dev Server"]:::dev
        DevBE["🐳 Docker Backend"]:::dev
        DevDB["🗄️ Local PostgreSQL"]:::dev
        
        DevFE --> DevBE
        DevBE --> DevDB
    end
    
    %% Production Frontend
    subgraph "Production Frontend (Vercel)"
        VercelEdge["🌐 Vercel Edge Network"]:::prod
        NextApp["⚡ Next.js App"]:::prod
        ServerActions["🔧 Server Actions"]:::prod
        
        VercelEdge --> NextApp
        NextApp --> ServerActions
    end
    
    %% Production Backend
    subgraph "Production Backend (EC2)"
        LoadBalancer["⚖️ Load Balancer"]:::prod
        FastAPIApp["🚀 FastAPI App"]:::prod
        Nginx["🌐 Nginx Proxy"]:::prod
        
        LoadBalancer --> Nginx
        Nginx --> FastAPIApp
    end
    
    %% Database
    subgraph "Database (Cloud)"
        PostgreSQL["🗄️ PostgreSQL"]:::db
        Backups["💾 Automated Backups"]:::db
        
        PostgreSQL --> Backups
    end
    
    %% External Services
    subgraph "External Services"
        GitHubAPI["📦 GitHub API"]:::external
        OpenAIAPI["🤖 OpenAI API"]:::external
        Analytics["📈 PostHog Analytics"]:::external
    end
    
    %% CI/CD
    subgraph "CI/CD Pipeline"
        GitHubActions["🔄 GitHub Actions"]:::cicd
        DockerBuild["🐳 Docker Build"]:::cicd
        Deploy["🚀 Deployment"]:::cicd
        
        GitHubActions --> DockerBuild
        DockerBuild --> Deploy
    end
    
    %% Connections
    ServerActions --> FastAPIApp
    FastAPIApp --> PostgreSQL
    FastAPIApp --> GitHubAPI
    FastAPIApp --> OpenAIAPI
    NextApp --> Analytics
    
    Deploy --> VercelEdge
    Deploy --> LoadBalancer
    
    %% Styles
    classDef dev fill:#e8f5e8,stroke:#388e3c,stroke-width:2px
    classDef prod fill:#e3f2fd,stroke:#1976d2,stroke-width:2px
    classDef db fill:#fce4ec,stroke:#c2185b,stroke-width:2px
    classDef external fill:#fff3e0,stroke:#f57c00,stroke-width:2px
    classDef cicd fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px
```

These diagrams provide a comprehensive visual overview of the GitDiagram system architecture, from high-level component relationships to detailed data flows and deployment strategies.