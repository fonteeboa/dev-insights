# Docker Desktop + WSL2 no Windows 11: meu reset mais forte

Atualmente mantenho dois ambientes de desenvolvimento: **um Linux e um Windows 11**.

No Linux, esse tipo de problema é bem menos frequente para mim. Já no Windows 11, eventualmente o **Docker Desktop + WSL2** entra em um estado estranho: o Docker fica preso em *Loading*, o `docker-desktop` aparece como `Installing`, o daemon não responde ou o WSL fica com processos/serviços presos.

Depois de passar por isso algumas vezes, acabei deixando um **script PowerShell salvo** para fazer um reset mais agressivo do ambiente.

A ideia é simples: **reiniciar o que precisa ser reiniciado sem sair apagando minhas distribuições WSL ou meu ambiente de desenvolvimento**.

## O script

> Execute o PowerShell como **Administrador**.

```powershell
#requires -RunAsAdministrator

$ErrorActionPreference = "SilentlyContinue"

Write-Host "=== Docker Desktop + WSL2 - Reset forte ===" -ForegroundColor Cyan
Write-Host ""
#
# Encerra o cliente WSL para liberar processos que podem estar travados.
taskkill.exe /F /IM "wsl.exe" 2>$null

# Encerra os hosts de distribuição WSL que podem permanecer presos.
taskkill.exe /F /IM "wslhost.exe" 2>$null

# Encerra o serviço WSL em processo caso esteja preso durante o desligamento.
taskkill.exe /F /IM "wslservice.exe" 2>$null

# Encerra o processo VMM usado pelo backend das distribuições WSL2.
taskkill.exe /F /IM "VmmemWSL.exe" 2>$null

# Fecha completamente a interface do Docker Desktop.
taskkill.exe /F /IM "Docker Desktop.exe" 2>$null

# Encerra o backend principal do Docker Desktop.
taskkill.exe /F /IM "com.docker.backend.exe" 2>$null

# Encerra o processo do HNS quando ele fica preso em STOP_PENDING ou START_PENDING.
taskkill.exe /F /IM "svchost.exe" /FI "SERVICES eq hns"

Write-Host ""
Write-Host "[1/8] Parando serviços..." -ForegroundColor Yellow

# Tenta parar o serviço WSL moderno caso esteja disponível nesta instalação.
Stop-Service WSLService -Force -ErrorAction SilentlyContinue

# Tenta parar o serviço legado caso exista em versões antigas do WSL.
Stop-Service LxssManager -Force -ErrorAction SilentlyContinue

# Para o serviço de computação usado pelas máquinas virtuais e pelo WSL2.
Stop-Service vmcompute -Force -ErrorAction SilentlyContinue

# Para o Host Network Service usado pela rede do WSL2 e Docker.
Stop-Service hns -Force -ErrorAction SilentlyContinue

Write-Host "[2/8] Verificando HNS..." -ForegroundColor Yellow

# Exibe o estado detalhado do HNS para identificar processos presos.
sc.exe queryex hns

Write-Host ""
Write-Host "[3/8] Verificando VM Compute..." -ForegroundColor Yellow

# Exibe o estado detalhado do serviço de computação das máquinas virtuais.
sc.exe queryex vmcompute

Write-Host ""
Write-Host "[4/8] Iniciando serviços novamente..." -ForegroundColor Yellow

# Inicia o serviço de computação necessário para o backend WSL2.
Start-Service vmcompute -ErrorAction SilentlyContinue

# Inicia o Host Network Service usado pela rede virtual do Docker e WSL2.
Start-Service hns -ErrorAction SilentlyContinue

# Inicia o serviço moderno do WSL quando disponível nesta versão.
Start-Service WSLService -ErrorAction SilentlyContinue

Write-Host ""
Write-Host "[5/8] Estado dos serviços..." -ForegroundColor Yellow

# Mostra apenas os serviços WSL, HNS e VM Compute disponíveis nesta máquina.
Get-Service WSLService,LxssManager,hns,vmcompute -ErrorAction SilentlyContinue |
    Format-Table Status,Name,DisplayName -AutoSize

Write-Host ""
Write-Host "[6/8] Estado do WSL..." -ForegroundColor Yellow

# Lista todas as distribuições e confirma se o backend Docker está registrado.
wsl.exe -l -v

# Exibe a configuração atual do WSL2 instalado na máquina.
wsl.exe --status

Write-Host ""
Write-Host "[7/8] Iniciando Docker Desktop..." -ForegroundColor Yellow

# Inicia o serviço do Docker Desktop antes de abrir sua interface.
Start-Service com.docker.service -ErrorAction SilentlyContinue

# Abre o Docker Desktop para inicializar novamente o backend Linux.
$dockerDesktop = "$env:ProgramFiles\Docker\Docker\Docker Desktop.exe"

if (Test-Path $dockerDesktop) {
    Start-Process $dockerDesktop
}
else {
    Write-Warning "Docker Desktop.exe não encontrado em: $dockerDesktop"
}

Write-Host ""
Write-Host "[8/8] Aguardando backend do Docker..." -ForegroundColor Yellow

# Aguarda o Docker Desktop iniciar sua distribuição e seu daemon interno.
Start-Sleep -Seconds 15

# Mostra novamente o estado das distribuições WSL após a inicialização.
wsl.exe -l -v

Write-Host ""
Write-Host "=== Validação Docker ===" -ForegroundColor Cyan

# Testa se o cliente Docker consegue conectar ao daemon Linux.
docker.exe version

# Mostra informações do daemon para confirmar que o backend está operacional.
docker.exe info

# Exibe os contextos Docker e destaca o contexto atualmente selecionado.
docker.exe context ls

Write-Host ""
Write-Host "=== Reset concluído ===" -ForegroundColor Green
```

