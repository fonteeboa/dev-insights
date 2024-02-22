# Git

O Git é essencial para o desenvolvimento de software, permitindo o controle de versões, colaboração eficaz entre desenvolvedores e o gerenciamento seguro de mudanças no código. Facilita o rastreamento de alterações, a reversão a versões anteriores e a integração de trabalho de diferentes membros da equipe, tornando o processo de desenvolvimento mais organizado e eficiente.

## Comandos

## Configuração e Setup

### git config

Configura o nome do usuário, email, editor, e muito mais.

```bash
Exemplo: git config --global user.name "Seu Nome" configura o nome do usuário para todos os repositórios.
```

### git init

Inicializa um novo repositório Git local.

```bash
Exemplo: git init cria um novo repositório Git no diretório atual.
```

## Stage & Snapshot

### git status

Mostra o status dos arquivos (modificados, não rastreados, etc.).

```bash
Exemplo: git status para ver o estado atual dos arquivos.

```

### git add

Adiciona arquivos ao stage para serem commitados.

```bash
Exemplo: git add . adiciona todos os arquivos modificados ao stage.

```

### git commit

Commita os arquivos do stage e salva um snapshot do projeto.

```bash
Exemplo: git commit -m "mensagem" commita com uma mensagem.

```

## Branch & Merge

### git branch

Lista, cria ou deleta branches.

```bash
Exemplo: git branch nova-branch cria uma nova branch.

```

### git checkout

Muda para outra branch ou restaura arquivos.

```bash
Exemplo: git checkout outra-branch muda para a branch especificada.

```

### git merge

Junta históricos de duas branches.

```bash
Exemplo: git merge outra-branch mescla outra-branch na branch atual.

```

## Inspeção & Comparação

### git log

Mostra o histórico de commits.

```bash
Exemplo: git log para ver o histórico de commits.

```

### git diff

Mostra diferenças entre commits, branches, etc.

```bash
Exemplo: git diff mostra diferenças não stageadas.

```

## Compartilhar & Atualizar

### git remote

Gerencia conjunto de repositórios rastreados.

```bash
Exemplo: git remote add origin URL adiciona um novo remote.

```

### git fetch

Baixa objetos e refs de outro repositório.

```bash
Exemplo: git fetch origin atualiza a informação do remote origin.

```

### git push

Atualiza repositório remoto com commits locais.

```bash
Exemplo: git push origin main envia commits locais para a branch main no remote origin.

```

### git pull

Atualiza repositório local com a última versão do remote.

```bash
Exemplo: git pull origin main atualiza o local com o remote.
```

## Desfazer

### git revert

Desfaz mudanças de um commit específico.

```bash
Exemplo: git revert <commit-hash> reverte as mudanças do commit especificado.

```

### git reset

Reseta o HEAD para um estado anterior.

```bash
Exemplo: git reset --hard HEAD~1 desfaz o último commit e as mudanças.

```

### git rm

Remove arquivos do index (stage) e do diretório de trabalho.

```bash
Exemplo: git rm arquivo.txt remove o arquivo do diretório de trabalho e do stage.
```

### git restore

Restaura arquivos do stage ou do histórico de commits.

```bash
Exemplo: git restore arquivo.txt desfaz modificações no arquivo.
```

### git clean

Remove arquivos não rastreados pelo Git.

```bash
Exemplo: git clean -fd remove diretórios e arquivos não rastreados.
```

## Trabalhando com Remotes

### git clone

Copia um repositório Git existente.

```bash
Exemplo: git clone <url> clona o repositório para o local.
```

### git push (revisão)

Envia mudanças para o repositório remoto.

```bash
Exemplo: git push origin main envia mudanças locais para a branch main no remote.

```

### git pull (revisão)

Atualiza seu repositório local com a versão do repositório remoto.

```bash
Exemplo: git pull origin main puxa as atualizações de main do origin para o local.
```

## Gerenciamento Avançado

### git rebase

Reaplica commits em cima de outra base.

```bash
Exemplo: git rebase main reaplica os commits da branch atual em cima da main.

```

### git blame

Mostra quem modificou cada linha de um arquivo.

```bash
Exemplo: git blame arquivo.txt mostra a autoria linha por linha.
```

### git show

Mostra informações sobre objetos no Git.

```bash
Exemplo: git show <commit-hash> mostra informações sobre o commit.
```

### git log --graph

Exibe o histórico de commits em forma de um gráfico ASCII.

```bash
Exemplo: git log --graph mostra a estrutura de branches e merges.
```

### git stash

Salva mudanças locais temporariamente em uma área limpa.

```bash
Exemplo: git stash push -m "mensagem" salva o trabalho atual com uma mensagem.

```

