# Questionario Interativo fluxograma versão original estrutura prévia

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

> # Questionário Interativo — dois fluxogramas - nova estrutura

Baseado na versão de 21/11/2026 do script `Questinario-Interativo`. A **seta pontilhada** marca a chamada comentada de `mode_1`. Na execução atual, o Bash passa por `main` e `choose_mode`. As perguntas e seus efeitos pertencem ao ramo pontilhado.

## 1. Visão geral do código

```mermaid
flowchart TB
    A(["Início do script"]) --> M{"Menu após pedir o nome"}
    M -->|1| I["Mostra mensagem<br/>do modo iniciante"]
    M -->|2 ou 3| O["Mostra mensagem do modo<br/>experiente ou aprendizado"]
    M -->|4| S["Faz contagem e sai"]
    M -->|Inválida| V["Mostra as opções e termina"]
    I -.->|Chamada comentada| Q1{"Questionário iniciante<br/>Acertou a pergunta 1?"}
    Q1 -->|Não| E1["Penalidade 1<br/>A pergunta 2 fica bloqueada"]
    Q1 -->|Sim| Q2{"Acertou a pergunta 2?"}
    Q2 -->|Não| E2["Penalidade 2<br/>O fluxo fica no laço de janelas"]
    Q2 -->|Sim| C["Conclusão<br/>Abre o relatório para preencher"]
```

As opções **1, 2 e 3** só exibem mensagens antes de o script terminar. Enquanto espera pela opção do menu, o script repete a pergunta após 20 segundos de inatividade.

## 2. Processos e consequências descritos

Os nomes junto às ações indicam as funções e os comandos usados pelo script. Nas perguntas, `mkdir` e `chmod` são as respostas esperadas.

```mermaid
flowchart TB
    A(["Início · main"]) --> N{"Leu o nome?<br/>choose_mode · read"}
    N -->|Falha| F(["Encerra com código 1<br/>return"])
    N -->|Sim| M["Saúda e exibe o menu<br/>echo"]
    M --> R{"Lê a opção<br/>read"}
    R -->|Após 20 s| T["Chama o usuário<br/>echo · continue"]
    T --> R
    R -->|Fim da espera| O{"Qual opção foi lida?"}
    O -->|1| I["Mensagem do modo iniciante<br/>echo"]
    O -->|2 ou 3| J["Mensagem do modo<br/>experiente ou aprendizado · echo"]
    O -->|4| K["Conta 5 segundos e encerra com código 0<br/>echo · sleep · return"]
    O -->|Inválida| L["Mostra as opções e termina<br/>printf"]
    I -.->|Chamada comentada| Q["mode_1<br/>Ignora SIGINT e SIGTERM e exibe aviso<br/>trap_end_signal · trap · zenity"]
    Q --> Q1{"P1 · Comando para criar pasta<br/>perguntar · zenity<br/>Resposta igual a mkdir?"}
    Q1 -->|Não| P1["trigger_penalty1<br/>Efeitos de erro e laço de janelas<br/>A pergunta 2 fica bloqueada"]
    Q1 -->|Sim| B["Confirma o primeiro acerto<br/>zenity"]
    B --> Q2{"P2 · Comando para mudar permissões<br/>perguntar · zenity<br/>Resposta igual a chmod?"}
    Q2 -->|Não| P2["trigger_penalty2<br/>Efeitos de erro, mensagens e contagem<br/>Depois, laço de janelas"]
    Q2 -->|Sim| C["Confirma o segundo acerto<br/>zenity"]
    C -->|Em segundo plano| E["right_answer_effect<br/>Alterna o papel de parede em laço<br/>xfconf-query"]
    C -->|Fluxo principal| W["Inicia a conclusão após 1 segundo<br/>sleep · complete_challenge"]
    W --> S["Exibe parabéns e notificações<br/>xfconf-query · zenity"]
    S --> H["Cria ou atualiza jornada.txt e abre no editor<br/>report_generator · echo · cowsay · xed"]
    H --> Z(["Exibe os agradecimentos<br/>zenity"])
```

### O que acontece nas penalidades

1. **Preparação comum.** `xfconf-query` tenta exibir a imagem de erro. Depois de uma pausa com `sleep`, `zenity` dispara notificações e `dir_maker` tenta criar 200 pastas na Área de Trabalho com `mkdir`. O script usa `wait` para aguardar os trabalhos em segundo plano já iniciados.
2. **Efeitos comuns.** `icon_desktop_size_effect` e `wallpaper_effect_on` iniciam alterações contínuas no tamanho dos ícones e no papel de parede. `zenity_scatter` abre alertas e usa `wmctrl` para reposicionar janelas.
3. **Diferença entre os erros.** A penalidade 1 segue dos alertas para `file_scattering`. A penalidade 2 inicia os alertas em segundo plano e também exibe mensagens ilustradas e uma contagem com `xcowsay_size_image_dialog` e `xcowsay`, antes de entrar em `file_scattering`.
4. **Consequência comum.** `file_scattering` abre e reposiciona janelas com `xed` e `wmctrl` em um laço sem saída. Na penalidade 1, isso impede o avanço à pergunta 2. Na penalidade 2, impede o retorno normal da função. As chamadas de `disable_input_devices` estão comentadas nas duas penalidades.

### O que acontece após dois acertos

`right_answer_effect` continua alternando imagens em segundo plano, inclusive depois que `complete_challenge` tenta definir a imagem de parabéns. `report_generator` acrescenta texto a `jornada.txt` na Área de Trabalho e abre o arquivo para preenchimento. A mensagem de agradecimento aparece após o comando `xed` devolver o controle ao script.
