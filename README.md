# 🦟 ZikaMaps API

<div align="center">

**Backend da plataforma ZikaMaps para monitoramento de focos de arboviroses**

[![Python](https://img.shields.io/badge/Python-3776AB?style=flat-square&logo=python&logoColor=white)](https://www.python.org/)
[![FastAPI](https://img.shields.io/badge/FastAPI-009688?style=flat-square&logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com/)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-336791?style=flat-square&logo=postgresql&logoColor=white)](https://www.postgresql.org/)
[![SQLAlchemy](https://img.shields.io/badge/SQLAlchemy-D71F00?style=flat-square)](https://www.sqlalchemy.org/)
[![JWT](https://img.shields.io/badge/JWT-000000?style=flat-square&logo=jsonwebtokens&logoColor=white)](https://jwt.io/)
[![WebSocket](https://img.shields.io/badge/WebSocket-Real--Time-4AADA8?style=flat-square)](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API)
[![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat-square&logo=docker&logoColor=white)](https://www.docker.com/)

</div>

---

## 📋 Sobre o Projeto

O **ZikaMaps API** é o backend da plataforma ZikaMaps, responsável pelo processamento dos dados de usuários, denúncias, imagens e atualizações em tempo real.

A API foi desenvolvida com **Python e FastAPI** e possui recursos de autenticação, autorização por perfil, persistência de dados com SQLAlchemy, gerenciamento de denúncias, upload de imagens e comunicação em tempo real através de WebSocket.

O projeto foi desenvolvido como parte do Trabalho de Conclusão de Curso (TCC) de **Análise e Desenvolvimento de Sistemas (ADS)**.

---

## ⚙️ Principais Funcionalidades

- 🔐 Autenticação de usuários com JWT
- 🔑 Hash de senhas utilizando bcrypt
- 👤 Gerenciamento de usuários e perfis
- 🏥 Controle de acesso para agentes
- 📍 Registro de denúncias com localização
- 📊 Gerenciamento de status das denúncias
- 📸 Upload e armazenamento de imagens
- 🔄 Atualizações em tempo real via WebSocket
- 🗄️ Persistência com SQLAlchemy
- 🐘 Suporte a PostgreSQL
- 🧪 Compatibilidade com SQLite em cenários de desenvolvimento
- 📖 Documentação automática com Swagger/OpenAPI
- 🐳 Suporte a Docker e Docker Compose

---

## 🏗️ Arquitetura

```text
Cliente / Front-end
        │
        ▼
     FastAPI
        │
 ┌──────┼─────────┐
 ▼      ▼         ▼
Auth  Reports  WebSocket
 │      │         │
 ▼      ▼         ▼
JWT   SQLAlchemy  Real-Time
        │
        ▼
    PostgreSQL
```

---

## 🔐 Autenticação

A API utiliza **JWT (JSON Web Token)** para autenticação.

O fluxo principal é:

```text
Usuário
   │
   ▼
POST /api/auth/signin
   │
   ▼
Validação de credenciais
   │
   ▼
JWT gerado
   │
   ▼
Bearer Token
   │
   ▼
Endpoints protegidos
```

As senhas são armazenadas utilizando **hash com bcrypt**.

O backend também possui autorização específica para usuários com o papel:

```text
agent
```

---

## 👥 Perfis

### 👤 Citizen

Usuário responsável por registrar e acompanhar denúncias.

### 🏥 Agent

Usuário autorizado a analisar denúncias e alterar seus status.

Os status utilizados nas denúncias são:

```text
pending
confirmed
resolved
discarded
```

---

## 📡 Endpoints

### 🔐 Autenticação

#### Criar usuário

```http
POST /api/auth/signup
```

Cria uma nova conta de usuário.

#### Login

```http
POST /api/auth/signin
```

Valida as credenciais e retorna um token JWT.

#### Usuário autenticado

```http
GET /api/auth/me
```

Retorna os dados do usuário autenticado.

#### Papel do usuário

```http
GET /api/auth/role
```

Retorna o papel principal do usuário autenticado.

#### Recuperação de senha

```http
POST /api/auth/reset-password
```

Inicia o processo de recuperação de senha.

> Atualmente, o envio de e-mail é simulado para desenvolvimento e o link de recuperação é exibido no console do servidor.

#### Atualização da senha

```http
POST /api/auth/update-password
```

Atualiza a senha utilizando um token específico de recuperação.

---

### 📍 Denúncias

#### Listar denúncias

```http
GET /api/reports
```

Consulta as denúncias disponíveis para o usuário autenticado.

#### Criar denúncia

```http
POST /api/reports
```

Cria uma nova denúncia contendo informações como descrição, endereço e localização geográfica.

#### Atualizar status

```http
PATCH /api/reports/{report_id}/status
```

Atualiza o status de uma denúncia.

Esse endpoint é protegido e exige permissão de agente.

---

### 📸 Imagens

#### Upload

```http
POST /api/reports/upload
```

Recebe uma imagem enviada pelo cliente e armazena seus dados.

#### Recuperar imagem

```http
GET /api/reports/image/{file_id}
```

Retorna a imagem armazenada a partir do seu identificador.

---

## 🔄 WebSocket

O sistema utiliza WebSocket para comunicação em tempo real.

Endpoint:

```text
/ws
```

Quando uma denúncia é criada ou atualizada, o backend pode transmitir eventos para os clientes conectados.

Exemplo:

```json
{
  "eventType": "INSERT",
  "new": {
    "id": "report-id",
    "status": "pending"
  }
}
```

Também existem eventos de atualização:

```json
{
  "eventType": "UPDATE",
  "new": {
    "id": "report-id",
    "status": "confirmed"
  }
}
```

---

## 🗄️ Banco de Dados

O backend utiliza **SQLAlchemy** como ORM.

Principais entidades:

```text
User
 ├── Profile
 ├── UserRole
 └── Report
       └── UploadedFile
```

### User

Armazena os dados principais de autenticação do usuário.

### Profile

Armazena informações complementares do usuário.

### UserRole

Armazena o papel de acesso do usuário.

### Report

Representa uma denúncia registrada no sistema.

Entre seus dados estão:

```text
id
user_id
description
address
status
date
lat
lng
image_id
created_at
updated_at
```

### UploadedFile

Armazena os arquivos enviados para o sistema e seus respectivos dados binários.

---

## 📂 Estrutura do Projeto

```text
zika-maps-back/
├── app/
│   ├── routers/
│   │   ├── auth.py
│   │   └── reports.py
│   ├── auth.py
│   ├── database.py
│   ├── models.py
│   ├── schemas.py
│   └── websocket.py
├── .env.example
├── .gitignore
├── Dockerfile
├── docker-compose.yml
├── database_setup.py
├── main.py
├── migrate_images_to_db.py
├── requirements.txt
└── update_db_schema.py
```

---

## 🛠️ Tecnologias

### Backend

- Python
- FastAPI
- SQLAlchemy
- Pydantic
- PyJWT
- Passlib
- bcrypt
- Uvicorn
- python-dotenv

### Banco de Dados

- PostgreSQL
- SQLite

### Comunicação

- REST API
- WebSocket

### Infraestrutura

- Docker
- Docker Compose

### Documentação

- OpenAPI
- Swagger UI

---

## 🚀 Instalação

### Pré-requisitos

- Python 3.11 ou superior
- PostgreSQL
- Git
- Docker e Docker Compose, caso deseje utilizar containers

### 1. Clone o repositório

```bash
git clone https://github.com/kaellcina/zika-maps-back.git
```

### 2. Acesse a pasta

```bash
cd zika-maps-back
```

### 3. Crie um ambiente virtual

No Windows:

```bash
python -m venv venv
```

Ative o ambiente:

```bash
venv\Scripts\activate
```

No Linux/macOS:

```bash
python3 -m venv venv
source venv/bin/activate
```

### 4. Instale as dependências

```bash
pip install -r requirements.txt
```

### 5. Configure o ambiente

Use o arquivo `.env.example` como modelo.

No Windows, copie o arquivo manualmente para:

```text
.env
```

Exemplo de configuração:

```env
DATABASE_URL=postgresql://user:password@host:5432/dbname
JWT_SECRET=sua_chave_secreta
JWT_ALGORITHM=HS256
ACCESS_TOKEN_EXPIRE_MINUTES=1440
ENV=development
PORT=5000
UPLOAD_DIR=uploads
BACKEND_URL=http://127.0.0.1:5000
```

> ⚠️ Nunca publique o arquivo `.env`. Ele deve permanecer apenas no ambiente local ou de produção.

### 6. Execute a API

```bash
uvicorn main:app --reload
```

A API ficará disponível em:

```text
http://127.0.0.1:5000
```

---

## 📖 Documentação da API

Com o servidor em execução, acesse:

### Swagger UI

```text
http://127.0.0.1:5000/docs
```

### OpenAPI

```text
http://127.0.0.1:5000/openapi.json
```

A documentação interativa é gerada automaticamente pelo FastAPI.

---

## 🐳 Docker

O projeto possui suporte para execução utilizando Docker.

Arquivos utilizados:

```text
Dockerfile
docker-compose.yml
```

Para iniciar os containers:

```bash
docker compose up --build
```

---

## 🔗 Projeto relacionado

### Front-end

https://github.com/kaellcina/zika-maps-front

### Aplicação

https://zika-maps-front.vercel.app/

---

## 👥 Equipe

| Nome | Papel | GitHub |
|---|---|---|
| **Kaell Soares Calacina** | Desenvolvedor & Documentador | [@kaellcina](https://github.com/kaellcina) |
| **Ana Lívia da Costa Silva** | Documentadora & Analista | [@liviacosttaa](https://github.com/liviacosttaa) |
| **Vitória Santos de Azevedo** | Testadora & Documentadora | [@csvick](https://github.com/csvick) |
| **Luiz Henrique Moutinho Laranjeira** | Documentador & Analista | [@luizhmoutinho](https://github.com/luizhmoutinho) |
| **João Etto de Souza Gomes** | Designer & Documentador | [@JoaoEtto](https://github.com/JoaoEtto) |

**Orientadora:** Luana Magalhães Leal

---

<div align="center">

**ZikaMaps API · TCC — ADS Fametro 2026**

Feito com ❤️ pela equipe ZikaMaps.

</div>
