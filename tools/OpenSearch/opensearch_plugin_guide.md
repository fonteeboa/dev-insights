# Estendendo o OpenSearch Dashboards: Um Guia Completo de Plugins

## Índice
1. [Introdução](#introdução)
2. [Pré-requisitos](#pré-requisitos)
3. [Entendendo a Arquitetura](#entendendo-a-arquitetura)
4. [Criação do Plugin Passo a Passo](#criação-do-plugin-passo-a-passo)
5. [Recursos Avançados](#recursos-avançados)
6. [Testes e Depuração](#testes-e-depuração)
7. [Implantação](#implantação)
8. [Solução de Problemas](#solução-de-problemas)
9. [Recursos](#recursos)

---

## Introdução

### O que é um Plugin do OpenSearch Dashboards?

Um plugin do OpenSearch Dashboards é uma extensão modular que adiciona funcionalidades personalizadas à sua instalação do OpenSearch Dashboards. Os plugins podem:

- Criar novas visualizações e dashboards
- Adicionar fontes de dados personalizadas
- Implementar interfaces de busca especializadas
- Integrar serviços externos
- Estender funcionalidades existentes

### Por que Criar um Plugin?

Crie um plugin quando você precisar:

- **Personalizar a interface do usuário** além das configurações padrão
- **Adicionar recursos específicos do negócio** não disponíveis no OpenSearch principal
- **Integrar serviços ou APIs de terceiros**
- **Criar componentes reutilizáveis** em múltiplos dashboards
- **Implementar processamento ou análise de dados especializados**

---

## Pré-requisitos

Antes de começar, certifique-se de ter:

### Software Necessário

| Software | Versão Mínima | Finalidade |
|----------|---------------|------------|
| **Node.js** | 14.x ou superior | Ambiente de execução |
| **Yarn** | 1.22.x | Gerenciador de pacotes |
| **Git** | 2.x | Controle de versão |
| **Editor de Código** | Qualquer | VS Code recomendado |

* Observação: as versões mínimas podem variar conforme a versão do OpenSearch Dashboards utilizada. Sempre verifique a documentação oficial da release alvo para garantir compatibilidade total do ambiente de desenvolvimento, o exemplo acima foi utilizado na versão 2.11 do opensearch Dashboards.

### Conhecimento Necessário

- **JavaScript/TypeScript**: Nível básico a intermediário
- **React**: Compreensão de componentes e hooks
- **APIs REST**: Como criar e consumir APIs
- **Linha de Comando**: Uso básico de terminal/prompt de comando

### Verifique Sua Configuração

Execute estes comandos para verificar seu ambiente:

```bash
# Verificar versão do Node.js
node --version

# Verificar versão do Yarn
yarn --version

# Verificar versão do Git
git --version
```

---

## Entendendo a Arquitetura

### Visão Geral da Estrutura do Plugin

Antes de mergulhar no código, vamos entender como os plugins do OpenSearch Dashboards são organizados:

```
meu-plugin/
│
├── opensearch_dashboards.json    # Metadados e dependências do plugin
├── package.json                   # Configuração do pacote NPM
├── tsconfig.json                  # Configuração do TypeScript
│
├── public/                        # Código client-side (executa no navegador)
│   ├── index.ts                   # Ponto de entrada público
│   ├── plugin.ts                  # Classe principal do plugin
│   ├── application.tsx            # Inicialização do app React
│   ├── components/                # Componentes React
│   │   └── app.tsx               # Componente principal do app
│   └── types.ts                   # Definições de tipos TypeScript
│
└── server/                        # Código server-side (executa no backend)
    ├── index.ts                   # Ponto de entrada do servidor
    ├── plugin.ts                  # Classe do plugin do servidor
    ├── routes/                    # Definições de rotas da API
    └── types.ts                   # Tipos do lado do servidor
```

### Conceitos-Chave

#### 1. **Ciclo de Vida do Plugin**

Os plugins seguem um ciclo de vida específico:

1. **Inicialização**: Plugin é carregado e inicializado
2. **Setup**: Dependências são configuradas, rotas registradas
3. **Start**: Plugin se torna ativo e funcional
4. **Stop**: Limpeza quando o plugin é desativado

#### 2. **Arquitetura Cliente-Servidor**

- **Public (Cliente)**: Gerencia UI, interações do usuário, lógica do navegador
- **Server (Servidor)**: Gerencia endpoints da API, processamento de dados, comunicação com OpenSearch

#### 3. **Serviços Core**

O OpenSearch Dashboards fornece serviços core:

- **HTTP**: Criar rotas de API e fazer requisições
- **Notifications**: Exibir mensagens toast e alertas
- **SavedObjects**: Armazenar e recuperar dados do plugin
- **Application**: Registrar e navegar entre apps

---

## Criação do Plugin Passo a Passo

### Passo 1: Configure Seu Ambiente de Desenvolvimento

#### 1.1 Clone o Repositório do OpenSearch Dashboards

```bash
# Clone o repositório
git clone https://github.com/opensearch-project/OpenSearch-Dashboards.git

# Navegue para o diretório
cd OpenSearch-Dashboards

# Faça checkout da versão que você está visando (ex: $VERSION)
git checkout $VERSION
```

**Por quê?** Você precisa do código-fonte do OpenSearch Dashboards para desenvolver e testar plugins localmente.

#### 1.2 Instale as Dependências

```bash
# Instale todas as dependências (pode levar 10-15 minutos)
yarn osd bootstrap
```

**O que isso faz?** 
- Instala todos os pacotes necessários
- Vincula dependências entre plugins
- Prepara o ambiente de desenvolvimento

**⏱️ Pegue um café!** Esse processo leva tempo, mas só precisa ser feito uma vez.

---

### Passo 2: Gere Seu Plugin

#### 2.1 Use o Gerador de Plugins

```bash
# Execute o gerador a partir do diretório raiz do OpenSearch Dashboards
node scripts/generate_plugin.js meu-plugin-customizado

# Quando solicitado, responda:
# - Gerar um plugin em ./plugins? Sim
# - Você gostaria de criar o plugin em uma pasta diferente? Não
# - Seu plugin deve ter código server-side? Sim
# - Seu plugin deve ter um componente de UI? Sim
```

**O que acabou de acontecer?**
- Um esqueleto completo do plugin foi criado em `plugins/meu-plugin-customizado`
- Todos os arquivos e pastas necessários foram gerados
- Configurações básicas do TypeScript foram definidas

#### 2.2 Navegue para Seu Plugin

```bash
cd plugins/meu-plugin-customizado
```

---

### Passo 3: Configure os Metadados do Plugin

#### 3.1 Entendendo `opensearch_dashboards.json`

Abra `opensearch_dashboards.json` e atualize:

```json
{
  "id": "meuPluginCustomizado",
  "version": "1.0.0",
  "opensearchDashboardsVersion": "$VERSION.0",
  "server": true,
  "ui": true,
  "requiredPlugins": ["data"],
  "optionalPlugins": ["visualizations"],
  "requiredBundles": []
}
```

**Explicação dos Campos:**

- **`id`**: Identificador único (camelCase, sem espaços)
- **`version`**: Versão do seu plugin (segue versionamento semântico)
- **`opensearchDashboardsVersion`**: Versão compatível do OSD
- **`server`**: Defina como `true` se o plugin tem código backend
- **`ui`**: Defina como `true` se o plugin tem UI frontend
- **`requiredPlugins`**: Plugins que DEVEM estar instalados
- **`optionalPlugins`**: Plugins que melhoram a funcionalidade se disponíveis

#### 3.2 Configure `package.json`

```json
{
  "name": "meu-plugin-customizado",
  "version": "1.0.0",
  "description": "Um plugin customizado para OpenSearch Dashboards",
  "main": "target/meu-plugin-customizado",
  "opensearchDashboards": {
    "version": "$VERSION.0",
    "templateVersion": "1.0.0"
  },
  "scripts": {
    "build": "yarn plugin-helpers build",
    "plugin-helpers": "node ../../scripts/plugin_helpers"
  },
  "devDependencies": {
    "@types/react": "^17.0.3"
  }
}
```

---

### Passo 4: Construa o Client-Side (Frontend)

#### 4.1 Crie a Classe do Plugin (`public/plugin.ts`)

```typescript
import { CoreSetup, CoreStart, Plugin } from '../../../src/core/public';

// Define o que seu plugin fornece a outros plugins durante o setup
export interface MeuPluginCustomizadoSetup {}

// Define o que seu plugin fornece após iniciar
export interface MeuPluginCustomizadoStart {}

export class MeuPluginCustomizado implements Plugin<MeuPluginCustomizadoSetup, MeuPluginCustomizadoStart> {
  
  /**
   * Setup é chamado quando o plugin é inicializado
   * É aqui que você registra sua aplicação
   */
  public setup(core: CoreSetup) {
    // Registrar a aplicação principal
    core.application.register({
      id: 'meuPluginCustomizado',
      title: 'Meu Plugin Customizado',
      
      // Onde o app aparece na navegação
      category: {
        id: 'categoriaCustomizada',
        label: 'Ferramentas Customizadas',
        order: 1000,
      },
      
      // A função que renderiza seu app
      mount: async (params) => {
        // Carregamento lazy do componente do app
        const { renderApp } = await import('./application');
        return renderApp(core, params);
      },
    });

    return {};
  }

  /**
   * Start é chamado quando todos os plugins estão configurados
   * É aqui que você pode usar serviços de outros plugins
   */
  public start(core: CoreStart) {
    return {};
  }

  /**
   * Stop é chamado quando o plugin está sendo desligado
   * Limpe recursos aqui
   */
  public stop() {
    // Código de limpeza aqui
  }
}
```

**Pontos-Chave:**

- **`setup()`**: Executa uma vez durante a inicialização - registre seu app aqui
- **`mount()`**: Carrega lazy seu app React para melhor performance
- **`start()`**: Executa depois que todos os plugins estão prontos - use dependências de plugins aqui

#### 4.2 Crie o Ponto de Entrada (`public/index.ts`)

```typescript
import { PluginInitializerContext } from '../../../src/core/public';
import { MeuPluginCustomizado } from './plugin';

/**
 * Este é o ponto de entrada para seu plugin
 * O OpenSearch Dashboards chama esta função para criar sua instância do plugin
 */
export function plugin(initializerContext: PluginInitializerContext) {
  return new MeuPluginCustomizado();
}

// Exportar tipos do plugin para outros plugins usarem
export { MeuPluginCustomizadoSetup, MeuPluginCustomizadoStart } from './plugin';
```

#### 4.3 Crie a Aplicação React (`public/application.tsx`)

```typescript
import React from 'react';
import ReactDOM from 'react-dom';
import { AppMountParameters, CoreStart } from '../../../src/core/public';
import { MeuAppPlugin } from './components/app';

/**
 * Renderiza a aplicação React no DOM
 * @param core - Serviços core do OpenSearch Dashboards
 * @param params - Parâmetros de montagem incluindo o elemento DOM
 */
export const renderApp = (
  core: CoreStart,
  params: AppMountParameters
) => {
  // Renderizar o app React
  ReactDOM.render(
    <MeuAppPlugin coreStart={core} />,
    params.element
  );

  // Retornar função de limpeza
  return () => {
    ReactDOM.unmountComponentAtNode(params.element);
  };
};
```

#### 4.4 Crie o Componente Principal (`public/components/app.tsx`)

```typescript
import React, { useState } from 'react';
import {
  EuiPage,
  EuiPageBody,
  EuiPageContent,
  EuiPageContentBody,
  EuiPageHeader,
  EuiTitle,
  EuiText,
  EuiButton,
  EuiSpacer,
} from '@elastic/eui';
import { CoreStart } from '../../../../src/core/public';

interface MeuAppPluginProps {
  coreStart: CoreStart;
}

export const MeuAppPlugin: React.FC<MeuAppPluginProps> = ({ coreStart }) => {
  const [contador, setContador] = useState(0);

  const handleClick = () => {
    setContador(contador + 1);
    
    // Mostrar notificação toast de sucesso
    coreStart.notifications.toasts.addSuccess({
      title: 'Botão Clicado!',
      text: `Contador agora é ${contador + 1}`,
    });
  };

  return (
    <EuiPage>
      <EuiPageBody>
        <EuiPageHeader>
          <EuiTitle size="l">
            <h1>Meu Plugin Customizado</h1>
          </EuiTitle>
        </EuiPageHeader>

        <EuiPageContent>
          <EuiPageContentBody>
            <EuiText>
              <h2>Bem-vindo ao Seu Plugin Customizado!</h2>
              <p>
                Este é um template básico de plugin. Você pode estendê-lo com sua própria
                funcionalidade.
              </p>
            </EuiText>

            <EuiSpacer size="l" />

            <EuiText>
              <p>Contador: <strong>{contador}</strong></p>
            </EuiText>

            <EuiSpacer size="m" />

            <EuiButton fill onClick={handleClick}>
              Incrementar Contador
            </EuiButton>
          </EuiPageContentBody>
        </EuiPageContent>
      </EuiPageBody>
    </EuiPage>
  );
};
```

**O que está acontecendo aqui?**

- **Componentes Elastic UI (EUI)**: Usando componentes UI pré-construídos para consistência
- **Gerenciamento de Estado**: Usando React hooks (`useState`) para interatividade
- **Serviços Core**: Acessando notificações através do `coreStart`

---

### Passo 5: Construa o Server-Side (Backend)

#### 5.1 Crie o Plugin do Servidor (`server/plugin.ts`)

```typescript
import {
  CoreSetup,
  CoreStart,
  Plugin,
  Logger,
  PluginInitializerContext,
} from '../../../src/core/server';
import { schema } from '@osd/config-schema';

export class MeuPluginCustomizadoServidor implements Plugin {
  private readonly logger: Logger;

  constructor(initializerContext: PluginInitializerContext) {
    this.logger = initializerContext.logger.get();
  }

  public setup(core: CoreSetup) {
    this.logger.info('MeuPluginCustomizado: Configurando');

    // Criar um roteador para endpoints da API
    const router = core.http.createRouter();

    // Registrar um endpoint GET
    router.get(
      {
        path: '/api/meu-plugin-customizado/hello',
        validate: false, // Adicionaremos validação depois
      },
      async (context, request, response) => {
        this.logger.info('Endpoint hello chamado');
        
        return response.ok({
          body: {
            message: 'Olá do meu plugin customizado!',
            timestamp: new Date().toISOString(),
          },
        });
      }
    );

    // Registrar um endpoint POST com dados
    router.post(
      {
        path: '/api/meu-plugin-customizado/data',
        validate: {
          body: schema.object({
            nome: schema.string(),
            valor: schema.number(),
          }),
        },
      },
      async (context, request, response) => {
        const { nome, valor } = request.body;
        
        this.logger.info(`Dados recebidos: ${nome} = ${valor}`);
        
        return response.ok({
          body: {
            sucesso: true,
            recebido: { nome, valor },
          },
        });
      }
    );

    return {};
  }

  public start(core: CoreStart) {
    this.logger.info('MeuPluginCustomizado: Iniciado');
    return {};
  }

  public stop() {
    this.logger.info('MeuPluginCustomizado: Parando');
  }
}
```

#### 5.2 Adicione Validação de Requisições (`server/plugin.ts` - aprimorado)

```typescript
import { schema } from '@osd/config-schema';

// Dentro da definição da sua rota:
router.get(
  {
    path: '/api/meu-plugin-customizado/busca',
    validate: {
      query: schema.object({
        termo: schema.string({ minLength: 1, maxLength: 100 }),
        pagina: schema.number({ defaultValue: 1, min: 1 }),
        tamanho: schema.number({ defaultValue: 10, min: 1, max: 100 }),
      }),
    },
  },
  async (context, request, response) => {
    const { termo, pagina, tamanho } = request.query;
    
    // Sua lógica de busca aqui
    
    return response.ok({
      body: { resultados: [], total: 0 },
    });
  }
);
```

**Por que a validação importa:**

- **Segurança**: Previne entradas maliciosas
- **Integridade de Dados**: Garante tipos de dados corretos
- **Contrato de API Claro**: Documenta parâmetros esperados

#### 5.3 Crie o Ponto de Entrada do Servidor (`server/index.ts`)

```typescript
import { PluginInitializerContext } from '../../../src/core/server';
import { MeuPluginCustomizadoServidor } from './plugin';

export function plugin(initializerContext: PluginInitializerContext) {
  return new MeuPluginCustomizadoServidor(initializerContext);
}

export { MeuPluginCustomizadoServidor as Plugin };
```

---

### Passo 6: Conecte Frontend ao Backend

#### 6.1 Crie um Serviço de API (`public/services/api.ts`)

```typescript
import { HttpSetup } from '../../../../src/core/public';

export class ApiService {
  private http: HttpSetup;

  constructor(http: HttpSetup) {
    this.http = http;
  }

  /**
   * Buscar mensagem hello do backend
   */
  async getHello(): Promise<{ message: string; timestamp: string }> {
    try {
      const response = await this.http.get('/api/meu-plugin-customizado/hello');
      return response;
    } catch (error) {
      throw new Error(`Falha ao buscar hello: ${error.message}`);
    }
  }

  /**
   * Enviar dados para o backend
   */
  async enviarDados(nome: string, valor: number): Promise<any> {
    try {
      const response = await this.http.post('/api/meu-plugin-customizado/data', {
        body: JSON.stringify({ nome, valor }),
      });
      return response;
    } catch (error) {
      throw new Error(`Falha ao enviar dados: ${error.message}`);
    }
  }
}
```

#### 6.2 Use a API no Seu Componente

```typescript
// Em public/components/app.tsx
import React, { useState, useEffect } from 'react';
import { ApiService } from '../services/api';

export const MeuAppPlugin: React.FC<MeuAppPluginProps> = ({ coreStart }) => {
  const [mensagem, setMensagem] = useState<string>('');
  const [carregando, setCarregando] = useState<boolean>(true);

  useEffect(() => {
    const api = new ApiService(coreStart.http);
    
    const buscarDados = async () => {
      try {
        const dados = await api.getHello();
        setMensagem(dados.message);
      } catch (error) {
        coreStart.notifications.toasts.addDanger({
          title: 'Erro',
          text: error.message,
        });
      } finally {
        setCarregando(false);
      }
    };

    buscarDados();
  }, [coreStart]);

  if (carregando) {
    return <div>Carregando...</div>;
  }

  return (
    <EuiPage>
      {/* Sua UI aqui */}
      <EuiText><p>{mensagem}</p></EuiText>
    </EuiPage>
  );
};
```

---

### Passo 7: Compile e Teste Seu Plugin

#### 7.1 Modo de Desenvolvimento

```bash
# Do diretório raiz do OpenSearch Dashboards
yarn start

# Isso irá:
# - Iniciar o servidor de desenvolvimento
# - Observar mudanças de arquivos
# - Habilitar hot reloading
# - Executar em http://localhost:5601
```

**Acesse seu plugin:**
1. Abra o navegador em `http://localhost:5601`
2. Procure por "Ferramentas Customizadas" na barra lateral esquerda
3. Clique em "Meu Plugin Customizado"

#### 7.2 Compilar para Produção

```bash
# Navegue para o diretório do seu plugin
cd plugins/meu-plugin-customizado

# Compile o plugin
yarn build

# Isso cria um arquivo ZIP em:
# build/meu-plugin-customizado-1.0.0.zip
```

---

## Recursos Avançados

### Trabalhando com Dados do OpenSearch

#### Consultar OpenSearch do Backend

```typescript
// Em server/plugin.ts
router.get(
  {
    path: '/api/meu-plugin-customizado/buscar-dados',
    validate: {
      query: schema.object({
        indice: schema.string(),
        consulta: schema.string(),
      }),
    },
  },
  async (context, request, response) => {
    const { indice, consulta } = request.query;
    
    try {
      // Obter cliente OpenSearch
      const client = context.core.opensearch.client.asCurrentUser;
      
      // Realizar busca
      const respostaBusca = await client.search({
        index: indice,
        body: {
          query: {
            match: {
              _all: consulta,
            },
          },
        },
      });
      
      return response.ok({
        body: {
          hits: respostaBusca.body.hits.hits,
          total: respostaBusca.body.hits.total,
        },
      });
    } catch (error) {
      this.logger.error(`Busca falhou: ${error}`);
      return response.customError({
        statusCode: 500,
        body: { message: 'Busca falhou' },
      });
    }
  }
);
```

### Adicionando Configuração do Plugin

#### Definir Schema de Configuração (`server/index.ts`)

```typescript
import { schema, TypeOf } from '@osd/config-schema';

export const configSchema = schema.object({
  habilitado: schema.boolean({ defaultValue: true }),
  chaveApi: schema.string({ defaultValue: '' }),
  maxResultados: schema.number({ defaultValue: 100, min: 1, max: 1000 }),
});

export type MeuPluginConfig = TypeOf<typeof configSchema>;

export const config = {
  schema: configSchema,
};
```

#### Usar Configuração no Plugin

```typescript
// Em server/plugin.ts
export class MeuPluginCustomizadoServidor implements Plugin {
  private config: MeuPluginConfig;

  constructor(initializerContext: PluginInitializerContext) {
    this.logger = initializerContext.logger.get();
    this.config = initializerContext.config.get<MeuPluginConfig>();
  }

  public setup(core: CoreSetup) {
    if (!this.config.habilitado) {
      this.logger.info('Plugin está desabilitado');
      return {};
    }

    // Usar valores de configuração
    const maxResultados = this.config.maxResultados;
    // ...
  }
}
```

#### Arquivo de Configuração do Usuário

Os usuários podem configurar seu plugin em `config/opensearch_dashboards.yml`:

```yaml
meuPluginCustomizado:
  habilitado: true
  chaveApi: "sua-chave-api-aqui"
  maxResultados: 50
```

### Criando Visualizações Customizadas

```typescript
// Em public/plugin.ts
public setup(core: CoreSetup, { visualizations }: SetupDeps) {
  // Registrar visualização customizada
  visualizations.createReactVisualization({
    name: 'minha_viz_customizada',
    title: 'Minha Visualização Customizada',
    icon: 'visArea',
    description: 'Uma visualização customizada para dados especializados',
    visConfig: {
      component: MeuComponenteVizCustomizado,
    },
    editorConfig: {
      optionsTemplate: MinhasOpcoesVizCustomizadas,
    },
    requestHandler: 'custom',
    responseHandler: 'none',
  });
}
```

### Objetos Salvos

#### Definir Tipo de Objeto Salvo

```typescript
// Em server/plugin.ts
public setup(core: CoreSetup) {
  core.savedObjects.registerType({
    name: 'meu-objeto-customizado',
    hidden: false,
    namespaceType: 'single',
    mappings: {
      properties: {
        titulo: { type: 'text' },
        descricao: { type: 'text' },
        config: { type: 'object', enabled: false },
      },
    },
    migrations: {},
  });
}
```

#### Usar Objetos Salvos

```typescript
// Criar
const objetoSalvo = await context.core.savedObjects.client.create(
  'meu-objeto-customizado',
  {
    titulo: 'Meu Objeto',
    descricao: 'Descrição aqui',
    config: { configuracao1: 'valor1' },
  }
);

// Ler
const objeto = await context.core.savedObjects.client.get(
  'meu-objeto-customizado',
  'id-do-objeto'
);

// Atualizar
await context.core.savedObjects.client.update(
  'meu-objeto-customizado',
  'id-do-objeto',
  { titulo: 'Título Atualizado' }
);

// Deletar
await context.core.savedObjects.client.delete(
  'meu-objeto-customizado',
  'id-do-objeto'
);
```

---

## Testes e Depuração

### Testes Unitários

#### Instalar Dependências de Teste

```bash
yarn add --dev @testing-library/react @testing-library/jest-dom
```

#### Exemplo de Teste de Componente

```typescript
// public/components/__tests__/app.test.tsx
import React from 'react';
import { render, screen, fireEvent } from '@testing-library/react';
import { MeuAppPlugin } from '../app';

describe('MeuAppPlugin', () => {
  const mockCoreStart = {
    notifications: {
      toasts: {
        addSuccess: jest.fn(),
      },
    },
  };

  it('renderiza o título', () => {
    render(<MeuAppPlugin coreStart={mockCoreStart as any} />);
    expect(screen.getByText('Meu Plugin Customizado')).toBeInTheDocument();
  });

  it('incrementa contador ao clicar no botão', () => {
    render(<MeuAppPlugin coreStart={mockCoreStart as any} />);
    const botao = screen.getByText('Incrementar Contador');
    
    fireEvent.click(botao);
    
    expect(screen.getByText('Contador: 1')).toBeInTheDocument();
  });
});
```

#### Executar Testes

```bash
yarn test
```

### Dicas de Depuração

#### Habilitar Logging de Debug

```typescript
// No seu plugin
this.logger.debug('Mensagem de debug aqui');
this.logger.info('Mensagem de info');
this.logger.warn('Mensagem de aviso');
this.logger.error('Mensagem de erro');
```

#### DevTools do Navegador

1. Abra DevTools do navegador (F12)
2. Vá para a aba Sources
3. Encontre os arquivos do seu plugin em `localhost:5601`
4. Defina breakpoints
5. Interaja com seu plugin para acionar breakpoints

#### Depuração Server-Side

Adicione isso à sua configuração de lançamento:

```json
{
  "type": "node",
  "request": "launch",
  "name": "Debug OpenSearch Dashboards",
  "program": "${workspaceFolder}/scripts/opensearch_dashboards",
  "args": ["--dev", "--no-base-path"],
  "console": "integratedTerminal"
}
```

---

## Implantação

### Instalando Seu Plugin

#### Método 1: Instalar do ZIP

```bash
# Compile seu plugin primeiro
cd plugins/meu-plugin-customizado
yarn build

# Instale o plugin
bin/opensearch-dashboards-plugin install file:///caminho/para/meu-plugin-customizado-1.0.0.zip

# Reinicie o OpenSearch Dashboards
```

#### Método 2: Instalar de URL

```bash
bin/opensearch-dashboards-plugin install https://exemplo.com/meu-plugin-customizado-1.0.0.zip
```

#### Método 3: Instalação Manual

```bash
# Copie o plugin para o diretório de plugins
cp -r meu-plugin-customizado /caminho/para/opensearch-dashboards/plugins/

# Reinicie o OpenSearch Dashboards
```

### Removendo Seu Plugin

```bash
bin/opensearch-dashboards-plugin remove meu-plugin-customizado

# Reinicie o OpenSearch Dashboards
```

### Atualizando Seu Plugin

```bash
# Remova a versão antiga
bin/opensearch-dashboards-plugin remove meu-plugin-customizado

# Instale a nova versão
bin/opensearch-dashboards-plugin install file:///caminho/para/meu-plugin-customizado-2.0.0.zip

# Reinicie o OpenSearch Dashboards
```

---

## Solução de Problemas

### Problemas Comuns e Soluções

#### Problema: Plugin Não Carrega

**Sintomas:**
- Plugin não aparece na barra lateral
- Mensagens de erro no console

**Soluções:**
```bash
# 1. Verifique se o ID do plugin corresponde em todos os arquivos
# 2. Verifique se opensearch_dashboards.json é válido
# 3. Verifique os logs do servidor:
tail -f /var/log/opensearch-dashboards/opensearch-dashboards.log

# 4. Reinicie com logging verboso:
bin/opensearch-dashboards --verbose
```

#### Problema: Falha na Compilação

**Sintomas:**
- `yarn build` produz erros

**Soluções:**
```bash
# 1. Limpe o cache e recompile
rm -rf build/ target/ node_modules/
yarn install
yarn build

# 2. Verifique erros do TypeScript:
yarn type-check

# 3. Verifique se todas as importações estão corretas
```

#### Problema: Chamadas de API Falham

**Sintomas:**
- Erros 404 em endpoints da API
- Erros de CORS

**Soluções:**
```typescript
// 1. Verifique o registro da rota:
router.get(
  {
    path: '/api/meu-plugin-customizado/endpoint',  // Deve começar com /api/
    validate: false,
  },
  handler
);

// 2. Verifique os logs do servidor em busca de erros

// 3. Teste o endpoint diretamente:
curl http://localhost:5601/api/meu-plugin-customizado/endpoint
```

#### Problema: Hot Reload Não Funciona

**Soluções:**
```bash
# 1. Pare o servidor de desenvolvimento
# 2. Limpe artefatos de build:
rm -rf optimize/ .cache/

# 3. Reinicie o servidor de desenvolvimento:
yarn start
```

### Otimização de Performance

#### Carregamento Lazy

```typescript
// Em vez de:
import { ComponentePesado } from './componente_pesado';

// Use:
const ComponentePesado = React.lazy(() => import('./componente_pesado'));

// No seu render:
<React.Suspense fallback={<div>Carregando...</div>}>
  <ComponentePesado />
</React.Suspense>
```

#### Minimizar Tamanho do Bundle

```typescript
// Evite importar bibliotecas inteiras:
// ❌ Ruim:
import _ from 'lodash';

// ✅ Bom:
import debounce from 'lodash/debounce';
```

---

## Checklist de Melhores Práticas

### Desenvolvimento

- [ ] Use TypeScript para type safety
- [ ] Siga as diretrizes do Elastic UI Framework
- [ ] Implemente tratamento de erros adequado
- [ ] Adicione estados de carregamento para operações assíncronas
- [ ] Valide todas as entradas do usuário
- [ ] Use nomes significativos para variáveis e funções
- [ ] Adicione comentários para lógica complexa

### Segurança

- [ ] Valide todas as entradas da API com schemas
- [ ] Sanitize dados fornecidos pelo usuário
- [ ] Use autenticação/autorização adequada
- [ ] Não exponha dados sensíveis nos logs
- [ ] Implemente limitação de taxa para APIs

### Performance

- [ ] Faça carregamento lazy de componentes pesados
- [ ] Minimize o tamanho do bundle
- [ ] Faça debounce de operações frequentes
- [ ] Use paginação para grandes conjuntos de dados
- [ ] Faça cache de dados acessados frequentemente

### Testes

- [ ] Escreva testes unitários para componentes
- [ ] Escreva testes de integração para APIs
- [ ] Teste cenários de erro
- [ ] Teste com diferentes conjuntos de dados
- [ ] Realize testes cross-browser

### Documentação

- [ ] Documente todas as APIs públicas
- [ ] Forneça exemplos de uso
- [ ] Documente opções de configuração
- [ ] Inclua guia de solução de problemas
- [ ] Mantenha o README atualizado

---

## Recursos

### Documentação Oficial

- **Docs do OpenSearch Dashboards**: https://opensearch.org/docs/latest/dashboards/
- **Desenvolvimento de Plugins**: https://opensearch.org/docs/latest/dashboards/dev-guide/
- **Referência da API**: https://opensearch.org/docs/latest/api-reference/

### Repositórios GitHub

- **OpenSearch Dashboards**: https://github.com/opensearch-project/OpenSearch-Dashboards
- **Plugins de Exemplo**: https://github.com/opensearch-project/OpenSearch-Dashboards/tree/main/src/plugins
- **Template de Plugin**: https://github.com/opensearch-project/opensearch-dashboards-plugin-template

### Framework de UI

- **Elastic UI Framework**: https://elastic.github.io/eui/
- **Componentes EUI**: https://elastic.github.io/eui/#/navigation/
- **Diretrizes de Design**: https://elastic.github.io/eui/#/guidelines/

### Comunidade

- **Fórum OpenSearch**: https://forum.opensearch.org/
- **Canal Slack**: https://opensearch.org/slack
- **Stack Overflow**: Tag `opensearch-dashboards`

### Recursos de Aprendizado

- **Documentação React**: https://react.dev/
- **Manual TypeScript**: https://www.typescriptlang.org/docs/
- **Melhores Práticas de API REST**: https://restfulapi.net/

---

## Referência Rápida

### Comandos Comuns

```bash
# Gerar plugin
node scripts/generate_plugin.js <nome-do-plugin>

# Instalar dependências
yarn osd bootstrap

# Iniciar servidor de desenvolvimento
yarn start

# Compilar plugin
yarn build

# Executar testes
yarn test

# Instalar plugin
bin/opensearch-dashboards-plugin install <caminho-do-plugin>

# Remover plugin
bin/opensearch-dashboards-plugin remove <nome-do-plugin>

# Listar plugins instalados
bin/opensearch-dashboards-plugin list
```

### Snippets de Código Úteis

#### Mostrar Notificação Toast

```typescript
coreStart.notifications.toasts.addSuccess('Mensagem de sucesso');
coreStart.notifications.toasts.addWarning('Mensagem de aviso');
coreStart.notifications.toasts.addDanger('Mensagem de erro');
```

#### Navegar para Outro App

```typescript
coreStart.application.navigateToApp('discover', {
  path: '#/view/algum-id',
});
```

#### Fazer Requisição HTTP

```typescript
const response = await coreStart.http.get('/api/endpoint');
const data = await coreStart.http.post('/api/endpoint', {
  body: JSON.stringify({ chave: 'valor' }),
});
```
