# Lista 01 — Desenvolvimento Seguro

**Aluna:** Maria Gabriela da Silva Pereira  
**Professor:** Petros Barreto  
**Disciplina:** Desenvolvimento Seguro  
**Turma:** 5º período — Noite

## Parte A — Tríade CID

### A1 — Classificação dos cenários

| Cenário | Atributo principal violado | Outros atributos potencialmente afetados | Justificativa |
|---|---|---|---|
| a | Confidencialidade | — | O acesso ao banco SQLite de um celular roubado permite que uma pessoa não autorizada consulte informações armazenadas. |
| b | Integridade | — | A alteração da data de uma visita sem registro compromete a confiabilidade e a rastreabilidade das informações. |
| c | Disponibilidade | — | A perda da chave criptográfica impede a leitura dos dados, mesmo que continuem armazenados no aparelho. |
| d | Confidencialidade | Integridade, caso o token também permita alterações | O vazamento do token permite acesso indevido ao histórico de visitas pela API. |
| e | Disponibilidade | — | A indisponibilidade do servidor impede o acesso aos serviços da API durante três dias. |
| f | Integridade | Disponibilidade dos registros corretos | A sincronização sobrescreve dados recentes com informações antigas, comprometendo sua correção e recuperação. |
| g | Confidencialidade | Integridade, dependendo das permissões do token | O token registrado em texto claro nos logs pode ser exposto a pessoas não autorizadas. |
| h | Disponibilidade | — | A exigência de internet impede o registro de frequência quando o técnico está sem conexão. |

### A2 — Disponibilidade e decisões de segurança

Os cenários c e h têm em comum o comprometimento da disponibilidade causado por decisões relacionadas à proteção ou ao funcionamento do aplicativo. No cenário c, a perda da chave criptográfica impede o acesso aos dados armazenados. Já no cenário h, a exigência de conexão com a internet impossibilita o registro de frequência em locais sem rede.

Esse tipo de problema costuma ser esquecido porque, ao implementar mecanismos de segurança, a equipe pode priorizar a confidencialidade e a integridade sem avaliar suficientemente os impactos na disponibilidade. No contexto do Checkpoint, isso é especialmente importante, pois os técnicos precisam continuar trabalhando mesmo em regiões com conectividade limitada. Portanto, as medidas de proteção devem considerar também a continuidade das operações.

## Parte B — Os cinco termos

### B1 — Classificação dos conceitos

| Item | Classificação | Justificativa |
|---|---|---|
| a | Ativo | O banco SQLite armazena informações dos clientes que precisam ser protegidas. |
| b | Ameaça | O roubo do celular é um evento que pode comprometer os dados e o funcionamento do aplicativo. |
| c | Vulnerabilidade | A ausência de criptografia facilita a leitura não autorizada dos dados armazenados. |
| d | Ataque | A extração e leitura do banco representam a exploração concreta de uma vulnerabilidade. |
| e | Risco | Representa a possibilidade de exposição de dados pessoais, considerando a frequência dos roubos e o impacto. |
| f | Ativo | O token de sessão é uma credencial que permite acesso aos recursos do sistema. |
| g | Vulnerabilidade | A aceitação de certificados TLS inválidos enfraquece a verificação da identidade do servidor. |
| h | Ataque | A interceptação do tráfego representa uma ação contra a comunicação do aplicativo. |
| i | Risco | A permanência do acesso de um funcionário desligado pode resultar em uso indevido do sistema. |
| j | Vulnerabilidade | A falha na revogação de sessões offline permite que credenciais continuem válidas indevidamente. |
| k | Ameaça | Um funcionário insatisfeito pode representar uma fonte potencial de ações maliciosas. |
| l | Ativo | O histórico de visitas armazenado no MySQL contém informações importantes para a operação. |

### B2 — Controle da equipe de desenvolvimento

**Itens sob controle direto da equipe de desenvolvimento:**

- **c)** Ausência de criptografia no banco SQLite.
- **g)** Aceitação de certificados TLS inválidos.
- **j)** Falha na revogação de sessões quando o aparelho está offline.

