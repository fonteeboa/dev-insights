# Configurações de Linting x Boas praticas de desenvolvimento

Cada uma dessas configurações tem como objetivo melhorar a legibilidade, manutenibilidade e qualidade do código com base nas melhores praticas encontradas em : 

- Solid Code
- Clean Code
- Don't Repeat Yourself (Princípio DRY)
- You Ain't Gonna Need It (Princípio YAGNI)
- Keep It Simple, Stupid (Princípio KISS)

Exemplo de configurações:

- Typescript Eslint Ban Ts Comment: Impede o uso de comentários específicos do TypeScript.
- Typescript Eslint Explicit Module Boundary Types: Exige a definição explícita dos tipos nos limites de módulos.
- Typescript Eslint No Array Constructor: Desencoraja o uso do construtor de arrays.
- Typescript Eslint No Explicit Any: Desencoraja o uso explícito do tipo "any".
- Typescript Eslint No Loss Of Precision: Previne a perda de precisão em operações numéricas.
- Typescript Eslint No Non Null Assertion: Previne a negação de valores nulos.
- Typescript Eslint No This Alias: Desencoraja o uso de aliases para "this".
- Typescript Eslint No Unnecessary Type Assertion: Desencoraja a assertividade desnecessária de tipos.
- Typescript Eslint No Unused Vars: Identifica variáveis que não estão sendo usadas.
- Typescript Eslint No Use Before Define: Identifica o uso de variáveis antes de serem definidas.
- Typescript Eslint No Var Requires: Desencoraja o uso de 'require' para módulos em TypeScript.
- Brace Style: Enforce estilos específicos de formatação de chaves.
- Camelcase: Garante o uso consistente de camelCase.
- Complexity: Limite de complexidade para funções.
- Consistent This: Define a consistência do uso de "this".
- Func Name Matching: Verifica a correspondência de nomes de funções.
- Func Names: Verifica a presença de nomes em funções.
- Key Spacing: Regula o espaçamento em chaves de objetos.
- Line Comment Position: Regula a posição de comentários de linha.
- Lines Between Class Members: Controla a quantidade de linhas entre membros de classes.
- Max Depth: Define a profundidade máxima de estruturas de código.
- Max Len: Define o comprimento máximo de linhas de código.
- Max Lines Per Function: Define o número máximo de linhas por função.
- Max Nested Callbacks: Define o número máximo de callbacks aninhados.
- Max Params: Define o número máximo de parâmetros em funções.
- Max Statements: Define o número máximo de declarações por função.
- No Compare Neg Zero: Impede a comparação com -0.
- No Console: Impede o uso de console.log e console.error em produção.
- No Debugger: Impede o uso de debugger em produção.
- No Dupe Args: Impede argumentos duplicados em funções.
- No Dupe Class Members: Impede membros duplicados em classes.
- No Dupe Else If: Impede blocos else-if duplicados.
- No Dupe Keys: Impede chaves de objeto duplicadas.
- No Duplicate Case: Impede casos duplicados em switch.
- No Duplicate Imports: Impede importações duplicadas.
- No Empty: Impede blocos de código vazios.
- No Ex Assign: Impede a atribuição a exceções.
- No Extra Boolean Cast: Impede a conversão booleana redundante.
- No Extra Semi: Impede ponto e vírgula extra.
- No Irregular Whitespace: Impede espaços em branco irregulares.
- No Magic Numbers: Identifica o uso direto de números mágicos.
- No Mixed Spaces And Tabs: Impede a mistura de espaços e tabs.
- No Prototype Builtins: Previne o uso incorreto de métodos para protótipos.
- No Redeclare: Impede a redeclaração de variáveis.
- No Undef: Identifica o uso de variáveis não definidas.
- No Unexpected Multiline: Impede quebras de linha inesperadas.
- No Unused Expressions: Identifica expressões não utilizadas.
- No Useless Escape: Impede escapes de caracteres desnecessários.
- No Useless Rename: Impede renomeações desnecessárias.
- No Var: Impede o uso de 'var' em favor de 'let' ou 'const'.
- Object Curly Spacing: Define o espaçamento em chaves de objetos.
- React Hooks Exhaustive Deps: Garante dependências completas em Hooks do React.
- React Hooks Rules Of Hooks: Garante a observância das regras de Hooks do React.
- React Display Name: Verifica a presença de 'displayName' em componentes do React.
- React No Deprecated: Impede o uso de métodos descontinuados no React.
- React No Direct Mutation State: Impede a mutação direta do estado no React.
- React Prop Types: Desativa a verificação de tipos de propriedades no React.
- Yoda: Impede condições 'Yoda style'.

