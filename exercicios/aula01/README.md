# Lista 01 — Segurança da informação: tríade, terminologia e ativos

**Disciplina:** Desenvolvimento Seguro (`F105457`) — UNIT 2026
**Unidade I** · **Entrega:** até a véspera da aula 02 · PR `[Aula 01] Seu Nome Completo`

A Parte D produz o rascunho do marco **S1** do Dossiê de Segurança, entregue na
aula 03. É individual, mesmo para quem já tem equipe: as versões são comparadas
na aula 03, e a divergência revela quais ativos parte da equipe não havia
percebido.

---

## ⚠️ Antes de começar

O objeto de todas as atividades desta disciplina é o aplicativo **Checkpoint**,
construído pela própria turma em Desenvolvimento Mobile. Testar qualquer outro
sistema não é autorizado e configura crime pelo artigo 154-A do Código Penal.

Um cuidado prático desta lista: **não faça commit do arquivo de banco extraído**.
O `.gitignore` deste repositório já bloqueia `*.db` e `*.sqlite`, mas confira
antes do push. Enviar dado, ainda que de teste, para repositório público é
exatamente o tipo de erro que a disciplina estuda.

---

## Parte A — A tríade CID (15 pontos)

**A1 (10 pontos).** Classifique cada cenário conforme o atributo violado —
confidencialidade, integridade ou disponibilidade. Alguns violam mais de um;
nesse caso, indique todos e diga qual é o principal.

| | Cenário no Checkpoint |
|---|---|
| a | O celular do técnico é roubado e o banco SQLite é aberto em um computador |
| b | O técnico altera a data de uma visita já registrada, sem deixar rastro |
| c | A chave de criptografia local é perdida na atualização do aplicativo |
| d | Um token vazado permite consultar o histórico de visitas pela API |
| e | O servidor da API fica indisponível durante três dias |
| f | A sincronização grava a versão antiga sobre a nova, em um conflito |
| g | O log do aplicativo registra o token de autenticação em texto claro |
| h | O aplicativo passa a exigir internet para registrar frequência |

**A2 (5 pontos).** Os itens **c** e **h** têm algo em comum que os distingue dos
demais. Identifique o que é, e explique por que esse tipo de problema é o mais
frequentemente esquecido em decisões de segurança.

---

## Parte B — Os cinco termos (20 pontos)

**B1 (12 pontos).** Para cada item, classifique como **ativo**, **ameaça**,
**vulnerabilidade**, **ataque** ou **risco**.

| | Item |
|---|---|
| a | O banco SQLite com dados de 40 clientes |
| b | Roubo de celular na rua |
| c | O banco SQLite não é criptografado |
| d | Alguém extrai o banco de um aparelho roubado e lê os endereços |
| e | Alto: aparelhos de campo são roubados com frequência e o dado é pessoal |
| f | O token de sessão do técnico |
| g | O aplicativo aceita certificado TLS inválido |
| h | Interceptação de tráfego em rede Wi-Fi pública |
| i | Um técnico desligado da empresa continua com acesso |
| j | A revogação de sessão não funciona quando o aparelho está offline |
| k | Funcionário insatisfeito |
| l | O histórico completo de visitas no MySQL |

**B2 (8 pontos).** Dos doze itens, separe os que estão **sob controle da equipe de
desenvolvimento** dos que **não estão**. Depois explique, em 6 a 10 linhas, por
que o trabalho de segurança se concentra em um grupo e não no outro — e o que se
faz a respeito do outro grupo.

---

## Parte C — Compromissos de segurança (20 pontos)

Toda proteção custa algo em outra dimensão.

**C1 (14 pontos).** Para cada decisão de segurança, indique o que se **ganha** e o
que se **perde**. O que se perde precisa ser concreto e aplicável ao contexto do
Checkpoint — técnico em campo, muitas vezes sem rede.

| | Decisão |
|---|---|
| a | Criptografar o banco SQLite local |
| b | Exigir reautenticação a cada 30 minutos |
| c | Bloquear a conta após três tentativas de senha erradas |
| d | Registrar em log todas as requisições, com corpo completo |
| e | Exigir senha de 16 caracteres com símbolos |
| f | Remover os metadados EXIF das fotos antes de armazenar |
| g | Apagar todos os dados locais se o aparelho ficar 7 dias sem sincronizar |

**C2 (6 pontos).** Escolha **duas** das sete decisões e escreva, para cada uma, a
recomendação que você daria à equipe: adotar, adotar com ajuste, ou não adotar.
A recomendação precisa dizer **o que** o ajuste seria, quando houver.

O item **g** é o mais interessante da lista. Ele protege dado em aparelho perdido
e pode destruir o trabalho de um técnico que passou duas semanas em área sem
cobertura.

---

## Parte D — Inventário de ativos (25 pontos)

Rascunho do marco S1.

**D1 (18 pontos).** Construa o inventário de ativos do Checkpoint no formato da
seção 6 da aula, com as cinco colunas: identificador, ativo, onde reside, titular
do dado, e valor.

