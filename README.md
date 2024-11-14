# AWS Step Functions

## Introdução

***O que é o "AWS Step Functions"?***

AWS Step Functions é um serviço de orquestração serverless que permite criar e gerenciar fluxos de trabalho complexos na nuvem AWS.

Aqui estão os principais pontos sobre o AWS Step Functions:

### Visão Geral

- É um serviço para **criar fluxos de trabalho visuais** que coordenam múltiplos serviços AWS.
- Permite **orquestrar aplicações distribuídas**, automatizar processos e criar pipelines de dados e machine learning.
- Baseado no **conceito de máquinas de estado** e tarefas.

### Componentes Principais

- **Máquinas de Estado**: Representam o fluxo de trabalho completo, definindo a sequência de eventos e lógica condicional.
- **Tarefas**: Ações específicas que realizam um trabalho, como chamar uma função Lambda ou fazer uma consulta de banco de dados.
- **Estados**: Passos individuais no fluxo de trabalho, como Task, Choice, Parallel, etc.

### Funcionalidades Chave

- **Configuração visual** e fácil de usar para fluxos de trabalho.
- **Tratamento** de erros, timeouts e processamento paralelo integrados.
- **Gerenciamento de estado** e rastreamento do progresso do fluxo de trabalho.
- **Processamento paralelo** em larga escala.
- **Integração** com diversos serviços AWS como Lambda, EC2, ECS, API Gateway, SQS, etc.

### Benefícios

- **Simplifica a orquestração** de aplicações baseadas em microsserviços.
- Melhora a **resiliência das aplicações** com tratamento automático de erros e retentativas.
- Separa a lógica do fluxo de trabalho da lógica de negócio, **facilitando o desenvolvimento**.
- **Escala automaticamente** para lidar com cargas de trabalho variáveis.
- **Modelo de cobrança baseado em volume**, pagando apenas pelo que for usado.

### Casos de Uso

- Orquestração de tarefas em sequência ou paralelo.
- Tomada de decisões baseadas em dados dentro do fluxo.
- Processamento de dados em etapas paralelas.
- Processamento dinâmico de elementos de dados.
- Fluxos de aprovação, pipelines de processamento de dados, resposta a incidentes de segurança, etc.

### Em resumo

***O AWS Step Functions fornece uma maneira poderosa e visual de criar fluxos de trabalho complexos, coordenando múltiplos serviços AWS de forma serverless e escalável.***

## Pré-Requisitos

- Conta na AWS
  - [Como criar uma conta na AWS ?](https://github.com/digitalinnovationone/aws-cloud-quickstart#como-criar-uma-conta-na-aws-)
- Conhecimento prévio em outros serviços AWS

## Recursos & Links Úteis

> [AWS Step Functions](https://aws.amazon.com/pt/step-functions/)
>
> [The AWS Step Functions Workshop](https://catalog.workshops.aws/stepfunctions/en-US/)
>
> [AWS Cloud Quick Start](https://github.com/digitalinnovationone/aws-cloud-quickstart)
>
> [Serverless e Amazon Bedrock](https://aws.amazon.com/pt/blogs/aws-brasil/como-criar-um-assistente-virtual-de-baixa-latencia-com-multiplos-modelos-usando-serverless-e-amazon-bedrock/)
>
> [AWS Step Functions Examples](https://github.com/aws-samples/aws-stepfunctions-examples)

## Processo de criação de uma Máquina de Estados

Passos para a criação de uma máquina de estados:

1. Utilizar um modelo como base
1. Selecionar implantar e executar
1. Ajustar as permissões
1. Modelar os processos e controles conforme o caso que se está desenvolvendo
1. Selecionar o modelo de IA
1. Ajustar a entrada do modelo de cada caixa conforme a etapa do processo
1. Da mesma forma, tratar a saída (armazenar, processar, ...) que irá compor com a próxima entrada

> **Nota**: No botão de código tem-se acesso ao código JSON da máquina de estado construída.

## Case exemplo

O exemplo que segue demonstra como o AWS Step Functions pode ser usado para orquestrar um processo de negócios combinando execução paralela, tomada de decisão condicional e encadeamento de tarefas.



Considerando um processo de negócios com uma série de etapas que compões o fluxo de trabalho, segue o código ASL como demonstração.

```json
{
  "Comment": "Processo de abertura de conta bancária",
  "StartAt": "ValidarDados",
  "States": {
    "ValidarDados": {
      "Type": "Parallel",
      "Branches": [
        {
          "StartAt": "ValidarNome",
          "States": {
            "ValidarNome": {
              "Type": "Task",
              "Resource": "arn:aws:lambda:us-east-1:123456789012:function:ValidarNome",
              "End": true
            }
          }
        },
        {
          "StartAt": "ValidarEndereco",
          "States": {
            "ValidarEndereco": {
              "Type": "Task",
              "Resource": "arn:aws:lambda:us-east-1:123456789012:function:ValidarEndereco",
              "End": true
            }
          }
        }
      ],
      "Next": "AnalisarResultados"
    },
    "AnalisarResultados": {
      "Type": "Choice",
      "Choices": [
        {
          "Variable": "$[0].valido",
          "BooleanEquals": false,
          "Next": "RejeitarConta"
        },
        {
          "Variable": "$[1].valido",
          "BooleanEquals": false,
          "Next": "RejeitarConta"
        }
      ],
      "Default": "AprovarConta"
    },
    "AprovarConta": {
      "Type": "Task",
      "Resource": "arn:aws:lambda:us-east-1:123456789012:function:AprovarConta",
      "Next": "NotificarCliente"
    },
    "RejeitarConta": {
      "Type": "Task",
      "Resource": "arn:aws:lambda:us-east-1:123456789012:function:RejeitarConta",
      "Next": "NotificarCliente"
    },
    "NotificarCliente": {
      "Type": "Task",
      "Resource": "arn:aws:lambda:us-east-1:123456789012:function:NotificarCliente",
      "End": true
    }
  }
}
```

Este fluxo de trabalho representa um processo de abertura de conta bancária e inclui as seguintes etapas:

- **ValidarDados:** Este é um estado Parallel que executa simultaneamente duas tarefas:
  - **ValidarNome:** Verifica se o nome do cliente é válido.
  - **ValidarEndereco:** Verifica se o endereço do cliente é válido.
- **AnalisarResultados:** Este é um estado Choice que avalia os resultados da validação:
  - Se alguma validação falhar, o fluxo segue para RejeitarConta.
  - Se ambas as validações forem bem-sucedidas, o fluxo segue para AprovarConta.
- **AprovarConta ou RejeitarConta:** Dependendo do resultado da análise, uma dessas tarefas é executada.
- **NotificarCliente:** Esta é a etapa final que notifica o cliente sobre o resultado do processo de abertura de conta.

## That's all

...Folks!!!
