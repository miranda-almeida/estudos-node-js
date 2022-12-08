## estudos-node-js
anotações sobre node.js, API REST express e rotas

# servidor node.js
1) cria pasta com o diretório src para organização de arquivos e abertura da pasta raíz no vscode: 

`mkdir nome-pasta nome-pasta/src && cd nome-pasta && code .`

2) inicia pacote node.js

`npm init -y`

3) realiza instalação do express

`npm i express@4.17` --define versão a partir de orientações para o projeto

4) adicional: instalação de linter nas configurações da trybe

`npm i eslint@6.8 eslint-config-trybe-backend@1.0 -D`

5) cria arquivos de configuração de lint e git

`touch .eslintignore .eslintrc.json .gitignore`

> observações sobre arquivos criados:
- .eslintignore: orienta ao ESLint que ignore arquivos e diretórios específicos;
- .eslintrc.json: determina sobrescrição de regras do ESLint;

##### .eslintignore
adiciona pasta `node_modules/`

##### .eslint.json
preenchimento do arquivo com o seguinte código, seguindo orientações da trybe:

```json
{

  "env": {

    "es2020": true

  },

  "extends": "trybe-backend",

  "rules": {

    "sonarjs/no-duplicate-string": ["error", 5]

  }

}
```
##### .gitignore
adiciona pasta `node_modules/`

# framework express
6) cria os arquivos `app.js` e `server.js` na pasta `src`

##### app.js
```js
const express = require('express');


const app = express();


module.exports = app;
```
realiza a importação do pacote do express, inicia o mesmo no aplicativo a partir da função `express()` e atribui ela a variável `app`, realizando assim sua exportação para reutilização em diferentes arquivos como o `server.js`

##### server.js
```js
const app = require('./app');

app.listen(3001, () => console.log('server running on port 3001'));
```
o `start` da aplicação será provido pelo trecho de código com a função `.listen()`, que recebe dois parâmetros:
- o port, onde no código é passado 3001 mas pode ser qualquer número não utilizado acima de 1023;
- uma função, no código acima foi passado apenas um `console.log()` para informar que o servidor está rodando

7) configurações do `package.json` na aba `scripts`
```json
"start": "node src/server.js",

"dev": "nodemon src/server.js",

"lint": "eslint --no-inline-config --no-error-on-unmatched-pattern -c .eslintrc.json ."
```
- o comando `start` é reconfigurado para a aplicação iniciar pelo server.js, já configurado como servidor inicial;
- o comando `dev` orienta a dependência de desenvolvedor do nodemon para inicializar também pelo server.js, auxiliando na reinicialização automática da aplicação cada vez que um arquivo for alterado e salvo;
- o comando lint adiciona os comandos de verificação do eslint

obs: realizar instalação do pacote nodemon através do comando `npm i nodemon@2.0 -D` --a tag -D adiciona o pacote como devDependencies, já que a utilidade do mesmo não é necessário na produção, apenas no desenvolvimento

# rotas (caminhos, paths, endpoints)
a anatomia de uma URL consiste em `protocolo`, `domínio` e `rota`:
```diff
+ https:// | protocolo
+ github.com | domínio
+ /login | rota
```

8) adiciona a seguinte linha de código no `app.js`:

`app.get('/', (req, res) => res.status(200).json({ message: 'Olá Mundo!' }));`

ao acessar a port pelo navegador através do localhost, é possível visualizar a mensagem

a) função `.get()`: utilizada para pedir algum dado, recebendo dois parâmetros:
  - '/': rota desejada, nesse caso é a raíz (página inicial)
  - (req, res) => {}: espera uma função de callback, que pode receber de dois a quatro parâmetros, sendo eles:
    - req: REQUEST, por meio dela recebemos os dados (envio por query, params e body);
    - res: RESPONSE, por meio dela respondemos o que nos é solicitado;
    - next: [add];
    - err: [add];

b) boas práticas consistem no envio de um `status code`, no caso da função `.status(200)`. os status são importantes para identificação da resposta das requisições. [acessar documentação do MDN sobre status code](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)

