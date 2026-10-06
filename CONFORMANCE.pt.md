> Tradução comunitária (rascunho) — Política NTARI P2-002, Transmissão Multilíngue Global (Global Multilingual Broadcast). Fonte: CONFORMANCE.md (original em inglês, snapshot de 2026-10-05). Rascunho comunitário assistido por máquina, pendente de revisão por mantenedor regional conforme P2-002 §3.1. As especificações técnicas centrais permanecem em inglês conforme §2.2.
>
> Notou algum erro nesta tradução? Correções de tradução são contribuições
> valiosas e muito bem-vindas: faça um fork do repositório e abra um pull
> request em https://github.com/NTARI-RAND/shelter.

> O original em inglês é normativo. Esta tradução é oferecida para leitura, não para interpretação: onde ela divergir de `CONFORMANCE.md`, prevalece o inglês. As palavras-chave normativas (MUST, MUST NOT, SHOULD, SHOULD NOT, MAY), os nomes de campos, os rótulos e os identificadores são mantidos em inglês porque constituem o contrato de conformidade.

# shelter — Autodescrição de conformidade

*Autoatestada até que um conjunto de testes cite estes IDs (registro: `JFA/jfa-conformance-suite.py`). Em estágio de proposta; "atendido por design" significa que a especificação declara a regra e exige o teste.*

**Forma:** a economia de apoio à reintegração da Parte VI da arquitetura — avaliação bidirecional, funcionamento offline-first em dispositivos de nível de entrada, dignidade e saída à frente da supervisão; o caso mais agudo nomeado para o problema em aberto 8.

| ID | Invariante | Status |
|---|---|---|
| COV-1 | Sem médias; distribuição completa | **Atendido por design** — INV-4, com guarda de grep na CI exigida |
| COV-2 | A avaliação mais baixa é a violação | **Atendido por design** — −1 sinalizado como dano, comentário obrigatório, assistente social (caseworker) notificado |
| COV-3 | Simétrico; toda alegação admite resposta | **Atendido por design** — avaliações bidirecionais; o caminho de disputa anota, nunca apaga |
| COV-4 | Controla se, nunca quanto | **Atendido por design** — nenhuma pontuação controla o valor de ninguém; a contagem é um sinal positivo, não uma classificação |
| REC-1 | Somente de adição | **Atendido por design** — eventos de avaliação imutáveis; a linha entre apagável e durável está em aberto (OQ-1) |
| REC-2 | Perdoar, nunca ocultar | **Atendido por design** — sinalizações/disputas contextualizam |
| REC-3 | Nenhuma PII em nada que seja compartilhado | **Atendido por design** — INV-5/6/8/10: pseudônimos, nenhuma PII nos logs, verificar sem reter, nenhuma categoria de status |
| REC-9 | Tipos de relação | **Parcial** — avaliações tipadas por direção (cidadão↔beneficiário, inspeção) mapeiam-se naturalmente; tipagem formal na construção |
| GOV-9 | Documento vivo de questões em aberto | **Atendido** — `OPEN-QUESTIONS.md` acompanha esta semente |
| GOV-10 | Commons copyleft | **Em aberto** — reutilizar o LBTAS vs. reimplementá-lo (OQ-3) |
| Problema 8 | Piso de custo do testemunho | **Nomeado** — registros autoatestados até que o testemunho seja acessível aqui (OQ-2) |

**Linha de honestidade:** não existe nenhum registro testemunhado; toda leitura de reputação é atestada pelo operador. A defesa do INV-10, "não coletamos isso", é arquitetural e deve sobreviver a toda mudança futura de esquema.
