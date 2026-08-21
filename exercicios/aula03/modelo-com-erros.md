# Modelo de Ameaças — Checkpoint
### Equipe 7 · entrega do S1

> ⚠️ **Este documento é material de exercício.** Ele foi escrito de propósito com
> **oito erros** de método e de redação, dos tipos vistos na seção 9 da Aula 03.
> Não use como referência de como escrever um modelo de ameaças.

---

## 1. Diagrama de fluxo de dados

```
  tecnico  ──▶  tela de login  ──▶  repositorio  ──▶  SQLite local
                                          │
                                          ▼
                                   sincronizador  ──▶  API REST  ──▶  MySQL
                                                            │
                                                            ▼
                                                        Firebase
```

## 2. Ativos

| # | Ativo |
|---|---|
| A1 | dados de clientes |
| A2 | observações |
| A5 | token |

## 3. Matriz STRIDE

| # | Elemento | STRIDE | Ameaça | Vulnerabilidade atual | P | I | Risco |
|---|---|---|---|---|---|---|---|
| L1 | tela de login | T | SQL Injection | campo sem validação | alta | alto | crítico |
| L2 | SQLite local | I | alguém lê o banco do aparelho perdido | banco em texto puro | alta | alto | risco de vazamento de dados |
| L3 | SQLite local | S | o banco se passa por outro banco do mesmo app | nenhuma | baixa | baixo | baixo |
| L4 | técnico | E | o técnico consegue privilégio de administrador | nenhuma | média | alto | alto |
| L5 | SQLite local | I | o banco não é criptografado | banco em texto puro | alta | alto | crítico |
| L6 | fluxo app ──▶ API | I | alguém lê as visitas em wi-fi público | `http://` como alternativa quando o `https` falha | baixa | médio | crítico |
| L7 | API REST | E | o técnico troca o id em `GET /visitas/{id}` e lê visita de outro | nenhuma, será corrigido no marco M4 | alta | alto | crítico |
| L8 | tela de login | D | entrada muito longa trava a tela | campo sem `maxLength` | baixa | baixo | baixo |
| L9 | sincronizador | T | alterar a visita em trânsito | sem TLS obrigatório | média | alto | alto |
| L10 | tela de login | I | senha aparece no log do sistema | `debugPrint` do formulário em `login_page.dart:64` | alta | alto | crítico |

## 4. Riscos priorizados

| # | Risco | Decisão |
|---|---|---|
| R1 | senha no log | tratar |
| R2 | banco em texto puro | tratar |
| R3 | dados em trânsito | tratar |
| R4 | IDOR na API | tratar |
| R5 | injeção no login | tratar |
| R6 | privilégio de administrador | tratar |
| R7 | entrada longa na tela | tratar |
| R8 | banco se passando por outro | tratar |

## 5. Conclusão

Todos os riscos identificados serão tratados ao longo do semestre. O aplicativo
estará seguro ao fim do desenvolvimento.
