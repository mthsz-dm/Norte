sequenceDiagram
    actor U as Usuário
    participant R as React
    participant AC as AuthController
    participant AS as AuthService
    participant UR as UserRepository
    participant DB as Postgres

    activate U
    U->>+R: preenche email/senha
    R->>+AC: POST /auth/login
    AC->>+AS: autenticar(email, senha)
    AS->>+UR: buscarPorEmail(email)
    UR->>+DB: SELECT (JDBC)
    DB-->>-UR: linha do usuário (ou vazio)
    UR-->>-AS: User ou null

    alt credenciais válidas
        AS->>AS: valida hash (BCrypt) e gera JWT
        AS-->>AC: token
        AC-->>R: 200 OK token
        R-->>U: redireciona pro dashboard
    else credenciais inválidas
        AS-->>AC: erro de autenticação
        AC-->>R: 401 Unauthorized
        R-->>U: exibe mensagem de erro
    end
    deactivate AS
    deactivate AC
    deactivate R
    deactivate U