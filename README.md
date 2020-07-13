# Back-end GoBarber parte 1

- Iniciando com o [TypeORM](https://typeorm.io/#/)
- Criar o arquivo `ormconfig.json`
- Detalhes de como configurar esse arquivo [TypeORM](https://typeorm.io/#/connection-options)
- Instação dos drivers: [TypeORM](https://typeorm.io/#/)

- Instalando o typeorm e o postgre:

```bash
yarn add typeorm pg
```

- Com isso podemos configurar o `ormconfig.json`

- Agora criamos um arquivo chmado `src/database/index.ts` esse arquivo será feita a conexão com a base de dados

- Poderiamos utilizar os parametros da conexão diretamente nesse arquivo porém para utilizarmos as migrations etc., o melhor é inserir os dados da conexão nesse arquivo de configuração.

- Agora com essa parte da conexão configurada chamamos ela em `src/server.ts`

**Lembrar de criar a base de dados**

- Depois de tudo feito para testar utilizamos o comando:

```bash
yarn dev:server
```

----

## Migrations

- Vamos utilizar as migrations para criar o ambiente, realizar alterações e assim por diante
- Inicialmente no arquivo `ormconfig.json` adicionamos a configuração para pasta migrations
- Criar a pasta `src/database/migrations/`
- No arquivo `ormconfig.json` adicionar a configuração de migrations:

```json
"migrations": [
  "./src/database/migrations/*.ts"
]
```

- Dessa forma todos os arquivos nessa pasta que forem .ts será uma migration

- Podemos também adicionar algumas configurações de linha de comando:

```json
"cli": {
  "migrationsDir": "./src/database/migrations"
}
```

- Porém se executarmos o comando ele irá gerar o arquivo em js, para podermos criar arquivos em ts precisamos informar isso no cli, para isso no arquivo `package.json` adicionamos em `"scripts"` o seguinte:

```json
"scripts": {
  "typeorm": "ts-node-dev ./node_modules/typeorm/cli.js"
}
```

- Agora para criar a migration utilizamos o comando:

```bash
yarn typeorm migration:create -n CreateAppointaments
```

- Será criado o arquivo em `src/database/migrations/`

- Nesse arquivo contem dois metodos o `up` e `down` o up eu utilizo para realizar alguma coisa como criar tabela, criar/alterar campo
- E no down utilizo para desfazer o que foi feito pelo up.

**Uma forma segura de utilizar id unico é utilizando o uuid ao invés de registro incremental, pois o tradicional é fácil tentar descobrir o próximo, diferente do uuid**

- Depois de inserir a estrutura da migration podemos executar o comando:

```bash
yarn typeorm migration:run
```

- Se for necessário desfazer a migration utilizamos o comando:

```bash
yarn typeorm migration:revert
```

- Para verificar as migrations que foram executados podemos utilizar o comando:

```bash
yarn typeorm migration:show
```

---

## Informar o caminho das entidades/model

- Precisamos informar ao typeorm qual o caminho das entidades no arquivo `ormconfig.json`:

```json
"entities": [
  "./src/models/*.ts"
],
```

---

## Model como Entidade - Uitlizando o Decoration do TypeORM

- Primeiro no arquivo  `tsconfig.js` habilite essas opções:

```json
"experimentalDecorators": true,
"emitDecoratorMetadata": true,
```

- Agora em `src/models/Appointments.ts` altere o conteúdo para:

```ts
import { Entity, Column, PrimaryGeneratedColumn } from 'typeorm';

@Entity('appointments')
class Appointment {
  @PrimaryGeneratedColumn('uuid')
  id: string;

// Quando o valor é string não precisa informar nada
  @Column()
  provider: string;

  @Column('timestamp with time zone')
  date: Date;
}

export default Appointment;

```

- o eslint irá apontar erro de que a variavel não está sendo iniciada, para corrigir isso no arquivo `tsconfig.json` altere o seguinte:

```json
"strictPropertyInitialization": false,
```


---

## Ajustando o Repository


- O TypeORM por padrão já possuí uma especie de repository

- Ajustar o arquivo `src/repository/AppointmentRepository.ts` // Utilizamos o @EntityRepository(Appointment) para trabalhar com o repository com TypeORM
- Ajustar o arquivo `src/services/CreateAppointmentService.ts`
- Ajustar o arquivo `src/routes/appointments.route.ts`

- Antes de executar o projeto precisamos instalar mais uma dependencia:

```bash
yarn add reflect-metadata
```

- No arquivo `src/server.ts` precisamos importar esse arquivo na primeira linha:

```ts
import 'reflect-metadata';
```

- Mais informações em [TypeORM](https://typeorm.io/#/)



---

## Tabela de Usuários

- Executar a migration:

```bash
 yarn typeorm migration:create -n CreateUsers
```

- Será criado um arquivo na pasta `src/database/migrations`

- Depois de definido os campos execute o comando:

```bash
yarn typeorm migration:run
```

- Será gerado a tabela na base de dados.

---

## Model de Usuários

- Crie o arquivo `src/models/User.ts`

- Algo interessante nesse código é que estamos utilizando também o Decoration:
- @Entity('NOME_DA_TABELA')
- @PrimaryGeneratedColumn('tipo')
- @Column('tipo')
- @CreateDateColumn()
- @UpdateDateColumn()

---

## Relacionar tabelas migration

- Criar outra migration para alterar o campo de provider para um campo relacional:

```bash
yarn typeorm migration:create -n AlterProviderFieldToProviderId
```

- Será criado outro arquivo dentro da pasta `src/database/migrations`, nesse arquivo iremos criar uma coluna na tabela de appointments que irá se relacionar com a tabela de usuários

---

## Ajustes nos models para relacionamento

- No model `src/models/Appointment.ts` iremos ajustar...

```ts
@ManyToMany(() => User) // Informamos que o campo abaixo faz referencia a tabela de usuários
@JoinColumn({ name: 'provider_id' }) // Informamos qual coluna nessa tabela atual se refere o relacionamento
provider: User;
```


---

## Criação de usuário

- Crie a rota `src/routes/users.routes.ts`

- No Insomnia criar a pasta User com a rota Create metodo POST

- Criar um serviço: `src/services/CreateUserService.ts`

- Nesse service diferente do de Appointments não iremos criar um repositorio, pois não precisamos de nenhum metodo adicional além dos padrões para buscar informações diretamente do model.

---

- Ajustes no Appointments para receber o id do usuário ao invés do nome para o cadastro do provider

- No arquivo `src/routes/appointments.routes.ts` altere no recebimento do body para receber o provider_id
- Mesma coisa no `src/services/CreateAppointmentService.ts` ajustar

---

## Cryptografia para Senha

- Adicionar a lib:

```bash
yarn add bcryptjs
```

- e instale a declaração de tipos:

```bash
yarn add @types/bcryptjs -D
```


- No arquivo `src/services/CreateUserService.ts` adicione a importação do bcryptjs

---

## Login

- Criar a rota `src/routes/sessions.routes.ts`

- Criar o service `src/services/AuthenticationUserService.ts`

- Criar rota no Insomnia `Session/Creat` do tipo POST

---

## JWT Toke

- Inicialmente instale a lib:

```bash
yarn add jsonwebtoken
```

- Instalar a lib:

```bash
yarn add @types/jsonwebtoken -D
```

- Para debugar o jwt podemos utilizar o site [JWT](https://jwt.io)

- Ajustar no arquivo `src/services/AuthenticationUserService.ts`


---

## Middleware

- Criar Middleware para verifcar se o usuário está logado ou não

- Crie o arquivo `src/middleware/ensureAuthenticated.ts`

- Criar o arquivo `src/config/auth.ts` para adicionar as configurações de autenticação

- No arquivo `src/middleware/ensureAuthenticated.ts` estamos adicionando uma prop para um objeto:

```ts
request.user = {
      id: sub,
    };
```

- Porém no request que é do tipo Request do express não possuí essa propriedade, dessa forma precisamos adicionar/anexar isso, como?

- Criamos uma pasta `src/@types/NOME_DA_LIB.d.ts`:
  - d: definition
  - ts typescript
- Nesse caso iremos criar `src/@types/express.d.ts` e precisamos descobrir qual o nome do namespace do express e por fim adicionar:

```ts
declare namespace Express {
  export interface Request {
    user: {
      id: string;
    };
  }
}

```


---

## imagem de Avatar para o usuário - ajuste na Tabela

- Crie uma nova migration para adicionar um novo campo na tabela:

```bash
yarn typeorm migration:create -n AddAvatarFieldToUsers
```

- Criar a rota para enviar a informação da imagem no arquivo `src/routes/users.routes.ts`

---

## Upload de imagem

- Utilize a lib:

```bash
yarn add multer
```

- Instale também a definição de tipos:

```bash
yarn add @types/multer -D
```

- Criar o arquivo `src/config/upload.ts` que irá armazenar as informações para upload de imagens

- Por enquanto as imagens serão armazenadas no disco, então crie uma pasta `tmp/`

- Por fim adicionamos essas informações em `src/routes/users.routes.ts`

---

## Serviço para salvar caminho do avatar na base

- Crie o serviço `src/services/UpdateUserAvatarService.ts`

- Na rota `src/routes/users.routes.ts` adicionar o serviço

- No model `src/models/User.ts` adicionar a coluna avatar

- Criar no Insomnia a rota `Users/UpdateAvatar` do tipo PATH

---

## Obter url da imagem salva

- Para servir os arquivos de forma estática vamos criar no arquivo `src/server.ts`

```ts
app.use('/files', express.static(uploadConfig.directory));
```
