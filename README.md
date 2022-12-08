# estudos-node-js
### anotações sobre node.js + API REST express

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





