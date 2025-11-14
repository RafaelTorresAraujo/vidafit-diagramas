# Diagrama de Sequência – VidaFit
Caso de uso: Realizar um treino

```mermaid
sequenceDiagram
    autonumber
    participant Usuário
    participant UI as "App UI"
    participant Auth as "AuthService"
    participant WorkoutCtrl as "WorkoutController"
    participant WorkoutService
    participant DB as "Database"
    participant Notif as "NotificationService"

    Usuário->>UI: abrir app / selecionar treino
    UI->>Auth: verificar token (se necessário)
    Auth-->>UI: token válido / erro

    Usuário->>UI: iniciar treino (clicar "Iniciar")
    UI->>WorkoutCtrl: startWorkout(treinoId, userId)
    WorkoutCtrl->>WorkoutService: createSession(treinoId, userId)
    WorkoutService->>DB: INSERT sessão {status: "ACTIVE", startedAt}
    DB-->>WorkoutService: OK (sessionId)
    WorkoutService-->>WorkoutCtrl: sessionCreated(sessionId)
    WorkoutCtrl-->>UI: mostrar cronômetro / instruções

    Note over Usuário,UI: Treino em progresso

    Usuário->>UI: pausar treino
    UI->>WorkoutCtrl: pauseSession(sessionId)
    WorkoutCtrl->>WorkoutService: updateStatus(sessionId, "PAUSED")
    WorkoutService->>DB: UPDATE sessão {status: "PAUSED", pausedAt}
    DB-->>WorkoutService: OK
    WorkoutService->>Notif: send(userId, "Treino pausado")
    Notif-->>Usuário: notificação

    Usuário->>UI: resumir treino
    UI->>WorkoutCtrl: resumeSession(sessionId)
    WorkoutCtrl->>WorkoutService: updateStatus(sessionId, "ACTIVE")
    WorkoutService->>DB: UPDATE sessão {status: "ACTIVE", resumedAt}
    DB-->>WorkoutService: OK
    WorkoutService-->>WorkoutCtrl: resumed
    WorkoutCtrl-->>UI: continuar cronômetro

    Usuário->>UI: finalizar treino
    UI->>WorkoutCtrl: completeSession(sessionId)
    WorkoutCtrl->>WorkoutService: finishSession(sessionId, metrics)
    WorkoutService->>DB: UPDATE sessão {status: "COMPLETED", finishedAt, metrics}
    DB-->>WorkoutService: OK
    WorkoutService->>Notif: send(userId, "Treino concluído")
    WorkoutService-->>WorkoutCtrl: sessionCompleted
    WorkoutCtrl-->>UI: mostrar resumo / estatísticas
