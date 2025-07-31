# Guia Completo de CI/CD com Arquivos YAML (Azure DevOps, Jenkins, GitHub Actions)

Este guia irá te colocar no caminho para dominar Integração Contínua e Entrega/Implantação Contínua (CI/CD), abordando conceitos essenciais, melhores práticas e exemplos reais utilizando ferramentas como Azure DevOps, Jenkins e GitHub Actions.

## Introdução: O que é CI/CD?

CI/CD (_Continuous Integration / Continuous Delivery/Deployment_) é um conjunto de práticas que automatizam a entrega de software, garantindo que cada mudança de código seja compilada, testada e entregue de forma confiável e rápida.

Analogia: Pense em uma linha de produção de uma fábrica de carros ou uma pizzaria automatizada:

- CI: Montagem + inspeção de qualidade (build e testes).
- CD: Entrega do produto ao cliente (deploy para homologação ou produção).

## Estrutura Geral de um Arquivo de Pipeline YAML

Um arquivo `pipeline.yml` descreve, como código, as etapas para construir, testar e implantar sua aplicação. Componentes principais comuns a Azure DevOps, Jenkins (Declarative Pipeline) e GitHub Actions:

### 1. Trigger / Events

Define quando a pipeline será acionada:

- Azure DevOps:

```yaml
trigger:
- main
```

- GitHub Actions:

```yaml
on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - develop
```

- Jenkins: normalmente usa \*webhooks- configurados no SCM, ou cronjobs no `Jenkinsfile`.

### 2. Agents / Runners / Nodes

Define onde os jobs serão executados (máquina virtual, container, agente físico):

- Azure DevOps:

```yaml
pool:
  vmImage: 'ubuntu-latest'
```

- GitHub Actions:

```yaml
runs-on: ubuntu-latest
```

- Jenkins:

```groovy
agent any
```

### 3. Stages / Jobs / Workflows

Dividem a pipeline em fases lógicas:

- Azure DevOps: `stages` → `jobs` → `steps`
- GitHub Actions: `jobs` → `steps`
- Jenkins: `stages` → `steps`

Exemplo Azure:

```yaml
stages:
- stage: Build
  jobs:
  - job: Compile
    steps:
      - script: npm install && npm run build
```

Exemplo Jenkins:

```groovy
pipeline {
  stages {
    stage('Build') {
      steps {
        sh 'npm install && npm run build'
      }
    }
  }
}
```

GitHub Actions example

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - run: npm install && npm run build
```

### 4. Steps / Tasks / Actions

São os comandos ou tarefas executadas:

- Podem ser scripts (`script`, `run`) ou tarefas pré-definidas (build, deploy, lint).
- Azure DevOps: tasks como `UseNode@1`, `PublishPipelineArtifact@1`.
- GitHub Actions: actions reutilizáveis (ex: `actions/checkout@v3`, `actions/setup-node@v3`).
- Jenkins: steps com `sh`, `bat`, ou plugins (maven, docker, etc.).

Exemplo GitHub Actions:

```yaml
steps:
  - uses: actions/checkout@v3
  - uses: actions/setup-node@v3
    with:
      node-version: '18'
  - run: npm install && npm test
```

### 5. Environment Variables / Secrets

Configura variáveis de ambiente e credenciais:

- Azure DevOps: variável no `variables:` ou Variable Groups / Key Vault.
- GitHub Actions: `env:` ou segredos no repositório (`secrets.MY_TOKEN`).
- Jenkins: `environment {}` ou Jenkins Credentials.

Exemplo Azure:

```yaml
variables:
  NODE_ENV: production
