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

---

## Debug

- Configurar o debug no vscode para o Node:

- Acesse a parte de debug da IDE

- Clique em `create a launch.json file`
- Se pedir escolha a opção Node

- Será criado uma arquivo `.vscode/launch.json`:

```json
{
  // Use IntelliSense to learn about possible attributes.
  // Hover to view descriptions of existing attributes.
  // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
  "version": "0.2.0",
  "configurations": [
    {
      "type": "node",
      "request": "attach",
      "protocol": "inspector",
      "restart": true,
      "name": "Debug",
      "skipFiles": [
        "<node_internals>/**"
      ],
      "outFiles": [
        "${workspaceFolder}/**/*.js"
      ]
    }
  ]
}

```

- essa configuração `"request": "attach",` indica que o debug irá acessar a nossa aplicação que já está executando, ao invés de utilizarmos o `launch` que no caso iria executar a aplicação novamente do zero.

- Precisamos ajustar em `scripts` dentro do `package.json`:

```json
"scripts": {
  "build": "tsc",
  "dev:server": "ts-node-dev --inspect --transpileOnly --ignore-watch node_modules src/server.ts"
},
```

- Adicionamos o `--inspect`

- Por fim podemos executar a aplicação normalmente:

```bash
yarn dev:server
```

- E por fim podemos executar o debug.

---

## Rotas

- Para iniciar a aplicação podemos realizar separação das rotas

- Criamos a pasta `src/routes`

- Criamos o arquivo `src/routes/index.ts` e o arquivo `src/routes/appointments.routes.ts`

- Os arquivos dentro da pasta `src/routes` podemos terminar com `.routes.ts` pois facilita depois a busca de cada um deles.

- No arquivo `src/routes/index.ts` podemos importar esse arquivo para lá:

```ts
import appointmentsRouter from './appointments.routes';

// ...

routes.use('/appointments', appointmentsRouter);
```

- Destacamos o seguinte:

```ts
routes.use('/appointments', appointmentsRouter);
```

- Toda vez que a aplicação realizar uma requisição para rota `/appointments/QUALQUER_COISA`, o node irá entender que ele deverá tratar o restante dentro do `src/routes/appointments.routes.ts` e dentro desse arquivo `src/routes/appointments.routes.ts` Não precisamos adicionar o `/appointments`:

```ts
// Dentro do arquivo `src/routes/appointments.routes.ts` ao invés de utilizar:

// appointmentsRouter.get('/appointments/', (req, res)=> {
  //...
// });

// Podemos utilizar
appoitmentsRouter.get('/', (req, res) => {
  // ...
  // O node já irá direcionar de http?s://meusite/appointments/ para essa function aqui.
});

```

**Lembre de adicionar no arquivo `src/server.ts`:**

```ts
import routes from './routes';

const app = express();
app.use(routes);
```

### Insomnia

- Para realizarmos testes...

- Crie o workspace `GoBarber Bootcamp`
- Criar a pasta `Appointments`
- Criar  uma rota `Create` metodo POST com JSON
- Configura o ambiente de desenvolvimento adicionando o base_url para `http://localhost:3333`


---

## Criando appointments sem base de dados apenas para testes

- Vamos adicionar a dependencia `uuidv4`:

```bash
yarn add uuidv4
```

- Nosso arquivo ficará assim:

```ts
import { Router } from 'express';
import { uuid } from 'uuidv4';

const appointmentsRouter = Router();

const appointments = [];

appointmentsRouter.post('/', (req, res) => {
  const { provider, date } = req.body;

  const appointment = {
    id: uuid(),
    provider,
    date,
  };

  appointments.push(appointment);

  return res.json(appointment);
});

export default appointmentsRouter;

```


## Trabalhado com datas

- Para isso podemos utilizar a biblioteca `data-fns`:

```bash
yarn add date-fns
```

- Iremos utilizar isso inicialmente em `src/routes/appointments.routes.ts`:

```ts
import { parseISO, startOfHour, isEqual } from 'date-fns';
```

- O `parseISO`, irá converter uma string para um `Date()`
- O `startOfHour` irá obter o horário inicial da hora informada
- O `isEqual` recebe duas datas e verifica se são iguais.

---

## Models

- Vamos criar um model em `src/models/Appointment.ts` criamos no formato de class

- Informamos as propriedades da classe informando sempre o tipo delas, e podemos criar um consturctor para definir o valor delas ao iniciar o object.

- Sempre que for amazenar algum dado na aplicação ou em uma base de dados utilizamos o model.
