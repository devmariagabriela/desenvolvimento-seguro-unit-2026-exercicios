# Exercício — Aula 02: Superfície de Ataque e as Listas da OWASP

**Disciplina:** Desenvolvimento Seguro (`F105457`) — UNIT 2026
**Entrega:** até a véspera da Aula 03, 23h59 — PR `[Aula 02] Seu Nome Completo`

> ⚠️ **O mapa da Parte A é insumo do marco S1**, entregue na Aula 03. Fazê-lo com
> cuidado agora economiza retrabalho depois.

> Alvo desta lista: o **Checkpoint da sua equipe**. Nenhum outro sistema. Vale
> reler o termo de conduta assinado na Aula 01.

---

## Parte A — Mapa de superfície de ataque (30 pontos)

Mapeie **todos** os pontos de entrada do Checkpoint da sua equipe, no estado atual
do código. Use exatamente este formato:

| # | Ponto de entrada | Fronteira | Quem controla | Aceita hoje | Categorias OWASP |
|---|---|---|---|---|---|
| E01 | campo e-mail (login) | F1 | quem tem o aparelho | texto livre, sem limite | A03, A07, M4 |

**Regras de preenchimento:**

1. **"Quem controla"** nunca é "o sistema" nem "o app". É sempre uma pessoa ou
   organização: o técnico, quem tem o aparelho, o servidor, quem estiver na rede,
   o Google, o autor do pacote.
2. **"Aceita hoje"** descreve o código que existe, não o que deveria existir. Se o
   campo não tem `validator`, escreva "texto livre, sem limite".
3. Um ponto pode ter **mais de uma** categoria. Pode também não ter nenhuma — e
   nesse caso justifique em nota.
4. Use `F1` entrada de usuário, `F2` o aparelho, `F3` a rede, `F4` serviço de
   terceiro.
5. Escreva as categorias móveis como `M4`, e explicite quando houver risco de
   confusão com os marcos do projeto de Mobile.

### Comandos que ajudam a encontrar os pontos

```bash
# F1 - campos de entrada, e quais tem validacao
grep -rn "TextFormField\|TextField" lib/
grep -rn "validator:" lib/

# F2 - armazenamento local e registro em log
grep -rn "sqflite\|shared_preferences\|path_provider\|secure_storage" pubspec.yaml lib/
grep -rn "print(\|debugPrint\|log(" lib/
grep -n "allowBackup\|debuggable\|usesCleartextTraffic" \
  android/app/src/main/AndroidManifest.xml

# F3 - rede
grep -rn "http\|Uri.parse\|Dio\|dio" lib/
grep -rn "http://" lib/ android/ ios/ 2>/dev/null

# F4 - cadeia de suprimentos
flutter pub deps --style=compact | wc -l
flutter pub outdated
```

### Exigências de completude

| Item | Por quê |
|---|---|
| **F2 tem mais linhas que F1** | a maior parte da superfície não aparece na tela |
| O backup automático do Android está no mapa | o comando do manifesto costuma não retornar nada, e a ausência é o achado: sem `android:allowBackup` declarado, o padrão do sistema é `true` |
| O log do sistema está no mapa | `debugPrint` em formulário é ponto de vazamento |
| As dependências transitivas estão no mapa, com o número | `flutter pub deps` mostra a diferença entre o que a equipe escolheu e o que veio junto |
| O total de pontos está contado | o número é o que permite comparar o sistema com ele mesmo depois |

**A2 (parte escrita).** Responda no `RESPOSTAS.md`:

1. Quantos pontos ficaram no mapa? Quantos são visíveis na interface?
2. Qual ponto **ninguém da equipe tinha percebido** antes de abrir o código?
   (Se não houver nenhum, o mapeamento provavelmente parou nos campos de tela.)
3. Qual fronteira tem mais pontos? Isso era o esperado?
4. Qual categoria da OWASP aparece em mais linhas? Existe **uma decisão única**
   que resolveria vários desses pontos de uma vez? Qual?

---

## Parte B — As vinte categorias no seu projeto (25 pontos)

Percorra as vinte categorias — dez do Top 10 (2021) e dez do Mobile Top 10 (2024)
— e escreva, para **cada uma**, uma das duas coisas:

```markdown
### A01 — Broken Access Control

**Aplica-se.** Onde: a API do marco M4 ainda não existe, mas a navegação por rota
nomeada em `app_routes.dart` permite abrir `/visitas` sem passar pelo login.
Evidência: `lib/core/routes/app_routes.dart:14`.

### A10 — Server-Side Request Forgery

**Não se aplica hoje.** O servidor não busca recurso em URL indicada pelo cliente.
Passará a se aplicar se o upload de foto do marco M6 for implementado por URL em
vez de por conteúdo.
```