**Itens que não estão diretamente sob controle da equipe:**

- **a, f, l)** Ativos que precisam ser protegidos: banco SQLite, token de sessão e histórico de visitas.
- **b, k)** Fontes de ameaça: roubo de celular e funcionário insatisfeito.
- **d, h)** Ataques: extração indevida do banco e interceptação de tráfego.
- **e, i)** Riscos: exposição de dados pessoais e manutenção indevida de acesso após desligamento.

**Justificativa:**

A equipe de desenvolvimento deve concentrar seus esforços nas vulnerabilidades que consegue corrigir diretamente no aplicativo.  
A ausência de criptografia, a validação inadequada de certificados TLS e as falhas na revogação de sessões são exemplos dessas fraquezas.  
Já ameaças externas, como o roubo de aparelhos, não podem ser eliminadas apenas por alterações no código.  
Entretanto, a equipe pode implementar controles que reduzam as consequências desses acontecimentos.  
Os ativos precisam ser identificados e protegidos de acordo com sua sensibilidade e importância.  
Os ataques devem ser considerados na definição dos testes e mecanismos de defesa.  
Os riscos precisam ser avaliados conforme a probabilidade de ocorrência e o impacto esperado.  
Dessa forma, a equipe atua sobre as causas técnicas controláveis e reduz a exposição aos fatores que não controla diretamente.

## Parte C — Compromissos de segurança

### C1 — Ganhos e perdas

| Item | Decisão de segurança | O que se ganha | O que se perde |
|---|---|---|---|
| a | Criptografar o banco SQLite local | Protege a confidencialidade dos dados caso o aparelho seja perdido ou roubado. | Aumenta a complexidade de implementação e o gerenciamento de chaves. Se a chave for perdida, os dados locais podem ficar inacessíveis. |
| b | Exigir reautenticação a cada 30 minutos | Reduz o tempo em que uma sessão abandonada pode ser utilizada por outra pessoa. | Interrompe o trabalho do técnico e pode impedir a continuidade das atividades em locais sem conexão, dependendo do mecanismo de autenticação. |
| c | Bloquear a conta após três tentativas incorretas | Dificulta tentativas repetidas de adivinhar a senha. | Um usuário legítimo pode ficar bloqueado por erros de digitação, e terceiros podem provocar bloqueios intencionais. |
| d | Registrar todas as requisições com o corpo completo | Facilita a investigação de erros e a rastreabilidade das operações. | Pode expor dados pessoais, credenciais e informações de clientes nos logs, além de aumentar o armazenamento necessário. |
| e | Exigir senha de 16 caracteres com símbolos | Pode aumentar a resistência a tentativas de descoberta de senha, quando a senha é suficientemente imprevisível. | Dificulta a memorização e a digitação em campo, podendo incentivar práticas inseguras, como anotar senhas. |
| f | Remover os metadados EXIF das fotografias | Reduz a exposição involuntária de informações, como localização e identificação do dispositivo. | Pode eliminar coordenadas e horários úteis para comprovar onde e quando uma visita foi realizada. |
| g | Apagar os dados locais após sete dias sem sincronização | Reduz a permanência de informações sensíveis em aparelhos perdidos ou sem gerenciamento. | Pode destruir registros ainda não sincronizados de técnicos que passam longos períodos trabalhando sem cobertura de rede. |

### C2 — Recomendações

**1. Item B — Exigir reautenticação a cada 30 minutos**

**Recomendação:** Adotar com ajuste.

Recomendo que o Checkpoint utilize um tempo de expiração de sessão baseado no risco da operação, em vez de exigir reautenticação obrigatória a cada 30 minutos. Para ações sensíveis, como alteração de credenciais ou acesso a informações restritas, a reautenticação deve ser exigida. Já durante o registro de visitas em campo, o aplicativo pode permitir a continuidade temporária de operações locais mediante autenticação segura no dispositivo, mesmo sem conexão.

**Justificativa:** Essa abordagem reduz o risco de acesso indevido sem comprometer excessivamente a produtividade dos técnicos. O custo é a maior complexidade de implementação e a permanência de algum risco de uso indevido durante o período offline.

