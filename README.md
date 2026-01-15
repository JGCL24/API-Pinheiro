# 🏟️ Pinheiro API – Sistema de Gerenciamento Arena Pinheiro

**API REST completa** para gerenciamento de arena esportiva com autenticação, controle de acesso e operações CRUD completas.

## 📖 Sobre o Projeto

Sistema de gestão para arenas esportivas que permite gerenciar:
- **Campos esportivos** e suas disponibilidades
- **Reservas** de campos por clientes
- **Comandas** de consumo (mesas e produtos)
- **Produtos** e controle de estoque
- **Pagamentos** vinculados a comandas, compras e reservas
- **Usuários** do sistema com dois níveis de acesso

### 🔐 Autenticação e Controle de Acesso

O sistema implementa autenticação HTTP Basic com dois níveis de usuário:

- **Admin**: Acesso total a todas operações (CRUD completo em todos os recursos)
- **Funcionário**: Acesso a operações do dia a dia, mas **não pode gerenciar usuários**

**Todas as operações exigem autenticação.** Requisições sem credenciais retornam `401 Unauthorized`.

## ✨ Funcionalidades Principais

### 📊 5 Visões SQL Complexas

1. **vw_item_comanda_completo** - Itens de comanda com cálculo automático de valores
2. **vw_item_compra_completo** - Itens de compra com totalizações
3. **vw_produtos_estoque** - Produtos com quantidades disponíveis
4. **vw_reservas_detalhe** - Reservas com informações completas
5. **vw_clientes_publicos** - Dados públicos de clientes

### 🔧 17 Módulos CRUD Completos

Todos com autenticação obrigatória e validação de permissões:
- Usuários, Clientes, Produtos, Campos, Mesas
- Comandas, Reservas, Compras, Pagamentos
- Estoque, Movimentações, Itens (comanda/compra)
- Relacionamentos de pagamentos (comanda/compra/reserva)

## 🚀 Como Executar

### 1️⃣ Pré-requisitos

- **Python 3.8+** instalado
- **PostgreSQL 12+** instalado e rodando
- **pip** (gerenciador de pacotes Python)

### 2️⃣ Configurar o Banco de Dados

#### Instalar PostgreSQL

**Windows:**
```bash
# Baixe em: https://www.postgresql.org/download/windows/
# Durante instalação, defina senha para usuário 'postgres'
```

**Linux (Ubuntu/Debian):**
```bash
sudo apt update
sudo apt install postgresql postgresql-contrib
```

**macOS:**
```bash
brew install postgresql@14
brew services start postgresql@14
```

#### Criar banco e estrutura

1. Acesse o PostgreSQL:
```bash
# Windows
psql -U postgres

# Linux/macOS
sudo -u postgres psql
```

2. Execute os comandos:
```sql
CREATE DATABASE arena_pinheiro;
\c arena_pinheiro
\i sql/create_database_complete.sql
```

Ou execute diretamente:
```bash
psql -U postgres -f sql/create_database_complete.sql
```

### 3️⃣ Configurar a Aplicação

1. **Clone ou baixe o projeto**

2. **Configure a conexão** no arquivo `db.py`:
```python
# Ajuste se necessário:
conn = psycopg2.connect(
    host="localhost",
    port="5432",
    database="arena_pinheiro",
    user="postgres",      # Seu usuário PostgreSQL
    password="sua_senha"  # Sua senha PostgreSQL
)
```

3. **Instale as dependências:**
```bash
pip install fastapi uvicorn psycopg2-binary python-multipart
```

### 4️⃣ Executar a API

```bash
cd pinheiro
python main.py
```

A API estará disponível em:
- **URL Base:** http://127.0.0.1:5000
- **Documentação Interativa:** http://127.0.0.1:5000/docs
- **Redoc:** http://127.0.0.1:5000/redoc

### 5️⃣ Testar a API

#### Credenciais de Teste

**Administrador:**
- Email: `admin@pinheiro.com`
- Senha: `admin123`
- Permissões: Acesso total

