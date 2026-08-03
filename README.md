# ✍️ API de Autores e Postagens com Flask

API REST desenvolvida em Python para gerenciar autores e postagens de um blog, utilizando autenticação por token JWT e banco de dados SQLite.

## 📋 Sobre o projeto

Este projeto consiste em uma API criada com Flask para realizar o gerenciamento de autores e postagens.

A aplicação permite cadastrar, consultar, alterar e excluir autores, além de cadastrar, consultar, alterar e excluir postagens associadas a esses autores.

O acesso às rotas principais é protegido por autenticação JWT. Para utilizar os recursos da API, o usuário realiza o login com nome e senha e recebe um token temporário, válido por 30 minutos.

Os dados são armazenados em um banco SQLite chamado `blog.db`, criado e manipulado com o Flask-SQLAlchemy.

O projeto foi dividido em dois arquivos principais:

- `Fals.py`: contém as rotas, autenticação e regras da API;
- `estrutura_banco_de_dados.py`: contém a configuração do Flask, banco de dados e modelos.

## 🎯 Objetivo

O projeto tem como objetivo demonstrar a criação de uma API REST completa utilizando Python e Flask.

A aplicação reúne conceitos importantes do desenvolvimento back-end, como:

- Criação de rotas HTTP;
- Operações CRUD;
- Autenticação por token JWT;
- Proteção de endpoints;
- Integração com banco de dados;
- Relacionamento entre tabelas;
- Recebimento e envio de dados em JSON;
- Validação de usuários;
- Uso de códigos de resposta HTTP;
- Organização da aplicação em arquivos separados.

## ✨ Funcionalidades

- Login por autenticação Basic;
- Geração de token JWT;
- Token com validade de 30 minutos;
- Proteção das rotas com decorator;
- Consulta de todos os autores;
- Consulta de um autor por ID;
- Cadastro de novos autores;
- Alteração de autores;
- Exclusão de autores;
- Consulta de todas as postagens;
- Cadastro de novas postagens;
- Alteração de postagens;
- Exclusão de postagens;
- Relacionamento entre autores e postagens;
- Armazenamento dos dados em SQLite;
- Criação automática das tabelas;
- Criação automática de um usuário administrador inicial;
- Respostas em formato JSON.

## 🛠️ Tecnologias utilizadas

- Python
- Flask
- Flask-SQLAlchemy
- SQLite
- PyJWT
- JSON
- JWT
- SQLAlchemy

## 🧱 Estrutura do banco de dados

O projeto possui duas tabelas principais:

### 👤 Tabela `autor`

Responsável por armazenar os usuários da aplicação.

| Campo | Tipo | Descrição |
|---|---|---|
| `id_autor` | Integer | Identificador único do autor |
| `nome` | String | Nome utilizado pelo autor |
| `email` | String | Endereço de e-mail |
| `senha` | String | Senha de acesso |
| `admin` | Boolean | Indica se o autor possui privilégios administrativos |

### 📝 Tabela `postagens`

Responsável por armazenar as postagens do blog.

| Campo | Tipo | Descrição |
|---|---|---|
| `id_postagem` | Integer | Identificador único da postagem |
| `titulo` | String | Título da postagem |
| `id_autor` | Integer | Identificador do autor responsável |

## 🔗 Relacionamento entre as tabelas

Cada postagem possui um autor associado por meio do campo:

```text
id_autor
```

Esse campo funciona como chave estrangeira e aponta para:

```text
autor.id_autor
```

O relacionamento é definido no modelo `Autor` por:

```python
postagens = db.relationship(Postagem)
```

Dessa forma, um autor pode possuir várias postagens.

A relação pode ser representada como:

```text
Autor
  │
  └── possui uma ou várias postagens
```

## 🔐 Autenticação da API

A API utiliza dois mecanismos de autenticação.

### 1. Autenticação Basic

A rota `/login` recebe o nome e a senha do usuário por autenticação Basic.

Exemplo:

```text
Usuário: Fabio
Senha: 12345
```

Quando os dados estão corretos, a API gera um token JWT.

### 2. Token JWT

O token contém o identificador do autor autenticado:

```json
{
  "id_autor": 1
}
```

Também possui uma data de expiração de 30 minutos.

O token deve ser enviado nas requisições protegidas por meio do cabeçalho:

```text
x-acess-token
```

Exemplo:

```text
x-acess-token: TOKEN_GERADO_NO_LOGIN
```

Caso o token não seja enviado, a API retorna uma mensagem informando que ele não foi incluído.

Caso o token seja inválido ou tenha expirado, a requisição é recusada.

## 🛡️ Decorator de proteção

O decorator:

```python
@token_obrigatorio
```

é responsável por proteger as rotas da aplicação.

Antes de executar uma função protegida, ele:

1. Procura o token no cabeçalho da requisição;
2. Verifica se o token foi enviado;
3. Decodifica o token utilizando a chave secreta;
4. Obtém o identificador do autor;
5. Procura o autor no banco de dados;
6. Autoriza a execução da rota;
7. Rejeita a requisição quando o token é inválido.

## 🌐 Rotas da API

### 🔑 Login

```http
GET /login
```

Realiza a autenticação do usuário e retorna um token JWT.

A autenticação deve ser enviada no formato Basic Auth.

Exemplo de resposta:

```json
{
  "token": "TOKEN_JWT"
}
```

## 👥 Rotas de autores

### Consultar todos os autores

```http
GET /autores
```

Retorna uma lista com os autores cadastrados.

Exemplo:

```json
{
  "autores": [
    {
      "id_autor": 1,
      "nome": "Fabio"
    }
  ]
}
```

### Consultar autor por ID

```http
GET /autores/1
```

Retorna os dados de um autor específico.

Exemplo:

```json
{
  "autor": {
    "id_autor": 1,
    "nome": "Fabio",
    "email": "fabio@gmail.com"
  }
}
```

### Cadastrar autor

```http
POST /autores
```

Corpo da requisição:

```json
{
  "nome": "Novo autor",
  "email": "autor@email.com",
  "senha": "12345"
}
```

Exemplo de resposta:

```json
{
  "mensagem": "Usuário criado com sucesso"
}
```

### Alterar autor

```http
PUT /autores/1
```

O corpo pode conter os campos que serão alterados:

```json
{
  "nome": "Nome atualizado",
  "email": "novo@email.com",
  "senha": "nova-senha"
}
```

Exemplo de resposta:

```json
{
  "Mensagem": "Usuário alterado com sucesso!"
}
```

### Excluir autor

```http
DELETE /autores/1
```

Exemplo de resposta:

```json
{
  "Mensagem": "Autor excluído com sucesso!"
}
```

## 📰 Rotas de postagens

### Consultar todas as postagens

```http
GET /postagens
```

Retorna todas as postagens cadastradas.

Exemplo:

```json
{
  "postagens": [
    {
      "titulo": "Minha primeira postagem",
      "id_autor": 1
    }
  ]
}
```

### Criar postagem

```http
POST /postagens
```

Corpo da requisição:

```json
{
  "titulo": "Minha primeira postagem",
  "id_autor": 1
}
```

Exemplo de resposta:

```json
{
  "mensagem": "Postagem criada com sucesso"
}
```

### Alterar postagem

```http
PUT /postagens/1
```

Corpo da requisição:

```json
{
  "titulo": "Título atualizado",
  "id_autor": 1
}
```

Exemplo de resposta:

```json
{
  "mensagem": "Postagem alterada com sucessso"
}
```

### Excluir postagem

```http
DELETE /postagens/1
```

Exemplo de resposta:

```json
{
  "mensagem": "Postagem excluída com sucesso!"
}
```

## 📁 Estrutura do projeto

```text
api-autores-postagens/
├── Fals.py
├── estrutura_banco_de_dados.py
├── instance/
│   └── blog.db
├── README.md
├── requirements.txt
├── .gitignore
└── LICENSE
```

### Arquivos do projeto

- `Fals.py`: contém as rotas, autenticação, operações CRUD e inicialização do servidor;
- `estrutura_banco_de_dados.py`: contém a configuração do Flask, modelos e inicialização do banco;
- `blog.db`: banco de dados SQLite gerado pela aplicação;
- `README.md`: documentação do projeto;
- `requirements.txt`: bibliotecas externas necessárias;
- `.gitignore`: define arquivos e pastas ignorados pelo Git;
- `LICENSE`: apresenta os termos de utilização do projeto.

## ⚙️ Pré-requisitos

