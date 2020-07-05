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

---

## Repository

- Repositorio seria uma conexão entre a persistencia dos dados e a rota
- No repositorio terá o metodo `create` para criar as informações, `find` para buscar/consultar as informações.
- Criamos um repositorio por model.
- Criamos no formato de class

- Vamos criar o repositorio `src/repositories/AppointmentsRepository.ts`

- Ao invés de utilizarmos a nossa variavel de persistencia direto na rota, iremos utiliza-la no repository.

- Foi movido várias responsabilidades do `src/routes/appointments.routes.ts` para o `models` e para o `repositories`, essa refatoração de código é bem comum de ser feita.

- Precisamos também adicionar uma rota para retornar todos os appointments,
- No arquivo `src/repositories/AppointmentsRepository.ts` adicionamos o metodo `all` e em `src/routes/appointments.routes.ts` adicionamos a rota para obter esses dados

- E criamos no Insomnia a rota List do tipo get para obter os dados do Appointments

---

- Ainda precisamos continuar melhorando o código das rotas, utilizando a técnica de `SoC` = `Separation of Concerns (Separação de Preocupações)`
- Então precisamos diminuir as preocupações da nossa rota ainda possuí várias preocupações, como de criar appointments

---

## Trabalhando com dados

- Na arquitetura de software quand queremos enviar dados de um arquivo para outro chamamos isso de `DTO - Data Transfer Object`
- No JavaScript é sempre melhor utilizar a desetruturação de objectos para podermos utilizar parametros nomeados.
- E o melhor de utilizar parametros nomeados é que se não utilizarmos dessa forma e faltar algum parametro o erro que será retornado é algo do tipo:
`esperado x paramentros, foi enviado y`, então não fica muito claro, agora no caso de parametros nomeados, o erro irá retornar o nome do parametro que faltou, dessa forma fica muito mais fácil de encontrar o error.

- E quando eu quero omitir um parametro da minha function?

- Podemos utilizar o recurso do TypeScript `Omit<CLASS_OU_INTERFACE, 'PARAMETRO_QUE_PRECISO_OMITIR'>` um exemplo disso pode ser visto em `src/models/Appointment.ts`:

```ts
 constructor({ provider, date }: Omit<Appointment, 'id'>) {
    this.id = uuid();
    this.provider = provider;
    this.date = date;
  }
```

- Estamos informando que recebemos todos os parametros da class `Appointment` porém não queremos que o id seja passado.

---

## SOLID e Services

- Services

- Temos um model que é a representação de como o dado é salvo, os campos dele
- Temos o repositório que irá trabalhar com o dado em si, irá listar, atualizar, deletar

- O service irá armazenar a regra de negócio da aplicação

- precisamos dividir melhor a preocupação da nossa aplicação, que é proposto pela `SoC`, no caso da rota ela só pode ser preocupar com:
  - Receber a requisição
  - Chamar outro arquivo
  - Devolver uma resposta
  - Caso tenha algo além disso, devemos abstrair isso dentro de um service


- Criar uma pasta `src/services/`

- Criamos um serviço especifico para criação de um agendamento, ele não irá listar, atualizar, nada disso, irá apenas criar o agendamento.

- Os serviços também não tem acesso direto aos dados da requisição e nem aos dados da respostas
- Os erros tratamos com `throw Error()` ao invés do `res.status(400).json({})`

- Crie o arquivo `src/services/CreateAppointmentService.ts`

- Dentro do serviço só posso ter apenas um metodo que é o `execute()`

- Outra coisa importante que é interessante separar é:
  - O que é transformação de dados e
  - O que é regra de negócio

- Outra coisa importante é que repetir código em muitos casos não é um problema, pois se automatizarmos de forma precoce pode ser que tenhamos um problema mais a frente

- Como o serviço irá utilizar o Repository, não podemos instanciar o Repository dentro dele, pois no caso quando criarmos outro serviço essa instancia será diferente uma das outras, ou seja cada serviço teria acesso a uma instancia diferente e não a mesma, nesse caso iremos aplicar o principio de `Dependecy Inversion (SOLID)`, nesse caso iremos enviar via `constructor` o paramentro contendo a instancia do repository.

- Com tudo pronto dentro do service podemos chamar isso dentro da rota `src/routes/appointments.routes.ts`


- Um conceito importante que temos que ter em mente é o `DRY` (Don't repeat yourself), ou seja não repetir regra de negócio na nossa aplicação

- Nesse projeto utilizamos dois conceitos do `SOLID` que no caso foi o `Single responsibility principle` e o `Dependency Inversion Principle`

---




