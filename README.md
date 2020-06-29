# Projeto com node e typescript

- Instale as dependencias:

```bash
yarn add express
```

```bash
yarn add typescript -D
```

- Gerar o arquivo de confgiruação do typescritp

```bash
yarn  tsc --init
```

- Será gerado o arquivo `tsconfig.json` na raiz do projeto

- Ajustar no arquivo `tsconfig.json`

```json
  "outDir": "./dist", // pasta onde serão gerados
  "rootDir": "./src", // pasta raiz do projeto
```

---

- Criar a pasta `src`

- Criar o arquivo `src/server.ts`

- Para gerar o modo de distribuíção podemos executar o comando:

```bash
yarn tsc
```

- Isso irá gerar a pasta `dist` com os códigos convertidos para `js`

- E utilizando o typescript podemos utilizar as ultimas funcionalidades do javascript como import/export,
não precisamos mais do sucrase.

- Instale também a lib:

```bash
yarn add @types/express -D
```

- Para executar o projeto assim como o nodemon utilizamos a lib:

```bash
yarn add ts-node-dev -D
```

- No arquivo `package.json` fazemos um ajuste:

```json
"scripts": {
  "build": "tsc",
  "dev:server": "ts-node-dev src/server.ts"
}
```

- Por fim no terminal utilizamos o comando:

```bash
yarn dev:server
```

- Para deixar a execução mais rápida podemos adicionar alguns parametros:

- `--transpileOnly` Não irá verificar se o tipo está certo ou errado apenas transforma o código, pois isso de verificar se está certo ou errado utilizamos o próprio vscode

- `--ignore-watch node_modules` para não verificar alteração na pasta node_modules

- Por fim podemos ajustar o `package.json`:

```json
"scripts": {
  "build": "tsc",
  "dev:server": "ts-node-dev --transpileOnly --ignore-watch node_modules src/server.ts"
}
```

---

## Padronização do código

- Editor Config

- É necessário instalar no vscode [Editor Config](https://www.notion.so/EditorConfig-898f716a2dae420bb84ba7c24dc03457#08b8eb2d9bbf47338dd9c0aa77404466)

- Depois de instalado clicar com o botão direito do mouse e clicar em `Generate .editorconfig`

```yml
root = true

[*]
indent_style = space
indent_size = 2
charset = utf-8
trim_trailing_whitespace = true
insert_final_newline = true
end_of_line = lf
```

-----

- ESLint

- Instale o ESLint para o vscode: [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)


- No arquivo de configuração do Vscode json adicionamos `settings.json`:

```json
"editor.codeActionsOnSave": {
  "source.fixAll.eslint": true
}
```

- Instale a dependencia no projeto:

```bash
yarn add eslint@6.8.0 -D
```

- Execute o comando:

```bash
yarn eslint --init
```

- `How would you like to use ESLint?`
  - `To check syntax, find problems, and enforce code style `
- `What type of modules does your project use?`
  - `JavaScript modules (import/export)`
- `Which framework does your project use?`
  - `None of these`
- `Does your project use TypeScript?`
  - `y`
- `Where does your code run?`
  - `Node`
- `How would you like to define a style for your project?`
  - `Use a popular style guide`
- `Which style guide do you want to follow?`
  - `Airbnb: https://github.com/airbnb/javascript`
- `What format do you want your config file to be in?`
  - `JSON`
- Aguardar
- Copiar tudo que ele sugere:

```bash
@typescript-eslint/eslint-plugin@latest eslint-config-airbnb-base@latest eslint@^5.16.0 || ^6.8.0 || ^7.2.0 eslint-plugin-import@^2.21.2 @typescript-eslint/parser@latest
```

- Porém remova:

```bash
eslint@^5.16.0 || ^6.8.0 || ^7.2.0
```

- `Would you like to install them now with npm?`
  - `n`

- Dessa forma removendo o eslint que já foi instalado ficará assim:

```bash
yarn add @typescript-eslint/eslint-plugin@latest eslint-config-airbnb-base@latest eslint-plugin-import@^2.21.2 @typescript-eslint/parser@latest
```


- `Would you like to install them now with npm?`
  - `n`

- Criar o arquivo `.eslintignore`

```yml
/*.js
node_modules
dist
```

- Para que o eslint entenda a importação de arquivos ts utilize o comando:

```bash
yarn add eslint-import-resolver-typescript -D
```

- No arquivo `eslintrc.json` adicione em `extends`:

```json
"plugin:@typescript-eslint/recommended"
```

- Para que as importações funcionem corretamente utilize:

```js
"settings": {
    "import/resolver": {
      "typescript": {}
    }
  }
```

- Em `rules` adicione o seguinte:

```json
"import/extensions": [
  "error",
  "ignorePackages",
  {
    "ts": "never"
  }
]
```

- Isso irá fazer com que o eslint entenda o `tsx`

---

## Prettier

- Instale no projeto o seguinte:

```bash
yarn add prettier eslint-config-prettier eslint-plugin-prettier -D
```

- No arquivo `.eslintrc.json` adicione em `extends` o seguinte:

```json
"prettier/@typescript-eslint",
"plugin:prettier/recommended"
```

- Em `plugins` adicione:

```json
"prettier"
```

- Em `rules` adicione:

```json
"prettier/prettier": "error"
```

- Para evitar conflitos entre o eslint e o prettier crie o arquivo `prettier.config.js`:

```js
module.exports = {
  singleQuote: true,
  trailingComma: 'all',
	arrowParens: 'avoid',
}
```