Antes de executar o projeto, tenha instalado:

- Python;
- Git;
- Visual Studio Code ou outro editor;
- Um programa para testar APIs, como Postman ou Insomnia;
- As bibliotecas presentes no `requirements.txt`.

## 🚀 Instalação

Clone o repositório:

```bash
git clone https://github.com/SEU-USUARIO/api-autores-postagens.git
```

Entre na pasta do projeto:

```bash
cd api-autores-postagens
```

Crie um ambiente virtual:

```bash
python -m venv .venv
```

Ative o ambiente virtual no Windows:

```bash
.venv\Scripts\activate
```

Instale as dependências:

```bash
python -m pip install -r requirements.txt
```

## 📦 Arquivo `requirements.txt`

O arquivo deve conter:

```text
Flask
Flask-SQLAlchemy
PyJWT
```

As bibliotecas abaixo não precisam ser adicionadas ao `requirements.txt`, pois fazem parte do próprio Python:

```text
json
datetime
functools
```

## ▶️ Como executar

Execute o arquivo principal:

```bash
python Fals.py
```

A API será iniciada em:

```text
http://localhost:5000
```

O modo de depuração estará ativado por meio de:

```python
debug=True
```

Para testar o login:

```http
GET http://localhost:5000/login
```

Utilize autenticação Basic com os dados iniciais:

```text
Usuário: Fabio
Senha: 12345
```

Depois de receber o token, envie-o nas demais requisições utilizando o cabeçalho:

```text
x-acess-token
```

## 🔄 Fluxo da aplicação

1. O arquivo `Fals.py` importa a aplicação e os modelos;
2. O arquivo `estrutura_banco_de_dados.py` configura o Flask;
3. O SQLAlchemy é conectado ao banco SQLite;
4. As tabelas `autor` e `postagens` são criadas;
5. Um autor administrador inicial é cadastrado;
6. O servidor Flask é iniciado;
7. O usuário envia nome e senha para `/login`;
8. A aplicação consulta o autor no banco;
9. Um token JWT com duração de 30 minutos é criado;
10. O token é enviado ao usuário;
11. O usuário inclui o token no cabeçalho das próximas requisições;
12. O decorator valida o token;
13. A rota solicitada executa a operação no banco;
14. A API devolve uma resposta em formato JSON.

## ⚠️ Limitações atuais

- O banco de dados é apagado e recriado sempre que a aplicação é iniciada;
- Todos os dados cadastrados anteriormente são perdidos durante uma nova inicialização;
- Um usuário padrão é criado automaticamente no banco;
- As senhas são armazenadas diretamente no banco sem hash;
- A chave secreta está escrita diretamente no código;
- O servidor utiliza o modo `debug`;
- A aplicação não valida se o e-mail já está cadastrado;
- A aplicação não valida se o nome de usuário já existe;
- A aplicação não valida todos os campos enviados nas requisições;
- Alguns blocos utilizam `except` sem especificar o tipo do erro;
- Algumas respostas podem não aplicar o status HTTP da maneira pretendida;
- Não existe paginação na consulta de autores e postagens;
- Não existe rota para consultar uma postagem específica por ID;
- Não existe controle de permissões baseado no campo `admin`;
- Qualquer usuário autenticado pode acessar as rotas protegidas;
- A exclusão de um autor com postagens relacionadas pode exigir tratamento adicional;
- Não existem testes automatizados;
- O projeto ainda não possui documentação interativa com Swagger;
- A aplicação foi estruturada para estudo e execução local.

## 👨‍💻 Autor

Desenvolvido por **Fabio** como projeto de estudo em Python, Flask, criação de APIs REST, autenticação JWT, banco de dados e operações CRUD.

## 📄 Licença

Copyright © 2026 Fabio. Todos os direitos reservados.

Este projeto possui uma licença proprietária e está disponível publicamente apenas para fins de estudo, demonstração técnica e apresentação de portfólio.

Não é permitida a reprodução, modificação, distribuição, comercialização, sublicenciamento ou utilização total ou parcial deste projeto sem autorização prévia e expressa do autor.

A disponibilização pública deste repositório não concede automaticamente permissão para copiar, modificar, distribuir ou utilizar comercialmente o código.

Consulte o arquivo [`LICENSE`](LICENSE) para conhecer os termos completos.
