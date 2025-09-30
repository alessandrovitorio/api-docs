# README - Aula: Documentação da API Filmes

**Objetivo da aula**

Esta aula tem como objetivo apresentar o que é uma **API**, para que serve, e como documentar uma API REST usando **OpenAPI / Swagger**. Trabalharemos com o exemplo prático **API Filmes**, integrado ao DER do banco de dados (veja instruções sobre a imagem do DER abaixo). Ao final, os alunos deverão saber criar um arquivo `filmes.json`/`filmes.yaml`, abrir o Swagger Editor localmente e testar os endpoints.

---

## 1) O que é uma API? (explicação simples para os alunos)

* **API (Application Programming Interface)** é um contrato que permite que sistemas diferentes conversem entre si.
* Em termos web, uma **API REST** expõe endpoints (URLs) que recebem requisições HTTP (GET, POST, PUT, DELETE) e retornam respostas (JSON, XML, etc.).
* **Para que serve:** integrar front-end com back-end, permitir que apps e serviços consumam dados (ex.: um app de celular consumindo lista de filmes), testar funcionalidades sem interface gráfica e padronizar comunicação.

**Exemplo didático:** A **API Filmes** expõe rotas para listar filmes, buscar por ID, criar, atualizar e deletar — funciona como um catálogo programático de filmes que qualquer cliente pode consultar.

---

## 2) Relacionamento com o DER (modelo de dados)

Antes de documentar a API, é importante entender o **modelo de dados** (DER). O DER mostra as tabelas (models) e seus relacionamentos — por exemplo, `Filme` e `Genero`.

> **Observação:** se você tem a imagem do DER (ERD.svg), coloque-a na pasta do projeto com o nome `ERD.svg` e, no README do repositório, use `![DER](ERD.svg)` para mostrá-la.

(Se preferir, insira o arquivo `ERD.svg` na mesma pasta do README para que a imagem apareça ao abrir o repositório.)

---

## 3) Por que documentar a API com OpenAPI/Swagger?

* Gera documentação legível e interativa (Swagger UI / Editor).
* Facilita o entendimento do contrato da API (endpoints, parâmetros, modelos de dados).
* Permite testar endpoints diretamente no navegador (quando a API estiver rodando localmente).
* Ajuda a manter consistência entre front-end e back-end e é um excelente item para o TCC.

---

## 4) Estrutura do arquivo OpenAPI (o que cada bloco significa e por que a ordem importa)

Ao escrever um documento OpenAPI/Swagger, seguimos uma ordem lógica que facilita leitura e manutenção. Abaixo a explicação de cada bloco (em ordem):

1. **`openapi`**

   * Declara a versão do spec (ex.: "3.0.0").
   * Importante porque ferramentas (Swagger Editor/UI) interpretam a spec a partir daí.

2. **`info`**

   * Metadados sobre a API: `title`, `version`, `description`.
   * Deve vir cedo para identificação rápida do projeto.

3. **`servers`**

   * Define a base URL (ex.: `http://localhost:3000/api/v1`).
   * Útil para o ambiente de desenvolvimento e testes. Swagger UI usa isso como prefixo.

4. **`paths`**

   * Lista os endpoints HTTP (ex.: `/filmes`, `/filmes/{id}`).
   * Cada caminho contém métodos (GET, POST, PUT, DELETE) e descreve parâmetros, requestBody e responses.
   * Colocar `paths` antes de `components` é comum na leitura, mas **`components`** contém reutilizáveis que as `paths` referenciam (por isso a presença de `$ref` nas `paths`).

