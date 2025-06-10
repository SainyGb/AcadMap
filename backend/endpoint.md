
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

# 📘 API - Cadastro de Evento

**Endpoint:**
```
POST /api/eventos/cadastro/{id}
```

**Descrição:**  
Cria um novo evento no sistema, *deve ser um usuario já cadastrado no sistema*.

**Headers obrigatórios:**
```http
X-User-Id: <UUID do usuário que está tentando aprovar>
```

**Path Parameters:**
- `id` – UUID do usuário que está tentando inserir o evento.

**Resposta:**
- `202 Accepted` – Evento criado com sucesso.
- `409 Conflit` – Tentativa de cadastrar um evento ja cadastrado, duplicação de evento.
- `400 Bad Request` – Erro na requisição.

---

## 🔸 Requisição

**Corpo esperado:**

```json
{
  "nome": "Congresso Nacional de Tecnologia",
  "classificacao": "A1",
  "vinculoSbc": "sem_vinculo",
  "adequadoDefesa": "nenhum",
  "areasPesquisaIds": [
    "c3398a61-b985-42af-9fe5-d6bddd971a87",
    "a3c5f2a4-081a-4a17-8c8b-9a60c431c931"
  ],
  "h5": 42,
  "linkEvento": "https://congresso-tecnologia.org",
  "linkGoogleScholar": "https://scholar.google.com/congresso-tecnologia",
  "linkSolSbc": "https://sol.sbc.org.br/congresso-tecnologia"
}
```

### Campos:
- `nome` (string): Nome do evento.
- `classificação` (string): Classificação em que o evento está (Enum = "a1", "a2"..."a8").
- `vinculoSbc` (string): Tipo de vinculo com a sbc (Enum = "sem_vinculo", "vinculo_top_10", "vinculo_top_20", "vinculo_comum" ).
- `adequadoDefesa` (string):  (Enum = "mestrado", "doutorado", "nenhum", "mestrado_doutorado)
- `areasPesquisaIds`(array de UUIDs): IDs das áreas de pesquisa associadas ao evento.
- `h5` (integer): Índice H5 do evento.
- `linkEvento` (string): Link do evento o qual está sendo inserido.
- `linkGoogleScholar` (string) : Link do googlescholar referente ao evento o qual está sendo inserido
- `linkSolSbc` (string) : Link repositório SOL-SBC referente ao evento o qual está sendo inserido.
---

## ✅ Resposta

**Status:** `201 Created`  
**Body:**

```json
{
  "idVeiculo": "1077ced6-3f23-444d-b341-4750cf2bd1e8",
  "nome": "Congresso Nacional de Tecnologia",
  "classificacao": "a1",
  "vinculoSbc": "sem_vinculo",
  "adequadoDefesa": "nenhum",
  "tipo": "evento",
  "status": "pendente",
  "h5": 42,
  "linkEvento": "https://congresso-tecnologia.org",
  "linkGoogleScholar": "https://scholar.google.com/congresso-tecnologia",
  "linkSolSbc": "https://sol.sbc.org.br/congresso-tecnologia",
  "areasPesquisaIds": [
    "a3c5f2a4-081a-4a17-8c8b-9a60c431c931",
    "c3398a61-b985-42af-9fe5-d6bddd971a87"
  ],
  "usuario": {
    "idUsuario": "11111111-aaaa-bbbb-cccc-000000000027",
    "nome": "Isabela Neves"
  }
}
```

### Campos:
- `idVeiculo` (UUID): Identificador único do evento criado.
- `nome`, `classificacao`, `vinculoSbc`, `adequacaoDefesa`: Mesmos campos enviados, com confirmação do que foi salvo.
- `tipo`, `status` : Valores padrões na criação de Evento. (Evento, Pendente)
- `h5`, `linkEvento`, `linkGoogleScholar`, `linkSolSbc` , `areaPesquisaIds`: Mesmos campos enviados, com confirmação do que foi salvo.
- `usuario` : Informação de Id e Nome do usuário o qual inseriu Evento.
---




