# API de Usuários

API REST para cadastro, autenticação e gerenciamento de usuários, endereços e telefones. O projeto foi desenvolvido com Java e Spring Boot, aplicando separação em camadas, persistência com PostgreSQL e autenticação stateless com JWT.

## Sobre o projeto

Esta API simula um serviço de usuários com fluxo completo de cadastro e login. Após autenticar, o usuário recebe um token JWT e pode atualizar seus dados, cadastrar endereços e cadastrar telefones.

O projeto demonstra conhecimentos importantes para desenvolvimento backend:

- Criação de APIs REST com Spring Boot
- Autenticação e autorização com Spring Security
- Geração e validação de tokens JWT
- Criptografia de senhas com BCrypt
- Persistência de dados com Spring Data JPA
- Relacionamento entre entidades usando JPA
- Organização em camadas: controller, service, DTO, converter, repository e security
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

- **Controller**: expõe os endpoints REST da aplicação.
- **Service**: concentra as regras de negócio.
- **DTOs**: transportam dados entre API e camada de negócio.
- **Converter**: converte DTOs em entidades e entidades em DTOs.
- **Repository**: realiza operações de persistência com Spring Data JPA.
- **Security**: configura autenticação, filtro JWT e criptografia de senha.

## Funcionalidades

- Cadastro de usuário
- Login com geração de token JWT
- Busca de usuário por e-mail
- Atualização de dados do usuário autenticado
- Exclusão de usuário por e-mail
- Cadastro e atualização de endereço
- Cadastro e atualização de telefone
- Proteção de rotas com Spring Security

## Modelo de dados

Um usuário possui:

- Nome
- E-mail
- Senha criptografada
- Lista de endereços
- Lista de telefones

Relacionamentos:

- `Usuario` 1:N `Endereco`
- `Usuario` 1:N `Telefone`

## Endpoints

Base URL local:

```text
http://localhost:8081
```

| Método | Endpoint | Autenticação | Descrição |
| --- | --- | --- | --- |
| `POST` | `/usuario` | Não | Cadastra um novo usuário |
| `POST` | `/usuario/login` | Não | Autentica usuário e retorna token JWT |
| `GET` | `/usuario?email={email}` | Sim | Busca usuário por e-mail |
| `PUT` | `/usuario` | Sim | Atualiza dados do usuário autenticado |
| `DELETE` | `/usuario/{email}` | Sim | Remove usuário por e-mail |
| `POST` | `/usuario/endereco` | Sim | Cadastra endereço para o usuário autenticado |
| `PUT` | `/usuario/endereco?id={id}` | Sim | Atualiza endereço pelo ID |
| `POST` | `/usuario/telefone` | Sim | Cadastra telefone para o usuário autenticado |
| `PUT` | `/usuario/telefone?id={id}` | Sim | Atualiza telefone pelo ID |

## Exemplos de uso

### Cadastro de usuário

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
      "cidade": "São Paulo",
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

### Requisição autenticada

Para acessar rotas protegidas, envie o token no header:

```http
Authorization: Bearer seu-token-jwt
```

### Atualizar dados do usuário

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

### Cadastrar endereço

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
  "cidade": "São Paulo",
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

### Pré-requisitos

- Java 21
- PostgreSQL
- Gradle ou Gradle Wrapper

### 1. Clone o repositório

```bash
git clone https://github.com/Danielchini05/usuario.git
cd usuario
```

### 2. Crie o banco de dados

No PostgreSQL, crie o banco:

```sql
CREATE DATABASE db_usuario;
```

### 3. Configure a conexão

O arquivo de configuração fica em:

```text
src/main/resources/application.properties
```

Configuração esperada:

```properties
spring.datasource.url=jdbc:postgresql://localhost:5432/db_usuario
spring.datasource.username=postgres
spring.datasource.password=postgres123
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.PostgreSQLDialect
server.port=8081
```

Ajuste usuário e senha conforme a configuração local do seu PostgreSQL.

### 4. Execute a aplicação

No Windows:

```bash
gradlew.bat bootRun
```

No Linux/macOS:

```bash
./gradlew bootRun
```

A API ficará disponível em:

```text
http://localhost:8081
```

## Segurança

O projeto utiliza Spring Security com JWT:

- As senhas são criptografadas com BCrypt antes de serem persistidas.
- O endpoint de login gera um token JWT válido por 1 hora.
- Rotas protegidas exigem o header `Authorization`.
- A API utiliza sessão stateless, adequada para serviços REST.

## Destaques técnicos

- Implementação de `UserDetails` na entidade `Usuario`
- Filtro customizado `JwtRequestFilter` para validar tokens em cada requisição
- Uso de `AuthenticationManager` para autenticar credenciais no login
- Separação entre entidade de banco e DTO de entrada/saída
- Atualizações parciais preservando valores existentes quando campos não são enviados
- Persistência em cascata para listas de endereços e telefones

## Melhorias futuras

- Adicionar validações com Bean Validation
- Criar tratamento global de exceções com `@ControllerAdvice`
- Adicionar testes unitários e de integração
- Documentar a API com Swagger/OpenAPI
- Externalizar a chave secreta JWT para variável de ambiente
- Criar ambiente Docker com PostgreSQL

## Autor

Desenvolvido por [Daniel Chini](https://github.com/Danielchini05).
