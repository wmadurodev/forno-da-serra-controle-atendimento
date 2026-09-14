# Controle de Atendimento - Requisitos Funcionais

## Visão Geral
Este projeto tem como objetivo criar uma aplicação para controlar o fluxo de atendimento de pedidos. O processo se inicia sempre que um cliente efetua uma ligação ou chamada de WhatsApp para fazer sua solicitação de compra.

## Casos de Uso / Funcionalidades

### 1. Início do Sistema
- Ao iniciar, o sistema deve verificar se existe um "Fluxo de Atendimento" com status "aberto". Se houver, ele deve ser apresentado ao usuário.
- Caso não exista nenhum fluxo aberto, o sistema deve exibir o histórico de fluxos de atendimento e permitir a criação de um novo.
- **Regra de Negócio:** O sistema deve permitir apenas um (1) fluxo de atendimento com o status "aberto" de cada vez.

### 2. Gestão de Fluxo de Atendimento
- **Novo Fluxo de Atendimento:** Permite iniciar um novo fluxo. O sistema recomendará a data corrente como identificador, podendo ser alterado pelo usuário.
- **Finalizar Fluxo de Atendimento:** Um fluxo aberto pode ser fechado a qualquer momento. Ao ser fechado, seus pedidos não poderão mais ser alterados, apenas visualizados.
- **Visualização de Histórico:** O sistema deve permitir que o usuário saia do fluxo corrente para consultar/abrir um fluxo de atendimento anterior.
- **Busca de Pedidos:** Dentro de um fluxo selecionado, o usuário deve poder buscar pedidos através do nome do cliente, identificador do cliente (telefone/documento), número da mesa ou endereço de entrega.

### 3. Gestão de Pedidos
- **Novo Pedido:** A partir de um fluxo aberto, o usuário cria um novo pedido ao receber contato do cliente.
  - Se for preenchido apenas o identificador e salvo, o pedido fica no estágio `"aguardando atendimento"`.
  - Se todos os dados necessários forem preenchidos, o pedido passa direto para o estágio `"em atendimento"`.
- **Iniciar Pedido:** Caso o pedido esteja `"aguardando atendimento"`, o usuário pode iniciar o atendimento. O sistema abre a tela para completar os dados. Ao salvar com tudo preenchido, o status muda para `"em atendimento"`.
- **Excluir Pedido:** Um pedido só pode ser excluído do sistema se estiver no estágio `"aguardando atendimento"`.
- **Executar Pedido:** Um pedido `"em atendimento"` avança para `"em execução"` quando o usuário informa os dados de execução: Mesa, Valor Pix (opcional), Restrições (opcional) e anexa uma imagem do pedido.
- **Cancelar Pedido:** Pedidos nos estágios `"em atendimento"` ou `"em execução"` podem ser cancelados. O usuário tem a opção de informar o motivo do cancelamento.
- **Pedido Retirado no Balcão:** Um pedido `"em execução"` com tipo de entrega `"Retirada no Balcao"` passa para o estágio `"retirado no balcao"`.
- **Pedido Enviado:** Um pedido `"em execução"` com tipo de entrega `"Delivery"` passa para o estágio `"enviado"`.
- **Pedido Entregue:** Um pedido `"enviado"` com tipo de entrega `"Delivery"` avança para o estágio `"entregue"`.
- **Pedido Devolvido:** Um pedido `"enviado"` com tipo de entrega `"Delivery"` pode passar para o estágio `"devolvido"` (ou "não entregue"). Neste caso, é obrigatório informar o motivo da devolução.

## Ciclo de Vida do Pedido (Transição de Status)
- **Aguardando Atendimento** -> **Em Atendimento** (ao preencher dados) -> **Em Execução** (ao informar mesa, imagem, etc)
- **Aguardando Atendimento** -> **Excluído** (removido do sistema)
- **Em Atendimento** / **Em Execução** -> **Cancelado** (com motivo opcional)
- **Em Execução** -> **Retirado no Balcão** (se Tipo de Entrega = Retirada no Balcão)
- **Em Execução** -> **Enviado** (se Tipo de Entrega = Delivery) -> **Entregue** ou **Devolvido** (com motivo obrigatório)