**Funcionário:**
- Email: `funcionario@pinheiro.com`
- Senha: `func123`
- Permissões: Não pode gerenciar usuários

#### Exemplo de Requisição (PowerShell)

**Sem autenticação (será rejeitado):**
```powershell
Invoke-WebRequest -Uri "http://127.0.0.1:5000/api/clientes/"
# Retorna: 401 Unauthorized
```

**Com autenticação:**
```powershell
$password = ConvertTo-SecureString "admin123" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential("admin@pinheiro.com", $password)
Invoke-WebRequest -Uri "http://127.0.0.1:5000/api/clientes/" -Credential $credential
# Retorna: 200 OK com lista de clientes
```

#### Usando a Documentação Interativa

1. Acesse http://127.0.0.1:5000/docs
2. Clique em **Authorize** (cadeado no topo direito)
3. Digite:
   - **Username:** admin@pinheiro.com
   - **Password:** admin123
4. Teste qualquer endpoint clicando em **Try it out**

## 📁 Estrutura do Projeto

```
pinheiro/
├── main.py              # Aplicação principal FastAPI
├── db.py                # Configuração de conexão PostgreSQL
├── auth.py              # Sistema de autenticação e autorização
├── models.py            # Modelos Pydantic para validação
├── crud_*.py            # 17 módulos CRUD (um por recurso)
├── init_views.py        # Criação das 5 views complexas
└── sql/
    └── create_database_complete.sql  # Script completo do banco
```

## 🔧 Endpoints da API

### Autenticação
Todos os endpoints exigem **HTTP Basic Authentication** com email/senha.

### Recursos Principais

| Recurso | Endpoints | Permissão |
|---------|-----------|-----------|
| **Usuários** | `/api/usuarios/` | ⚠️ Apenas Admin |
| **Clientes** | `/api/clientes/` | ✅ Admin + Funcionário |
| **Produtos** | `/api/produtos/` | ✅ Admin + Funcionário |
| **Campos** | `/api/campos/` | ✅ Admin + Funcionário |
| **Mesas** | `/api/mesas/` | ✅ Admin + Funcionário |
| **Comandas** | `/api/comandas/` | ✅ Admin + Funcionário |
| **Reservas** | `/api/reservas/` | ✅ Admin + Funcionário |
| **Compras** | `/api/compras/` | ✅ Admin + Funcionário |
| **Pagamentos** | `/api/pagamentos/` | ✅ Admin + Funcionário |
| **Estoque** | `/api/estoques/` | ✅ Admin + Funcionário |
| **Views** | `/api/views/*` | ✅ Admin + Funcionário |

Cada recurso possui operações CRUD completas:
- `GET /` - Listar todos
- `GET /{id}` - Buscar por ID
- `POST /` - Criar novo
- `PUT /{id}` - Atualizar
- `DELETE /{id}` - Deletar

## 📊 Banco de Dados

### Estrutura (16 Tabelas)

1. **usuario** - Usuários do sistema (admin/funcionário)
2. **cliente** - Clientes da arena
3. **produto** - Produtos vendidos
4. **estoque** - Controle de estoque
5. **movimenta** - Movimentações de estoque
6. **campo** - Campos esportivos
7. **mesa** - Mesas do estabelecimento
8. **reserva** - Reservas de campos
9. **comanda** - Comandas de consumo
10. **item_comanda** - Itens das comandas
11. **compra** - Compras de estoque
12. **item_compra** - Itens das compras
13. **pagamento** - Pagamentos realizados
14. **pag_comanda** - Relacionamento pagamento-comanda
15. **pag_compra** - Relacionamento pagamento-compra
16. **pag_reserva** - Relacionamento pagamento-reserva

### 5 Views SQL Complexas

#### 1. vw_item_comanda_completo
```sql
SELECT ic.id_item_comanda, ic.id_comanda, ic.id_produto, ic.quantidade,
       p.preco as preco_unitario, (ic.quantidade * p.preco) as subtotal,
       p.nome as produto_nome
FROM item_comanda ic
INNER JOIN produto p ON ic.id_produto = p.id_produto;
```

