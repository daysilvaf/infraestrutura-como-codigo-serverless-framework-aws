API node.js COM SERVERLESS FRAMEWORK EM AMBIENTE AWS

Repositório contendo código fonte de um projeto criado em uma infraestrutura em nuvem AWS com API Gateway, DynamoDB, AWS Lambda e AWS CloudFormation utilizando o framework Serverless para o desenvolvimento baseado em infraestrutura as a Code.

ETAPAS

Pré requisitos:

• Possuir uma conta na AWS e instalar Node.js na máquina

• Instalar o AWS CLI: https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-welcome.html

SETUP INICIAL

Credenciais AWS:

Criar usuário: AWS Management Console -> IAM Dashboard -> Create New User -> <nome do usuário> -> Permissions "Administrator Access" -> Programmatic Access -> Dowload Keys

No terminal: ```$ aws configure``` -> colar as credenciais geradas anteriormente

CONFIGURAR O FRAMEWORK SERVERLESS

```$ npm i -g serverless```

DESENVOLVIMENTO DO PROJETO

```
$ serverless
Login/Register: No
Update: No
Type: Node.js REST API
Name: dio-live
```

```
$ cd dio-live
$ code
```

• No arquivo ```serverless.yml``` adicionar a região region: us-east-1 dentro do escopo de provider:

• Salvar e realizar o deploy ```$ serverless deploy -v```

ESTRUTURAR CÓDIGO

• Criar o diretório ```"src"``` e mover o arquivo ```"handler.js"``` para dentro dele

• Renomear o arquivo ```"handler.js"``` para ```"hello.js"```

• Atualizar o código

```
const hello = async (event) => {
/////
module.exports = {
    handler:hello
}
```

Atualizar o arquivo ```"serverless.yml"```

```handler: src/hello.handler```

```$ serverless deploy -v```

DynamoDB

Atualizar o arquivo ```serverless.yml```

```
resources:
  Resources:
    ItemTable:
      Type: AWS::DynamoDB::Table
      Properties:
          TableName: ItemTable
          BillingMode: PAY_PER_REQUEST
          AttributeDefinitions:
            - AttributeName: id
              AttributeType: S
          KeySchema:
            - AttributeName: id
              KeyType: HASH
```

DESENVOLVER FUNÇÕES LAMBDA


- Pasta /src do repositório
- Obter arn da tabela no DynamoDB AWS Console -> DynamoDB -> Overview -> Amazon Resource Name (ARN)
- Atualizar arquivo serverless.yml com o código a seguir, abaixo do region:

```
  iam:
    role:
        statements:
          - Effect: Allow
            Action:
              - dynamodb:PutItem
              - dynamodb:UpdateItem
              - dynamodb:GetItem
              - dynamodb:Scan
            Resource:
              - arn:aws:dynamodb:us-east-1:167880115321:table/ItemTable
```

INSTALAR DEPENDÊNCIAS

```npm init ```npm i uuid aws-sdk```

Atualizar lista de funções no arquivo ```serverless.yml```

```
functions:
hello:
  handler: src/hello.handler
  events:
    - http:
        path: /
        method: get
insertItem:
  handler: src/insertItem.handler
  events:
    - http:
        path: /item
        method: post
fetchItems:
  handler: src/fetchItems.handler
  events:
    - http:
        path: /items
        method: get
fetchItem:
  handler: src/fetchItem.handler
  events:
    - http:
        path: /items/{id}
        method: get
updateItem:
  handler: src/updateItem.handler
  events:
    - http:
        path: /items/{id}
        method: put
```