5. **`components`** (schemas, examples, responses reutilizáveis)

   * Define modelos de dados (`schemas`) que serão referenciados por `$ref` dentro das `paths`.
   * Mantém a spec DRY (Don't Repeat Yourself): você define `Filme` uma vez e reutiliza em várias respostas/requests.

6. **Exemplos e respostas**

   * Colocar exemplos nos `requestBody` e `responses` ajuda muito nos testes e na compreensão.

**Por que esta ordem?**

* A ordem facilita a leitura: metadados, onde a API está (servers), o que ela faz (paths) e quais são os modelos (components). Mesmo que a spec aceite outra ordem tecnicamente, seguir esta convenção torna o arquivo mais compreensível para humanos e ferramentas.

---

## 5) Passo a passo que os alunos devem digitar (guia para a aula)

### Passo 0 — Preparação

* Abra o **VS Code** (ou editor preferido).
* Crie a pasta do projeto: `API-Filmes-Docs`.
* Dentro dela crie `index.html` (Swagger Editor/UI local) e o arquivo de definição `filmes.json`.
* Se for usar a imagem do DER, coloque `ERD.svg` na mesma pasta.

### Passo 1 — Esqueleto inicial

Digite no `filmes.json`:

```json
{
  "openapi": "3.0.0",
  "info": {
    "title": "API de Filmes",
    "version": "1.0.0",
    "description": "Documentação inicial da API de Filmes."
  },
  "servers": [
    { "url": "http://localhost:3000/api/v1", "description": "Servidor local" }
  ],
  "paths": {}
}
```

**Por que começar por aqui?** É o mínimo que uma spec precisa para ser válida: versão do spec, informações sobre a API, e a base para incluir endpoints (`paths`).

### Passo 2 — Endpoint GET /filmes (listar)

Adicione ao `paths`:

```json
"/filmes": {
  "get": {
    "summary": "Lista todos os filmes",
    "description": "Retorna todos os filmes cadastrados.",
    "responses": {
      "200": { "description": "Lista de filmes retornada com sucesso" }
    }
  }
}
```

**Por que primeiro o GET?** Porque é o endpoint mais simples e sem corpo (body), ideal para entender a estrutura de `paths` e `responses`.

### Passo 3 — Path param: GET /filmes/{id}

Adicione:

```json
"/filmes/{id}": {
  "get": {
    "summary": "Busca filme por ID",
    "parameters": [
      { "name": "id", "in": "path", "required": true, "schema": { "type": "integer" }, "description": "ID do filme" }
    ],
    "responses": {
      "200": { "description": "Filme encontrado" },
      "404": { "description": "Filme não encontrado" }
    }
  }
}
```

**Por que depois do GET simples?** Introduz parâmetros de rota (path parameters) — um conceito importante antes de lidar com `requestBody`.

### Passo 4 — POST /filmes (requestBody)

Adicione o POST no `/filmes`:

```json
"post": {
  "summary": "Adiciona um novo filme",
  "requestBody": {
    "required": true,
    "content": {
      "application/json": {
        "schema": {
          "type": "object",
          "properties": {
            "titulo": { "type": "string" },
            "ano": { "type": "integer" },
            "duracao": { "type": "integer" },
            "generoId": { "type": "integer" }
          },
          "required": ["titulo", "ano", "generoId"]
        }
      }
    }
  },
  "responses": { "201": { "description": "Filme criado com sucesso" } }
}
```

**Por que aqui?** POST exige um corpo com um `schema`. É natural apresentá-lo depois que os alunos entenderem endpoints e parâmetros.

### Passo 5 — PUT e DELETE (alterar e remover)

No `paths` `/filmes/{id}` adicione PUT e DELETE. Explique que PUT também usa `requestBody` (semelhante ao POST) e DELETE normalmente não precisa de `requestBody`, apenas `id` na URL.

**Por que nessa ordem?** Primeiro CRUD (Read), depois Create, depois Update/Delete — segue a lógica de aprendizado: observar os dados, criar novo, modificar e apagar.

### Passo 6 — Busca por gênero (filtros)

Adicione `/filmes-genero/{genero}` com parâmetro `genero` (string) e explique quando usar filtros via path vs query params (ex.: `?genero=Fantasia` como alternativa usando query params).

---

## 6) `components.schemas` — modelos reutilizáveis

Explique que modelos (Filme, FilmeInput) devem ser definidos em `components.schemas` e referenciados nas `paths` com `$ref`.

**Exemplo:**

```json
"components": {
  "schemas": {
    "Filme": {
      "type": "object",
      "properties": {
        "id": { "type": "integer" },
        "titulo": { "type": "string" },
        "ano": { "type": "integer" },
        "duracao": { "type": "integer" },
        "generoId": { "type": "integer" }
      },
      "required": ["id", "titulo", "ano"]
    }
  }
}
```

**Por que usar `components`?** Evita repetição e mantém a documentação consistente. Se você mudar um campo do modelo, atualiza uma vez só.

---

## 7) Exemplo completo da spec (JSON)

Abaixo está o **arquivo JSON** completo (use `filmes.json`). Cole no Swagger Editor local (ou salve como `filmes.json` e aponte o `index.html` para ele).

```json
{
    "openapi": "3.0.0",
    "info": {
        "title": "API Filmes",
        "description": "Esta API permite gerenciar uma lista de filmes. \nÉ possível criar, listar, atualizar e deletar filmes, \nalém de buscar filmes por gênero. \nIdeal para fins educacionais e TCC, demonstrando CRUD completo e uso de filtros.\n",
        "version": "1.0.0"
    },
    "servers": [
        {
            "url": "http://localhost:3000/api/v1",
            "description": "Servidor local para testes"
        }
    ],
    "paths": {
        "/filmes": {
            "get": {
                "summary": "Lista todos os filmes cadastrados",
                "description": "Retorna uma lista completa de filmes armazenados no banco de dados, \nincluindo ID, título, ano, duração e ID do gênero. \nÚtil para exibir todos os filmes disponíveis.\n",
                "operationId": "filmesGET",
                "responses": {
                    "200": {
                        "description": "Lista de filmes retornada com sucesso",
                        "content": {
                            "application/json": {
                                "schema": {
                                    "type": "array",
                                    "items": {
                                        "$ref": "#/components/schemas/Filme"
                                    },
                                    "x-content-type": "application/json"
                                }
                            }
                        }
                    }
                },
                "x-swagger-router-controller": "Default"
            },
            "post": {
                "summary": "Adiciona um novo filme",
                "description": "Cria um novo registro de filme no banco de dados. \nTodos os campos obrigatórios devem ser fornecidos, \nincluindo título, ano de lançamento e gênero. \nA duração é opcional, mas recomendada.\n",
                "operationId": "filmesPOST",
                "requestBody": {
                    "content": {
                        "application/json": {
                            "schema": {
                                "$ref": "#/components/schemas/FilmeInput"
                            },
                            "examples": {
                                "exemplo": {
                                    "summary": "Exemplo de payload para criação",
                                    "value": {
                                        "titulo": "Formatura 3Semestre DS",
                                        "ano": 2025,
                                        "duracao": 120,
                                        "generoId": 5
                                    }
                                }
                            }
                        }
                    },
                    "required": true
                },
                "responses": {
                    "201": {
                        "description": "Filme criado com sucesso",
                        "content": {
                            "application/json": {
                                "schema": {
                                    "$ref": "#/components/schemas/Filme"
                                }
                            }
                        }
                    }
                },
                "x-swagger-router-controller": "Default"
            }
        },
        "/filmes/{id}": {
            "get": {
                "summary": "Busca um filme pelo ID",
                "description": "Recupera os detalhes de um filme específico usando seu ID. \nRetorna todas as informações do filme, como título, ano, duração e gênero.\n",
                "operationId": "filmesIdGET",
                "parameters": [
                    {
                        "name": "id",
                        "in": "path",
                        "description": "ID do filme que será consultado",
                        "required": true,
                        "style": "simple",
                        "explode": false,
                        "schema": {
                            "type": "integer"
                        }
                    }
                ],
                "responses": {
                    "200": {
                        "description": "Filme encontrado",
                        "content": {
                            "application/json": {
                                "schema": {
                                    "$ref": "#/components/schemas/Filme"
                                }
                            }
                        }
                    },
                    "404": {
                        "description": "Filme não encontrado no banco de dados"
                    }
                },
                "x-swagger-router-controller": "Default"
            },
            "put": {
                "summary": "Atualiza os dados de um filme",
                "description": "Atualiza as informações de um filme existente usando seu ID. \nÉ necessário fornecer ao menos os campos obrigatórios (título e ano). \nCampos opcionais, como duração, podem ser atualizados se desejado.\n",
                "operationId": "filmesIdPUT",
                "parameters": [
                    {
                        "name": "id",
                        "in": "path",
                        "description": "ID do filme a ser atualizado",
                        "required": true,
                        "style": "simple",
                        "explode": false,
                        "schema": {
                            "type": "integer"
                        }
                    }
                ],
                "requestBody": {
                    "content": {
                        "application/json": {
                            "schema": {
                                "$ref": "#/components/schemas/FilmeInput"
                            },
                            "examples": {
                                "exemplo": {
                                    "summary": "Exemplo de payload para atualização",
                                    "value": {
                                        "titulo": "Formatura 3Semestre DS ETEC",
                                        "ano": 2025,
                                        "duracao": 220,
                                        "generoId": 5
                                    }
                                }
                            }
                        }
                    },
                    "required": true
                },
                "responses": {
                    "200": {
                        "description": "Filme atualizado com sucesso",
                        "content": {
                            "application/json": {
                                "schema": {
                                    "$ref": "#/components/schemas/Filme"
                                }
                            }
                        }
                    },
                    "404": {
                        "description": "Filme não encontrado"
                    }
                },
                "x-swagger-router-controller": "Default"
            },
            "delete": {
                "summary": "Deleta um filme existente",
                "description": "Remove um filme do banco de dados utilizando seu ID. \nApós a exclusão, o filme não poderá mais ser recuperado.\n",
                "operationId": "filmesIdDELETE",
                "parameters": [
                    {
                        "name": "id",
                        "in": "path",
                        "description": "ID do filme a ser deletado",
                        "required": true,
                        "style": "simple",
                        "explode": false,
                        "schema": {
                            "type": "integer"
                        }
                    }
                ],
                "responses": {
                    "204": {
                        "description": "Filme deletado com sucesso"
                    },
                    "404": {
                        "description": "Filme não encontrado"
                    }
                },
                "x-swagger-router-controller": "Default"
            }
        },
        "/filmes-genero/{genero}": {
            "get": {
                "summary": "Busca filmes por gênero",
                "description": "Retorna uma lista de filmes que pertencem ao gênero informado. \nO parâmetro gênero deve ser passado como string (ex: Fantasia, Ação). \nÚtil para filtros e pesquisas de categoria.\n",
                "operationId": "filmes_generoGeneroGET",
                "parameters": [
                    {
                        "name": "genero",
                        "in": "path",
                        "description": "Nome do gênero para filtragem",
                        "required": true,
                        "style": "simple",
                        "explode": false,
                        "schema": {
                            "type": "string"
                        }
                    }
                ],
                "responses": {
                    "200": {
                        "description": "Lista de filmes do gênero informado",
                        "content": {
                            "application/json": {
                                "schema": {
                                    "type": "array",
                                    "items": {
                                        "$ref": "#/components/schemas/Filme"
                                    },
                                    "x-content-type": "application/json"
                                }
                            }
                        }
                    }
                },
                "x-swagger-router-controller": "Default"
            }
        }
    },
    "components": {
        "schemas": {
            "Filme": {
                "required": [
                    "ano",
                    "id",
                    "titulo"
                ],
                "type": "object",
                "properties": {
                    "id": {
                        "type": "integer",
                        "description": "ID único do filme"
                    },
                    "titulo": {
                        "type": "string",
                        "description": "Nome do filme"
                    },
                    "ano": {
                        "type": "integer",
                        "description": "Ano de lançamento"
                    },
                    "duracao": {
                        "type": "integer",
                        "description": "Duração em minutos"
                    },
                    "generoId": {
                        "type": "integer",
                        "description": "ID do gênero do filme"
                    }
                },
                "example": {
                    "ano": 6,
                    "generoId": 5,
                    "titulo": "titulo",
                    "id": 0,
                    "duracao": 1
                }
            },
            "FilmeInput": {
                "required": [
                    "ano",
                    "titulo"
                ],
                "type": "object",
                "properties": {
                    "titulo": {
                        "type": "string",
                        "description": "Nome do filme"
                    },
                    "ano": {
                        "type": "integer",
                        "description": "Ano de lançamento"
                    },
                    "duracao": {
                        "type": "integer",
                        "description": "Duração em minutos"
                    },
                    "generoId": {
                        "type": "integer",
                        "description": "ID do gênero do filme"
                    }
                }
            }
        }
    }
}
```

---

## 8) Como abrir no Swagger Editor offline (passo rápido)

1. Baixe o Swagger Editor (repositório oficial). 2. Coloque `index.html` do editor na mesma pasta que `filmes.json` (ou modifique `index.html` para apontar para `filmes.json`). 3. Abra `index.html` com Live Server no VS Code ou em um servidor local (recomendado) para evitar problemas de CORS. 4. A documentação aparecerá no editor e você pode navegar e testar.

---

## 9) Atividade prática sugerida (cronograma da aula)

* **0–10 min**: Introdução teórica (O que é API, por que documentar). Mostre o DER.
* **10–25 min**: Digitar o esqueleto (`openapi`, `info`, `servers`, `paths`).
* **25–45 min**: Implementar GET /filmes e GET /filmes/{id} e testar no Swagger Editor.
* **45–65 min**: Implementar POST /filmes com `requestBody` e `components.schemas`.
* **65–80 min**: PUT e DELETE; busca por gênero; exemplos práticos de requests.
* **80–90 min**: Perguntas, revisão, salvar arquivos para entrega do TCC.

---

## 10) Entregáveis para o TCC / avaliação

* `filmes.json` (ou `filmes.yaml`) com a documentação completa.
* Print(s) do Swagger UI/Editor mostrando endpoints e exemplos.
* DER (imagem ERD.svg).
* Scripts SQL com DDL (criação de tabelas) e DML (inserts com exemplos).
* Readme com orientações (este arquivo).

---

## 11) Boas práticas e dicas finais

* Use **status codes** adequados (200, 201, 204, 404, 400, 500).
* Documente **erros** também (ex.: 400 quando payload inválido).
* Reutilize `schemas` em `components` e use `$ref`.
* Insira **exemplos** (em `requestBody` e `responses`) — facilitam a apresentação.
* Mantenha o `servers` apontando para o ambiente certo (local para testes; produção em outro arquivo/env).

---

Se quiser, eu já deixei pronto o `filmes.json` (acima) — copie o bloco `JSON` para um arquivo `filmes.json` na pasta do projeto e abra no Swagger Editor offline.

> Precisa que eu gere um ZIP com a pasta `API-Filmes-Docs` (contendo `index.html` do Swagger Editor já configurado + `filmes.json` + `ERD.svg`) para você baixar e distribuir aos alunos? Se quiser, eu monto isso agora.