```

### 6. Artifacts e Caching

Permitem guardar resultados de build ou dependências para outras etapas:

- Azure DevOps: `PublishPipelineArtifact@1`, `Cache@2`.
- GitHub Actions: `actions/cache@v3`.
- Jenkins: `stash/unstash` ou plugins de cache.

### 7. Deployments e Approvals

Executam entrega contínua, com possíveis aprovações:

- Azure DevOps: `deployment` jobs com `environment:` configurado e approvals.
- GitHub Actions: `environments:` com regras de proteção e reviewers.
- Jenkins: geralmente via stages manuais ou integração com ferramentas de release.

Exemplo Azure:

```yaml
- stage: Deploy
  dependsOn: Build
  jobs:
  - deployment: DeployToProd
    environment: Production
    strategy:
      runOnce:
        deploy:
          steps:
            - script: ./deploy.sh
```

## Boas Práticas Gerais para Pipelines YAML

- Versione sempre o pipeline no repositório.
- Falhe rápido (rodar testes cedo).
- Mantenha pipelines curtas (< 10-15 min se possível).
- Use cache de dependências.
- Proteja segredos e credenciais.
- Configure gates/approvals para produção.
- Reutilize templates para evitar duplicação.
- Monitore falhas e tempos de execução.

## Exemplo Simples (Node.js)

```yaml
trigger:
- main

pool:
  vmImage: 'ubuntu-latest'

steps:
- task: UseNode@1
  inputs:
    version: '18.x'
  displayName: 'Instalar Node.js'

- script: npm install
  displayName: 'Instalar dependências'

- script: npm run build
  displayName: 'Build da aplicação'

- script: npm test
  displayName: 'Executar testes'

- task: PublishPipelineArtifact@1
  inputs:
    artifactName: 'node-app'
    targetPath: '$(Build.ArtifactStagingDirectory)'
  displayName: 'Publicar artefato'
```

## Exemplo Simples (Python)

```yaml
trigger:
- main

pool:
  vmImage: 'ubuntu-latest'

steps:
- task: UsePythonVersion@0
  inputs:
    versionSpec: '3.10'
  displayName: 'Usar Python 3.10'

- script: |
    python -m pip install --upgrade pip
    pip install -r requirements.txt
  displayName: 'Instalar dependências'

- script: pytest
  displayName: 'Rodar testes'

- task: PublishPipelineArtifact@1
  inputs:
    artifactName: 'python-app'
    targetPath: '$(Build.ArtifactStagingDirectory)'
  displayName: 'Publicar artefato'
```

## Pipeline com Múltiplos Estágios (Build → Test → Deploy)

```yaml
stages:
- stage: Build
  jobs:
  - job: BuildAndTest
    pool: ubuntu-latest
    steps:
    - script: npm install && npm run build && npm test
    - task: PublishPipelineArtifact@1
      inputs:
        artifactName: 'app-build'
        targetPath: '$(Build.ArtifactStagingDirectory)'

- stage: Staging
  dependsOn: Build
  jobs:
  - deployment: DeployToStaging
    environment: 'Staging'
    strategy:
      runOnce:
        deploy:
          steps:
          - download: current
          - script: echo "Deploy em Homologação..."

- stage: Production
  dependsOn: Staging
  jobs:
  - deployment: DeployToProd
    environment: 'Production'
    strategy:
      runOnce:
        deploy:
          steps:
          - download: current
          - script: echo "Deploy em Produção..."
```

_(Aprovações manuais ou automáticas podem ser configuradas nos Environments antes do deploy em Produção.)_

## Conclusão

CI/CD é a base da entrega ágil e confiável de software. Usando Azure DevOps Pipelines com arquivos YAML, você automatiza todo o ciclo de desenvolvimento, reduz erros manuais e garante entregas rápidas.

> "Um bom pipeline é como uma linha de produção bem ajustada: rápido, seguro e sempre pronto para entregar valor ao usuário."

> Observação: Esta documentação reúne minhas anotações e materiais de estudo utilizados no dia a dia, organizados em um único documento com o objetivo de compartilhar conceitos e facilitar o entendimento para todos os interessados.

> Espero que seja útil e agregue valor aos seus estudos ou projetos!
