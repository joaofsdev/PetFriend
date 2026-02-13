# 📋 Organização de Tarefas - PetFriend Connect

## 👥 Divisão da Equipe

| Membro | Foco Principal | Tecnologias |
|--------|----------------|-------------|
| **João Francisco** | Backend | Node.js, Express, Prisma, MySQL |
| **Iago Koch** | Frontend | React.js, Axios, CSS |

---

## 🗓️ Sprint 1 - Setup e Autenticação (Semana 1)

### João - Backend
- [ ] Inicializar projeto Node.js com Express
- [ ] Configurar Prisma e conexão MySQL
- [ ] Criar schema do banco (todas as tabelas)
- [ ] Executar migrations
- [ ] Implementar CRUD de Usuários
- [ ] Implementar autenticação (JWT)
- [ ] Criar middleware de autenticação

### Iago - Frontend
- [ ] Inicializar projeto React (Create React App ou Vite)
- [ ] Configurar estrutura de pastas
- [ ] Criar componentes base (Header, Footer, Layout)
- [ ] Configurar React Router
- [ ] Criar tela de Login
- [ ] Criar tela de Cadastro
- [ ] Implementar contexto de autenticação

### Entregáveis Sprint 1
- [ ] Ambiente configurado
- [ ] Login funcionando end-to-end
- [ ] Cadastro funcionando end-to-end

---

## 🗓️ Sprint 2 - CRUD de Pets (Semana 2)

### João - Backend
- [ ] Implementar POST /api/pets (criar)
- [ ] Implementar GET /api/pets (listar)
- [ ] Implementar GET /api/pets/:id (detalhe)
- [ ] Implementar PUT /api/pets/:id (atualizar)
- [ ] Implementar DELETE /api/pets/:id (remover)
- [ ] Adicionar validações com express-validator

### Iago - Frontend
- [ ] Criar página "Meus Pets"
- [ ] Criar componente de listagem de pets
- [ ] Criar formulário de cadastro de pet
- [ ] Criar modal de edição de pet
- [ ] Implementar confirmação de exclusão
- [ ] Conectar com API (Axios)

### Entregáveis Sprint 2
- [ ] CRUD completo de Pets funcionando
- [ ] Interface amigável para gestão de pets

---

## 🗓️ Sprint 3 - Agendamento (Semana 3)

### João - Backend
- [ ] Implementar CRUD de Serviços
- [ ] Implementar gestão de Agenda (disponibilidade)
- [ ] **Implementar transação de reserva (CRÍTICO)**
  - [ ] Verificar disponibilidade com lock
  - [ ] Criar reserva
  - [ ] Bloquear agenda
  - [ ] Registrar log
  - [ ] Rollback em caso de erro
- [ ] Implementar listagem de reservas
- [ ] Implementar cancelamento de reserva

### Iago - Frontend
- [ ] Criar página de listagem de Cuidadores
- [ ] Criar filtros de busca
- [ ] Criar página de perfil do Cuidador
- [ ] Criar componente de calendário/agenda
- [ ] **Criar fluxo de agendamento**
  - [ ] Seleção de data/horário
  - [ ] Seleção de pet e serviço
  - [ ] Confirmação
  - [ ] Feedback de sucesso/erro
- [ ] Criar página "Minhas Reservas"

### Entregáveis Sprint 3
- [ ] Sistema de agendamento funcionando
- [ ] Transação atômica garantindo consistência

---

## 🗓️ Sprint 4 - Polimento (Semana 4)

### João - Backend
- [ ] Revisar e otimizar queries
- [ ] Adicionar tratamento de erros global
- [ ] Documentar API (Swagger/Postman)
- [ ] Testar cenários de concorrência
- [ ] Criar seeds para dados de teste

### Iago - Frontend
- [ ] Melhorar responsividade
- [ ] Adicionar loading states
- [ ] Melhorar mensagens de erro
- [ ] Revisar UX geral
- [ ] Testar fluxos completos

### Ambos
- [ ] Testar integração completa
- [ ] Corrigir bugs encontrados
- [ ] Preparar apresentação
- [ ] Finalizar documentação

