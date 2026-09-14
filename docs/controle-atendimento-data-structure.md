# Controle de Atendimento - Estrutura de Dados

Este documento descreve as entidades do domínio, seus atributos, tipagem forte e os relacionamentos para orientar o desenvolvimento assertivo do sistema de controle de atendimento.

## Entidades

### 1. Fluxo de Atendimento
Um fluxo de atendimento agrupa diversos pedidos e representa o ciclo de trabalho de um período (por exemplo, um dia de trabalho).

**Relacionamentos:**
- Um Fluxo de Atendimento possui muitos Pedidos (1 : N).

**Atributos:**
- `identificador`: String (max 15 caracteres). Valor default é a data corrente. Funciona como chave/nome do fluxo.
- `status`: Enum (`"aberto"`, `"fechado"`).

### 2. Pedido
Representa a solicitação de compra de um cliente, que sempre pertence a um Fluxo de Atendimento.

**Atributos Base:**
- `identificador`: String (max 20 caracteres).
- `status`: Enum (`"aguardando atendimento"`, `"em atendimento"`, `"em execucao"`, `"retirado no balcao"`, `"enviado"`, `"entregue"`, `"devolvido"`).
- `cancelamento`: Boolean (indicador de se o pedido foi cancelado).

**Dados do Cliente e Entrega:**
- `nomeCliente`: String (max 50 caracteres).
- `tipoEntrega`: Enum (`"Delivery"`, `"Retirada no Balcao"`).
- `endereco`: String (max 500 caracteres).

**Dados de Pagamento e Execução:**
- `tipoPagamento`: Enum (`"Pix"`, `"Cartao"`, `"Dinheiro"`).
- `valorPix`: Number / Monetário (Valor decimal da transação).
- `mesa`: String (max 20 caracteres).

**Detalhes Adicionais:**
- `observacao`: String (max 100 caracteres).
- `restricoes`: String (max 500 caracteres).
- `imagemPedido`: String / URL (Referência para a imagem cadastrada do pedido).

**Motivos de Exceção:**
- `motivoCancelamento`: String (max 100 caracteres).
- `motivoDevolucao`: String (max 100 caracteres). O sistema deverá sugerir opções pré-definidas ("Nao encontrado", "Devolvido pelo cliente") mas permitir a entrada livre de texto por parte do usuário.

## Regras de Integridade de Dados
- **Imutabilidade Condicional:** Um fluxo de atendimento com `status` "fechado" torna os dados dos pedidos associados estritamente leitura (read-only).
- **Validação de Transição:** As propriedades de execução (como `mesa`, `imagemPedido`, `valorPix` e `restricoes`) são preenchidas para efetivar a transição para `"em execucao"`.
- O motivo de devolução é requerido quando o `status` transita para `"devolvido"`.