### git stash pop

Aplica mudanças salvas com git stash.

```bash
Exemplo: git stash pop aplica a última mudança stashed.

```

### git cherry-pick

Aplica o commit de outra branch na branch atual.

```bash
Exemplo: git cherry-pick <commit-hash> aplica o commit especificado.

```

## Tags

### git tag

Lista, cria, ou deleta tags.

```bash
Exemplo: git tag v1.0.0 cria uma tag para marcar uma versão.

```

## Git Hooks

### git hook

Ganchos de scripts que são disparados por eventos importantes.

```bash
Exemplo: Personalizar .git/hooks/pre-commit para rodar testes antes de cada commit.

```

## Reflog

### git reflog

Mostra um log de mudanças na referência do HEAD.

```bash
Exemplo: git reflog ajuda a encontrar commits perdidos.

```

## Submódulos

### git submodule

Gerencia outro repositório dentro de um repositório como um submódulo.

```bash
Exemplo: git submodule add URL adiciona um novo submódulo.

```

## Ferramentas de Debugging

### git bisect

Usa busca binária para encontrar o commit que introduziu um bug.

```bash
Exemplo: git bisect start para começar a bisecção.

```

## Ferramentas de Merge

### git mergetool

Abre uma ferramenta gráfica para resolver conflitos de merge.

```bash
Exemplo: git mergetool após um conflito de merge.

```

## Trabalhando com Remotos

### git remote show

Mostra informações sobre o repositório remoto.

```bash
Exemplo: git remote show origin mostra detalhes do remote origin.

```

### git remote prune

Remove referências locais a branches remotos deletados.

```bash
Exemplo: git remote prune origin limpa referências antigas.

```

## Git Archive

### git archive

Cria um arquivo (como um .tar ou .zip) de árvores de commits.

```bash
Exemplo: git archive --format zip --output /tmp/arquivo.zip HEAD cria um arquivo zip do estado atual.

```

## Git Worktree

### git worktree

Gerencia múltiplas árvores de trabalho ligadas a um repositório.

```bash
Exemplo: git worktree add ../nova-diretoria branch cria uma nova worktree.

```

## Outros Comandos Úteis

### git ls-tree

Lista o conteúdo de uma árvore de commits.

```bash
Exemplo: git ls-tree HEAD mostra a árvore do HEAD.

```

### git mv

Move ou renomeia um arquivo, diretório, ou link simbólico.

```bash
Exemplo: git mv arquivo_antigo.txt arquivo_novo.txt.

```

### git gc

Limpa arquivos desnecessários e otimiza o repositório local.

```bash
Exemplo: git gc para otimizar o repositório.

```

### git fsck

Verifica a integridade do sistema de arquivos Git.

```bash
Exemplo: git fsck para verificar erros.

```

### git filter-branch

Reescreve branches.

```bash
Exemplo: git filter-branch --tree-filter 'rm -f senha.txt' HEAD remove um arquivo de todo o histórico.

```

## Informações e Ajuda

### git help

Mostra a ajuda para os comandos Git.

```bash
Exemplo: git help commit mostra a ajuda do comando commit.

```

### git version

Mostra a versão do Git instalada.

```bash
Exemplo: git version para ver a versão atual.

```

## Git Ignore

### .gitignore

Especifica arquivos intencionalmente não rastreados para ignorar.

```bash
Exemplo: Adicionar *.log no .gitignore para ignorar arquivos de log.

```

## Git Attributes

### .gitattributes

Permite definir atributos de caminhos específicos.

```bash
Exemplo: Adicionar *.txt linguist-detectable=true no .gitattributes.

```

## Gerenciamento de LFS (Large File Storage)

### git lfs track

Rastreia arquivos grandes com Git LFS.

```bash
Exemplo: git lfs track "*.psd" para rastrear arquivos Photoshop.

```

### git lfs ls-files

Lista todos os arquivos rastreados pelo Git LFS.

```bash
Exemplo: git lfs ls-files para ver arquivos LFS.

```

## Performance

### git count-objects

Mostra informações sobre objetos do banco de dados Git.

```bash
Exemplo: git count-objects para ver estatísticas do repositório.

```

## Networking

### git daemon

Permite que o Git seja servido como um daemon para protocolos sem estado.

```bash
Exemplo: git daemon --reuseaddr --base-path=/path/to/repo --export-all --verbose para servir um repositório.

```

## Subtree

### git subtree

Ferramenta para subprojetos, permite repositórios dentro de outro.

```bash
Exemplo: git subtree add --prefix=subprojeto subprojeto_repo master --squash adiciona um subprojeto.
```
