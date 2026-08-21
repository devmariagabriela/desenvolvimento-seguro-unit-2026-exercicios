# Exercícios — Desenvolvimento Seguro

**UNIT 2026** · Disciplina `F105457` · Prof. Petros Barreto
**40 horas** · 20 aulas de 2h · 2 créditos · 5º período

Repositório público da disciplina. Contém as listas semanais e a especificação do
trabalho final, o **Dossiê de Segurança**.

Os slides ficam em repositório separado, cujo endereço é distribuído em sala e no
ambiente virtual.

---

## Esta disciplina é integrada a Desenvolvimento Mobile

A turma é a mesma, e as duas disciplinas trabalham sobre o **mesmo aplicativo**:
o [Checkpoint](https://github.com/petrosbarreto/checkpoint-app).

```
MOBILE   constrói o Checkpoint
SEGURO   especifica os requisitos de segurança  →  Mobile implementa
         audita o resultado
```

Não haverá um segundo aplicativo. O trabalho final desta disciplina — o Dossiê de
Segurança — integra o marco M7 do projeto de Desenvolvimento Mobile.

As equipes são as mesmas das duas disciplinas. Quem especifica um requisito de
segurança aqui é quem o implementa lá.

---

## ⚠️ Regras que valem para todo o semestre

**O alvo de toda atividade prática é o aplicativo da própria equipe.** Testar
sistema de terceiro, mesmo sem causar dano e mesmo por curiosidade, configura
acesso não autorizado — crime tipificado no artigo 154-A do Código Penal, com
reclusão de 1 a 4 anos. Não é necessário causar dano, e a ausência de aviso não
constitui autorização.

**Técnica de ataque é estudada para permitir defesa.** Não é possível escrever
defesa eficaz contra um mecanismo que não se compreende. O uso indevido é
responsabilidade de quem o pratica.

**Nunca versione dado nem segredo.** O `.gitignore` deste repositório bloqueia
`*.db`, `*.sqlite`, `*.jks`, `*.keystore`, `key.properties`, `.env` e
`google-services.json`, e a verificação automática **bloqueia o pull request** se
algum passar. Enviar dado para repositório público é exatamente o erro que a
disciplina estuda — e o histórico do Git preserva o conteúdo mesmo após a remoção.

---

## Preparação do ambiente

```bash
# 1. Faça fork deste repositório

# 2. Clone o seu fork
git clone https://github.com/SEU-USUARIO/desenvolvimento-seguro-unit-2026-exercicios.git
cd desenvolvimento-seguro-unit-2026-exercicios

# 3. Aponte para o original
git remote add upstream https://github.com/petrosbarreto/desenvolvimento-seguro-unit-2026-exercicios.git
```

Ferramentas, instaladas conforme a aula:

| Aula | Ferramenta | Uso |
|---|---|---|
| 01 | `adb`, DB Browser for SQLite | extrair e inspecionar o banco local |
| 02 | navegador | referências da OWASP |
| 10 | `curl`, Postman | testar a API com entrada maliciosa |
| 11 | `apktool`, `jadx` | procurar segredo embutido no APK |
| 14 | mitmproxy ou Burp Suite Community | interceptar tráfego |
| 15 | `flutter_secure_storage`, `sqlcipher` | criptografia no aparelho |
| 17 | `exiftool` | inspecionar metadados de foto |
| 18 | OSV-Scanner, `dart pub outdated` | vulnerabilidade em dependência |

O `adb` e o DB Browser já foram usados na aula 04 de Desenvolvimento Mobile.

---

## Entrega de uma lista

```bash
git switch main && git pull upstream main
git switch -c aula07
# resolva em exercicios/aula07/
git status                      # confira que nenhum .db entrou
git add exercicios/aula07
git commit -m "Aula 07: modelo de autorização e menor privilégio"
git push -u origin aula07
```

Abrir pull request com o título `[Aula NN] Seu Nome Completo`. Para os marcos do
dossiê, `[Seguro SN] Nome da Equipe`.

Pull request com título fora do padrão não é corrigido.

---

## Formato de cada entrega

```
exercicios/aulaNN/
├── RESPOSTAS.md          respostas, análises e justificativas
├── <artefato>.md         inventário, especificação, política — conforme a lista
└── evidencias/
    ├── capturas/         com dado sensível parcialmente oculto
    └── saidas/           saída de comando, em texto
```

Três regras valem para todas as listas.

As seções do `RESPOSTAS.md` seguem a numeração do enunciado. **Todo achado de
segurança precisa de passos de reprodução** — achado sem reprodução não é achado,
é impressão. E **toda proteção proposta declara o que custa** em outra dimensão;
proposta sem custo declarado indica custo não percebido.

---

## Critérios de correção

| Critério | Pontos | O que se avalia |
|---|---|---|
| Corretude técnica | 35 | a análise e a classificação estão certas |
| Justificativa das decisões de segurança | 30 | as escolhas e os riscos aceitos se sustentam |
| Qualidade dos artefatos e das evidências | 25 | reprodutíveis, legíveis, sem dado exposto |
| Clareza da comunicação | 10 | organização e redação |

A justificativa pesa 30 pontos porque segurança é compromisso, e não propriedade.
Nenhum exercício desta disciplina pede "deixar seguro"; todos pedem identificar o
risco, decidir e justificar o que se aceitou.

### Prazos

Entrega até a véspera da aula seguinte, 23h59. Atraso de até sete dias desconta 20
pontos; acima disso a lista não é corrigida.

---

## Avaliação da disciplina

O plano de ensino determina que **50% da nota da UP2 provém da avaliação dos
projetos pelos preceptores e mentores do Porto Digital**.

### UP1

| Instrumento | Peso |
|---|---|
| Prova escrita, objetiva e subjetiva contextualizada | 40% |
| Marcos S1 e S2 do dossiê | 40% |
| Listas das aulas 01 a 10 | 20% |

### UP2

| Instrumento | Peso |
|---|---|
| **Avaliação do projeto pelo Porto Digital** | **50%** |
| Marcos S3, S4 e S5 do dossiê | 30% |
| Seminário sobre um caso real de incidente | 20% |

---

## Trabalho final: Dossiê de Segurança do Checkpoint

| Marco | Aula | Entrega | Peso |
|---|---|---|---|
| S1 | 03 | Modelo de ameaças STRIDE, com riscos priorizados | 15% |
| S2 | 08 | Especificação de autenticação, sessão e autorização | 20% |
| S3 | 12 | Especificação de validação, configuração, erros e logs | 20% |
| S4 | 17 | Plano de proteção de dados: criptografia e LGPD | 20% |
| S5 | 20 | Dossiê consolidado, com teste do app, correções e evidências | 25% |

Cada especificação é escrita aqui e implementada no marco correspondente de
Desenvolvimento Mobile. O S5 verifica se foi cumprida — e o que não foi entra como
**pendência declarada**, que vale pontos em vez de descontar.

Especificação completa em [`projeto/README.md`](projeto/README.md).

---

## Índice das listas

| Unidade | Aulas | Tema |
|---|---|---|
| **I** | [01](exercicios/aula01) · 02 | Fundamentos e superfície de ataque |
| **I** | 03 | Modelagem de ameaças |
| **I** | 04 · 05 · 06 | Gerenciamento de sessão e autenticação |
| **I** | 07 · 08 | Autorização |
| **I** | 09 · 10 | Manejo de entrada de usuário |
| **I** | 11 · 12 | Configuração, manejo e registro de erros |
| **II** | 13 · 14 · 15 | Fundamentos da criptografia |
| **II** | 16 · 17 | Engenharia segura de software |
| **II** | 18 · 19 · 20 | Práticas de laboratório e dossiê |

---

## Bibliografia

**Básica**
- BASTA, A. *Segurança de computadores e teste de invasão*. Cengage Learning, 2014.
- MACHADO, F. N. R. *Segurança da informação: princípios e controle de ameaças*. Érica, 2016.
- KIM, D. *Fundamentos de segurança de sistemas de informação*. LTC, 2014.

**Complementar**
- STALLINGS, W. *Criptografia e segurança de redes*. 6. ed. Pearson, 2015.
- IMONIANA, J. O. *Auditoria de sistemas de informação*. 3. ed. Atlas, 2016.
- MATTOS, A. C. M. *Sistemas de informação: uma visão executiva*. 2. ed. Saraiva, 2014.
- O'BRIEN, J. A. *Sistemas de informação e as decisões gerenciais na era da internet*. 3. ed. Saraiva, 2011.

**Referências técnicas de acesso livre**
- OWASP Top 10 — <https://owasp.org/Top10/>
- OWASP Mobile Application Security — <https://mas.owasp.org>
- OWASP Cheat Sheet Series — <https://cheatsheetseries.owasp.org>
- LGPD, Lei nº 13.709/2018 — <https://www.planalto.gov.br/ccivil_03/_ato2015-2018/2018/lei/l13709.htm>
- Código Penal, art. 154-A — <https://www.planalto.gov.br/ccivil_03/decreto-lei/del2848compilado.htm>

---

## Colaboração e integridade

É permitido discutir ideias com colegas, consultar referências e usar assistentes
de IA, com declaração do uso no `RESPOSTAS.md`.

Não é permitido: inventar vulnerabilidade, fabricar evidência, testar sistema de
terceiro, ou entregar análise que a equipe não consiga explicar.

Achado fabricado zera a entrega. Em segurança, relatório com achado inventado é
pior do que relatório vazio: ele consome o tempo de quem vai investigar e destrói
a confiança em todos os outros achados do mesmo documento.

---

Dúvidas podem ser registradas como [issue](../../issues) neste repositório.
