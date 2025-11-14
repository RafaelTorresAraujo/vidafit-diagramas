# Diagrama de Estados – Ciclo de Vida do Treino

```mermaid
stateDiagram-v2
    [*] --> Planejado

    Planejado : enum scheduledTime
    Planejado --> Ativo : iniciar()
    Planejado --> Cancelado : cancelar()

    Ativo : startedAt\nstatus = ACTIVE
    Ativo --> Pausado : pausar()
    Ativo --> Concluido : concluir()
    Ativo --> Falha : erroSistema

    Pausado : pausedAt\nstatus = PAUSED
    Pausado --> Ativo : resumir()
    Pausado --> Cancelado : cancelar()
    Pausado --> Falha : erroSistema

    Concluido : finishedAt\nstatus = COMPLETED
    Concluido --> Arquivado : arquivar()
    Concluido --> [*]

    Cancelado : cancelledAt\nstatus = CANCELED
    Cancelado --> Arquivado : arquivar()
    Cancelado --> [*]

    Falha : errorInfo
    Falha --> Arquivado : registrarErro()
    Arquivado : archivedAt
    Arquivado --> [*]
