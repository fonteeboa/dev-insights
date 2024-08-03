# Cron.d (Automação de tarefas no ambiente Linux)

### O que é o cron.d ?

O diretório cron.d é uma pasta usada em sistemas baseados em Unix, como o Linux, para armazenar arquivos de configuração do cron. O cron é um utilitário que permite agendar a execução de tarefas automaticamente em horários específicos, como rodar scripts, executar comandos ou realizar outras operações programadas.

Dentro do diretório cron.d, você pode colocar arquivos individuais que contêm instruções de agendamento para o cron. Esses arquivos contêm as informações sobre quando e qual comando ou script deve ser executado. Cada linha dentro desses arquivos segue a sintaxe cron, que especifica os minutos, horas, dias do mês, meses e dias da semana em que a tarefa deve ser executada.

### Configurando um arquivo no cron.d

A estrutura de um arquivo de configuração cron no diretório cron.d segue um padrão específico para definir o agendamento das tarefas. Geralmente, cada linha no arquivo segue o formato:

```
# Example of job definition:
# .---------------- Minuto (0 - 59)
# |  .------------- Hora (0 - 23)
# |  |  .---------- Dia do mês (1 - 31)
# |  |  |  .------- Mês (1 - 12) ou nomes de meses como jan, feb, mar, etc.
# |  |  |  |  .---- Dia da semana (0 - 6) (domingo=0 ou 7) ou nomes de dias da semana como sun,mon,tue,wed,thu,fri,sat.
# |  |  |  |  |
# *  *  *  *  *  nome-de-usuário  comando a ser executado
```

Essas linhas de configuração representam o momento em que um determinado comando ou script será executado, permitindo automatizar a execução de tarefas repetitivas no sistema operacional.

### Exemplos

#### Minuto (0 - 59)

Executar o comando a cada 15 minutos:

```
*/15 * * * * nome-de-usuário comando
```

#### Hora (0 - 23)

Executar o comando todos os dias às 8 da manhã:
```
0 8 * * * nome-de-usuário comando
```

####  Dia do mês (1 - 31)

Executar o comando no dia 15 de cada mês às 18 horas:
```
0 18 15 * * nome-de-usuário comando
```

#### Mês (1 - 12) ou nomes de meses

Executar o comando no dia 1 de janeiro às 00:30:
```
30 0 1 1 * nome-de-usuário comando
```

#### Dia da semana (0 - 6) ou nomes de dias da semana

Executar o comando toda segunda-feira às 10 da manhã:
```
0 10 * * 1 nome-de-usuário comando
```