---

Estas configurações, quando aplicadas em um ambiente de desenvolvimento, ajudam a promover boas práticas e princípios de design de software, melhorando assim a qualidade do código produzido.

# Exemplo no arquivo: 

```
"rules": {
    "@typescript-eslint/ban-ts-comment": "warn",
    "@typescript-eslint/explicit-module-boundary-types": "warn",
    "@typescript-eslint/no-array-constructor": "warn",
    "@typescript-eslint/no-explicit-any": "warn",
    "@typescript-eslint/no-explicit-any": "warn",
    "@typescript-eslint/no-loss-of-precision": "warn",
    "@typescript-eslint/no-non-null-assertion": "warn",
    "@typescript-eslint/no-this-alias": "warn",
    "@typescript-eslint/no-unnecessary-type-assertion": "warn",
    "@typescript-eslint/no-unused-vars": "warn",
    "@typescript-eslint/no-unused-vars": "warn",
    "@typescript-eslint/no-use-before-define": "warn",
    "@typescript-eslint/no-var-requires": "warn",
    "brace-style": ["warn", "1tbs", { "allowSingleLine": true }],
    "camelcase": "warn",
    "complexity": ["warn", 5],
    "consistent-this": ["warn", "self"],
    "func-name-matching": "warn",
    "func-names": "warn",
    "key-spacing": ["warn", { "beforeColon": false, "afterColon": true }],
    "line-comment-position": ["warn", { "position": "above" }],
    "lines-between-class-members": ["warn", "always"],
    "max-depth": ["warn", 3],
    "max-len": ["warn", { "code": 200 }],
    "max-lines-per-function": ["warn", { "max": 60 }],
    "max-nested-callbacks": ["warn", 2],
    "max-params": ["warn", 3],
    "max-statements": ["warn", 20],
    "no-compare-neg-zero": "warn",
    "no-console": ["warn", { allow: ["warn", "error"] }],
    "no-debugger": "warn",
    "no-dupe-args": "warn",
    "no-dupe-class-members": "warn",
    "no-dupe-else-if": "warn",
    "no-dupe-keys": "warn",
    "no-duplicate-case": "warn",
    "no-duplicate-imports": "warn",
    "no-empty": "warn",
    "no-ex-assign": "warn",
    "no-extra-boolean-cast": "warn",
    "no-extra-semi": "warn",
    "no-irregular-whitespace": "warn",
    "no-magic-numbers": "warn",
    "no-mixed-spaces-and-tabs" : "warn",
    "no-prototype-builtins": "warn",
    "no-redeclare": "warn",
    "no-undef": "warn",
    "no-unexpected-multiline": "warn",
    "no-unused-expressions": "warn",
    "no-unused-vars": "warn",
    "no-use-before-define": "warn",
    "no-useless-escape": "warn",
    "no-useless-rename": "warn",
    "no-var": "warn",
    "object-curly-spacing": ["warn", "always"],
    "react-hooks/exhaustive-deps": "warn",
    "react-hooks/rules-of-hooks": "warn",
    "react/display-name": "warn",
    "react/no-deprecated": "warn",
    "react/no-direct-mutation-state": "warn",
    "react/prop-types": "off",
    "yoda": "warn",
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
