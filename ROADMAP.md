# Roadmap do 3pi

> **Objetivo:** evoluir o fork do Pi para melhorar custo por tarefa concluída, confiabilidade e autonomia controlada sem perder a simplicidade, a compatibilidade com o upstream ou a capacidade de reverter mudanças.
>
> **Regra de ouro:** nenhuma otimização é considerada ganho até ser medida em tarefas comparáveis. Não trocar evidência por intuição.

## Estado atual

Este documento é a fonte de verdade do plano, não uma declaração de que as features já foram implementadas.

- [ ] Estabelecer baseline reproduzível de tarefas, custo, tokens, latência e sucesso.
- [ ] Auditar o estado atual do fork, workflows, branches e divergência do upstream.
- [ ] Definir e executar o processo de migração de issues/PRs do upstream, sem duplicação e sem merge automático.
- [ ] Avaliar features candidatas dos projetos doadores uma por vez.
- [ ] Publicar resultados e decisões com links para PRs, testes e medições.

**Situação inicial:** não há neste roadmap evidência de benchmark próprio concluído. Até que os testes sejam executados e registrados, ganhos de custo ou qualidade são hipóteses.

## Regras não negociáveis

1. **Não fazer push direto na `main` por automação.** Alterações de código e configuração passam por branch e Pull Request.
2. **Não fazer merge automaticamente.** O responsável humano revisa e decide.
3. **Manter upstream rastreável.** Sincronizações são PRs revisáveis; não reescrever histórico compartilhado nem forçar push na `main`.
4. **Uma variável por experimento.** Evitar combinar várias mudanças no mesmo benchmark.
5. **Medir a tarefa completa.** Incluir tokens de entrada e saída, chamadas de ferramenta, tentativas, custo monetário e latência.
6. **Preservar uma baseline.** Cada experimento compara a mesma suíte, modelos, prompts, limites e ambiente.
7. **Toda feature deve ser reversível.** Preferir flags, extensões ou mudanças isoladas quando isso for viável.
8. **Não importar um framework inteiro sem justificativa.** Estudar o componente mínimo necessário e suas dependências/licença.
9. **Segurança não pode regredir silenciosamente.** Registrar permissões, acesso a arquivos, execução de comandos e tratamento de credenciais.
10. **O roadmap é atualizado em cada sessão de trabalho.** Atualizar status, evidências, decisões e próximo passo antes de encerrar.

## Prioridades e fases

Os prazos são deliberadamente substituídos por critérios de saída. Só avançar quando as evidências exigidas existirem.

### Fase 0 — Controle do projeto e baseline (P0)

**Estado:** A FAZER

- [ ] Confirmar branch principal, remote upstream, workflow de sincronização e permissões.
- [ ] Auditar o merge acidental registrado em `720767516eb4606a081c66ad1da7cb47d6551164`; avaliar a correção em separado. Não reverter nem fazer merge automaticamente.
- [ ] Inventariar issues e PRs do upstream e do fork com paginação completa; documentar limitações, duplicatas e correspondências. Não declarar migração completa com resultados parciais.
- [ ] Escolher um conjunto pequeno e representativo de tarefas de programação: bug reproduzível, mudança pequena, refatoração e tarefa que exija testes.
- [ ] Registrar versões do Pi, Node, sistema operacional, modelo/provedor, parâmetros, prompts e comandos de execução.
- [ ] Capturar baseline com pelo menos 3 execuções por tarefa quando viável; publicar resultados brutos e mediana.
- [ ] Definir limites de tempo, custo e tentativas, além de critérios para considerar uma tarefa concluída.

**Saída exigida:** instruções de reprodução, conjunto fixo de tarefas, resultados de baseline e relatório do estado do repositório.

### Fase 1 — Eficiência de contexto e edição (P0)

**Estado:** BLOQUEADA ATÉ A BASELINE

Candidatos para investigar:

- [ ] Seleção de arquivos/contexto relevante e redução de leituras repetidas.
- [ ] Formatos de edição por diff/patch em vez de reescrever arquivos completos, quando compatível.
- [ ] Compactação/resumo do histórico com verificação de que requisitos importantes não se perdem.
- [ ] Evitar chamadas redundantes de ferramentas e leituras repetidas de estado.
- [ ] Encerrar a execução quando critérios objetivos de conclusão forem satisfeitos.

