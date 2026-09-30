# Coders Growth

![.NET 7](https://img.shields.io/badge/.NET-7.0-purple)
![C#](https://img.shields.io/badge/C%23-11-blue)
![SQL Server](https://img.shields.io/badge/SQL%20Server-2019-red)
![License](https://img.shields.io/badge/License-MIT-green)

Sistema de gerenciamento de personagens e habilidades do universo Street Fighter. Projeto de treinamento do programa de estágio da Invent Software, construído com arquitetura limpa, API RESTful e três interfaces de usuário.

---

## Funcionalidades

- **CRUD de Personagens**: criação, edição, consulta e exclusão de personagens com atributos (vida, energia, velocidade, força, inteligência)
- **CRUD de Habilidades**: gerenciamento de habilidades com nome e descrição
- **Associação Muitos-para-Muitos**: vinculação de habilidades a personagens
- **Filtros Avançados**: busca por nome, classificação herói/vilão e período de criação
- **Validação de Dados**: regras de negócio via FluentValidation (nomes únicos, limites de atributos)
- **Tratamento de Erros**: middleware global com RFC 7807 Problem Details

---

## Arquitetura

O projeto segue o padrão **Clean Architecture** com inversão de dependência:

```
┌─────────────────────────────────────────────────┐
│                   Web / Forms                    │  ← Interface (API + SPA + Desktop)
├─────────────────────────────────────────────────┤
│                   Service                        │  ← Lógica de Negócio + Validação
├─────────────────────────────────────────────────┤
│                   Infra                          │  ← Repositórios + Migrations + BD
├─────────────────────────────────────────────────┤
│                   Domain                         │  ← Entidades + Enums + Interfaces
└─────────────────────────────────────────────────┘
```

- **Domain**: entidades puras (`Personagem`, `Habilidade`), enums e interfaces do repositório
- **Service**: serviços de negócio, validadores FluentValidation
- **Infra**: implementação dos repositórios (linq2db), migrations (FluentMigrator), contexto de conexão
- **Web**: API REST (ASP.NET Core), Swagger, frontend SAP UI5
- **Forms**: cliente desktop Windows Forms

---

## Stack Tecnológica

| Camada | Tecnologia |
|--------|------------|
| Backend | .NET 7.0 / C# / ASP.NET Core Web API |
| Frontend Web | SAP UI5 (SAPUI5) |
| Frontend Desktop | Windows Forms (.NET 7) |
| ORM | linq2db 5.2.0 |
| Migrations | FluentMigrator 5.2.0 |
| Validação | FluentValidation 11.9.1 |
| Testes | xUnit 2.4.2 + coverlet |
| Banco de Dados | Microsoft SQL Server |
| Documentação API | Swagger / OpenAPI |
| Serialização | Newtonsoft.Json 13.0.3 |

---

## Pré-requisitos

- [.NET 7.0 SDK](https://dotnet.microsoft.com/download/dotnet/7.0) ou superior
- [SQL Server](https://www.microsoft.com/pt-br/sql-server/sql-server-downloads) (Express, LocalDB ou instância completa)
- [Node.js](https://nodejs.org/) _(opcional — apenas para desenvolvimento do frontend SAP UI5)_
- [Visual Studio 2022](https://visualstudio.microsoft.com/) _(recomendado, mas não obrigatório)_

---

## Instalação e Configuração

1. **Clone o repositório**

```bash
git clone https://github.com/seu-usuario/desafio-coders-growth.git
cd desafio-coders-growth
```

2. **Restore das dependências**

```bash
dotnet restore Cod3rsGrowth.sln
```

3. **Configure o banco de dados**

Edite o arquivo `Cod3rsGrowth.Web/appsettings.json` e atualize a string de conexão `ConexaoPadrao` para apontar para a sua instância do SQL Server:

```json
{
  "ConnectionStrings": {
    "ConexaoPadrao": "Server=SUA_INSTANCIA;Database=Cod3rsGrowth;Trusted_Connection=True;TrustServerCertificate=True"
  }
}
```

> **Nota:** As migrations rodam automaticamente na primeira execução, criando as tabelas e populando os dados iniciais (13 habilidades e 33 personagens).

---

## Execução

### Web API + Frontend SAP UI5

```bash
# Perfil HTTP (porta 5062 — abre Swagger)
dotnet run --project Cod3rsGrowth.Web --launch-profile http

# Perfil HTTPS (porta 5051 — serve o frontend SAP UI5)
dotnet run --project Cod3rsGrowth.Web --launch-profile https
```

| URL | Descrição |
|-----|-----------|
| `http://localhost:5062/swagger` | Swagger UI (documentação da API) |
| `https://localhost:5051` | Frontend SAP UI5 |

### Windows Forms (Desktop)

```bash
dotnet run --project Cod3rsGrowth.Forms
```

### Frontend SAP UI5 (modo desenvolvimento)

```bash
cd Cod3rsGrowth.Web/wwwroot
npm install
npm run dev
```

---

## Testes

### Testes Unitários (xUnit)

```bash
dotnet test Cod3rsGrowth.Tests/Cod3rsGrowth.Tests.csproj
```

Os 42 testes unitários utilizam repositórios mock em memoria — **não necessitam de banco de dados**.

### Cobertura de Código

```bash
dotnet test Cod3rsGrowth.Tests/Cod3rsGrowth.Tests.csproj /p:CollectCoverage=true
```

### Testes de Integração UI (OPA5 — SAP UI5)

```bash
cd Cod3rsGrowth.Web/wwwroot
npm install
npm run test
```

---

## Estrutura do Projeto

```
├── Cod3rsGrowth.sln                  # Solução Visual Studio 2022
│
├── Cod3rsGrowth.Domain/              # Camada de Domínio
│   ├── Entities/                     # Entidades: Personagem, Habilidade, Filtro
│   ├── Enums/                        # CategoriasEnum (Fraco, Médio, Bom, etc.)
│   └── Interfaces/                   # IRepositorio<T> (interface genérica)
│
├── Cod3rsGrowth.Service/             # Camada de Serviço
│   ├── Services/                     # PersonagemServico, HabilidadeServico
│   └── Validators/                   # Validadores FluentValidation
│
├── Cod3rsGrowth.Infra/               # Camada de Infraestrutura
│   ├── Repositories/                 # Implementações dos repositórios (linq2db)
│   ├── Migrations/                   # FluentMigrator (criação + seed)
│   └── ContextoConexao.cs            # DataConnection do linq2db
│
├── Cod3rsGrowth.Web/                 # API REST + Frontend
│   ├── Controllers/                  # PersonagemController, HabilidadeController
│   ├── Properties/                   # launchSettings.json
│   └── wwwroot/                      # Frontend SAP UI5 (SPA)
│       ├── app/                      # Views, controllers, modelos
│       └── tests/                    # Testes OPA5
│
├── Cod3rsGrowth.Forms/               # Cliente Desktop Windows Forms
│   └── Forms/                        # FormPrincipal, Formularios, FormFiltrar
│
└── Cod3rsGrowth.Tests/               # Testes Unitários
    ├── Tests/                        # 42 testes (personagens + habilidades)
    └── RepositoriesMock/             # Repositórios mock em memória
```

---

## Banco de Dados

O sistema utiliza **FluentMigrator** para gerenciar o esquema do banco de dados. As migrations são executadas automaticamente na inicialização.

### Tabelas

| Tabela | Descrição |
|--------|-----------|
| `personagens` | Dados dos personagens (nome, vida, energia, velocidade, força, inteligência, vilão) |
| `habilidades` | Habilidades disponíveis (nome, descrição) |
| `personagens_habilidades` | Tabela de associação (N:N) com chaves estrangeiras em cascata |

### Dados Iniciais

- **13 habilidades**: Ataque a Distância, Defesa, Velocidade de Movimento, Velocidade de Ataque, Combos, Ataque Especial, Recuperação, Agarre, Esquiva, Super Ataque, Controle de Zona, Resistência, Técnica Aérea
- **33 personagens**: Ryu, Ken, Chun-Li, Blanka, E. Honda, Guile, Zangief, Dhalsim, Balrog, Vega, Sagat, M. Bison, Cammy, Akuma, Sakura, Alex, Ibuki, Dudley, Makoto, C. Viper, Juri, Laura, Rashid, Necalli, F.A.N.G, Kolin, Ed, Menat, Zeku, Falke, G, Kage, Lucia

---

## Endpoints da API

### Personagens

| Método | Rota | Descrição |
|--------|------|-----------|
| `GET` | `/api/Personagem` | Listar todos (com filtros: `Nome`, `EVilao`, `DataBase`, `DataTeto`) |
| `GET` | `/api/Personagem/{id}` | Obter por ID (inclui habilidades associadas) |
| `POST` | `/api/Personagem` | Criar novo personagem |
| `PUT` | `/api/Personagem/{id}` | Atualizar personagem |
| `DELETE` | `/api/Personagem/{id}` | Remover personagem |

### Habilidades

| Método | Rota | Descrição |
|--------|------|-----------|
| `GET` | `/api/Habilidade` | Listar todas (com filtros: `Nome`, `DataBase`, `DataTeto`) |
| `GET` | `/api/Habilidade/{id}` | Obter por ID |
| `POST` | `/api/Habilidade` | Criar nova habilidade |
| `PUT` | `/api/Habilidade/{id}` | Atualizar habilidade |
| `DELETE` | `/api/Habilidade/{id}` | Remover habilidade |

---

## Licença

Este projeto está sob a licença MIT.
