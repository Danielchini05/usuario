# API de Usuarios

API REST para cadastro, autenticacao e gerenciamento de usuarios, enderecos e telefones. O projeto foi desenvolvido com Java e Spring Boot, aplicando separacao em camadas, persistencia com PostgreSQL e autenticacao stateless com JWT.

## Sobre o projeto

Esta API simula um servico de usuarios com fluxo completo de cadastro e login. Apos autenticar, o usuario recebe um token JWT e pode atualizar seus dados, cadastrar enderecos e cadastrar telefones.

O projeto demonstra conhecimentos importantes para desenvolvimento backend:

- Criacao de APIs REST com Spring Boot
- Autenticacao e autorizacao com Spring Security
- Geracao e validacao de tokens JWT
- Criptografia de senhas com BCrypt
- Persistencia de dados com Spring Data JPA
- Relacionamento entre entidades usando JPA
- Organizacao em camadas: controller, service, DTO, converter, repository e security
- Banco de dados PostgreSQL

## Tecnologias utilizadas

- Java 21
- Spring Boot
- Spring Web MVC
- Spring Security
- Spring Data JPA
- JWT com JJWT
- PostgreSQL
- Lombok
- Gradle

## Arquitetura

```text
src/main/java/com/javanauta/usuario
|-- business
|   |-- converter
|   |-- dto
|   `-- UsuarioService.java
|-- controller
|   `-- UsuarioController.java
`-- infrastructure
    |-- entity
    |-- exceptions
    |-- repository
    `-- security
```

### Principais responsabilidades

- **Controller**: expoe os endpoints REST da aplicacao.
- **Service**: concentra as regras de negocio.
- **DTOs**: transportam dados entre API e camada de negocio.
- **Converter**: converte DTOs em entidades e entidades em DTOs.
- **Repository**: realiza operacoes de persistencia com Spring Data JPA.
- **Security**: configura autenticacao, filtro JWT e criptografia de senha.

## Funcionalidades

- Cadastro de usuario
- Login com geracao de token JWT
- Busca de usuario por e-mail
- Atualizacao de dados do usuario autenticado
- Exclusao de usuario por e-mail
- Cadastro e atualizacao de endereco
- Cadastro e atualizacao de telefone
- Protecao de rotas com Spring Security

## Modelo de dados

Um usuario possui:

- Nome
- E-mail
- Senha criptografada
- Lista de enderecos
- Lista de telefones

Relacionamentos:

- `Usuario` 1:N `Endereco`
- `Usuario` 1:N `Telefone`

## Endpoints

Base URL local:

```text
http://localhost:8081
```

| Metodo | Endpoint | Autenticacao | Descricao |
| --- | --- | --- | --- |
| `POST` | `/usuario` | Nao | Cadastra um novo usuario |
| `POST` | `/usuario/login` | Nao | Autentica usuario e retorna token JWT |
| `GET` | `/usuario?email={email}` | Sim | Busca usuario por e-mail |
| `PUT` | `/usuario` | Sim | Atualiza dados do usuario autenticado |
| `DELETE` | `/usuario/{email}` | Sim | Remove usuario por e-mail |
| `POST` | `/usuario/endereco` | Sim | Cadastra endereco para o usuario autenticado |
| `PUT` | `/usuario/endereco?id={id}` | Sim | Atualiza endereco pelo ID |
| `POST` | `/usuario/telefone` | Sim | Cadastra telefone para o usuario autenticado |
| `PUT` | `/usuario/telefone?id={id}` | Sim | Atualiza telefone pelo ID |

## Exemplos de uso

### Cadastro de usuario

```http
POST /usuario
Content-Type: application/json
```

```json
{
  "nome": "Daniel Chini",
  "email": "daniel@email.com",
  "senha": "123456",
  "enderecos": [
    {
      "rua": "Rua das Flores",
      "numero": 100,
      "complemento": "Apto 10",
      "cidade": "Sao Paulo",
      "estado": "SP",
      "cep": "01000-000"
    }
  ],
  "telefones": [
    {
      "ddd": "11",
      "numero": "999999999"
    }
  ]
}
```

### Login

```http
POST /usuario/login
Content-Type: application/json
```

```json
{
  "email": "daniel@email.com",
  "senha": "123456"
}
```

Resposta:

```text
Bearer eyJhbGciOiJIUzI1NiJ9...
```

### Requisicao autenticada

Para acessar rotas protegidas, envie o token no header:

```http
Authorization: Bearer seu-token-jwt
```

### Atualizar dados do usuario

```http
PUT /usuario
Authorization: Bearer seu-token-jwt
Content-Type: application/json
```

```json
{
  "nome": "Daniel Chini Atualizado",
  "senha": "novaSenha123"
}
```

### Cadastrar endereco

```http
POST /usuario/endereco
Authorization: Bearer seu-token-jwt
Content-Type: application/json
```

```json
{
  "rua": "Avenida Paulista",
  "numero": 1000,
  "complemento": "Conj 101",
  "cidade": "Sao Paulo",
  "estado": "SP",
  "cep": "01310-100"
}
```

### Cadastrar telefone

```http
POST /usuario/telefone
Authorization: Bearer seu-token-jwt
Content-Type: application/json
```

```json
{
  "ddd": "11",
  "numero": "988888888"
}
```

## Como executar o projeto

### Pre-requisitos

- Java 21
- PostgreSQL
- Gradle ou Gradle Wrapper

### 1. Clone o repositorio

```bash
git clone https://github.com/Danielchini05/usuario.git
cd usuario
```

### 2. Crie o banco de dados

No PostgreSQL, crie o banco:

```sql
CREATE DATABASE db_usuario;
```

### 3. Configure a conexao

O arquivo de configuracao fica em:

```text
src/main/resources/application.properties
```

Configuracao esperada:

```properties
spring.datasource.url=jdbc:postgresql://localhost:5432/db_usuario
spring.datasource.username=postgres
spring.datasource.password=postgres123
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.PostgreSQLDialect
server.port=8081
```

Ajuste usuario e senha conforme a configuracao local do seu PostgreSQL.

### 4. Execute a aplicacao

No Windows:

```bash
gradlew.bat bootRun
```

No Linux/macOS:

```bash
./gradlew bootRun
```

A API ficara disponivel em:

```text
http://localhost:8081
```

## Seguranca

O projeto utiliza Spring Security com JWT:

- As senhas sao criptografadas com BCrypt antes de serem persistidas.
- O endpoint de login gera um token JWT valido por 1 hora.
- Rotas protegidas exigem o header `Authorization`.
- A API utiliza sessao stateless, adequada para servicos REST.

## Destaques tecnicos

- Implementacao de `UserDetails` na entidade `Usuario`
- Filtro customizado `JwtRequestFilter` para validar tokens em cada requisicao
- Uso de `AuthenticationManager` para autenticar credenciais no login
- Separacao entre entidade de banco e DTO de entrada/saida
- Atualizacoes parciais preservando valores existentes quando campos nao sao enviados
- Persistencia em cascata para listas de enderecos e telefones

## Melhorias futuras

- Adicionar validacoes com Bean Validation
- Criar tratamento global de excecoes com `@ControllerAdvice`
- Adicionar testes unitarios e de integracao
- Documentar a API com Swagger/OpenAPI
- Externalizar a chave secreta JWT para variavel de ambiente
- Criar ambiente Docker com PostgreSQL

## Autor

Desenvolvido por [Daniel Chini](https://github.com/Danielchini05).
