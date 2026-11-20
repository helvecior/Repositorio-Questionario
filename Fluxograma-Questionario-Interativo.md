# Fluxograma do Questionario Interativo

Fluxograma baseado no script [`Questinario-Interativo`](./Questinario-Interativo), na branch `development-area`.

```mermaid
flowchart TB
    A([Funcao main]) --> B[trap_end_signal<br/>Desabilita sinais]
    B --> C[zenity<br/>Exibe aviso inicial]
    C --> D[perguntar<br/>Apresenta pergunta 1]
    D --> E{Resposta 1<br/>e mkdir?}

    E -- Sim --> F[zenity<br/>Resposta correta]
    E -- Nao --> P1[trigger_penalty1]

    F --> G[perguntar<br/>Apresenta pergunta 2]
    G --> H{Resposta 2<br/>e chmod?}

    H -- Sim --> I[zenity<br/>Resposta correta]
    I --> J[right_answer_effect<br/>Inicia efeito visual]
    J --> K[Aguarda 1 segundo]
    K --> L[complete_challenge<br/>Conclui o desafio]

    H -- Nao --> P2[trigger_penalty2]

    subgraph Saidas[Fluxos de saida]
        direction LR

        subgraph Penalidade_1[Penalidade da pergunta 1]
            direction TB
            P1 --> P1A[xfconf-query<br/>Muda imagem para erro]
            P1A --> P1B[zenity<br/>Exibe notificacoes]
            P1B --> P1C[dir_maker<br/>Cria diretorios]
            P1C --> P1D[icon_desktop_size_effect<br/>wallpaper_effect_on]
            P1D --> P1E[zenity_scatter<br/>Abre alertas espalhados]
            P1E --> P1F[file_scattering<br/>Abre e movimenta arquivos]
            P1F --> P1G([Encerra com codigo 1])
        end

        subgraph Penalidade_2[Penalidade da pergunta 2]
            direction TB
            P2 --> P2A[xfconf-query<br/>Muda imagem para erro]
            P2A --> P2B[zenity<br/>Exibe notificacoes]
            P2B --> P2C[dir_maker<br/>Cria diretorios]
            P2C --> P2D[icon_desktop_size_effect<br/>wallpaper_effect_on]
            P2D --> P2E[zenity_scatter<br/>Exibe mensagens]
            P2E --> P2F[xcowsay<br/>Exibe contagem progressiva]
            P2F --> P2G[file_scattering<br/>Abre e movimenta arquivos]
            P2G --> P2H([Encerra com codigo 1])
        end

        subgraph Conclusao[Conclusao com duas respostas corretas]
            direction TB
            L --> L1[xfconf-query<br/>Muda imagem para parabens]
            L1 --> L2[zenity<br/>Exibe conclusao]
            L2 --> L3[zenity<br/>Exibe notificacoes]
            L3 --> L4[report_generator<br/>Cria jornada.txt]
            L4 --> L5[xed<br/>Abre jornada.txt]
            L5 --> L6[zenity<br/>Exibe mensagem final]
            L6 --> L7([Fim])
        end
    end
```
