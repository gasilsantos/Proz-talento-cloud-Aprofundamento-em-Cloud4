# Proz-talento-cloud-Aprofundamento-em-Cloud4
Projeto: Sistema de Cadastro e Login
# Projeto: Sistema de Cadastro e Login

## Objetivo

O objetivo deste projeto é projetar e implementar um sistema de cadastro e login robusto e completo, utilizando tecnologias adequadas para garantir segurança, escalabilidade e facilidade de uso. O sistema deve permitir o cadastro de novos usuários e a autenticação através de login, garantindo a proteção dos dados e a integridade das informações.

---

## Entregáveis

### 1. Documentação do Projeto

#### 1.1 Diagrama de Banco de Dados

O banco de dados será estruturado para armazenar as informações dos usuários de maneira segura. A tabela principal será a de **Usuários**, que armazenará os dados essenciais, como nome, e-mail, senha (criptografada) e data de criação da conta. Além disso, será utilizado um sistema de logs para registrar tentativas de login.

**Tabela Usuários**
| Campo            | Tipo          | Descrição                                       |
|------------------|---------------|-------------------------------------------------|
| id               | INT (PK)      | Identificador único do usuário                  |
| nome             | VARCHAR       | Nome completo do usuário                        |
| email            | VARCHAR       | E-mail do usuário (único)                       |
| senha            | VARCHAR       | Senha criptografada                             |
| data_criacao     | DATETIME      | Data e hora de criação da conta                 |

**Tabela Logs de Acesso**
| Campo            | Tipo          | Descrição                                       |
|------------------|---------------|-------------------------------------------------|
| id               | INT (PK)      | Identificador único do log                      |
| id_usuario       | INT (FK)      | Referência ao usuário                           |
| data_acesso      | DATETIME      | Data e hora da tentativa de login               |
| sucesso          | BOOLEAN       | Se a tentativa de login foi bem-sucedida        |

#### 1.2 Diagrama de Classes

**Classe Usuário**
- **Atributos**: `id`, `nome`, `email`, `senha`
- **Métodos**:
  - `cadastrar(nome, email, senha)`: Realiza o cadastro de um novo usuário.
  - `logar(email, senha)`: Verifica as credenciais e realiza o login do usuário.
  - `validarSenha(senha)`: Valida o formato da senha (mínimo de 8 caracteres, uma letra maiúscula, um número, etc.).
  - `criptografarSenha(senha)`: Criptografa a senha do usuário para armazenamento seguro.

**Classe Login**
- **Atributos**: `usuario`, `email`, `senha`
- **Métodos**:
  - `realizarLogin(email, senha)`: Realiza a tentativa de login e registra no log.
  - `gerarToken(usuario)`: Gera um token de autenticação para sessões.

#### 1.3 Manual do Usuário

O manual descreve os passos para utilizar o sistema de cadastro e login:

1. **Cadastro de Novo Usuário**:
   - Acesse a página de cadastro.
   - Preencha os campos de **Nome**, **E-mail** e **Senha** (deve atender aos critérios de segurança: pelo menos 8 caracteres, uma letra maiúscula, um número).
   - Clique em **Cadastrar**.
   - Se os dados estiverem corretos, o sistema exibirá uma mensagem de sucesso. Caso contrário, você receberá uma mensagem de erro.

2. **Login no Sistema**:
   - Acesse a página de login.
   - Preencha seu **E-mail** e **Senha**.
   - Clique em **Entrar**.
   - Se os dados estiverem corretos, você será autenticado e redirecionado para a área restrita. Caso contrário, uma mensagem de erro será exibida.

---

### 2. Implementação do Sistema

#### 2.1 Código-fonte do Sistema de Cadastro e Login

O código do sistema de cadastro e login será implementado utilizando as seguintes tecnologias:

- **Frontend**: HTML, CSS e JavaScript.
- **Backend**: Node.js com Express.js para criar as rotas de cadastro e login.
- **Banco de Dados**: MySQL ou PostgreSQL para armazenar as informações dos usuários.
- **Segurança**: Criptografia de senhas utilizando **bcrypt** e tokens JWT (JSON Web Tokens) para autenticação de sessões.

**Exemplo de Código (Backend)**:

```javascript
const express = require('express');
const bcrypt = require('bcrypt');
const jwt = require('jsonwebtoken');
const db = require('./db'); // Conexão com o banco de dados

const app = express();
app.use(express.json());

// Cadastro de novo usuário
app.post('/register', async (req, res) => {
    const { nome, email, senha } = req.body;
    
    // Verifica se o usuário já existe
    const userExist = await db.query('SELECT * FROM usuarios WHERE email = ?', [email]);
    if (userExist.length) return res.status(400).send('Usuário já registrado.');

    // Criptografa a senha
    const salt = await bcrypt.genSalt(10);
    const hashedPassword = await bcrypt.hash(senha, salt);

    // Insere novo usuário no banco
    await db.query('INSERT INTO usuarios (nome, email, senha) VALUES (?, ?, ?)', [nome, email, hashedPassword]);

    res.status(201).send('Usuário registrado com sucesso.');
});

// Login do usuário
app.post('/login', async (req, res) => {
    const { email, senha } = req.body;
    const user = await db.query('SELECT * FROM usuarios WHERE email = ?', [email]);

    if (!user.length) return res.status(400).send('Usuário não encontrado.');

    // Verifica a senha
    const validPassword = await bcrypt.compare(senha, user[0].senha);
    if (!validPassword) return res.status(400).send('Senha incorreta.');

    // Gera um token JWT
    const token = jwt.sign({ id: user[0].id }, process.env.JWT_SECRET, { expiresIn: '1h' });
    res.header('auth-token', token).send('Login bem-sucedido.');
});

app.listen(3000, () => {
    console.log('Servidor rodando na porta 3000');
});
```

#### 2.2 Banco de Dados

O banco de dados será criado com base no diagrama apresentado, com as tabelas **Usuários** e **Logs de Acesso**. A senha dos usuários será armazenada de forma criptografada, garantindo que ela não seja exposta em caso de vazamento de dados.

---

### 3. Demonstração do Sistema

#### 3.1 Funcionalidades

- **Cadastro de Usuários**: O sistema permitirá que novos usuários se registrem, validando os campos obrigatórios e aplicando critérios de segurança, como a validação de senhas e a verificação se o e-mail já está registrado.
- **Login de Usuários**: O login validará o e-mail e a senha criptografada, gerando um token de autenticação JWT para sessões.

#### 3.2 Explicação do Código-fonte

O sistema foi implementado com foco em segurança e escalabilidade. Os principais pontos são:

- **Segurança**: Senhas são criptografadas usando **bcrypt**, e o sistema utiliza **JWT** para autenticação. Isso garante que senhas não sejam armazenadas em texto simples e que o login seja seguro.
- **Banco de Dados**: O banco foi estruturado para armazenar de forma eficiente os dados dos usuários e os logs de acesso, permitindo futuras auditorias e análises.
- **Validação**: Todos os dados do usuário são validados no backend para garantir que o sistema só aceite entradas seguras e válidas.

---

## Conclusão

Este projeto implementa um sistema de cadastro e login seguro e eficiente, utilizando as melhores práticas de desenvolvimento web e segurança. O sistema está preparado para ser escalado e integrado com outras funcionalidades em um futuro próximo, além de garantir que os dados dos usuários estejam protegidos.

### Contato

Para dúvidas ou mais informações, entre em contato com a equipe de desenvolvimento.
