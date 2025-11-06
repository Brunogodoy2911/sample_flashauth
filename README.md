# API Flask de Autenticação e Gerenciamento de Usuários

Um projeto de API RESTful robusto construído com Flask, Flask-SQLAlchemy
e Flask-Login, focado em autenticação segura (com bcrypt) e
gerenciamento de usuários com diferentes níveis de permissão (roles).

## ✨ Funcionalidades

-   **Autenticação Segura**: Login de usuários com senhas hasheadas
    usando bcrypt.
-   **Gerenciamento de Sessão**: Uso do Flask-Login para gerenciar
    sessões de usuários (login, logout, current_user).
-   **CRUD de Usuários**: Operações completas de Criar, Ler, Atualizar e
    Deletar usuários.
-   **Controle de Acesso Baseado em Papel (RBAC)**:
    -   Rotas protegidas que exigem login (`@login_required`).
    -   Lógica de permissão para `user` e `admin`.
    -   Usuários `admin` podem deletar outros usuários.
    -   Usuários `user` só podem atualizar suas próprias informações.
-   **Banco de Dados MySQL**: Configurado para rodar com um banco de
    dados MySQL (facilmente configurável com o `docker-compose.yml`
    incluído).

## 🚀 Começando

Siga estas instruções para configurar e rodar o projeto localmente.

### 1. Pré-requisitos

-   Python 3.10+
-   Pip (gerenciador de pacotes do Python)
-   Docker e Docker Compose (para rodar o banco de dados MySQL
    facilmente)

### 2. Configuração do Banco de Dados

Clone este repositório.

No terminal, na raiz do projeto, inicie o contêiner do banco de dados
MySQL:

``` bash
docker-compose up -d
```

Isso iniciará um servidor MySQL na porta 3306 com as credenciais
definidas em `docker-compose.yml` (usuário: `admin`, senha: `admin123`,
banco: `flask-crud`).

### 3. Configuração do Ambiente Virtual

Crie um ambiente virtual:

``` bash
python -m venv .venv
```

Ative o ambiente virtual:

-   macOS/Linux: `source .venv/bin/activate`
-   Windows: `.venv\Scripts\activate`

### 4. Instalação das Dependências

Instale as dependências listadas no `requirements.txt`:

``` bash
pip install -r requirements.txt
```

**IMPORTANTE**: O seu `requirements.txt` está faltando duas bibliotecas
essenciais usadas no `app.py`. Instale-as:

``` bash
pip install bcrypt PyMySQL
```

Recomendação: Atualize seu `requirements.txt` com:

``` bash
pip freeze > requirements.txt
```

### 5. Inicialização do Banco de Dados

Antes de rodar a aplicação pela primeira vez, você precisa criar as
tabelas no banco de dados.

Abra um terminal Python com o ambiente ativado:

``` bash
python
```

Execute os seguintes comandos:

``` python
from app import app, db
with app.app_context():
    db.create_all()
exit()
```

### 6. Rodando a Aplicação

Com o banco de dados rodando e as dependências instaladas, inicie o
servidor Flask:

``` bash
python app.py
```

O servidor estará rodando em `http://127.0.0.1:5000`.

## 📖 Documentação da API (Endpoints)

Todos os endpoints que recebem ou enviam dados usam o formato
`application/json`.

### 🔐 Autenticação

#### POST /login

Autentica um usuário e inicia uma sessão.

**Corpo da Requisição (JSON):**

``` json
{
  "username": "nome_do_usuario",
  "password": "senha_do_usuario"
}
```

**Respostas:**

-   `200 OK`: { "message": "Login bem-sucedido" }
-   `400 Bad Request`: { "message": "Credenciais inválidas" }

#### GET /logout

Desconecta o usuário logado.

**Autenticação:** Requerida.

**Respostas:**

-   `200 OK`: { "message": "Logout bem-sucedido" }
-   `401 Unauthorized`

### 👤 Gerenciamento de Usuários

#### POST /user

Cria um novo usuário.

**Corpo da Requisição:**

``` json
{
  "username": "novo_usuario",
  "password": "senha_forte_123"
}
```

**Respostas:**

-   `200 OK`: { "message": "Usuário cadastrado com sucesso" }
-   `400 Bad Request`: { "message": "Credenciais inválidas!" }

#### GET /user/`<int:id_user>`{=html}

Busca os detalhes de um usuário.

**Respostas:**

-   `200 OK`: { "username": "nome_do_usuario" }
-   `404 Not Found`: { "message": "Usuário não encontrado!" }

#### PUT /user/`<int:id_user>`{=html}

Atualiza a senha de um usuário.

**Regras de Permissão:**

-   `user`: Só pode atualizar seu próprio perfil.
-   `admin`: Pode atualizar qualquer perfil.

**Respostas:**

-   `200 OK`: { "message": "Usuário atualizado com sucesso!" }
-   `403 Forbidden`: { "message": "Atualização não permitida" }

#### DELETE /user/`<int:id_user>`{=html}

Deleta um usuário.

**Regras de Permissão:**

-   Apenas `admin`.
-   Um admin **não pode deletar a si mesmo**.

**Respostas:**

-   `200 OK`: { "message": "Usuário deletado com sucesso" }
-   `403 Forbidden`: { "message": "Deleção não permitida" }

------------------------------------------------------------------------

Feito com ❤️ usando Flask.
