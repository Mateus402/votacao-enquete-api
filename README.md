# Node

### Iniciando o projeto Node do zero

- 1 - Criando o arquivo ```package.json```
```powershell
npm init -y
```

- 2 - Instalando o TypeScript e vinculando ele com o Node
```powershell
npm install typescript @types/node -D
```

- 3 - Criando o arquivo de configuração do TypeScript
```powershell
npx tsc --init
```

- 4 - Configurar o TypeScript com o [Target Mapping](https://github.com/microsoft/TypeScript/wiki/Node-Target-Mapping), um repositório da Microsoft. 
    
  - 4.1 - Verifique qual versão do Node você está utilizando e localize-a no repositório:
  - 4.2 - No arquivo ```tsconfig.json``` altere as informações conforme o repositório

- 5 - Criar as pasta e arquivo ```src``` ```http``` ```server.ts``` respectivamente. O ```server.ts``` é onde vai rodar a API.

- 6 - Como o Node não roda nativamente o TypeScript, é necessário utilizar uma biblioteca que converte o TypeScript para JavaScript. Nesse caso, utilizei a TSX.
```powershell
npm install tsx -D

```

- 7 - No arquivo ```package.json``` criar um script para executar o TSX
```powershell
"scripts": {
    "dev": "tsx watch src/http/server.ts"
  }
```

- 8 - Para executar o servidor, basta executar o comando 
```powershell
npm run dev
```

- 9 - Criando o servidor Node com o Fastify. Para instalar o Fastify, basta executar o comando
```powershell
npm i fastify
```

- 10 - No arquivo ```server.ts``` você incializa o fastify
```javascript
import fastify from 'fastify'

const app = fastify()

app.listen({ port: 3333}).then(() =>{
    console.log('HTTP Server running')
})
```
<br>
<br>
<br>

# Configurando o Docker

Com o docker instalado, basta criar o arquivo ```docker-compose.yml``` e inserir as informações de configuração
```docker
version: '3.7'

services:
  postgres:
    image: bitnami/postgresql:latest
    ports:
      - '5432:5432'
    environment:
      - POSTGRES_USER=docker
      - POSTGRES_PASSWORD=docker
      - POSTGRES_DB=polls
    volumes:
      - polls_pg_data:/bitnami/postgresql

  redis:
    image: bitnami/redis:latest
    environment:
      - ALLOW_EMPTY_PASSWORD=yes
    ports:
      - '6379:6379'
    volumes:
      - 'polls_redis_data:/bitnami/redis/data'

volumes:
  polls_pg_data:
  polls_redis_data:
```

- Para rodar o docker é só executar o comando
```powershell
docker compose up -d
```
<br>
<br>

# Prisma ORM

Para instalar o Prima você precisa executar o seguinte comando
```powershell
npm install -D prisma
```

Para iniciar o Prisma

```powershell
npx prisma init
```

Com esse comando o arquivo ```.env``` será criado

Agora é necessário configurar o arquivo ```.env``` conforme as informações passadas no arquivo ```docker-compose.yml```

Para gerar as tabelas inseridas no arquivo ```schema.prisma``` é necessário executar o comando
```powershell
npx prisma migrate dev
```

O Prisma tem uma inferface integrada
```powershell
npx prisma studio
```

<br>
<br>

# Rotas

Para Inserir uma nova enquete use a rota POST:
```
http://localhost:3333/polls
```

É nescessário incluir os paramêtros obrigatórios TITLE e OPTIONS:
EX:
```json
{
    "title" : "Qual o melhor Framework Node.JS?",
    "options": ["Express", "Fastify", "NestJS", "HapiJS"]
}
```


Para buscar uma enquete pelo ID use a rota GET:
```
http://localhost:3333/polls/:id
```

#

Para incluir um voto, sendo ele unico para uma opção de cada enquete, utilize a rota POST:
```
http://localhost:3333/polls/:id/votes
```
A API verifica se esse usuário já votou anteriormente pelo Cookie, caso não tenha feito um voto, será incluso um novo, caso já tenha votado e seja a mesma opção do voto anterior, retorna Status 400, ou, se ele votar em uma nova opção, o voto anterior será excluído e esse novo será incluido.

#

É necessário passar como paramêtro o ID da enquete que está votando
EX:
```json
{
    "pollOptionId": "e1c36ac2-eee8-412b-a2d8-b91fb723a764"
}
```

#

Rota WebScoket RealTime:
```
ws://localhost:3333/polls/2713d13e-b52c-47e6-ba92-c44705cdf02a/results
```