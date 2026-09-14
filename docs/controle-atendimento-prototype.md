# Controle de Atendimento - Protótipo e Interface de Usuário (UI/UX)

Este documento especifica a interface da aplicação mobile (Flutter, offline, SQLite, sem autenticação) para o sistema de controle de atendimento. O objetivo é fornecer todas as definições visuais, fluxos de navegação e componentes para a geração assertiva do código de UI.

## Fluxo de Telas (Navegação)

### Tela 1: Splash Screen
- **Objetivo:** Tela inicial de carregamento ao abrir o app.
- **Comportamento:** Informa ao usuário que o sistema está sendo inicializado. Em background, o sistema acessa o SQLite para verificar se existe um *Fluxo de Atendimento* com status `aberto`.
- **Navegação Automática:**
  - Se existir fluxo aberto: Redireciona para a **Tela 4 (Execução do Fluxo de Atendimento)**.
  - Se não existir fluxo aberto: Redireciona para a **Tela 2 (Home)**.

### Tela 2: Home
- **Objetivo:** Listar o histórico de fluxos de atendimento e permitir criar um novo.
- **Componentes:**
  - Lista de fluxos ordenados por data decrescente (mais recentes primeiro).
  - Botão de ação (ex: FAB) "Novo Fluxo".
- **Operações:**
  - `Clique em "Novo Fluxo"`: Abre a **Tela 3 (Cadastro do Fluxo de Atendimento)**.
  - `Clique em um fluxo listado`: Abre a **Tela 4 (Execução do Fluxo de Atendimento)** para visualização.

### Tela 3: Cadastro de Fluxo de Atendimento
- **Objetivo:** Criar um novo fluxo de atendimento diário.
- **Componentes:**
  - Campo de entrada: `Identificador` (Pré-preenchido com a data atual).
  - Footer com botões: `Confirmar` e `Cancelar`.
- **Operações:**
  - `Clique em Confirmar`: Salva o fluxo no banco e abre a **Tela 4 (Execução do Fluxo de Atendimento)**.
  - `Clique em Cancelar`: Retorna para a **Tela 2 (Home)** sem salvar.

### Tela 4: Execução do Fluxo de Atendimento
- **Objetivo:** Quadro Kanban-style (Visualizador com rolagem horizontal) para gerenciar os pedidos de um fluxo específico.
- **Componentes:**
  - **Header:** Exibe o `Identificador` do fluxo. Botões: `"Novo Pedido"` e `"Busca de Pedidos"`.
  - **Quadro Kanban (Rolagem Horizontal):** Organizado em colunas, cada uma contendo os Cards de Pedidos para os respectivos status:
    1. Aguardando Atendimento
    2. Em Atendimento
    3. Em Execução
    4. Retirado no Balcão
    5. Enviado
    6. Entregue
    7. Devolvido
  - *Nota:* Não há coluna para "Cancelado". Pedidos cancelados são mantidos em sua coluna de origem, com o Card alterado visualmente.
- **Operações do Header:**
  - `Clique em "Novo Pedido"`: Abre a **Tela 5 (Cadastro de Pedido)**.
  - `Clique em "Busca de Pedidos"`: (Não implementar por enquanto).

#### Especificação dos Cards de Pedidos (Por Status)
1. **Aguardando Atendimento**
   - **Elementos:** `Identificador do pedido`.
   - **Footer:** Botões `Atendimento` e `Exclusão`.
   - **Operações:**
     - `Atendimento`: Abre a Tela 5.
     - `Exclusão`: Exibe um *Dialog* de confirmação e, se confirmado, deleta o pedido do banco e atualiza a tela.
2. **Em Atendimento**
   - **Elementos:** `Identificador do pedido`, `Nome do Cliente`, `Tipo de Entrega`, `Tipo de Pagamento`, `Endereço`, `Observação`.
   - **Estado Cancelado:** Se a flag de cancelamento for `true`, o Footer não é exibido e a palavra **"Cancelado"** aparece em destaque (em vermelho) no card.
   - **Footer (se não cancelado):** Botões `Executar` e `Cancelar`.
   - **Operações:**
     - `Executar`: Abre a Tela 6.
     - `Editar (no card)`: Abre a Tela 5 para edição.
     - `Cancelar`: Abre a Tela 7.
