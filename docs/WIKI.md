# 📚 Wiki - PetFriend Connect

## Sumário

1. [Visão Geral](#visão-geral)
2. [Casos de Uso](#casos-de-uso)
3. [Detalhamento Técnico](#detalhamento-técnico)
4. [Guia de Contribuição](#guia-de-contribuição)

---

## Visão Geral

### O Problema
Donos de pets enfrentam dificuldades para:
- Encontrar cuidadores confiáveis em sua região
- Garantir que não haja conflito de horários
- Manter informações atualizadas sobre seus animais

### A Solução
O PetFriend Connect oferece uma plataforma centralizada que:
- Conecta donos a cuidadores verificados
- Garante agendamentos sem conflitos através de transações ACID
- Permite gestão completa das informações dos pets

---

## Casos de Uso

### UC01 - Cadastrar Pet

**Ator:** Dono

**Pré-condições:** Usuário autenticado como Dono

**Fluxo Principal:**
1. Dono acessa a tela "Meus Pets"
2. Dono clica em "Adicionar Pet"
3. Sistema exibe formulário de cadastro
4. Dono preenche: nome, espécie, raça, idade, observações
5. Dono clica em "Salvar"
6. Sistema valida os dados
7. Sistema salva o pet no banco
8. Sistema exibe mensagem de sucesso

**Fluxo Alternativo - Dados Inválidos:**
- 6a. Sistema identifica campos inválidos
- 6b. Sistema exibe mensagens de erro específicas
- 6c. Retorna ao passo 4

---

### UC02 - Agendar Serviço

**Ator:** Dono

**Pré-condições:** 
- Usuário autenticado como Dono
- Pelo menos um pet cadastrado

**Fluxo Principal:**
1. Dono acessa a tela "Cuidadores"
2. Sistema lista cuidadores disponíveis
3. Dono seleciona um cuidador
4. Sistema exibe perfil e agenda do cuidador
5. Dono seleciona data/horário desejado
6. Dono seleciona o pet e tipo de serviço
7. Dono clica em "Confirmar Agendamento"
8. **Sistema inicia transação**
9. Sistema verifica disponibilidade (com lock)
10. Sistema cria a reserva
11. Sistema bloqueia o horário na agenda
12. Sistema registra log de confirmação
13. **Sistema confirma transação (COMMIT)**
14. Sistema exibe confirmação ao usuário

**Fluxo Alternativo - Horário Indisponível:**
- 9a. Sistema identifica que horário já foi reservado
- 9b. **Sistema cancela transação (ROLLBACK)**
- 9c. Sistema exibe mensagem: "Horário não disponível"
- 9d. Retorna ao passo 5

---

### UC03 - Gerenciar Usuários (Admin)

**Ator:** Administrador

**Pré-condições:** Usuário autenticado como Admin

**Fluxo Principal:**
1. Admin acessa o painel administrativo
2. Admin clica em "Gestão de Usuários"
3. Sistema exibe lista de usuários com filtros
4. Admin pode buscar por nome, email ou tipo
5. Admin seleciona um usuário
6. Sistema exibe detalhes completos do usuário
7. Admin pode editar dados ou desativar conta
8. Sistema registra ação no log de auditoria
9. Sistema exibe confirmação da ação

**Fluxo Alternativo - Reativar Usuário:**
- 7a. Admin clica em "Reativar" em usuário inativo
- 7b. Sistema solicita confirmação
- 7c. Sistema reativa o usuário
- 7d. Sistema registra no log de auditoria

---

### UC04 - Analisar Denúncia (Admin)

**Ator:** Administrador

**Pré-condições:** 
- Usuário autenticado como Admin
- Existe denúncia pendente no sistema

**Fluxo Principal:**
1. Admin acessa "Denúncias" no painel
2. Sistema lista denúncias ordenadas por data
3. Admin seleciona uma denúncia pendente
4. Sistema exibe detalhes: denunciante, denunciado, motivo
5. Admin analisa as informações
6. Admin muda status para "Em Análise"
7. Admin decide a ação: resolver ou aplicar penalidade
8. Admin adiciona parecer/observação
9. Admin marca como "Resolvida" ou "Arquivada"
10. Sistema notifica as partes envolvidas
11. Sistema registra no log de auditoria

**Ações Possíveis:**
- Arquivar (denúncia improcedente)
- Advertir usuário denunciado
- Suspender temporariamente o denunciado
- Banir permanentemente o denunciado

---

### UC05 - Configurar Sistema (Admin)

**Ator:** Administrador

**Pré-condições:** Usuário autenticado como Admin

**Fluxo Principal:**
1. Admin acessa "Configurações" no painel
2. Sistema exibe configurações atuais
3. Admin pode alterar:
   - Taxa da plataforma (%)
   - Tempo mínimo para cancelamento
   - Limites de cadastro (pets, serviços)
   - Ativar/desativar verificação obrigatória
   - Ativar modo de manutenção
4. Admin salva alterações
5. Sistema valida os valores
6. Sistema atualiza configurações
7. Sistema registra alteração no log

**Fluxo Alternativo - Modo Manutenção:**
- 3a. Admin ativa "Modo Manutenção"
- 3b. Sistema solicita mensagem personalizada
- 3c. Sistema bloqueia acesso de usuários comuns
- 3d. Apenas admins podem acessar

---

## Detalhamento Técnico

### Transação de Agendamento (Código Conceitual)

```javascript
// services/reservaService.js

async function criarReserva(dados) {
  const { donoId, cuidadorId, petId, servicoId, dataInicio, dataFim } = dados;

  // Usa transação do Prisma para garantir atomicidade
  return await prisma.$transaction(async (tx) => {
    
    // 1. Verifica disponibilidade (com lock para evitar race condition)
    const agenda = await tx.agenda.findFirst({
      where: {
        cuidadorId,
        data: dataInicio,
        disponivel: true,
      },
    });

    if (!agenda) {
      throw new Error('Horário não disponível');
    }

    // 2. Cria a reserva
    const reserva = await tx.reserva.create({
      data: {
        donoId,
        cuidadorId,
        petId,
        servicoId,
        dataInicio,
        dataFim,
        status: 'CONFIRMADA',
      },
    });

    // 3. Bloqueia o horário na agenda
    await tx.agenda.update({
      where: { id: agenda.id },
      data: { disponivel: false, reservaId: reserva.id },
    });

    // 4. Registra log
    await tx.log.create({
      data: {
        tipo: 'RESERVA_CONFIRMADA',
        reservaId: reserva.id,
        mensagem: `Reserva #${reserva.id} confirmada`,
      },
    });

    return reserva;
  });
}
```

### Schema Prisma

```prisma
// prisma/schema.prisma

generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "mysql"
  url      = env("DATABASE_URL")
}

enum TipoUsuario {
  DONO
  CUIDADOR
  ADMIN
}

enum StatusReserva {
  PENDENTE
  CONFIRMADA
  CANCELADA
  CONCLUIDA
}

model Usuario {
  id        Int         @id @default(autoincrement())
  nome      String
  email     String      @unique
  senha     String
  tipo      TipoUsuario
  telefone  String?
  endereco  String?
  createdAt DateTime    @default(now())
  updatedAt DateTime    @updatedAt

  // Relações
  pets           Pet[]
  reservasDono   Reserva[]  @relation("DonoReservas")
  reservasCuidador Reserva[] @relation("CuidadorReservas")
  agendas        Agenda[]
  servicos       Servico[]
}

model Pet {
  id          Int      @id @default(autoincrement())
  nome        String
  especie     String
  raca        String?
  idade       Int?
  observacoes String?  @db.Text
  donoId      Int
  createdAt   DateTime @default(now())
  updatedAt   DateTime @updatedAt

  // Relações
  dono     Usuario   @relation(fields: [donoId], references: [id])
  reservas Reserva[]
}

model Servico {
  id         Int      @id @default(autoincrement())
  nome       String
  descricao  String?
  preco      Decimal  @db.Decimal(10, 2)
  cuidadorId Int
  createdAt  DateTime @default(now())

  // Relações
  cuidador Usuario   @relation(fields: [cuidadorId], references: [id])
  reservas Reserva[]
}

model Agenda {
  id         Int      @id @default(autoincrement())
  cuidadorId Int
  data       DateTime @db.Date
  horaInicio String
  horaFim    String
  disponivel Boolean  @default(true)
  reservaId  Int?     @unique

  // Relações
  cuidador Usuario  @relation(fields: [cuidadorId], references: [id])
  reserva  Reserva? @relation(fields: [reservaId], references: [id])

  @@index([cuidadorId, data, disponivel])
}

model Reserva {
  id         Int           @id @default(autoincrement())
  donoId     Int
  cuidadorId Int
  petId      Int
  servicoId  Int
  dataInicio DateTime
  dataFim    DateTime
  status     StatusReserva @default(PENDENTE)
  createdAt  DateTime      @default(now())
  updatedAt  DateTime      @updatedAt

  // Relações
  dono     Usuario  @relation("DonoReservas", fields: [donoId], references: [id])
  cuidador Usuario  @relation("CuidadorReservas", fields: [cuidadorId], references: [id])
  pet      Pet      @relation(fields: [petId], references: [id])
  servico  Servico  @relation(fields: [servicoId], references: [id])
  agenda   Agenda?
  logs     Log[]
}

