
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
realiza exportação na constante ```app```, que inicializa o express e registra os middlewares que serão utilizados inicialmente.
a função ```app.listen()``` será adicionada ao arquivo ```server.js```, para que no momento de realizar testes de integração, as configurações de inicialização, rota e middleware do express estejam separadas da inicialização do express. dessa forma é possível criar um mock das rotas e facilitar o processo de testagem da API.

d) cria o arquivo ```src/server.js```
##### server.js
```js
const app = require('./app');
const connection = require('./db/connection');

const PORT = 3001;

app.listen(PORT, async () => {
  console.log(`API TrybeCash está sendo executada na porta ${PORT}`);

  // O código abaixo é para testarmos a comunicação com o MySQL
  const [ result ] = await connection.execute('SELECT 1');
  if (result) {
    console.log('MySQL connection OK');
  }
});
```
dentro da função `app.listen()` foi adicionado um trecho de código que executa a função `connection.execute()`, que recebe como parâmetro uma consulta do sql: `SELECT 1`.
a função `connection.execute()` realiza uma conexão com o mysql, executa o sql `SELECT 1` passado como parâmetro e recebe uma resposta que é armazenada na constante `result`.
depois é verificado com um `if` se o objeto `result` contém alguma coisa e, em caso de positivo, é impresso no console a mensagem `MySQL connection OK`. 

##### código sql trybe missions
```sql
DROP DATABASE IF EXISTS trybestrelar;

CREATE DATABASE trybestrelar;

USE trybestrelar;

CREATE TABLE
    missions (
        id INT NOT NULL AUTO_INCREMENT,
        name VARCHAR(90) NOT NULL,
        year VARCHAR(45) NOT NULL,
        country VARCHAR(90) NOT NULL,
        destination VARCHAR(90) NOT NULL,
        PRIMARY KEY(id)
    );

INSERT INTO missions 
    (`id`,`name`,`year`,`country`,`destination`) 
VALUES 
    (1,'Mariner 2','1962','Estados Unidos','Vênus'),
    (2,'Venera 4','1967','URSS','Vênus'),
    (3,'Mariner 5','1967','Estados Unidos','Vênus'),
    (4,'Apollo 11','1969','Estados Unidos','Lua'),
    (5,'Mariner 10','1973','Estados Unidos','Mercúrio e Vênus'),
    (6,'Voyager 1','1977','Estados Unidos','Espaço interestelar'),
    (7,'Venera 16','1983','URSS','Vênus'),
    (8,'Phobos 1','1988','URSS','Marte'),
    (9,'Phobos 2','1988','URSS','Marte'),
    (10,'Galileo','1989','Estados Unidos','Júpiter'),
    (11,'Mars Pathfinder','1996','Estados Unidos','Marte'),
    (12,'Cassini-Huygens','1997','Estados Unidos e Europa','Saturno'),
    (13,'Nozomi (Planeta B)','1998','Japão','Marte'),
    (14,'Estação Internacional Espacial','1998','Estados Unidos e Rússia','Órbita terrestre'),
    (15,'Mars Polar Lander','1999','Estados Unidos','Marte'),
    (16,'2001 Mars Odyssey','2001','Estados Unidos','Marte'),
    (17,'Genesis','2001','Estados Unidos','Terra'),
    (18,'MESSENGER','2004','Estados Unidos','Mercúrio'),
    (19,'Telescópio Espacial Kepler','2009','Estados Unidos','Espaço interestelar'),
    (20,'Telescópio Espacial James Webb','2021','Estados Unidos, União Europeia e Canadá','Espaço interestelar');
 ```
verifica script do mysql rodando dentro do docker: <br />
![image](https://user-images.githubusercontent.com/66367744/207420516-3b052370-9144-4340-8dbe-2a6cfea313aa.png) <br />
![image](https://user-images.githubusercontent.com/66367744/207420772-4745f0d8-0dc8-4e5a-bc72-b7a69420f584.png) <br />

##### docker compose para subir banco de dados
![image](https://user-images.githubusercontent.com/66367744/207421193-0d193c2c-9303-4a6b-a857-9d6aac26ba2a.png)


