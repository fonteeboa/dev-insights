# Configurações de Linting x Boas praticas de desenvolvimento

Cada uma dessas configurações tem como objetivo melhorar a legibilidade, manutenibilidade e qualidade do código com base nas melhores praticas encontradas em : 

- Solid Code
- Clean Code
- Don't Repeat Yourself (Princípio DRY)
- You Ain't Gonna Need It (Princípio YAGNI)
- Keep It Simple, Stupid (Princípio KISS)

Exemplo de configurações:

- @typescript-eslint/no-explicit-any: Desencoraja o uso explícito do tipo any em TypeScript para promover tipos mais específicos e seguros.
- @typescript-eslint/no-unnecessary-type-assertion: Indica quando uma asserção de tipo não é necessária em TypeScript.
- Brace Style: Define o estilo de colocação de chaves para declarações de blocos de código, permitindo um estilo '1tbs' com exceção para declarações de linha única.
- Camelcase: Força o uso do estilo de nomenclatura 'camelCase'.
- Complexity: Limita a complexidade ciclomática de uma função para 5.
- Consistent This: Exige que a palavra-chave 'this' seja referenciada como 'self' para consistência.
- Func Names: Requer a declaração de nomes de funções para melhorar a clareza do código.
- Indent: Força a indentação de 3 espaços para cada nível de identação.
- Key Spacing: Define regras para espaçamento consistente em chaves de objetos e valores associados.
- Lines Between Class Members: Exige linhas em branco entre os membros de uma classe para melhor legibilidade.
- Max Depth: Limita a profundidade máxima de aninhamento para 3 níveis.
- Max Len: Limita o comprimento de uma linha de código para no máximo 120 caracteres.
- Max Lines Per Function: Limita o número máximo de linhas permitidas em uma função para 30 linhas.
- Max Nested Callbacks: Restringe o número máximo de callbacks aninhados para 2.
- Max Params: Limita o número máximo de parâmetros permitidos para 3.
- Max Statements: Estabelece um limite para o número máximo de declarações em uma função para 10.
- No Dupe Args: Impede a duplicação de argumentos em funções para evitar confusão e erros de execução.
- No Dupe Class Members: Impede a definição duplicada de membros de classe.
- No Dupe Else If: Proíbe a presença de blocos else if duplicados para evitar lógica redundante.
- No Dupe Keys: Proíbe chaves duplicadas em objetos.
- No Duplicate Case: Previne a declaração duplicada de casos em instruções de switch.
- No Duplicate Imports: Previne a importação duplicada de módulos.
- No Extra Semi: Evita o uso desnecessário de ponto e vírgula no final de instruções.
- No Magic Numbers: Desencoraja o uso direto de números mágicos no código, incentivando o uso de constantes descritivas.
- No Redeclare: Impede a redeclaração de variáveis.
- No Unused Expressions: Aponta expressões não utilizadas.
- No Unused Vars: Indica variáveis não utilizadas.
- No Use Before Define: Evita o uso de variáveis antes de sua declaração.
- Object Curly Spacing: Exige o uso consistente de espaçamento ao redor de chaves em objetos.

---

Estas configurações, quando aplicadas em um ambiente de desenvolvimento, ajudam a promover boas práticas e princípios de design de software, melhorando assim a qualidade do código produzido.

# Exemplo no arquivo: 

```
"rules": {
    "@typescript-eslint/no-explicit-any": "error",
    "@typescript-eslint/no-unnecessary-type-assertion": "error",
    "brace-style": ["error", "1tbs", { "allowSingleLine": true }],
    "camelcase": "error", 
    "complexity": ["error", 5],
    "consistent-this": ["error", "self"], 
    "func-names": "error", 
    "key-spacing": ["error", { "beforeColon": false, "afterColon": true }], 
    "lines-between-class-members": ["error", "always"],
    "max-depth": ["error", 3],
    "max-len": ["error", { "code": 200 }],
    "max-lines-per-function": ["error", { "max": 60 }], 
    "max-nested-callbacks": ["error", 2],
    "max-params": ["error", 3],
    "max-statements": ["error", 20],
    "no-dupe-args": "error",
    "no-dupe-class-members": "error",
    "no-dupe-else-if": "error",
    "no-dupe-keys": "error",
    "no-duplicate-case": "error",
    "no-duplicate-imports": "error",
    "no-extra-semi": "error",
    "no-magic-numbers": "error", 
    "no-redeclare": "error",
    "no-unused-expressions": "error",
    "no-unused-vars": "error",
    "no-use-before-define": "error", 
    "object-curly-spacing": ["error", "always"],
}

```

# Observações

### Referencia
As normas (rules) suportadas pelo eslint podem ser verificadas em seus repositórios. Como por exemplo ESlint (https://eslint.org/docs/latest/rules)

### Atenção
Adapte as configurações de acordo com a necessidade do seu projeto, porém, mantenha o compromisso contínuo de aprimorar e aperfeiçoar o código em busca da excelência.

### Tipos de configurações
No ESLint e em muitas outras ferramentas de linting, como Prettier, é possível configurar regras com diferentes níveis de severidade, como "error", "warn" e "off", que determinam como os problemas encontrados pelo linting devem ser tratados. Aqui está uma explicação geral desses níveis de severidade:

- Error: Quando uma regra está configurada como error, ela indica um problema sério ou uma prática que deve ser estritamente seguida.
Se uma regra com error for violada, isso geralmente impede a compilação ou a execução do código.
Esses problemas são tipicamente mais críticos e devem ser corrigidos antes de fazer commits, para manter a consistência e a qualidade do código.

- Warn (ou "Warning"): Quando uma regra está configurada como warn, ela indica um aviso sobre uma prática que pode ser problemática ou não desejada, mas que não é tão crítica quanto um erro.
Violações de regras configuradas como warn geralmente não impedem a execução ou compilação do código, mas são ressaltadas como áreas que podem precisar de atenção.
É uma boa prática corrigir problemas identificados como warn, mas eles podem ser menos urgentes do que os marcados como error.

- Off: Quando uma regra está configurada como off, isso significa que a regra está desativada e não será aplicada ao código.
Pode ser útil desativar temporariamente uma regra específica, especialmente se ela estiver causando problemas ou se não for relevante para o contexto atual de desenvolvimento. No entanto, desativar regras deve ser feito com cautela, pois pode levar a práticas inadequadas ou problemas futuros.

Ao escolher entre error e warn, normalmente error é preferível para problemas mais sérios, enquanto warn é usado para avisos menos críticos. O uso de off é mais para desabilitar temporariamente regras que podem não ser aplicáveis em certos contextos ou situações específicas de desenvolvimento.