Requisitos:

| Requisito | Por quê |
|---|---|
| mínimo de 8 ativos | menos que isso indica inventário superficial |
| ao menos 1 credencial, chave ou token | não são o dado, mas dão acesso a ele |
| ao menos 1 ativo **agregado** | valor superior à soma das partes |
| a coluna "titular" preenchida em todos | determina responsabilidade legal |
| classificação por sensibilidade, com o critério declarado | |

**D2 (7 pontos).** Escolha **três** ativos e, para cada um, proponha uma proteção
e **declare o que ela custa**.

| Ativo | Proteção | O que se perde |
|---|---|---|
| | | |

Proposta sem custo declarado indica custo não percebido. Toda proteção tem um.

---

## Parte E — Redigir achados (20 pontos)

Um achado de segurança mal redigido não permite agir. Esta parte pratica a
redação.

**E1 (12 pontos).** Reescreva os três achados abaixo em formato utilizável. Cada
um precisa nomear: o **ativo** afetado, a **vulnerabilidade** (não a
consequência), o **impacto** concreto e a **forma de reproduzir**.

| | Achado mal redigido |
|---|---|
| a | "Existe risco de vazamento de dados." |
| b | "O aplicativo é vulnerável a ataques." |
| c | "Os logs não estão seguros." |

Para o item **a**, use o resultado da atividade prática da aula — a extração do
banco SQLite — como base.

**E2 (8 pontos).** Execute a atividade prática da aula, se ainda não executou:

```bash
adb shell pm list packages | grep checkpoint
adb exec-out run-as br.edu.unit.checkpoint \
    cat databases/checkpoint.db > checkpoint.db
```

Abra o arquivo no DB Browser for SQLite e registre:

| Pergunta | Resposta |
|---|---|
| O arquivo abriu sem senha? | |
| Quais campos são legíveis? | |
| Quantos registros havia? | |
| Quanto tempo levou o procedimento inteiro? | |

Entregue uma **captura de tela** com os dados sensíveis parcialmente ocultos, em
`evidencias/banco-extraido.png`.

⚠️ **Não faça commit do arquivo `checkpoint.db`.** A captura basta como evidência,
e o cuidado com isso é parte do que se avalia nesta questão.

---

## Parte F — Investigação (sem pontos, obrigatória)

A ausência desta parte reduz a nota da lista em 10 pontos.

Procure um **incidente real de vazamento ou falha de segurança em aplicativo
móvel**, documentado em fonte confiável — reportagem de veículo especializado,
relatório de empresa de segurança, comunicado da própria empresa, ou decisão de
autoridade de proteção de dados.

Escreva de 10 a 15 linhas informando:

- qual aplicativo, quando, e o que aconteceu;
- qual dos atributos da tríade foi violado;
- se a causa foi **ameaça externa** ou **vulnerabilidade do próprio aplicativo**;
- quantas pessoas foram afetadas, se houver o dado;
- houve consequência para a empresa? Multa, notificação, ação judicial?
- a fonte, com link.

Casos de aplicativos brasileiros são preferíveis, pelo enquadramento na LGPD, mas
não obrigatórios.

---

## Formato da entrega

```
exercicios/aula01/
├── RESPOSTAS.md              Partes A, B, C, E e F
├── inventario-ativos.md      Parte D
└── evidencias/
    └── banco-extraido.png    Parte E2
```

## Distribuição dos pontos

| Critério | Pontos |
|---|---|
| Corretude técnica | 35 |
| Justificativa das decisões de segurança | 30 |
| Qualidade dos artefatos e das evidências | 25 |
| Clareza da comunicação | 10 |
| Ausência da Parte F | −10 |
| **Commit do arquivo de banco** | **−20** |

---

## Observações sobre algumas questões

**Parte A2.** Os itens **c** e **h** violam **disponibilidade**, e ambos decorrem
de decisões tomadas *para melhorar a segurança*. É o atributo mais esquecido
precisamente porque as decisões que o prejudicam parecem, à primeira vista,
aumentar a proteção.

**Parte B1.** Os itens **i** e **j** são o par mais instrutivo. "Um técnico
desligado continua com acesso" é o **risco**; "a revogação não funciona offline" é
a **vulnerabilidade**. Um é consequência, o outro é causa — e só a causa pode ser
corrigida.

**Parte C1, item g.** Apagar dados após sete dias sem sincronizar é uma proteção
real contra aparelho perdido, e pode destruir duas semanas de trabalho de um
técnico em área sem cobertura. Não existe resposta certa: existe decisão
justificada, com o risco residual declarado. É exatamente o tipo de compromisso
que o dossiê final registra.

**Parte E1.** A diferença entre os três achados mal redigidos e a versão utilizável
não é de vocabulário técnico. É que a versão utilizável permite a alguém
**reproduzir** o problema e **decidir** o que fazer. Se o achado não permite as
duas coisas, ele não é um achado — é uma impressão.
