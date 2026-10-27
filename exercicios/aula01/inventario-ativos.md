# Parte D — Inventário de ativos do Checkpoint

## D1 — Identificação e classificação dos ativos

**Critério de classificação:** Alta: dados pessoais, credenciais ou informações cuja exposição pode causar danos significativos. Média: informações operacionais cuja alteração ou indisponibilidade prejudica o serviço. Baixa: informações públicas ou de impacto reduzido.

| ID | Ativo | Localização prevista no cenário | Responsável pelos dados | Valor para a operação | Sensibilidade |
|---|---|---|---|---|---|
| AT-01 | Banco SQLite de visitas | Armazenamento local do dispositivo | Organização responsável pelas visitas | Permite consultar e registrar atividades offline | Alta |
| AT-02 | Dados cadastrais dos clientes | Banco local e/ou servidor | Organização responsável pelo cadastro | Identifica os clientes atendidos | Alta |
| AT-03 | Token de sessão do técnico | Armazenamento de sessão do aplicativo | Equipe responsável pela autenticação | Autoriza o acesso ao sistema | Alta |
| AT-04 | Histórico agregado de visitas | Banco MySQL do servidor | Organização responsável pelas visitas | Permite auditoria, relatórios e acompanhamento das atividades | Alta |
| AT-05 | Registros de presença e horários | Banco local e/ou servidor | Organização responsável pelas visitas | Comprova a realização dos atendimentos | Média |
| AT-06 | Fotografias das visitas | Armazenamento local e/ou remoto | Organização responsável pelas visitas | Fornece evidências das atividades executadas | Alta |
| AT-07 | Credenciais dos usuários | Serviço de autenticação | Organização responsável pelo controle de acesso | Protege a identidade e o acesso dos técnicos | Alta |
| AT-08 | Registros de sincronização | Aplicativo e/ou servidor | Equipe responsável pela operação | Ajuda a identificar falhas e conflitos de dados | Média |
| AT-09 | Configurações de comunicação com a API | Aplicativo móvel | Equipe de desenvolvimento | Permite a comunicação entre aplicativo e servidor | Média |
| AT-10 | Logs de operação e segurança | Aplicativo e/ou servidor | Equipe responsável pela operação | Auxilia no diagnóstico e na investigação de incidentes | Alta |

**Observação:** Este é um inventário preliminar baseado no cenário descrito no exercício. As localizações e os ativos devem ser confirmados na implementação real do Checkpoint antes da entrega.

## D2 — Proteções e custos
### AT-01 — Banco SQLite de visitas

**Proteção proposta:** Criptografar o banco de dados local, armazenando a chave criptográfica de forma segura no dispositivo, e restringir o acesso aos dados pelo aplicativo.

**Benefício:** Reduz o risco de exposição de informações de clientes caso o celular seja perdido ou roubado.

**Custo e compromisso:** Exige implementação e gerenciamento adequados das chaves. Se a chave for perdida ou ficar inacessível, os registros locais podem não ser recuperados, prejudicando o trabalho offline.

### AT-03 — Token de sessão do técnico

**Proteção proposta:** Armazenar o token em mecanismo seguro do sistema operacional, limitar seu tempo de validade e impedir que seja registrado em logs.

**Benefício:** Diminui a possibilidade de utilização indevida da sessão caso informações do aplicativo sejam expostas.

**Custo e compromisso:** A expiração do token pode exigir nova autenticação, dificultando o acesso durante períodos sem internet. É necessário definir um mecanismo de continuidade offline com permissões limitadas.

### AT-04 — Histórico agregado de visitas

**Proteção proposta:** Implementar controle de acesso por perfil, backups periódicos e registro de alterações relevantes no servidor.

**Benefício:** Protege a integridade e a disponibilidade do histórico, reduzindo o risco de alterações não autorizadas e perda de registros.

**Custo e compromisso:** Aumenta os custos de armazenamento, manutenção e monitoramento. Os backups também exigem testes de restauração e proteção contra acesso indevido.

**Observação:** As medidas descritas são propostas de proteção e não representam funcionalidades já verificadas no Checkpoint.