### Entregáveis Sprint 4
- [ ] Sistema completo e testado
- [ ] Documentação finalizada
- [ ] Apresentação pronta

---

## 🗓️ Sprint 5 - Painel Administrativo (Semana 5)

### João - Backend
- [ ] Implementar middleware de autorização admin
- [ ] Criar CRUD de usuários (admin)
  - [ ] GET /api/admin/usuarios (listar todos)
  - [ ] GET /api/admin/usuarios/:id (detalhes)
  - [ ] PUT /api/admin/usuarios/:id (editar)
  - [ ] DELETE /api/admin/usuarios/:id (desativar)
  - [ ] PATCH /api/admin/usuarios/:id/ativar (reativar)
- [ ] Implementar gestão de denúncias
  - [ ] GET /api/admin/denuncias (listar)
  - [ ] GET /api/admin/denuncias/:id (detalhes)
  - [ ] PATCH /api/admin/denuncias/:id (atualizar status)
- [ ] Implementar dashboard de estatísticas
  - [ ] GET /api/admin/dashboard (view vw_dashboard_admin)
- [ ] Implementar configurações do sistema
  - [ ] GET /api/admin/configuracoes
  - [ ] PUT /api/admin/configuracoes/:chave
- [ ] Implementar logs de auditoria
  - [ ] Registrar todas as ações de admin
  - [ ] GET /api/admin/logs

### Iago - Frontend
- [ ] Criar página Dashboard Admin
  - [ ] Cards de estatísticas principais
  - [ ] Gráficos de usuários e reservas
  - [ ] Lista de atividades recentes
- [ ] Criar página de Gestão de Usuários
  - [ ] Tabela com filtros e busca
  - [ ] Modal de detalhes do usuário
  - [ ] Ações: editar, desativar, reativar
- [ ] Criar página de Denúncias
  - [ ] Lista de denúncias com status
  - [ ] Modal de análise de denúncia
  - [ ] Workflow de resolução
- [ ] Criar página de Configurações do Sistema
  - [ ] Formulário de configurações
  - [ ] Toggle para manutenção
- [ ] Implementar sidebar de admin
- [ ] Implementar proteção de rotas admin

### Entregáveis Sprint 5
- [ ] Painel administrativo completo
- [ ] Gestão de usuários funcionando
- [ ] Sistema de denúncias funcionando

---

## 📊 Quadro Kanban

### 📥 Backlog
- Notificações por email
- Sistema de avaliações
- Chat entre dono e cuidador
- Upload de fotos do pet
- Geolocalização de cuidadores

### 🚧 Em Progresso


### 👀 Em Revisão


### ✅ Concluído


---

## 🎯 Critérios de Aceite por Feature

### CRUD de Pets
- [ ] Usuário consegue cadastrar pet com todos os campos
- [ ] Campos obrigatórios são validados
- [ ] Usuário consegue ver lista de seus pets
- [ ] Usuário consegue editar informações do pet
- [ ] Usuário consegue excluir pet (com confirmação)
- [ ] Apenas o dono pode ver/editar seus pets

### Transação de Agendamento
- [ ] Sistema verifica disponibilidade antes de confirmar
- [ ] Reserva é criada apenas se horário disponível
- [ ] Horário é bloqueado após confirmação
- [ ] Dois usuários não conseguem reservar mesmo horário
- [ ] Em caso de erro, nenhuma alteração é persistida
- [ ] Log de confirmação é registrado

---

## 🔗 Links Úteis

- [Documentação Prisma](https://www.prisma.io/docs)
- [Documentação React](https://react.dev)
- [Documentação Express](https://expressjs.com)
- [MySQL Transactions](https://dev.mysql.com/doc/refman/8.0/en/commit.html)

---

## ⚠️ Riscos Identificados

| Risco | Impacto | Mitigação |
|-------|---------|-----------|
| Configuração MySQL | Alto | Usar Docker ou serviço cloud |
| Conflito de merge | Médio | Commits frequentes, PRs pequenos |
| Atraso no backend | Alto | Dev 2 pode usar mock data |
| Complexidade da transação | Alto | Pesquisar e testar cedo |