**Doadores para estudar:** [Aider](https://github.com/Aider-AI/aider) (edição, mapa do repositório e contexto) e o próprio [Pi upstream](https://github.com/earendil-works/pi) (extensões e contexto nativo).

**Aceitação:** comparação A/B na mesma suíte; relatar tokens de entrada/saída, custo, latência, sucesso, tentativas e falhas. Só adotar se o ganho for repetível e não houver regressão relevante de qualidade.

### Fase 2 — Execução, recuperação e segurança (P1)

**Estado:** NÃO INICIADA

- [ ] Mapear o comportamento atual de shell, edição, testes, erros e permissões.
- [ ] Avaliar checkpoints e recuperação segura após falha de patch/teste.
- [ ] Avaliar sandbox, limites de escrita e modos de aprovação graduais.
- [ ] Garantir que timeouts, limites de saída e cancelamento funcionem conforme esperado.
- [ ] Testar tarefas interrompidas, comandos com falha, arquivos modificados e repetição da execução.

**Doadores para estudar:** [OpenAI Codex CLI](https://github.com/openai/codex) para políticas de execução e sandbox; [OpenHands Software Agent SDK](https://github.com/OpenHands/software-agent-sdk) para ambientes de execução; [Pi upstream](https://github.com/earendil-works/pi) como base de integração.

**Aceitação:** testes de segurança e regressão documentados; permissões e comportamento padrão explícitos; mudança reversível. Não retirar shell nem habilitar autonomia maior sem evidência e análise de risco.

### Fase 3 — Roteamento de modelos e custo (P1)

**Estado:** NÃO INICIADA

- [ ] Medir custo real por tarefa por modelo/provedor usando tarefas equivalentes.
- [ ] Definir quando roteamento/fallback é permitido e quais erros o acionam.
- [ ] Separar custo estimado de custo efetivamente faturado.
- [ ] Avaliar falhas de autenticação, limites de taxa, indisponibilidade e respostas incompatíveis.
- [ ] Manter opção de selecionar explicitamente o modelo e desligar o roteamento.

**Aceitação:** ganho medido em custo por tarefa concluída, sem degradação inaceitável da taxa de sucesso; fallback testado e comportamento previsível. Não presumir que um modelo barato é mais econômico se precisar de mais tentativas.

### Fase 4 — Harness de avaliação contínua (P0 transversal)

**Estado:** A FAZER; acompanhar todas as fases

- [ ] Manter tarefas e critérios de sucesso versionados.
- [ ] Automatizar execução repetível sem expor segredos.
- [ ] Guardar resultados por commit/PR e configuração.
- [ ] Medir taxa de conclusão, tokens, custo, latência, chamadas de ferramenta, tentativas e regressões.
- [ ] Comparar baseline e candidato; repetir resultados inesperados.
- [ ] Distinguir falha do harness, falha do provedor e falha real da tarefa.

**Doador para estudar:** [SWE-agent](https://github.com/SWE-agent/SWE-agent) para desenho de avaliação em tarefas reais de engenharia de software. Usar como referência, não como prova de que o 3pi terá o mesmo resultado.

**Aceitação:** qualquer feature apresentada como “mais barata”, “mais rápida” ou “melhor” inclui comandos de reprodução e resultados comparáveis.

### Fase 5 — Integração seletiva e manutenção (P2)

**Estado:** NÃO INICIADA

- [ ] Revisar licença, dependências, manutenção e superfície de segurança antes de adaptar código de doadores.
- [ ] Preferir uma extensão isolada quando cumprir o requisito.
- [ ] Documentar origem, versão/commit do doador e alterações locais.
- [ ] Executar testes relevantes, build, lint e typecheck disponíveis.
- [ ] Preparar PR pequeno, com motivação, trade-offs, resultados e instruções de rollback.
- [ ] Atualizar este roadmap e o registro de decisões após cada merge aprovado.

**Aceitação:** revisão humana aprovada, testes passando, impacto documentado e caminho de reversão conhecido.

## Matriz de doadores

| Doador | O que investigar | O que NÃO assumir |
|---|---|---|
| [Pi upstream](https://github.com/earendil-works/pi) | Extensões, contexto, APIs, compatibilidade e correções upstream | Que todo upstream change deve entrar imediatamente |
| [Aider](https://github.com/Aider-AI/aider) | Seleção de arquivos, mapa do repositório, formatos de edição | Que menos texto de patch significa menos tokens totais |
| [OpenAI Codex CLI](https://github.com/openai/codex) | Sandbox, aprovações e controles de execução | Que a arquitetura inteira é compatível com Pi |
| [OpenHands SDK](https://github.com/OpenHands/software-agent-sdk) | Isolamento e ciclo de vida do ambiente de execução | Que o framework completo compensa o custo de integração |
| [SWE-agent](https://github.com/SWE-agent/SWE-agent) | Protocolo de benchmark e tarefas de engenharia | Que resultados publicados se reproduzem no 3pi sem controlar ambiente/modelo |

Nenhum código é copiado automaticamente. Para cada candidato, registrar: feature exata, caminho/commit de origem, licença, dependências, plano de teste, estimativa de esforço e decisão (adotar, adaptar, rejeitar ou adiar).

## Protocolo mínimo de benchmark

Para cada tarefa e configuração, registrar:

- Identificador da tarefa e commit do código testado.
- Modelo, provedor, versão/parâmetros e configuração do harness.
- Resultado: passou/falhou segundo critérios objetivos.
- Tokens de entrada e saída, quando disponíveis; não misturar estimativas e medições.
- Custo monetário real ou estimado, identificando qual.
- Duração, chamadas de modelo/ferramenta e número de tentativas.
- Erros, intervenção humana e observações.
- Comando/script para reproduzir.

Comparar baseline e candidato nas mesmas tarefas e condições. Relatar mediana e dispersão; não esconder falhas em uma média agregada. Se a amostra for pequena, declarar isso. Não adotar uma mudança com base em uma única execução.

## Definition of Done — quando uma feature está concluída

Uma feature só pode ser marcada como **CONCLUÍDA** se:

1. Existe issue/objetivo e hipótese explícita.
2. A implementação está num PR revisável.
3. Os testes relevantes passaram, com resultados registrados.
4. O benchmark foi comparado à baseline, quando a mudança promete eficiência ou qualidade.
5. Segurança, compatibilidade e regressões foram consideradas.
6. Há instrução de ativação/desativação ou rollback.
7. A documentação e este roadmap foram atualizados.

Status permitidos: **A FAZER**, **EM INVESTIGAÇÃO**, **EM IMPLEMENTAÇÃO**, **BLOQUEADA**, **EM VALIDAÇÃO**, **CONCLUÍDA**, **REJEITADA**. Sempre anexar link para issue/PR/benchmark; status sem evidência não significa conclusão.

## Registro de decisões

Adicionar uma entrada para cada decisão significativa:

| Data | Decisão | Evidência | Consequência / próxima revisão |
|---|---|---|---|
| 2026-10-09 | Estabelecer roadmap com baseline antes de otimizações | Não há benchmark próprio documentado neste arquivo | Executar Fase 0; atualizar após inventário |

## Handoff obrigatório ao encerrar uma sessão

Copiar para a próxima sessão um resumo com:

1. **Fase atual e objetivo imediato**
2. **Concluído nesta sessão** — com links e evidência
3. **Não concluído/bloqueios**
4. **Decisões tomadas e justificativas**
5. **Arquivos/branches/PRs alterados**
6. **Próxima ação única, concreta e verificável**
7. **Testes/benchmark a executar e resultados existentes**

Não afirmar que algo foi feito se apenas foi planejado, pesquisado ou discutido.

## Próximo passo recomendado

Completar a **Fase 0**: auditar o estado real do fork e da sincronização, esclarecer o merge acidental e fechar um inventário paginado de issues/PRs. Em paralelo, preparar a primeira versão do benchmark antes de implementar otimizações de tokens.