**2. Item G — Apagar dados locais após sete dias sem sincronização**

**Recomendação:** Não adotar a exclusão automática e indiscriminada após sete dias.

Recomendo substituir essa medida por criptografia dos dados locais, proteção das chaves no armazenamento seguro do dispositivo e políticas de retenção que diferenciem registros já sincronizados daqueles ainda pendentes. A exclusão automática deve ocorrer apenas quando houver confirmação de sincronização ou outra condição previamente definida e validada pela equipe.

**Justificativa:** Um técnico pode permanecer mais de sete dias em uma região sem cobertura de internet. Apagar automaticamente os dados poderia causar perda irreversível de registros de visitas e comprometer o serviço. A alternativa exige mais armazenamento e gerenciamento de dados, além de manter algum risco residual caso o aparelho seja comprometido antes da sincronização.

## Parte E — Redação de achados

### E1 — Reescrita dos achados

**Escopo e limite:** os itens abaixo são modelos de achados verificáveis para o Checkpoint, não afirmações de que uma vulnerabilidade já foi comprovada. O repositório-base do professor usa armazenamento em memória no marco M1; a implementação SQLite e a sincronização são marcos posteriores. É necessário testar a versão instalada pela própria equipe antes de marcar qualquer item como confirmado.

**a) Possível exposição do banco de visitas local (condicional ao resultado da E2)**

- **Ativo:** banco SQLite local e dados das visitas e dos clientes.
- **Vulnerabilidade a verificar:** armazenamento de dados sensíveis em SQLite sem criptografia ou sem proteção suficiente para o cenário de acesso ao arquivo.
- **Impacto concreto:** quem obtiver uma cópia do banco poderá ler nomes, endereços, horários ou observações de visitas, caso esses campos estejam presentes e legíveis.
- **Reprodução autorizada:** (1) instalar a versão da equipe em emulador ou aparelho de teste; (2) criar visitas fictícias; (3) identificar o pacote com `adb shell pm list packages | grep checkpoint`; (4) tentar exportar o banco da própria instalação usando `run-as` em versão depurável; (5) abrir a cópia no DB Browser for SQLite; (6) anotar quais campos aparecem e se houve solicitação de senha. **Não afirmar a vulnerabilidade se o banco não existir, a extração falhar ou o conteúdo estiver protegido.**
- **Status:** não verificado; depende da E2.

**b) Possível fragilidade na autenticação ou na comunicação com a API**

- **Ativo:** sessão do técnico, credenciais e registros de visita acessados pela API.
- **Vulnerabilidade a verificar:** ausência de validação adequada do certificado TLS ou de autorização no acesso às visitas; não há comprovação dessa falha no repositório-base M1.
- **Impacto concreto:** se uma dessas falhas existir, poderá ocorrer exposição do token ou acesso a visitas de outro usuário.
- **Reprodução autorizada:** (1) usar apenas a API e as contas de teste da própria equipe; (2) inspecionar a configuração do cliente HTTP e a validação de certificados; (3) testar com contas fictícias de perfis diferentes se uma sessão consegue acessar dados que não lhe pertencem; (4) registrar resposta, versão e evidências com tokens ocultos. Não executar interceptação ou testes em serviços de terceiros.
- **Status:** não verificado; depende da implementação M4/M5 e de testes da equipe.

**c) Possível exposição de dados sensíveis nos logs**

- **Ativo:** token de sessão, dados pessoais e registros de diagnóstico.
- **Vulnerabilidade a verificar:** instruções de log que imprimam tokens, cabeçalhos `Authorization` ou corpos de requisições contendo dados pessoais.
- **Impacto concreto:** pessoas com acesso aos registros de diagnóstico podem recuperar credenciais ou informações privadas.
- **Reprodução autorizada:** (1) executar a versão de teste do Checkpoint; (2) autenticar com uma conta fictícia e realizar uma visita de teste; (3) examinar apenas os logs gerados pelo próprio aplicativo e servidor da equipe; (4) procurar nomes de cabeçalhos e dados fictícios, sem copiar segredos reais; (5) registrar arquivo, linha ou captura com informações ocultas, se houver exposição.
- **Status:** não verificado; depende de logs efetivamente observados.