3. **Em Execução**
   - **Elementos:** `Identificador do pedido`, `Nome do Cliente`, `Tipo de Entrega`, `Tipo de Pagamento`, `Endereço` (só se Tipo de Entrega for Delivery), `Observação`, `Mesa`, `Restrições`, `Imagem do pedido`.
   - **Estado Cancelado:** Similar à coluna anterior, se cancelado, exibe "Cancelado" em vermelho e oculta o footer.
   - **Footer (se não cancelado):** Botão de acordo com o tipo de entrega (`Retirado` ou `Enviado`) e botão `Cancelar`.
   - **Operações:**
     - `Retirado` (Aparece se "Retirada no Balcão"): Altera status para "Retirado no Balcão" e atualiza a tela.
     - `Enviado` (Aparece se "Delivery"): Altera status para "Enviado" e atualiza a tela.
     - `Cancelar`: Abre a Tela 7.
4. **Retirado no Balcão**
   - **Elementos (Somente Leitura):** `Identificador`, `Nome`, `Tipo de Entrega`, `Tipo de Pagamento`, `Observação`, `Mesa`, `Restrições`, `Imagem do pedido`.
5. **Enviado**
   - **Elementos:** `Identificador`, `Nome`, `Tipo de Entrega`, `Tipo de Pagamento`, `Endereço` (se Delivery), `Observação`, `Mesa`, `Restrições`, `Imagem`.
   - **Footer:** Botões `Entregue` e `Devolvido`.
   - **Operações:**
     - `Entregue`: Altera status para "Entregue" e atualiza a tela.
     - `Devolvido`: Abre a Tela 8.

### Tela 5: Cadastro / Edição de Pedido
- **Objetivo:** Formulário para inclusão e edição de pedidos.
- **Componentes:** Campos para `Identificador do Pedido`, `Nome do Cliente`, `Tipo de Entrega`, `Tipo de Pagamento`, `Endereço`, `Observação`.
- **Regra de UI:** Todos os campos devem ficar desabilitados até que o `Identificador` seja preenchido e confirmado.
- **Operações:**
  - Após confirmação do `Identificador`:
    - Se já existir: O sistema recupera e auto-preenche endereço, tipo de pagamento e tipo de entrega. Se gravado, o pedido vai para "em atendimento".
    - Se não existir: Se o usuário deixar informações incompletas, será salvo como "aguardando atendimento". Se preencher o necessário (incluindo o Endereço para casos de Delivery), avança de status.
  - `Clique em Gravar`: Aplica a lógica acima e persiste no banco (SQLite).
  - `Clique em Cancelar`: Retorna sem salvar nada (ou abre a Tela 7 se já estivesse em andamento).

### Tela 6: Execução de Pedido
- **Objetivo:** Adicionar informações necessárias para a execução.
- **Componentes:**
  - Botão/Visualizador para `Captura de Imagem` usando a câmera do dispositivo.
  - Campos: `Restrições` (Opcional), `Valor Pix` (Opcional), `Mesa` (Obrigatório dependendo do fluxo/cenário).
  - Footer com botões `Gravar` e `Cancelar`.
- **Operações:**
  - `Gravar`: Salva os dados no pedido e altera o status para "Em Execução".
  - `Cancelar`: Fecha a tela modal.

### Tela 7: Dialog/Tela de Cancelamento de Pedido
- **Objetivo:** Modal de confirmação para cancelar o pedido.
- **Componentes:** Campo para `Motivo` (Opcional). Botoes `Confirmar` e `Cancelar`.
- **Operações:**
  - `Confirmar`: Altera a flag `cancelamento` do pedido para `true`. Atualiza os cards visuais.
  - `Cancelar`: Fecha o modal sem realizar a ação.

### Tela 8: Dialog/Tela de Devolução de Entrega
- **Objetivo:** Informar o motivo pelo qual a entrega não pôde ser efetuada.
- **Componentes:** Campo de seleção/texto livre para `Motivo da devolução` (Valor *default* recomendado na UI: "Não encontrado"). Botoes `Confirmar` e `Cancelar`.
- **Operações:**
  - `Confirmar`: Salva o motivo, altera o status do pedido para "Devolvido" e atualiza a tela.
  - `Cancelar`: Fecha o modal sem realizar a ação.