⚠️ **"Não se aplica" é um resultado legítimo de auditoria** — desde que venha com
a razão. O que não vale ponto é deixar a categoria em branco, ou escrever "não se
aplica" sem justificativa.

Para as que **se aplicam**, indique arquivo e, quando possível, linha.

**Pontuação:** 1 ponto por categoria corretamente classificada e justificada, mais
5 pontos pela consistência do conjunto (as categorias que se aplicam batem com o
mapa da Parte A).

---

## Parte C — Três reduções de superfície (25 pontos)

Escolha **três** pontos do seu mapa e proponha uma redução para cada. As três
propostas precisam usar **formas diferentes**:

| Forma | O que é |
|---|---|
| **Remover** | o ponto de entrada deixa de existir |
| **Restringir** | o ponto continua, aceitando menos |
| **Reduzir alcance** | o ponto continua aceitando o mesmo, mas o dado atinge menos |

Formato de cada proposta:

```markdown
### C1 — Restringir: campo de observações

**Ponto:** E05, campo de observações (F1)
**Hoje:** texto livre, sem limite de tamanho
**Proposta:** `maxLength: 1000` no campo, e validação de tamanho no servidor
**Forma:** restringir

**O que isso custa:**
- funcionalidade: o técnico não consegue registrar observação longa; em visita
  complexa, ele vai precisar resumir ou dividir em duas visitas
- implementação: baixa, cerca de 2 linhas no widget e 1 validação no endpoint
- atrito para o usuário: o contador de caracteres aparece na tela e o campo trava
  ao chegar no limite

**Risco residual:** 1000 caracteres ainda permitem carga maliciosa; o limite trata
D (negação de serviço), não M4 (validação). A sanitização continua necessária.
```

⚠️ **Proposta sem custo declarado não passa da metade dos pontos do item.** As três
seções — funcionalidade, implementação, atrito — precisam estar preenchidas, e
"nenhum custo" só é aceitável com argumento.

**C4 (parte escrita).** Qual das três reduções você **não** faria, se a decisão
fosse sua? Por quê? Uma resposta honesta aqui vale mais que três propostas
perfeitas — reconhecer que uma proteção não vale o preço é o raciocínio que a
disciplina cobra.

---

## Parte D — Cadeia de suprimentos (20 pontos)

```bash
flutter pub deps --style=compact > deps.txt
flutter pub outdated
```

**D1 (6 pts).** Quantas dependências a equipe **declarou** no `pubspec.yaml`?
Quantas existem no total, contando as transitivas? Qual a razão entre os dois
números?

**D2 (8 pts).** Escolha **três** dependências transitivas — pacotes que a equipe
não escolheu — e para cada uma responda:

| Pergunta | Onde olhar |
|---|---|
| Quem mantém? | página do pacote no pub.dev |
| Qual foi a última publicação? | pub.dev, aba *Versions* |
| Quantos *likes* e qual a pontuação? | pub.dev, cabeçalho |
| Qual pacote declarado a trouxe? | `flutter pub deps` |
| Ela tem acesso a rede, arquivo ou plataforma nativa? | descrição e código do pacote |

**D3 (6 pts).** Responda:

1. Uma dependência transitiva sem manutenção há três anos, com acesso à rede, é
   `M2 (OWASP)`. O que a equipe pode fazer a respeito, considerando que não a
   escolheu?
2. Por que a categoria de cadeia de suprimentos é **mais grave** em aplicativo
   móvel que em servidor? (Pense em como se aplica uma correção urgente em cada
   caso.)

---

## Entrega

```
exercicios/aula02/
├── RESPOSTAS.md          # Partes A, B, C e D
├── mapa-superficie.md    # a tabela da Parte A, se preferir arquivo separado
└── deps.txt              # saida do flutter pub deps
```

---

## Rubrica

| Critério | Pontos |
|---|---|
| Corretude técnica do mapeamento e da classificação | 35 |
| Justificativa das decisões, com custo declarado | 30 |
| Completude: F2 tratada com o mesmo cuidado que F1 | 25 |
| Clareza e organização do artefato | 10 |

**Prazo:** véspera da Aula 03, 23h59. Atraso de até 7 dias: −20 pontos. Acima
disso, não é corrigido.

---

## 🎁 Bônus (+10 pontos)

Compare o mapa da sua equipe com o da seção 7 do material da aula, que mapeou 23
pontos no `checkpoint-app` de referência.

Encontre **um ponto de entrada que existe no seu projeto e não está naquele mapa**,
e explique por que ele existe no seu — decisão de arquitetura da equipe,
dependência adicional, permissão declarada no manifesto.

*(Encontrar um ponto que o material esqueceu também vale o bônus. Nesse caso,
abra uma [Issue](../../issues) descrevendo o achado.)*
