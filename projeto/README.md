# Trabalho final: Dossiê de Segurança do Checkpoint

**Disciplina:** Desenvolvimento Seguro (`F105457`) — UNIT 2026
**Professor:** Petros Barreto
**Formato:** equipes de 3 a 5 pessoas — **as mesmas de Desenvolvimento Mobile**
**Entrega final:** aula 20, e integra o marco M7 do projeto de Mobile

---

## O que é

O Dossiê de Segurança é o documento que reúne o que a equipe **especificou**,
**implementou** e **verificou** em termos de segurança no aplicativo
[Checkpoint](https://github.com/petrosbarreto/checkpoint-app), construído na
disciplina de Desenvolvimento Mobile.

```
                DESENVOLVIMENTO SEGURO          DESENVOLVIMENTO MOBILE
                (esta disciplina)               (a outra)

  semana 3      S1  modelo de ameaças
                     │
                     ├──── define o que proteger
                     ▼
  semana 8      S2  especificação de
                    autenticação, sessão
                    e autorização  ──────────▶  M5 (sem. 19) implementa
                                                 Firebase Auth com as regras
                     │
  semana 12     S3  especificação de
                    validação, config,
                    erros e logs  ───────────▶  M2 (sem. 12) implementa
                                                 validação nos formulários
                     │
  semana 17     S4  plano de proteção
                    de dados: cripto
                    e LGPD  ─────────────────▶  M3 (sem. 14) e M6 (sem. 20)
                                                 SQLite cifrado, fotos, GPS
                     │
  semana 20     S5  DOSSIÊ: testa o app,
                    corrige, documenta  ◀─────  o app pronto
                     │
                     └──────────────────────▶  integra o M7 de Mobile
```

A lógica é simples: **esta disciplina escreve o requisito de segurança, a outra o
implementa, e esta verifica se foi cumprido.** Nada é construído duas vezes.

---

## Os cinco marcos

Cada marco é entregue como pull request no repositório da equipe, com o título
`[Seguro SN] Nome da Equipe`.

### S1 — Modelo de ameaças · aula 03 · 15%

| Artefato | Conteúdo |
|---|---|
| Diagrama de fluxo de dados | onde o dado entra, onde é armazenado, para onde vai, com as fronteiras de confiança marcadas |
| Matriz STRIDE | para cada componente, as ameaças das seis categorias |
| Riscos priorizados | probabilidade × impacto, com os 10 riscos mais altos justificados |
| Ativos e o que se protege | qual dado, de quem, contra quem |

O elemento central é a **fronteira de confiança**. Ela é a linha a partir da qual
o dado deixa de estar sob controle do sistema, e é onde as vulnerabilidades se
concentram.

```
FRONTEIRAS DE CONFIANCA DO CHECKPOINT

  [ tecnico ]
       │  ← fronteira 1: entrada de usuario
       ▼
  ┌─────────────────────────┐
  │  app no aparelho         │      o aparelho pode ser perdido,
  │  ┌───────────────────┐   │      emprestado, roubado ou ter
  │  │ SQLite local      │   │      root habilitado
  │  │ token de sessao   │   │
  │  │ fotos             │   │
  │  └───────────────────┘   │
  └───────────┬─────────────┘
              │  ← fronteira 2: rede (nao confiavel)
              ▼
  ┌─────────────────────────┐
  │  API REST + MySQL        │      ← fronteira 3: autorizacao por recurso
  └─────────────────────────┘
              │  ← fronteira 4: servico de terceiro
              ▼
  ┌─────────────────────────┐
  │  Firebase (Auth, FCM)    │
  └─────────────────────────┘
```

Um modelo de ameaças que não identifica as quatro fronteiras está incompleto. A
fronteira 1 costuma ser esquecida: a monitora que registra a frequência é usuária
legítima, e a entrada que ela digita ainda assim não é confiável.

### S2 — Autenticação, sessão e autorização · aula 08 · 20%

| Artefato | Conteúdo |
|---|---|
| Requisitos de autenticação | política de credencial, fatores, tratamento de tentativas falhas |
| Ciclo de vida da sessão | criação, renovação, expiração, revogação, logout |
| Decisão sobre armazenamento do token | onde fica no aparelho, e por quê |
| Modelo de autorização | papéis, permissões por recurso, e onde a verificação ocorre |
| Casos de abuso | de 6 a 10 cenários de tentativa de burla, com a defesa prevista |

Uma exigência específica: o documento precisa responder **o que acontece quando o
técnico é desligado da empresa**. O aplicativo funciona offline, com token
armazenado localmente; revogar acesso em um sistema que não depende de rede é um
problema de projeto, não de configuração, e a resposta precisa ser explícita.

Os **casos de abuso** são o inverso dos casos de uso. Em vez de descrever o que o
usuário legítimo quer fazer, descrevem o que alguém tentaria fazer indevidamente.

```
EXEMPLO DE CASO DE ABUSO

  Identificador   AB-03
  Título          Técnico registra visita em rota que não é a sua
  Ator            técnico autenticado
  Motivação       fechar meta de visitas sem se deslocar
  Passos          1. autentica normalmente
                  2. intercepta a requisição de criação de visita
                  3. altera o identificador da rota para outra
                  4. envia
  O que impede    verificação, NO SERVIDOR, de que a rota pertence
                  ao técnico autenticado
  Verificação     teste automatizado que envia rota alheia e
                  espera resposta 403
```

### S3 — Entrada, configuração, erros e logs · aula 12 · 20%

| Artefato | Conteúdo |
|---|---|
| Regras de validação | por campo do formulário de visita, com o tipo de validação e onde ocorre |
| Estratégia contra injeção | como o acesso ao SQLite e à API impede injeção |
| Inventário de segredos | o que é segredo no projeto, onde está, e como não vaza |
| Política de erros | o que a mensagem mostra ao usuário e o que registra internamente |
| Política de logs | o que é registrado, o que **nunca** é, retenção e acesso |

A política de logs é o artefato que produz mais descobertas desagradáveis. A
tabela abaixo é o modelo, e a coluna da direita é a que costuma revelar problemas
já existentes no código.

| Evento | Registrar | Nunca registrar |
|---|---|---|
| Tentativa de login | usuário, resultado, horário, origem | a senha, nem seu tamanho |
| Sincronização | quantidade de registros, duração, resultado | conteúdo das visitas |
| Erro de rede | endpoint, código de status | token, cabeçalho de autorização |
| Registro de visita | identificador da visita, técnico | coordenadas exatas, nome de terceiros |

### S4 — Proteção de dados: criptografia e LGPD · aula 17 · 20%

| Artefato | Conteúdo |
|---|---|
| Inventário de dados pessoais | quais dados, de quem, por que são necessários |
| Decisão de criptografia em repouso | o que é cifrado, com qual algoritmo, e onde a chave fica |
| Proteção em trânsito | TLS, fixação de certificado — decidido e justificado |
| Minimização | quais dados **deixaram de ser coletados** após a análise |
| Base legal e retenção | fundamento na LGPD e por quanto tempo cada dado é mantido |
| Tratamento de aparelho perdido | o que acontece com os dados locais |

A seção de **minimização** é a mais valorizada, e é contraintuitiva: ela pede que
a equipe **remova** algo. Um inventário que conclui que todos os dados coletados
são necessários provavelmente não foi feito com rigor.

```
EXEMPLO DE MINIMIZACAO APLICADA AO CHECKPOINT

  antes  a foto da visita e salva com todos os metadados EXIF,
         incluindo coordenadas do momento da captura e modelo
         do aparelho

  analise  a coordenada ja e gravada no registro da visita;
           o EXIF duplica o dado e o expoe a quem receber a foto

  depois  os metadados EXIF sao removidos antes de armazenar
          e antes de enviar

  ganho  um dado pessoal a menos em um lugar a mais
```

### S5 — Dossiê consolidado · aula 20 · 25%

| Artefato | Conteúdo |
|---|---|
| Relatório de teste do próprio app | o que foi testado, como, e o que foi encontrado |
| Vulnerabilidades encontradas | classificadas por severidade, com passos de reprodução |
| Correções | o que foi corrigido, com o commit e a evidência de antes e depois |
| Pendências declaradas | o que **não** foi corrigido, e por quê |
| Verificação das especificações | S2, S3 e S4 foram implementadas? O que divergiu? |
| Dossiê consolidado | os cinco marcos revisados, coerentes entre si |

As **pendências declaradas** valem pontos, e não descontam. Uma equipe que
encontra oito vulnerabilidades, corrige cinco e declara três com justificativa
demonstra mais compreensão do que uma que declara ter corrigido tudo.

Isso é deliberado, e reflete a prática: nenhum sistema real é entregue sem risco
residual. O que se espera de um profissional é que o risco residual esteja
**conhecido e registrado**, e não escondido.

---

## O que testar na aula 18

Roteiro mínimo do laboratório, aplicado ao próprio aplicativo da equipe.

| Alvo | Verificação | Ferramenta |
|---|---|---|
| Banco local | o SQLite é legível fora do app? | `adb exec-out run-as`, DB Browser |
| Token | onde está armazenado? É legível? | inspeção de `SharedPreferences` e do secure storage |
| Tráfego | é possível interceptar e ler? | mitmproxy com certificado instalado |
| Tráfego | o app aceita certificado inválido? | mitmproxy sem confiança |
| API | é possível acessar recurso de outro técnico? | `curl` com identificador alterado |
| API | é possível operar sem token? | `curl` sem cabeçalho de autorização |
| Entrada | o formulário aceita entrada maliciosa? | strings de teste de injeção |
| APK | há segredo embutido no pacote? | `apktool`, `jadx`, `grep` |
| Logs | há dado sensível no `adb logcat`? | uso normal com logcat aberto |
| Dependências | há biblioteca com vulnerabilidade conhecida? | `dart pub outdated`, OSV-Scanner |
| Permissões | o app pede mais do que precisa? | leitura do `AndroidManifest.xml` |
| Fotos | os metadados EXIF são removidos? | `exiftool` na foto armazenada |

Cada verificação produz uma linha no relatório, mesmo quando o resultado é
negativo. "Verificado, não encontrado" é informação; ausência de linha não é.

---

## Regras do laboratório

Duas regras valem para todas as atividades práticas e constam do termo assinado
na aula 01.

**O alvo é sempre o aplicativo da própria equipe.** Testar sistema de terceiro,
mesmo sem causar dano e mesmo por curiosidade, configura acesso não autorizado, e
é crime tipificado no artigo 154-A do Código Penal brasileiro, com pena de
detenção. Nenhuma atividade desta disciplina autoriza acesso a sistema que não
seja o da equipe.

**Técnica de ataque é estudada para permitir defesa.** Não é possível escrever
defesa eficaz contra um ataque que não se compreende. O conteúdo é o mesmo
estudado por quem trabalha profissionalmente em defesa de sistemas, e o uso
indevido é responsabilidade de quem o pratica.

---

## Níveis de escopo

A equipe escolhe no S1 e declara no `README.md` do dossiê.

| Nível | Escopo | Nota máxima |
|---|---|---|
| **1 — Essencial** | os cinco marcos com o conteúdo mínimo; roteiro de teste completo | 8,5 |
| **2 — Completo** | + criptografia do banco local efetivamente implementada; remoção de EXIF; testes automatizados dos casos de abuso | 10,0 |
| **3 — Avançado** | + fixação de certificado; detecção de aparelho com root; revogação de sessão funcionando offline | 10,0 + bônus |

Bônus, até 1,5 ponto:

- Verificação com a OWASP MASVS, nível 1, com o checklist preenchido
- Análise de uma biblioteca de terceiro usada pelo app, buscando vulnerabilidade conhecida
- Roteiro de resposta a incidente: o que a equipe faria se o vazamento ocorresse
- Comparação de duas estratégias de armazenamento de token, com o risco de cada uma

---

## Critérios de avaliação

| Critério | Pontos |
|---|---|
| Corretude técnica | 35 |
| Justificativa das decisões de segurança | 30 |
| Qualidade dos artefatos e das evidências | 25 |
| Clareza da comunicação | 10 |

### O que reduz a nota de forma significativa

Vulnerabilidade relatada sem passos de reprodução. Correção sem evidência de
antes e depois. Modelo de ameaças sem fronteiras de confiança. Política de logs
que não lista o que nunca deve ser registrado. Inventário de dados que conclui
que nada pode ser minimizado. Especificação escrita e nunca implementada, sem que
a divergência apareça no S5.

### O que zera a entrega

Teste realizado em sistema de terceiro. Vulnerabilidade inventada. Evidência
fabricada.

---

## Relação com a avaliação do Porto Digital

O plano de ensino determina que 50% da nota da UP2 provém da avaliação do projeto
pelos preceptores e mentores do Porto Digital.

O Dossiê de Segurança é o artefato submetido. Isso tem uma consequência sobre a
forma: o documento precisa ser legível por alguém **de fora**, que não acompanhou
o semestre e não conhece o Checkpoint.

Por isso o dossiê começa com um **resumo executivo de uma página** contendo: o
que é o aplicativo, o que foi auditado, quantas vulnerabilidades foram
encontradas por severidade, quantas foram corrigidas, e qual o risco residual.

Um profissional de mercado avaliando o trabalho lê essa página primeiro, e
frequentemente decide a partir dela quanto do resto vai ler.

---

## Estrutura da entrega

```
dossie-seguranca/
├── README.md                     resumo executivo (1 página) + nível escolhido
├── s1-modelo-ameacas/
│   ├── fluxo-de-dados.md         com as fronteiras de confiança
│   ├── stride.md                 a matriz
│   └── riscos.md                 priorizados
├── s2-autenticacao/
│   ├── requisitos.md
│   ├── ciclo-de-vida-sessao.md
│   ├── autorizacao.md
│   └── casos-de-abuso.md
├── s3-entrada-config-logs/
│   ├── validacao.md
│   ├── segredos.md
│   └── erros-e-logs.md
├── s4-protecao-dados/
│   ├── inventario-dados-pessoais.md
│   ├── criptografia.md
│   └── lgpd.md
├── s5-dossie/
│   ├── relatorio-teste.md
│   ├── vulnerabilidades.md
│   ├── correcoes.md
│   └── pendencias.md
└── evidencias/
    ├── antes-depois/
    ├── capturas/
    └── saidas-de-comando/
```

---

Dúvidas podem ser registradas como *issue* no repositório de exercícios.
