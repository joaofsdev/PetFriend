# 🐾 PetFriend Connect

> Plataforma para conectar donos de pets a cuidadores locais de confiança.

![Status](https://img.shields.io/badge/status-em%20desenvolvimento-yellow)
![Node](https://img.shields.io/badge/Node.js-18+-green)
![React](https://img.shields.io/badge/React-18+-blue)
![MySQL](https://img.shields.io/badge/MySQL-8.0-orange)

---

## 📋 Índice

- [Domínio do Problema](#-domínio-do-problema)
- [Requisitos Funcionais](#-requisitos-funcionais)
- [Requisitos Não Funcionais](#-requisitos-não-funcionais)
- [Tecnologias](#-tecnologias)
- [Arquitetura](#-arquitetura)
- [Modelagem de Dados](#-modelagem-de-dados)
- [Organização de Tarefas](#-organização-de-tarefas)
- [Como Executar](#-como-executar)

---

## 🎯 Domínio do Problema

### Contexto
Proprietários de animais de estimação frequentemente precisam de cuidadores temporários para seus pets, seja para passeios diários, hospedagem durante viagens ou cuidados especiais. No entanto, enfrentam os seguintes desafios:

- **Falta de confiança**: Dificuldade em encontrar cuidadores confiáveis e verificados
- **Conflitos de agenda**: Problemas ao garantir que o cuidador tenha disponibilidade real na data desejada
- **Desorganização de informações**: Dificuldade em gerenciar dados dos animais (vacinas, comportamento, necessidades especiais)
- **Reservas duplicadas**: Risco de dois donos reservarem o mesmo horário com o mesmo cuidador

### Solução Proposta
O **PetFriend Connect** resolve esses problemas oferecendo:

1. **Gestão centralizada de pets** (CRUD completo)
2. **Sistema de agendamento atômico** que garante consistência nas reservas
3. **Visualização de disponibilidade** em tempo real dos cuidadores

---

## ✅ Requisitos Funcionais

### RF01 - Gestão de Usuários
| ID | Descrição | Prioridade |
|----|-----------|------------|
| RF01.1 | O sistema deve permitir o cadastro de usuários (Donos e Cuidadores) | Alta |
| RF01.2 | O sistema deve permitir autenticação via email e senha | Alta |
| RF01.3 | O sistema deve permitir a edição do perfil do usuário | Média |

### RF02 - Gestão de Pets (CRUD)
| ID | Descrição | Prioridade |
|----|-----------|------------|
| RF02.1 | O dono deve poder cadastrar um novo pet com: nome, espécie, raça, idade e observações | Alta |
| RF02.2 | O dono deve poder visualizar a lista de todos os seus pets cadastrados | Alta |
| RF02.3 | O dono deve poder atualizar as informações de um pet | Alta |
| RF02.4 | O dono deve poder remover o cadastro de um pet | Alta |
| RF02.5 | O sistema deve validar campos obrigatórios no cadastro do pet | Alta |

### RF03 - Gestão de Cuidadores
| ID | Descrição | Prioridade |
|----|-----------|------------|
| RF03.1 | O cuidador deve poder definir sua disponibilidade de horários | Alta |
| RF03.2 | O sistema deve listar cuidadores disponíveis para o dono | Alta |
| RF03.3 | O cuidador deve poder definir os tipos de serviço que oferece | Média |
| RF03.4 | O cuidador deve poder definir seu preço por serviço | Média |

### RF04 - Agendamento de Serviços (Transação Crítica)
| ID | Descrição | Prioridade |
|----|-----------|------------|
| RF04.1 | O sistema deve verificar a disponibilidade do cuidador antes de confirmar | Alta |
| RF04.2 | O sistema deve criar o registro da reserva de forma atômica | Alta |
| RF04.3 | O sistema deve bloquear o horário na agenda do cuidador após confirmação | Alta |
| RF04.4 | O sistema deve impedir reservas duplicadas no mesmo horário | Alta |
| RF04.5 | O sistema deve registrar log de confirmação da reserva | Alta |
| RF04.6 | O dono deve poder cancelar uma reserva | Média |
| RF04.7 | O cuidador deve poder visualizar suas reservas agendadas | Alta |

---

## 🔒 Requisitos Não Funcionais

### RNF01 - Desempenho
| ID | Descrição |
|----|-----------|
| RNF01.1 | O sistema deve responder a requisições em no máximo 2 segundos |
| RNF01.2 | O sistema deve suportar pelo menos 100 usuários simultâneos |

### RNF02 - Segurança
| ID | Descrição |
|----|-----------|
| RNF02.1 | Senhas devem ser armazenadas com hash bcrypt |
| RNF02.2 | Autenticação deve usar tokens JWT |
| RNF02.3 | APIs devem validar permissões de acesso |

### RNF03 - Confiabilidade
| ID | Descrição |
|----|-----------|
| RNF03.1 | Transações de agendamento devem ser ACID |
| RNF03.2 | O sistema deve fazer rollback em caso de falha na transação |

### RNF04 - Usabilidade
| ID | Descrição |
|----|-----------|
| RNF04.1 | Interface deve ser responsiva (mobile e desktop) |
| RNF04.2 | Mensagens de erro devem ser claras e em português |

### RNF05 - Manutenibilidade
| ID | Descrição |
|----|-----------|
| RNF05.1 | Código deve seguir padrões ESLint |
| RNF05.2 | Projeto deve ter estrutura de pastas organizada |

---

## 🛠 Tecnologias

| Tecnologia | Papel | Justificativa |
|------------|-------|---------------|
| **React.js** | Frontend | Permite criar uma interface componentizada e reativa, facilitando a filtragem de cuidadores e o gerenciamento dos pets em uma única página (SPA). Possui grande comunidade e documentação. |
| **Node.js** | Backend | Alta escalabilidade e permite usar a mesma linguagem (JS/TS) em todo o projeto, facilitando a curva de aprendizado e compartilhamento de código. |
| **MySQL** | Banco de Dados | Banco relacional amplamente utilizado, confiável e com suporte total a **transações ACID**, garantindo que os agendamentos sejam consistentes e evitando reservas duplicadas. |
| **Prisma** | ORM | Facilita a modelagem do banco de dados e as consultas, gerando tipos TypeScript automaticamente. Possui migrations e excelente integração com Node.js. |
| **Express.js** | API REST | Framework minimalista e flexível para criação de APIs, com grande ecossistema de middlewares. |
| **JWT** | Autenticação | Padrão seguro e stateless para autenticação, ideal para SPAs. |
| **Axios** | HTTP Client | Biblioteca robusta para requisições HTTP no frontend, com interceptors e tratamento de erros. |

---

### Estrutura de Pastas

```
PetFriend/
├── backend/
│   ├── prisma/
│   │   └── schema.prisma
│   ├── src/
│   │   ├── controllers/
│   │   ├── middlewares/
│   │   ├── routes/
│   │   ├── services/
│   │   ├── utils/
│   │   └── server.js
│   ├── package.json
│   └── .env
│
├── frontend/
│   ├── public/
│   ├── src/
│   │   ├── components/
│   │   ├── pages/
│   │   ├── services/
│   │   ├── contexts/
│   │   ├── hooks/
│   │   └── App.jsx
│   ├── package.json
│   └── .env
│
└── README.md
```

---
### Principais Entidades

| Entidade | Descrição |
|----------|-----------|
| **Usuario** | Representa donos e cuidadores (diferenciados pelo campo `tipo`) |
| **Pet** | Animal de estimação cadastrado por um dono |
| **Agenda** | Slots de disponibilidade dos cuidadores |
| **Reserva** | Agendamento de serviço entre dono e cuidador |
| **Servico** | Tipos de serviço oferecidos (passeio, hospedagem, etc.) |

---

## 🚀 Como Executar

### Pré-requisitos
- Node.js 18+
- MySQL 8.0+
- npm ou yarn

### Backend

```bash
# Entrar na pasta do backend
cd backend

# Instalar dependências
npm install

# Configurar variáveis de ambiente
cp .env.example .env
# Editar .env com suas configurações

# Executar migrations
npx prisma migrate dev

# Iniciar servidor
npm run dev
```

### Frontend

```bash
# Entrar na pasta do frontend
cd frontend

# Instalar dependências
npm install

# Iniciar aplicação
npm start
```

---

## 🔄 Fluxo da Transação de Agendamento

```
┌─────────────────────────────────────────────────────────────────┐
│                    TRANSAÇÃO ATÔMICA                            │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  1. BEGIN TRANSACTION                                           │
│     │                                                           │
│     ▼                                                           │
│  2. Verificar disponibilidade do cuidador (SELECT FOR UPDATE)   │
│     │                                                           │
│     ├── ❌ Indisponível ──▶ ROLLBACK ──▶ Retorna erro          │
│     │                                                           │
│     ▼ ✅ Disponível                                             │
│  3. Criar registro da Reserva (INSERT)                          │
│     │                                                           │
│     ▼                                                           │
│  4. Atualizar Agenda - marcar como indisponível (UPDATE)        │
│     │                                                           │
│     ▼                                                           │
│  5. Registrar log de confirmação (INSERT)                       │
│     │                                                           │
│     ▼                                                           │
│  6. COMMIT                                                      │
│     │                                                           │
│     ▼                                                           │
│  7. Retornar sucesso ao cliente                                 │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## 👥 Equipe

| Nome | Responsabilidade |
|------|------------------|
| João Francisco da Silva | Backend (Node.js, Prisma, MySQL) |
| Iago Koch | Frontend (React.js) |

---

## 📄 Licença

Este projeto foi desenvolvido para fins acadêmicos.

---
