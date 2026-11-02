# Projeto Integrador — SOS Emergência (Aula 12)

O SOS Emergência é um projeto acadêmico desenvolvido com front-end React, página institucional em Next.js e API Node.js/Express conectada ao banco de dados PostgreSQL.

Nesta etapa, correspondente à Aula 12, foram implementados mecanismos de segurança para validar os dados recebidos pela API, proteger as senhas dos usuários e controlar o acesso às rotas privadas. Para isso, foram utilizadas as bibliotecas **Joi, bcrypt e JWT (JSON Web Token)**.

## 1. Segurança implementada

### Validação de dados com Joi

O Joi é utilizado para verificar os dados enviados pelos usuários antes de permitir o cadastro ou a atualização de informações.

Nas rotas de criação e atualização de contatos, os campos `nome`, `telefone` e `parentesco` são obrigatórios.

Quando algum campo está ausente ou inválido, a API retorna o código HTTP **400 (Bad Request)**, acompanhado de uma mensagem explicando o problema.

### Proteção de senhas com bcrypt

As senhas não são armazenadas em texto puro no banco de dados.

Durante o cadastro, a API utiliza `bcrypt.hash(senha, 10)` para gerar um hash seguro, armazenado na coluna `usuarios.senha_hash` do PostgreSQL.

No login, a função `bcrypt.compare` verifica se a senha informada corresponde ao hash armazenado.

### Autenticação com JWT

Após um login válido, a API gera um token JWT, utilizado para identificar o usuário nas requisições às rotas protegidas.

O token possui validade de **1 hora** e é enviado no cabeçalho da requisição utilizando o formato `Authorization: Bearer TOKEN`.

Quando o token está ausente, inválido ou expirado, a API bloqueia o acesso e retorna **401 (Unauthorized)**.

## 2. Login para demonstração local

**Login:** `luna`  
**Senha:** `1234`

Essas credenciais são utilizadas exclusivamente para demonstração em ambiente acadêmico.

O arquivo `backend/seed-luna.js` gera o hash bcrypt da senha e o armazena na coluna `usuarios.senha_hash`.

## 3. Preparação do PostgreSQL

1. Criar o banco de dados `sos_emergencia` no pgAdmin.
2. Em uma instalação nova, executar o arquivo `database/sos_emergencia.sql` no Query Tool.
3. Caso o banco da Aula 10/11 já exista e contenha informações que precisam ser preservadas, executar somente `database/aula12_migracao.sql`.
4. Configurar o arquivo `backend/.env` com os dados de conexão do PostgreSQL e a variável `JWT_SECRET`.
5. Não publicar o arquivo `.env` no GitHub, pois ele contém informações confidenciais.

**Atenção:** o script completo `sos_emergencia.sql` recria as tabelas de exemplo. Portanto, deve ser utilizado somente em um banco de desenvolvimento sem dados importantes.

## 4. Executar a API

No terminal, acessar a pasta `backend` e executar:

```bash
cd backend
npm install
npm run seed:luna
npm run dev
```

A API ficará disponível em:

`http://localhost:3000`

## 5. Executar o front-end

Em outro terminal, executar:

```bash
cd frontend/react-app
npm install
npm run dev
```

Abrir o endereço informado pelo Vite, normalmente:

`http://localhost:5173`

Para testar o acesso, utilizar as credenciais de demonstração `luna` e `1234`.

Após o login, o sistema utiliza o token JWT para acessar as funcionalidades protegidas.

## 6. Principais rotas da API

| Método | Rota | Funcionalidade |
|---|---|---|
| POST | `/api/registro` | Cadastra usuário, valida dados com Joi e gera hash bcrypt |
| POST | `/login` | Verifica as credenciais e gera o token JWT |
| GET | `/api/privado` | Demonstra o funcionamento da autenticação JWT |
| GET | `/api/me` | Retorna os dados do usuário autenticado |
| GET | `/api/contatos` | Lista contatos |
| POST | `/api/contatos` | Cadastra contatos com validação Joi |
| PUT | `/api/contatos/:id` | Atualiza contatos com validação Joi |
| DELETE | `/api/contatos/:id` | Remove contatos |
| GET | `/api/health` | Verifica a conexão com o PostgreSQL |

As rotas de contatos e as rotas privadas exigem autenticação JWT.

## 7. Testes de segurança realizados

Os testes foram realizados no Postman, com verificação do armazenamento das senhas no pgAdmin.

| Teste | Resultado esperado |
|---|---|
| Login com credenciais válidas | 200 OK e geração de token JWT |
| Login com senha incorreta | 401 Unauthorized |
| Cadastro ou atualização com dados inválidos | 400 Bad Request |
| Acesso à rota privada sem token | 401 Unauthorized |
| Acesso à rota privada com token válido | 200 OK |
| Consulta à coluna `senha_hash` no pgAdmin | Senha armazenada como hash bcrypt |

Os testes demonstram o funcionamento da validação de dados, da proteção das senhas e do controle de acesso à API.

## 8. Tecnologias utilizadas

- Node.js e Express — desenvolvimento da API.
- PostgreSQL — armazenamento dos dados.
- Joi — validação das informações recebidas.
- bcrypt — geração e verificação de hashes de senhas.
- JWT — autenticação e proteção das rotas.
- Postman — testes dos endpoints.
- pgAdmin — administração e consulta ao banco de dados.
- React e Next.js — interfaces do projeto.
- Git e GitHub — versionamento e publicação do código.

## 9. Versionamento e entrega

O projeto utiliza Git para registrar as alterações realizadas durante o desenvolvimento.

Os commits dos integrantes podem ser consultados no histórico do repositório.

**Repositório da Aula 12:**

https://github.com/mariapereira112/sos-emergencia-aula12

A entrega também inclui uma apresentação em vídeo, com duração de 3 a 5 minutos, demonstrando as funcionalidades de segurança e os testes realizados.

## 10. Considerações finais

A implementação do Joi, bcrypt e JWT trouxe melhorias importantes para a segurança do SOS Emergência.

Com essas ferramentas, a API passou a validar os dados recebidos, proteger as senhas armazenadas no banco de dados e impedir o acesso não autorizado às rotas privadas.

Os testes realizados no Postman e no pgAdmin permitiram verificar o funcionamento desses recursos e demonstrar a aplicação prática dos conteúdos estudados na Aula 12.