## O que ele faz?

Basicamente, o script tenta limpar processos presos do **WSL2, Docker Desktop e HNS**, reinicia os serviços necessários e depois valida o estado do WSL e do Docker.

O ponto que considero mais importante é que ele **não remove minhas distribuições Ubuntu** e não começa apagando containers, volumes ou imagens.

## Um exemplo real

Em um dos casos que encontrei, o ambiente estava inconsistente.

O WSL mostrava algo parecido com:

```text
NAME              STATE        VERSION
Ubuntu-22.04      Stopped      2
docker-desktop    Installing   2
Ubuntu            Stopped      2
```

Enquanto o Docker retornava erro de conexão com:

```text
dockerDesktopLinuxEngine
```

E uma tentativa de acessar diretamente:

```powershell
wsl.exe -d docker-desktop
```

retornava:

```text
Wsl/Service/WSL_E_DISTRO_NOT_FOUND
```

Também já encontrei o HNS preso em estados como:

```text
START_PENDING
```

ou:

```text
STOP_PENDING
```

Nesses casos, simplesmente executar `wsl --shutdown` ou reiniciar o Docker Desktop não era suficiente.

## Um detalhe importante

O script é uma rotina de recuperação, não uma garantia de que o Docker Desktop vai iniciar automaticamente.

Por exemplo, em uma execução o Windows conseguiu deixar:

```text
hns        Running
vmcompute  Running
WSLService Running
```

e o WSL voltou a mostrar:

```text
docker-desktop    Stopped    2
```

mas o executável do Docker Desktop não estava no caminho esperado pelo script.

Nesse cenário, o script ainda cumpriu sua parte de recuperação do WSL/serviços, mas foi necessário iniciar o Docker Desktop manualmente.

Isso também é útil porque mostra que **"WSL saudável" e "Docker daemon saudável" são coisas diferentes**.

## ⚠️ Cuidado com `wsl --unregister`

Existe um segundo procedimento que mantenho separado do script principal:

```powershell
wsl.exe --unregister docker-desktop
```

Esse comando **não é um restart**.

`wsl --unregister` remove a distribuição WSL informada. Portanto, ele pode remover dados armazenados nessa distribuição e, dependendo de como o Docker Desktop estiver configurado, afetar o estado interno do Docker.

Por isso, eu **não coloco esse comando no fluxo normal do script**.

Uso somente quando encontro uma situação específica em que:

```text
docker-desktop    Installing
```

mas:

```powershell
wsl.exe -d docker-desktop
```

retorna:

```text
Wsl/Service/WSL_E_DISTRO_NOT_FOUND
```

Mesmo nesse caso, vale tratar como **último recurso**.

E uma regra pessoal no meu ambiente:

```text
NÃO executar:

wsl --unregister Ubuntu
wsl --unregister Ubuntu-22.04
```

Essas são minhas distribuições de desenvolvimento e não fazem parte do processo normal de recuperação do Docker.

## Meu resumo

Para mim, esse script virou uma espécie de **"reset forte" do Docker Desktop + WSL2 no Windows 11**.

Não é um procedimento oficial do Docker ou da Microsoft. É simplesmente um **playbook pessoal de recuperação** baseado nos problemas que encontrei no meu ambiente de desenvolvimento.

Ter o script salvo é muito mais prático do que ficar repetindo manualmente a mesma sequência toda vez que o Docker Desktop decide ficar preso em *Loading*.
