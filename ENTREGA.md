# Entrega para o cliente

Roteiro para o robô passar a funcionar **em nome do cliente**: WhatsApp na conta da Meta da empresa dele e Google na conta do responsável pela agenda. Durante a montagem, tudo pode rodar nas contas do desenvolvedor, com o número de teste da Meta.

Por que nas contas do cliente:
- **WhatsApp no portfólio empresarial do cliente**, com o desenvolvedor como administrador. Se o desenvolvedor sair do projeto, o número continua com o cliente, e um bloqueio na conta pessoal do desenvolvedor na Meta não o afeta.
- **Agenda e e-mails na conta Google do responsável:** os alarmes que o robô grava no evento só tocam para a conta que criou o evento. Numa agenda compartilhada, o celular do responsável não tocaria.

Quem faz: **D** = desenvolvedor · **R** = responsável no cliente.

## 0. O que pedir ao cliente antes de começar

| Item | Para quê |
|---|---|
| Um **chip novo**, ou um número cujo WhatsApp tenha sido apagado | Número do robô. Não pode estar em uso no app do WhatsApp |
| Razão social, CNPJ, endereço e telefone da empresa | Portfólio empresarial na Meta |
| Cartão CNPJ ou contrato social em PDF | Verificação da empresa na Meta (recomendada) |
| Os números de WhatsApp que podem mandar pedidos (o responsável e quem mais) | `whatsapp_autorizados`. Número fora da lista é ignorado |
| O e-mail do responsável | Respostas, avisos e lembretes (`email_responsavel`) |
| Os e-mails da equipe | Convite do Google e lembretes por e-mail (`emails_equipe`). Pode ficar vazio |
| 20 minutos do responsável junto com o desenvolvedor | Os logins da Meta e do Google |

## 1. WhatsApp na conta da empresa (cerca de 1 hora)

1. **R:** em https://business.facebook.com, criar o **portfólio empresarial** com os dados da empresa. Depois, **Configurações → Pessoas → Adicionar**: o e-mail do desenvolvedor, com **controle total**.
   Recomendado: **Central de segurança → Verificação da empresa**, com o cartão CNPJ. Portfólio novo, sem verificação, é o perfil que a Meta mais bloqueia (erro `131031`, conta bloqueada).
2. **D:** em https://developers.facebook.com, **Criar app** → caso de uso WhatsApp → **portfólio do cliente** → nome `Agenda <Empresa>`. Anote o **ID do app** e a **chave secreta** (Configurações do app → Básico). Detalhes no [SETUP, passo 1](SETUP.md#1-app-do-whatsapp-na-meta).
3. **D + R:** **número próprio**, como no [SETUP, passo 12](SETUP.md#12-trocar-o-número-de-teste-por-um-número-próprio): WhatsApp → Configuração da API → **Adicionar número de telefone** → nome de exibição (o nome da empresa; a Meta aprova) → código por SMS ou ligação no chip. Anote o **ID da conta do WhatsApp Business** e o **ID do número**.
   Se o envio falhar com o erro `133010` (número não registrado): no [Explorador da Graph API](https://developers.facebook.com/tools/explorer), `POST <ID do número>/register` com o corpo `{"messaging_product": "whatsapp", "pin": "<6 dígitos>"}`. Guarde o PIN: ele vira a verificação em duas etapas do número.
4. **D:** **token permanente** no portfólio do cliente, como no [SETUP, passo 2](SETUP.md#2-token-permanente-do-whatsapp): usuário do sistema → atribuir o app e a conta do WhatsApp com controle total → gerar token com validade **Nunca**.
5. **D:** no n8n, atualizar as duas credenciais (o token e a chave secreta não saem do n8n):
   - **WhatsApp OAuth account:** ID e chave secreta do app **novo**;
   - **WhatsApp account:** token **novo** e ID da conta do WhatsApp **nova**.
6. **D:** no workflow 1, **desligar e ligar** a chave de ativação. É na ativação que o n8n registra o webhook no app novo. Fechar o n8n.
7. **D:** ligar a conta do WhatsApp ao app novo: `POST <ID da conta>/subscribed_apps` no Explorador da Graph API, como no [SETUP, passo 9](SETUP.md#9-ativar). A resposta esperada é `{"success": true}`.
8. **D:** do seu WhatsApp, mandar `oi` para o número novo. Deve chegar o menu com os botões. Confira a execução no n8n.

O número novo não tem lista de destinatários: qualquer número em `whatsapp_autorizados` conversa com ele.

## 2. Google na conta do responsável (15 minutos)

Com o app OAuth publicado ([INSTALACAO, etapa 5](INSTALACAO.md#5-integrações-1-h-30)), qualquer conta Google pode autorizar.

1. **D + R:** n8n → Credentials → **Gmail account** → **Sign in with Google** → na janela, **Usar outra conta** → o responsável entra com a conta dele → **Avançado → Acessar** → marcar todas as permissões → Continuar.
2. **D + R:** o mesmo em **Google Calendar account**. Fechar o n8n.
3. **R:** no celular do responsável, o app Google Agenda logado nessa conta, com as notificações liberadas, e o número do robô salvo como "Agenda". Detalhes no [SETUP, passo 10](SETUP.md#10-celular-do-responsável).

A partir daqui, os eventos vão para a agenda principal do responsável, e os e-mails do robô saem do Gmail dele.

## 3. Configuração final (D)

Com os dados da seção 0, troque o `CONFIG` nos dois workflows (nós "Normalizar entrada" e "Configuração"):

- `email_responsavel` (workflows 1 e 2);
- `whatsapp_autorizados`: o responsável e a equipe. O número do desenvolvedor fica até o fim da semana de testes;
- `emails_equipe`.

## 4. Teste com o responsável (30 minutos)

Do celular do responsável, o roteiro do [SETUP, passo 11](SETUP.md#11-testes): encaminhar um pedido real, mandar `agenda`, mandar um áudio. Conferir o evento verde na agenda dele, o alarme no celular e, no dia seguinte, o e-mail de lembrete. Apagar os eventos de teste no final.

## 5. Entregar

- **Guia de uso:** [`docs/apresentacao/guia-do-cliente.pdf`](docs/apresentacao/guia-do-cliente.pdf). Antes de imprimir ou enviar, colocar o número do robô e o contato de suporte (`guia-do-cliente.html`, ver o [LEIA-ME](docs/apresentacao/LEIA-ME.md)).
- **Formulário de reserva:** o link está no guia. O usuário e a senha (credencial Basic Auth do n8n) vão para o responsável **à parte**, nunca no PDF.
- **Suporte:** os alertas ("NÃO agendado", "resposta não entregue", "lembretes PARADOS") chegam no e-mail do responsável. Combinar que ele encaminha para o suporte. A rotina semanal do [OPERACAO](docs/OPERACAO.md#rotina-semanal-5-minutos) fica com o desenvolvedor.
- **Custos:** zero no plano grátis. Render, Supabase e Groq estão no plano grátis, e responder pelo WhatsApp em até 24 horas é grátis. Limites em [OPERACAO](docs/OPERACAO.md#limites-do-plano-grátis).

## 6. Depois da entrega

- Tirar o número do desenvolvedor de `whatsapp_autorizados`, se não for mais testar.
- Apagar o app de teste (portfólio do desenvolvedor) e os eventos de teste da agenda do desenvolvedor.
- Fora desta entrega: lembretes pelo WhatsApp (mensagens pagas, com modelo aprovado), leitura de áudio, leitura do PDF do edital, notas fiscais.