model Log {
  id        Int      @id @default(autoincrement())
  tipo      String
  reservaId Int?
  mensagem  String
  createdAt DateTime @default(now())

  // Relações
  reserva Reserva? @relation(fields: [reservaId], references: [id])
}
```

### Endpoints da API

#### Autenticação
| Método | Rota | Descrição |
|--------|------|-----------|
| POST | `/api/auth/register` | Cadastrar usuário |
| POST | `/api/auth/login` | Fazer login |
| GET | `/api/auth/me` | Obter usuário logado |

#### Pets (CRUD)
| Método | Rota | Descrição |
|--------|------|-----------|
| GET | `/api/pets` | Listar pets do usuário |
| GET | `/api/pets/:id` | Obter pet específico |
| POST | `/api/pets` | Cadastrar novo pet |
| PUT | `/api/pets/:id` | Atualizar pet |
| DELETE | `/api/pets/:id` | Remover pet |

#### Cuidadores
| Método | Rota | Descrição |
|--------|------|-----------|
| GET | `/api/cuidadores` | Listar cuidadores |
| GET | `/api/cuidadores/:id` | Obter perfil do cuidador |
| GET | `/api/cuidadores/:id/agenda` | Ver disponibilidade |

#### Reservas
| Método | Rota | Descrição |
|--------|------|-----------|
| GET | `/api/reservas` | Listar reservas do usuário |
| GET | `/api/reservas/:id` | Obter reserva específica |
| POST | `/api/reservas` | **Criar reserva (transação)** |
| PATCH | `/api/reservas/:id/cancelar` | Cancelar reserva |

#### Admin - Dashboard
| Método | Rota | Descrição | Permissão |
|--------|------|-----------|-----------|
| GET | `/api/admin/dashboard` | Estatísticas gerais | ADMIN |

#### Admin - Gestão de Usuários
| Método | Rota | Descrição | Permissão |
|--------|------|-----------|-----------|
| GET | `/api/admin/usuarios` | Listar todos os usuários | ADMIN |
| GET | `/api/admin/usuarios/:id` | Detalhes do usuário | ADMIN |
| PUT | `/api/admin/usuarios/:id` | Atualizar usuário | ADMIN |
| DELETE | `/api/admin/usuarios/:id` | Desativar usuário | ADMIN |
| PATCH | `/api/admin/usuarios/:id/ativar` | Reativar usuário | ADMIN |

#### Admin - Denúncias
| Método | Rota | Descrição | Permissão |
|--------|------|-----------|-----------|
| GET | `/api/admin/denuncias` | Listar denúncias | ADMIN |
| GET | `/api/admin/denuncias/:id` | Detalhes da denúncia | ADMIN |
| PATCH | `/api/admin/denuncias/:id` | Atualizar status | ADMIN |
| POST | `/api/admin/denuncias/:id/parecer` | Adicionar parecer | ADMIN |

#### Admin - Configurações do Sistema
| Método | Rota | Descrição | Permissão |
|--------|------|-----------|-----------|
| GET | `/api/admin/configuracoes` | Listar configurações | ADMIN |
| PUT | `/api/admin/configuracoes/:chave` | Atualizar configuração | ADMIN |

#### Admin - Logs de Auditoria
| Método | Rota | Descrição | Permissão |
|--------|------|-----------|-----------|
| GET | `/api/admin/logs` | Listar logs de ações | ADMIN |
| GET | `/api/admin/logs/:id` | Detalhes do log | ADMIN |

---

## Glossário

| Termo | Definição |
|-------|-----------|
| **Dono** | Usuário proprietário de pets que busca cuidadores |
| **Cuidador** | Usuário que oferece serviços de cuidado para pets |
| **Admin** | Administrador do sistema com acesso total para gerenciar usuários, denúncias e configurações |
| **Pet** | Animal de estimação cadastrado no sistema |
| **Reserva** | Agendamento de serviço entre dono e cuidador |
| **Agenda** | Slots de disponibilidade de um cuidador |
| **Transação ACID** | Operação atômica que garante consistência do banco |
| **Denúncia** | Reclamação de um usuário contra outro, analisada pelo admin |
| **Log de Auditoria** | Registro de todas as ações administrativas no sistema |
