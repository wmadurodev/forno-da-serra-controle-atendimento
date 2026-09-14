# Controle de Atendimento - Requisitos Não Funcionais

Este documento especifica as diretrizes técnicas e os requisitos não funcionais que deverão guiar a arquitetura e a implementação do sistema de controle de atendimento. O objetivo é fornecer contexto técnico claro para a geração e estruturação do código.

## 1. Stack Tecnológica e Plataforma
- **Plataforma Alvo:** Dispositivos móveis (Mobile).
- **Framework Principal:** A aplicação deverá ser construída utilizando **Flutter**.

## 2. Arquitetura de Dados e Conectividade
- **Operação Offline:** A aplicação deve ser capaz de funcionar **100% offline**. Não haverá dependência primária de conectividade com a internet ou APIs externas para o fluxo principal de atendimento.
- **Banco de Dados Local:** A persistência dos dados (Fluxos de Atendimento e Pedidos) deverá ser feita localmente no dispositivo utilizando o banco de dados embutido **SQLite**.

## 3. Segurança e Controle de Acesso
- **Sem Autenticação/Autorização:** A aplicação não possuirá nenhum mecanismo de login, autenticação ou controle de permissões de usuário (autorização). O acesso às telas e às funcionalidades do sistema será livre e direto logo após a abertura do aplicativo.
