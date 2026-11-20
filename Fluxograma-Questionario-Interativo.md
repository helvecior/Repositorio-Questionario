# Fluxograma do Questionario Interativo

Fluxograma baseado no script [`Questinario-Interativo`](./Questinario-Interativo), na branch `development-area`.

```mermaid
flowchart TD
    A([Inicio: main]) --> B[Desabilita sinais SIGINT e SIGTERM]
    B --> C[Exibe aviso inicial]
    C --> D[Apresenta pergunta 1: comando para criar pasta]
    D --> E{Resposta 1 e mkdir?}

    E -- Sim --> F[Exibe mensagem de resposta correta]
    E -- Nao --> P1[trigger_penalty1]

    F --> G[Apresenta pergunta 2: comando para alterar permissoes]
    G --> H{Resposta 2 e chmod?}

    H -- Sim --> I[Exibe mensagem de resposta correta]
    I --> J[Inicia efeito visual de resposta correta]
    J --> K[Aguarda 1 segundo]
    K --> L[complete_challenge]

    H -- Nao --> P2[trigger_penalty2]

    subgraph Penalidade_1[Penalidade da primeira pergunta]
        P1 --> P1A[Muda imagem do desktop para erro]
        P1A --> P1B[Exibe notificacoes Prepare-se...]
        P1B --> P1C[Cria 200 diretorios na Area de Trabalho]
        P1C --> P1D[Altera tamanho dos icones e inicia efeito do papel de parede]
        P1D --> P1E[Abre 50 janelas de alerta espalhadas]
        P1E --> P1F[Abre e movimenta muitos arquivos no editor]
        P1F --> P1G([Encerra com codigo 1])
    end

    subgraph Penalidade_2[Penalidade da segunda pergunta]
        P2 --> P2A[Muda imagem do desktop para erro]
        P2A --> P2B[Exibe notificacoes Prepare-se...]
        P2B --> P2C[Cria 200 diretorios na Area de Trabalho]
        P2C --> P2D[Altera tamanho dos icones e inicia efeito do papel de parede]
        P2D --> P2E[Abre alertas, mensagens e contagem progressiva]
        P2E --> P2F[Abre e movimenta muitos arquivos no editor]
        P2F --> P2G([Encerra com codigo 1])
    end

    subgraph Conclusao[Conclusao com duas respostas corretas]
        L --> L1[Muda imagem do desktop para parabens]
        L1 --> L2[Exibe mensagem de conclusao]
        L2 --> L3[Exibe 100 notificacoes de parabens]
        L3 --> L4[Cria jornada.txt na Area de Trabalho]
        L4 --> L5[Abre jornada.txt no editor para preenchimento]
        L5 --> L6[Exibe mensagem final do autor]
        L6 --> L7([Fim])
    end
```
