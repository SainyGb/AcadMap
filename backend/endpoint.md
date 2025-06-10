
# 📘 API - Veículo de Publicação

Este documento descreve os endpoints disponíveis no backend, incluindo os métodos HTTP esperados, headers e parâmetros de entrada.

Base URL:  
```
/api/veiculo
```

---

## ✅ 1. **Aprovar Veículo de Publicação**

**Endpoint:**
```
PUT /api/veiculo/aprovar-veiculo/{id}
```

**Descrição:**  
Aprova um veículo de publicação caso o usuário **não seja um pesquisador** e **não esteja vinculado** ao veículo.

**Headers obrigatórios:**
```http
X-User-Id: <UUID do usuário que está tentando aprovar>
```

**Path Parameters:**
- `id` – UUID do veículo a ser aprovado

**Resposta:**
- `202 Accepted` – Veículo aprovado com sucesso
- `405 Method Not Allowed` – Usuário sem permissão para aprovar
- `400 Bad Request` – Erro na requisição (ex: veículo inexistente)

---

## ❌ 2. **Negar Veículo de Publicação**

**Endpoint:**
```
PUT /api/veiculo/negar-veiculo/{id}
```

**Descrição:**  
Nega um veículo de publicação, sob as mesmas regras de aprovação.

**Headers obrigatórios:**
```http
X-User-Id: <UUID do usuário que está tentando negar>
```

**Path Parameters:**
- `id` – UUID do veículo a ser negado

**Resposta:**
- `202 Accepted` – Veículo negado com sucesso
- `405 Method Not Allowed` – Usuário sem permissão para negar
- `400 Bad Request` – Erro na requisição

---

## 📄 3. **Listar Veículos Pendentes**

**Endpoint:**
```
GET /api/veiculo/periodico-pendente
```

**Descrição:**  
Retorna todos os veículos de publicação com status **pendente**.

**Headers obrigatórios:**
```http
X-User-Id: <UUID do usuário solicitante>
```

**Resposta:**
- `200 OK` – Lista de veículos pendentes
- `404 Not Found` – Nenhum veículo pendente encontrado

---

## ⚙️ Regras internas de negócio

- **Permissões de ação** (aprovar/negar) são negadas se o usuário:
  - Tem perfil de **pesquisador**; ou
  - Está **vinculado** ao veículo (`id_usuario` do veículo = `X-User-Id`).


# 📘 API - Cadastro de usuário

**Endpoint:** `POST /api/usuario/cadastro`  
**Descrição:** Cria um novo usuário no sistema.

---

## 🔸 Requisição

**Content-Type:** `application/json`  
**Corpo esperado:**

```json
{
  "nome": "Maria da Silva",
  "email": "maria.silva@example.com",
  "senha": "senha123",
  "tipoPerfil": "PESQUISADOR",
  "idPrograma": "c6f2e2de-ef91-4f3f-9e43-2f95f8dd4a21",
  "idsAreasPesquisa": [
    "a1b2c3d4-e5f6-7890-ab12-34567890cdef",
    "123e4567-e89b-12d3-a456-426614174000"
  ]
}
```

### Campos:
- `nome` (string): Nome completo do usuário.
- `email` (string): E-mail do usuário.
- `senha` (string): Senha para acesso.
- `tipoPerfil` (string): Tipo do perfil do usuário. Ex: `"PESQUISADOR"`, `"ADMIN"`, etc.
- `idPrograma` (UUID): Identificador do programa vinculado.
- `idsAreasPesquisa` (array de UUIDs): IDs das áreas de pesquisa associadas ao usuário.

---

## ✅ Resposta

**Status:** `201 Created`  
**Body:**

```json
{
  "idUsuario": "c8f6df88-9227-42e4-bd0a-36f8f9e7e021",
  "nome": "Maria da Silva",
  "email": "maria.silva@example.com",
  "tipoPerfil": "PESQUISADOR",
  "idPrograma": "c6f2e2de-ef91-4f3f-9e43-2f95f8dd4a21",
  "idsAreasPesquisa": [
    "a1b2c3d4-e5f6-7890-ab12-34567890cdef",
    "123e4567-e89b-12d3-a456-426614174000"
  ]
}
```

### Campos:
- `idUsuario` (UUID): Identificador único do usuário criado.
- `nome`, `email`, `tipoPerfil`, `idPrograma`, `idsAreasPesquisa`: mesmos campos enviados, com confirmação do que foi salvo.

---