#### 2. vw_item_compra_completo
```sql
SELECT ic.id_item_compra, ic.id_compra, ic.id_produto, ic.quantidade,
       p.preco as preco_unitario, (ic.quantidade * p.preco) as subtotal,
       p.nome as produto_nome
FROM item_compra ic
INNER JOIN produto p ON ic.id_produto = p.id_produto;
```

#### 3. vw_produtos_estoque
```sql
SELECT p.id_produto, p.nome, p.preco, e.quant_present
FROM produto p
LEFT JOIN estoque e ON e.id_produto = p.id_produto;
```

#### 4. vw_clientes_publicos
```sql
SELECT id_cliente, nome, email, tipo, cpf
FROM cliente;
```

#### 5. vw_reservas_detalhe
```sql
SELECT r.id_reserva, r.data, r.quant_horas, r.status, 
       r.cpf_cliente, r.id_campo
FROM reserva r;
```

## 🛠️ Tecnologias Utilizadas

- **FastAPI** - Framework web moderno e rápido
- **PostgreSQL** - Banco de dados relacional
- **Psycopg2** - Driver PostgreSQL para Python
- **Pydantic** - Validação de dados
- **Uvicorn** - Servidor ASGI de alta performance
- **HTTP Basic Auth** - Autenticação simples e segura

## 📝 Exemplos de Uso

### Criar um Cliente (Admin/Funcionário)
```bash
POST /api/clientes/
Authorization: Basic admin@pinheiro.com:admin123

{
  "cpf": "12345678901",
  "nome": "João Silva",
  "email": "joao@email.com",
  "tipo": "regular",
  "id_usuario_cadastrou": 1
}
```

### Listar Produtos com Estoque
```bash
GET /api/views/produtos-estoque
Authorization: Basic funcionario@pinheiro.com:func123
```

### Criar Reserva de Campo
```bash
POST /api/reservas/
Authorization: Basic admin@pinheiro.com:admin123

{
  "data": "2026-01-20",
  "quant_horas": 2,
  "status": "confirmada",
  "cpf_cliente": "12345678901",
  "id_campo": 1
}
```

### Tentar Criar Usuário como Funcionário (Bloqueado)
```bash
POST /api/usuarios/
Authorization: Basic funcionario@pinheiro.com:func123

# Retorna: 403 Forbidden
# "Acesso negado. Apenas administradores podem gerenciar usuários."
```

## 🔒 Segurança

- ✅ Autenticação obrigatória em todos os endpoints
- ✅ Controle de acesso baseado em roles (admin/funcionário)
- ✅ Validação de dados com Pydantic
- ✅ Proteção contra SQL Injection (prepared statements)
- ✅ Tratamento de erros e exceções

## 🐛 Solução de Problemas

### Erro: "Não é possível conectar ao banco"
```bash
# Verifique se o PostgreSQL está rodando
# Windows
pg_ctl status

# Linux
sudo systemctl status postgresql

# Verifique as credenciais em db.py
```

### Erro: "401 Unauthorized"
- Verifique se está enviando credenciais corretas
- Use: `admin@pinheiro.com` / `admin123` ou `funcionario@pinheiro.com` / `func123`

### Erro: "403 Forbidden" ao acessar /api/usuarios/
- Operações de usuário são exclusivas para **admin**
- Faça login com: `admin@pinheiro.com` / `admin123`

### Porta 5000 já em uso
```bash
# Pare processos Python
taskkill /F /IM python.exe

# Ou altere a porta em main.py:
uvicorn.run(app, host="127.0.0.1", port=8000)
```

## 👨‍💻 Desenvolvido por

**Disciplina:** Fundamentos de Banco de Dados  
**Instituição:** [Sua Instituição]  
**Ano:** 2026

---

📚 **Documentação Completa:** http://127.0.0.1:5000/docs  
🔗 **API Base URL:** http://127.0.0.1:5000/api/#   A P I - P i n h e i r o  
 