# 3.3 Documentação da API

A documentação da API é um recurso fundamental para a compreensão e utilização dos serviços disponibilizados pelo sistema. Por meio dela, é possível visualizar os **endpoints** disponíveis, os métodos de acesso (GET, POST, PUT, DELETE), os parâmetros aceitos, os modelos de dados de entrada e saída, bem como os códigos de resposta retornados pelo servidor.  

Para este projeto, foi utilizada a ferramenta **Swagger UI**, que permite a visualização da documentação de forma interativa, além de possibilitar a execução de requisições de teste diretamente na interface.  

Conforme ilustrado na **Figura 1**, a interface do Swagger UI apresenta todos os endpoints da aplicação, organizados por recurso.  

---

## Figura 1 – Interface do Swagger UI com os endpoints da API de Filmes
*(inserir print da tela inicial do Swagger UI com a lista de endpoints)*  

---

Na **Figura 2**, observa-se um endpoint expandido (`GET /filmes`), que apresenta os parâmetros de entrada, o modelo de resposta e os códigos HTTP que podem ser retornados.  

---

## Figura 2 – Detalhamento do endpoint GET /filmes no Swagger UI
*(inserir print mostrando o endpoint expandido e um exemplo de resposta JSON)*  

---

A **Figura 3** demonstra o funcionamento de uma requisição de cadastro de filme, utilizando o método `POST /filmes`. É possível verificar o corpo da requisição (em formato JSON), contendo os dados a serem cadastrados, e o exemplo da resposta de sucesso.  

---

## Figura 3 – Cadastro de filme via Swagger UI (POST /filmes)
*(inserir print com o corpo da requisição e resposta 201 – Created)*  

---

Além dos endpoints, o Swagger UI permite a visualização dos **schemas** que definem a estrutura dos objetos utilizados. Na **Figura 4**, tem-se a representação do objeto `Filme`, que corresponde diretamente à tabela do banco de dados já descrita no dicionário de dados.  

---

## Figura 4 – Estrutura do schema Filme no Swagger UI
*(inserir print da aba de schemas mostrando a definição de Filme)*  

---

## 3.3.1 Tabela de Endpoints da API

A Tabela 1 resume os principais endpoints implementados, destacando método, rota, descrição e tipo de resposta.  

| Método | Endpoint        | Descrição                        | Resposta esperada |
|--------|-----------------|----------------------------------|------------------|
| GET    | `/filmes`       | Retorna todos os filmes          | 200 (OK)         |
| GET    | `/filmes/{id}`  | Retorna um filme específico      | 200 (OK), 404    |
| POST   | `/filmes`       | Cadastra um novo filme           | 201 (Created)    |
| PUT    | `/filmes/{id}`  | Atualiza dados de um filme       | 200 (OK), 404    |
| DELETE | `/filmes/{id}`  | Remove um filme do sistema       | 204 (No Content) |

**Tabela 1 – Endpoints da API de Filmes documentados no Swagger UI**

---

## 3.3.2 Integração com os Artefatos do Projeto

A documentação da API está diretamente relacionada aos demais artefatos desenvolvidos no projeto:  

- O **DER** serviu como base conceitual para identificar as entidades que originaram os recursos da API.  
- O **dicionário de dados** forneceu os atributos necessários para a definição dos schemas.  
- Os comandos **DDL/DML** serviram de referência para a implementação das operações CRUD representadas nos endpoints.  

Dessa forma, a documentação cumpre um papel essencial de **integração e padronização**, funcionando como contrato entre o backend e os demais sistemas que irão consumir os serviços implementados.
