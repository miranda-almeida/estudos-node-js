
    Configurar um container docker com MySQL;
    Utilizar o mysql2 para acessar um servidor MySQL através de uma aplicação Express;
    Aplicar o conceito de variáveis de ambiente para separar os parâmetros de configuração do seu código;
    Escrever consultas SQL utilizando prepared statements;
    Desenvolver um CRUD com Express integrado ao MySQL;
    Escrever testes de integração com mock do banco de dados.

# configurando mysql no express

a) instalar dependências necessárias para configurar um projeto express capaz de conectar ao mysql na raíz da aplicação:
```cli
npm i express@4.17 mysql2@2.3
```
o módulo/drive mysql2 é responsável por permitir a comunicação entre uma aplicação node.js e o mysql.
esse tipo de biblioteca é o client, contendo todo o código necessário para a integração de comandos sql para o banco de dados e respostas recebidas do mesmo.

b) criar o arquivo ```src/db/connection.js```, responsável pela conexão com o servidor mysql utilizando a biblioteca mysql2:
##### connection.js
```js
const mysql = require('mysql2/promise');

const connection = mysql.createPool({
  host: 'localhost',
  port: 33060,
  user: 'root',
  password: 'root',
  database: 'trybecashdb',
  waitForConnections: true,
  connectionLimit: 10,
  queueLimit: 0,
});

module.exports = connection;
```

a importação da biblioteca mysql2 é feita através do recurso de ```promises```, permitindo a utilização do mysql de forma assíncrona com ```async``` e ```await```.
em seguinda é criada a constante ```connection``` que recebe um ```pool de conexões``` criado pela função ```createPool()```. <br />

##### pool de conexões
é um repositório que contém um conjunto de conexões estabelecidas previamente com o bando de dados.
essas conexões serão reutilizadas durante a execução da aplicação conforme o necessário.
quando uma operação no banco de dados for executada a aplicação irá:
1) requisitar uma conexão no ```pool``` de conexões;
2) utilizar essa conexão para enviar uma operação sql ao servidor mysql;
3) devolver a conexão para o ```pool``` de conexões ao final da operação com o mysql;
4) tornar a conexão disponível para ser utilizada em requisições futuras.

ao não utilizar o ```pool```, para cada operação com o mysql uma nova conexão seria aberta e descartada após seu uso, fazendo com que cada execução de uma nova operação abrisse outra uma conexão.
cada nova conexão com o mysql demanda tempo e adiciona um atraso para cada requisição da API como consequência.

o uso de um ```pool``` de conexões acelera o processo de execução de consultas no mysql, reutilizando conexões em operações futuras e removendo o trabalho de gerar novas conexões.

a função ```createPool()``` recebe um objeto com os seguintes parâmetros:

![image](https://user-images.githubusercontent.com/66367744/207408188-4afb9913-e4ed-445e-bd42-9aeebb023f80.png)

c) cria o arquivo ```src/app.js```, configura o express e testa sua comunicação com o mysql:
##### app.js
```js
const express = require('express');

const app = express();

app.use(express.json());

module.exports = app;
```

