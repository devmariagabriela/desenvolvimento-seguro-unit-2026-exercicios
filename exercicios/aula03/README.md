# Exercício — Aula 03: Modelagem de Ameaças com STRIDE

**Disciplina:** Desenvolvimento Seguro (`F105457`) — UNIT 2026
**Entrega:** até a véspera da Aula 04, 23h59

Esta lista tem **duas entregas**, em dois pull requests:

| PR | Título | Conteúdo | Quem |
|---|---|---|---|
| 1 | `[Seguro S1] Nome da Equipe` | Parte A — o marco S1 | equipe |
| 2 | `[Aula 03] Seu Nome Completo` | Partes B, C e D | individual |

O **S1 vale 15% da nota do Dossiê de Segurança**, além de contar nesta lista.

> Alvo: o **Checkpoint da sua equipe**. Nenhum outro sistema.
> ⚠️ **Sem dado pessoal real em nenhum artefato** — nome, endereço e coordenada
> são fictícios, inclusive em captura de tela.

---

## Parte A — Marco S1: modelo de ameaças (45 pontos)

Quatro artefatos, em `exercicios/aula03/S1/`.

### A1 — Diagrama de fluxo de dados (10 pts)

O DFD do Checkpoint da equipe, com:

- **entidades externas** — quem interage e não está sob controle da equipe;
- **processos** — cada tela e cada endpoint que transforma dado;
- **armazenamentos** — SQLite, preferências, arquivos, MySQL;
- **fluxos** — as setas, cada uma com o nome do dado que passa;
- **as quatro fronteiras** — `F1` entrada de usuário, `F2` o aparelho, `F3` a
  rede, `F4` serviço de terceiro.

⚠️ **`F2` é uma caixa, não uma linha.** Tudo que está dentro do aparelho está em
ambiente não confiável, inclusive o que não atravessa fronteira nenhuma. Um DFD
que desenha F2 como seta perdeu o ponto.

Ferramenta livre: ASCII, Draw.io, Excalidraw, ou papel fotografado com legibilidade.

**Consistência exigida:** cada ponto de entrada do mapa da Aula 02 aparece no DFD,
como fluxo ou como armazenamento.

### A2 — Matriz STRIDE (20 pts)

Uma linha por (elemento × categoria aplicável):

| # | Elemento | STRIDE | Ameaça | Vulnerabilidade atual | P | I | Risco |
|---|---|---|---|---|---|---|---|

**Quais categorias se aplicam a qual elemento:**

```
                        S    T    R    I    D    E
  entidade externa      x         x
  processo              x    x    x    x    x    x
  armazenamento              x   (x)   x    x
  fluxo de dados             x         x    x
```

Aplicar categoria fora desse quadro é erro de método e desconta.

**Quatro exigências de preenchimento:**

1. **A coluna "ameaça" descreve o que alguém quer conseguir.** Não a técnica, não
   a vulnerabilidade. "SQL injection" é técnica; "alterar a visita de outro
   técnico" é ameaça.
2. **A coluna "vulnerabilidade atual" aponta código** — arquivo e, quando
   possível, linha. Quando a ameaça existe sem vulnerabilidade correspondente,
   escreva `nenhuma conhecida`, e isso é um resultado.
3. **A coluna "risco" tem um nível** — baixo, médio, alto ou crítico — coerente
   com a matriz de probabilidade × impacto. Não é o lugar da consequência.
4. **P e I têm justificativa**, uma linha cada, na tabela ou em nota de rodapé.
   Nível sem justificativa não conta.

**Cobertura mínima:** todos os processos com as seis categorias, e ao menos uma
ameaça de **R** e uma de **D** no modelo. São as duas mais esquecidas.

### A3 — Riscos priorizados (10 pts)

Os **dez** riscos mais altos, cada um com a decisão de tratamento:

| # | Risco | Onde | Nível | Ativo atingido | Decisão | Razão |
|---|---|---|---|---|---|---|

Decisão é uma de quatro: **tratar**, **transferir**, **evitar**, **aceitar**.

⚠️ **Ao menos um risco precisa ser aceito, com justificativa.** Se a equipe não
encontra nenhum que valha aceitar, o critério de custo não foi aplicado — e um
modelo que decide tratar tudo perde pontos em justificativa.

### A4 — Ativos (5 pts)

O inventário A1–A8 da Aula 01, atualizado para o estado atual do projeto. Cada
risco alto da A3 aponta qual ativo atinge.

Ao menos um ativo precisa ser **agregado** — um conjunto que vale mais que a soma
das partes.

---

## Parte B — Corrigir um modelo com erros (25 pontos)

O arquivo [`modelo-com-erros.md`](modelo-com-erros.md) é um modelo de ameaças do
Checkpoint escrito com **oito erros** plantados, dos tipos vistos na seção 9 da
aula. Seis estão em linhas específicas da matriz STRIDE; dois são de estrutura do
documento, e um desses é uma **omissão** — algo que deveria estar e não está.

O documento tem **outros problemas reais além dos oito**, e encontrá-los conta como
bônus (abaixo). Os oito são os que correspondem diretamente aos erros da seção 9.

Para cada erro encontrado:

```markdown
### Erro 1 — L1, coluna "Ameaça"

**Tipo:** técnica escrita na coluna da ameaça

**Por que é erro:** "SQL Injection" descreve *como* o ataque é feito, não o que
alguém quer conseguir. Escrito assim, a linha não diz qual ativo está em jogo e
não orienta a correção.

**Linha reescrita:**

| L1 | tela de login | T | alterar ou ler dado de outro técnico manipulando a
consulta | consulta concatenada em `usuario_repository.dart:41` | alta | alto |
crítico |
```

**Pontuação:** 3 pontos por erro corretamente identificado, classificado e
reescrito (24), mais 1 ponto pela organização. Erro apontado que não existe
**desconta 2 pontos** — o objetivo é análise, não varredura.

Cada problema real encontrado **além dos oito**, com justificativa correta, vale
**+3 de bônus**, até **+6**. Para erro de omissão ou de estrutura, a "linha
reescrita" é substituída pelo trecho que deveria constar no documento.

---

## Parte C — Três ameaças que só existem no móvel (20 pontos)

Descreva **três cenários de ameaça na fronteira F2** — o aparelho — que **não
existiriam** se o Checkpoint fosse uma aplicação web com o mesmo propósito.

Formato de cada um:

```markdown
### C1 — Extração do banco de aparelho perdido

**Categoria STRIDE:** I (vazamento de informação)
**Elemento do DFD:** armazenamento `SQLite local`
**Ativo atingido:** A1, A2, A3

**Cenário:** o técnico esquece o aparelho no balcão de um cliente. Quem o encontra
habilita a depuração USB, extrai o `checkpoint.db` e abre o arquivo em qualquer
leitor de SQLite. As 200 visitas sincronizadas naquele aparelho ficam legíveis —
nome, endereço e coordenada de cada cliente atendido.

**Por que não existiria na web:** em aplicação web o banco fica em servidor
administrado, com acesso físico controlado. O equivalente exigiria comprometer o
servidor; aqui, exige encontrar um celular.

**O que muda na defesa:** na web, protege-se o perímetro do servidor. Aqui, o
perímetro não existe — a proteção tem de estar no dado (cifra) e na chave (fora do
aplicativo, no Keystore).
```

A última seção — **o que muda na defesa** — é a que vale mais. O cenário é o meio;
a conclusão de projeto é o fim.

Os três cenários precisam usar **categorias STRIDE diferentes** e **elementos
diferentes** do DFD.

---

## Parte D — Um risco aceito (10 pontos)

Escolha um risco do seu modelo que a equipe decidiu **aceitar** e escreva a
análise completa:

| Item | Conteúdo |
|---|---|
| O risco | ameaça, vulnerabilidade, nível, ativo atingido |
| A proteção recusada | o que seria feito para tratá-lo |
| O custo dessa proteção | funcionalidade, implementação, atrito, e **novo risco criado** |
| A razão da aceitação | por que o risco é menor que o custo |
| O risco residual | o que permanece, e como a equipe saberia se se concretizou |

A linha mais importante é **"novo risco criado"**. Toda proteção mexe em alguma
coisa: mais cópias do dado protegem contra perda e aumentam a exposição ao
vazamento; bloqueio após três tentativas protege contra força bruta e permite
bloqueio malicioso; cifra protege o dado e cria o problema de onde guardar a chave.

⚠️ "Não temos tempo de implementar" **não é** aceitação de risco — é dívida.
Aceitação de risco é a conclusão de que a proteção custa mais do que vale, e a
diferença é o que se avalia aqui.

---

## Entrega

```
exercicios/aula03/
├── RESPOSTAS.md          # Partes B, C e D          -> PR [Aula 03]
└── S1/                                              -> PR [Seguro S1]
    ├── README.md         # resumo do modelo, uma pagina
    ├── dfd.md            # ou dfd.png
    ├── matriz-stride.md
    ├── riscos.md
    └── ativos.md
```

---

## Rubrica

| Critério | Pontos |
|---|---|
| Corretude técnica: DFD, categorias aplicadas, distinção ameaça/vulnerabilidade | 35 |
| Justificativa das decisões, com custo declarado | 30 |
| Completude: F2 e as categorias R e D tratadas | 25 |
| Clareza e organização do artefato | 10 |

**Prazo:** véspera da Aula 04, 23h59. Atraso de até 7 dias: −20 pontos. Acima
disso, não é corrigido — e no caso do S1, o marco entra como não entregue.

---

## Lista de verificação antes de abrir o PR

- [ ] o DFD tem entidades externas, processos, armazenamentos e fluxos
- [ ] as quatro fronteiras estão no diagrama, e **F2 é caixa**
- [ ] cada ponto do mapa da Aula 02 aparece no DFD
- [ ] a matriz cobre todos os processos com as seis categorias
- [ ] nenhuma categoria foi aplicada fora do quadro da A2
- [ ] há ao menos uma ameaça de **R** e uma de **D**
- [ ] nenhuma linha tem técnica na coluna da ameaça
- [ ] nenhuma linha tem consequência na coluna do risco
- [ ] a coluna de vulnerabilidade aponta arquivo do projeto
- [ ] P e I têm justificativa
- [ ] os dez riscos mais altos têm decisão registrada
- [ ] ao menos um risco foi aceito, com a razão escrita
- [ ] há ao menos um ativo agregado no inventário
- [ ] nenhum dado pessoal real em nenhum arquivo, inclusive em imagem
- [ ] `git status` limpo, sem `.db`, `.env` ou keystore
