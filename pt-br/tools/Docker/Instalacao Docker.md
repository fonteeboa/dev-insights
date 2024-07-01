# Instalação Docker

## Instalação Padrão
Se a preferência for realizar a instalação padrão, recomenda-se seguir os passos fornecidos na documentação oficial do Docker, disponível aqui. Este guia oferece instruções detalhadas para várias plataformas, como Windows, macOS e Linux.

## Instalação em Outro Diretório (Windows)

### Desafio Enfrentado

No ambiente Windows, até a data deste documento, a configuração padrão do Docker não permite a seleção direta do diretório de instalação durante o processo de instalação. Isso levou à necessidade de buscar alternativas para contornar essa limitação.

Várias abordagens foram exploradas para resolver essa questão. Uma delas envolveu a criação de links simbólicos usando comandos como:

```
mklink /D "C:\ProgramData\Docker" "D:\Docker\ProgramData"
mklink /D "C:\ProgramData\DockerDesktop" "D:\Docker\ProgramDockerDesktop"
mklink /D "C:\Users\User\AppData\Local\Docker" "D:\Docker\AppData"
```

### Solução Encontrada

No entanto, nenhuma dessas estratégias demonstrou ser eficaz para redirecionar os diretórios conforme desejado.

A abordagem que provou ser eficiente foi a execução do Docker Desktop por meio do prompt de comando (cmd) e a adição de flags específicas durante o processo de instalação, conforme exemplo abaixo:

```
start /w "" "Docker Desktop Installer.exe" install --installation-dir=D:\Docker\installation --wsl-default-data-root="D:\Docker\Dados\WSL"
```

Ao utilizar este método, foi possível redirecionar não apenas o diretório de instalação principal, mas também outros diretórios associados ao Docker para os caminhos desejados.

### Observação 

É importante ressaltar que as etapas podem variar dependendo das versões do Docker ou do sistema operacional. Portanto, consulte a documentação oficial ou fóruns de suporte para informações atualizadas ou soluções alternativas.