### E2 — Extração do banco SQLite

**Resultado:** pendente de execução na versão instalada e autorizada do Checkpoint da equipe. A inspeção do repositório-base do professor não substitui o teste no aparelho; o README do projeto informa que o marco M1 utiliza um repositório em memória e que SQLite é implementado no M3.

**Procedimento previsto para o laboratório:**

```bash
adb devices
adb shell pm list packages | grep checkpoint
# Confirme o nome real do pacote e o nome do banco antes de executar.
adb exec-out run-as br.edu.unit.checkpoint \
    cat databases/checkpoint.db > checkpoint.db
```

O comando `run-as` normalmente exige instalação depurável. Se o pacote ou banco forem diferentes, registrar a diferença; se a extração falhar, registrar a mensagem de erro, sem fabricar resultado. Abrir somente o arquivo de teste no DB Browser for SQLite.

| Pergunta | Resposta |
|---|---|
| O arquivo abriu sem senha? | Não verificado — requer teste prático |
| Quais campos são legíveis? | Não verificado — requer inspeção do banco |
| Quantos registros havia? | Não verificado — requer contagem real |
| Quanto tempo levou o procedimento inteiro? | Não verificado — cronometrar execução |

**Evidência exigida:** `exercicios/aula01/evidencias/banco-extraido.png`, captura verdadeira do DB Browser com dados sensíveis ocultos. **Nunca adicionar `checkpoint.db`, arquivos `.sqlite` ou credenciais ao Git.**

## Parte F — Investigação de incidente real

**Caso: exposição de localizações pelo aplicativo Strava (2017–2018).**

1. O Strava é um aplicativo móvel que registra corridas, caminhadas e outras atividades por GPS.
2. Em novembro de 2017, a empresa disponibilizou um mapa de calor com atividades compartilhadas por usuários.
3. Em janeiro de 2018, pesquisadores identificaram que o mapa permitia inferir posições de instalações militares.
4. Em regiões pouco movimentadas, trajetos repetidos destacavam bases e rotas de deslocamento.
5. O atributo da tríade CID principalmente comprometido foi a **confidencialidade**.
6. A exposição ocorreu por uma decisão de produto e privacidade no tratamento e na publicação de dados agregados.
7. Não há necessidade de supor invasão de servidores ou roubo de senhas para explicar o incidente.
8. A ameaça foi a possibilidade de terceiros correlacionarem dados públicos para descobrir informações sensíveis.
9. Usuários do aplicativo, inclusive militares, podiam ter suas rotinas e localizações inferidas.
10. Não foi divulgado um número preciso de pessoas efetivamente prejudicadas por essa exposição.
11. Como consequência, o Departamento de Defesa dos EUA revisou orientações sobre o uso de dispositivos e aplicativos de localização.
12. O Strava anunciou restrições ao detalhamento e ao acesso ao mapa de calor em março de 2018.
13. O caso demonstra que anonimização e agregação não garantem privacidade quando padrões de localização permitem inferências.
14. **Fontes:** Reuters (13/03/2018): https://uk.investing.com/news/world-news/exclusive--fitness-app-strava-overhauls-map-that-revealed-military-positions-1077575 ; Privacy International (29/01/2018): https://privacyinternational.org/examples/1946/strava-fitness-app-exposes-jogging-routes-around-military-bases ; comunicado de senadores dos EUA (14/02/2018): https://www.coons.senate.gov/news/press-releases/sens-coons-flake-press-strava-ceo-on-privacy-data-security-failures-after-fitness-tracking-app-reveals-sensitive-information-of-us-military-service-members

## Declaração de uso de IA

Utilizei o ChatGPT como ferramenta de apoio para organizar as respostas, revisar a redação e compreender conceitos de segurança da informação. Também utilizei a ferramenta para estruturar modelos condicionais de achados e sintetizar fontes públicas do caso Strava. Nenhum teste dinâmico, extração de banco ou captura de evidência foi realizado por esta assistência; essas etapas devem ser executadas e registradas pela equipe.
