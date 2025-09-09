# Descrição dos Relacionamentos

> Dicionário de termos:
> (1) - Um único registro/dado.
> (N) - Vários registros/dados do mesmo tipo.

Vamos começar pelo Benchmark:

Um Benchmark pode ter vários controles, mas um controle pode ter apenas um único benchmark. (1 - N)

Um Controle pode ter vários históricos de estado (HistoricoEstado), e um histórico pode ter vários registros de controle. (N - N)

Um histórico de estado (HistoricoEstado) pode ter vários registros de conta (Account), e uma conta pode ter varios historicos. (N - N)

Foi-se criado uma tabela associativa especial para que pudesse se concentrar os relacionamentos entre Accont e Benchmark. Uma conta (Account) pode ter vários Benchmarks e um Benchmark pode ter várias contas. (N - N)

---

# Descrição das Tabelas
Bem neste minimundo passado como desafio resolvi enxergar como o ambiente se comportar de acordo com uma auto-interpretação, bem, pelo que imaginei este é um banco de dados que se concentra em:

- Armazenar as informações de **Benchmarks(Testes**).
- Estes **Benchmarks** possuem alguns **Controles**.
- Vários **Benchmarks** pode estar associado uma ou várias Contas.
- **Contas** e **Controles** estão associados a um **histórico** (**HistoricoEstado**).
- Este **histórico** gera um “log” para cada vez que a prorpiedade “**EstadoAtual**” de um **controle** muda de “ok” para “alarm” e vice-versa.
- Ou seja, cada vez que uma conta executa um **Benchmark**, ele utiliza 1 ou mais **controles** para gerar os “resultados” desejados, e assim que estes **controles** mudam de estado é registrado dentro do **histórico**, com as informações de tempo (**Timestamp**) e **estado** que o **controle** esta.

---
## Benchmark

### Chaves

| Key  | Descrição                                                            | Tipo da Chave                                  |
| ---- | -------------------------------------------------------------------- | ---------------------------------------------- |
| ID   | Esta chave vai servir de identificador daquele registro de Benchmark | Chave-Primaria<br><br>Int (Auto-Incrementavel) |
| Name | Nome do Benchmark                                                    | Varchar                                        |

### Exemplo de dado da tabela


| ID  | Name                                        |
| --- | ------------------------------------------- |
| 1   | Teste de Performasse 1                      |
| 2   | Teste de LGPD                               |
| 3   | Teste de Escalabilidade                     |
| 4   | Teste da Conexão com Clound (Cenario AWS)   |
| 5   | Teste da Conexão com Clound (Cenario AZURE) |

---

## Controle
### Chaves

| Key           | Descrição                                                                                                                                                          | Tipo da Chave                                    |
| ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------ |
| ID            | Esta chave vai servir de identificador daquele registro de Controle                                                                                                | Chave-Primaria<br><br>Int (Auto-Incrementavel)   |
| Name          | Nome do Controle                                                                                                                                                   | Varchar                                          |
| Description   | Descrição do que aquele controle faz.                                                                                                                              | Varchar                                          |
| EstadoAtual   | Esta “coluna” pode receber 2 únicos tipos de registros:<br><br>OK: O Controle esta operando.<br><br>ALARM: O controle não esta operando corretamente ou falhou<br> | ENUM (Lista de Dados)                            |
| Benchmarck_ID | Identificador de qual Benchmark aquele teste pertence.                                                                                                             | Chave Estrangeira (Vinculada a tabela Benchmark) |

### Exemplo de dado da tabela


| ID  | Name               | Description                              | EstadoAtual | Benchmark_ID |
| --- | ------------------ | ---------------------------------------- | ----------- | ------------ |
| 1   | SSD Performasse    | Velocidade de Leitura e Escrita do SSD   | ALARM       | 1            |
| 2   | Velocidade da Rede | Velocidade de Download e Upload de dados | ALARM       | 1            |
| 3   | Estado Cluster     | Conexão entre Clusters                   | OK          | 3            |
| 4   | Firewall Estado    | Verifica se o Firewall esta Ativo        | OK          | 2            |
| 5   | API Estado         | Conexão com API via Endpoint             | OK          | 4            |

---

## HistóricoEstado
### Chaves

| Key         | Descrição                                                                                                                        | Tipo da Chave                                   |
| ----------- | -------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------- |
| ID          | Esta chave vai servir de identificador daquele registro do Histórico                                                             | Chave-Primaria<br><br>Int (Auto-Incrementavel)  |
| Estado      | Armazena o valor da propriedade **EstadoAtual** do controle correspondente, registrando cada mudança de estado no **histórico**. | ENUM (Lista de Dados)                           |
| Timestamp   | Armazena a informação de tempo de quando aquele registro foi catalogado                                                          | Registro de tempo                               |
| Account_ID  | Identificador de qual **Conta** fez aquela interação com o **Controle**                                                          | Chave-Estrangeira (Vinculada a tabela Account)  |
| Controle_ID | Identificador do **Controle** utilizado pela **Conta**                                                                           | Chave-Estrangeira (Vinculada a tabela Controle) |

### Exemplo de dado da tabela

| ID  | Estado | TimeStamp           | Account_ID | Controle_ID |
| --- | ------ | ------------------- | ---------- | ----------- |
| 1   | OK     | 2025-09-05 12:45:01 | 3          | 3           |
| 2   | OK     | 2025-09-06 22:32:57 | 3          | 1           |
| 3   | ALARM  | 2025-09-05 12:45:01 | 2          | 1           |
| 4   | OK     | 2025-09-09 08:01:34 | 2          | 4           |
| 5   | ALARM  | 2025-09-09 09:15:02 | 5          | 2           |

---

## Account
### Chaves

| Key  | Descrição                                                        | Tipo da Chave                                  |
| ---- | ---------------------------------------------------------------- | ---------------------------------------------- |
| ID   | Esta chave vai servir de identificador daquele registro da Conta | Chave-Primaria<br><br>Int (Auto-Incrementavel) |
| Name | Nome da conta                                                    | Varchar                                        |

### Exemplo de dado da tabela

| ID  | Name                     |
| --- | ------------------------ |
| 1   | georges@gmail.com        |
| 2   | bl00m@miau.com           |
| 3   | jojotheCat@Ballister.com |
| 4   | geovanna@Lay.com         |
| 5   | daniel@capivara.com      |

---

## AccountBenchmark
### Chaves

| Key          | Descrição                                                                                                           | Tipo da Chave                                    |
| ------------ | ------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------ |
| ID           | Esta chave vai servir de identificador de quais **Contas (Account)** estão vinculadas a determinados **Benchmarks** | Chave-Primaria<br><br>Int (Auto-Incrementavel)   |
| Account_ID   | Identificador da conta, para que se possa ter acesso a um Benchmark                                                 | Chave-Estrangeira (Vinculada a tabela Account)   |
| Benckmark_ID | Identificador de qual **Benchmark** vai estar vinculado a determinada **Conta**                                     | Chave-Estrangeira (Vinculada a tabela Benchmark) |

### Exemplo de dado da tabela

| ID  | Account_ID | Benckmark_ID |
| --- | ---------- | ------------ |
| 1   | 1          | 1            |
| 2   | 1          | 2            |
| 3   | 3          | 5            |
| 4   | 1          | 3            |
| 5   | 4          | 2            |
| 6   | 4          | 1            |
| 7   | 5          | 2            |
| 8   | 2          | 4            |

---

# Índices, segunda parte do desafio

Aqui o desafio solicitou 3 cenários diferentes onde poderíamos criar índices para poder navegar mais facilmente pelos registros do banco, como diz explicitamente que não é para escrever **DDL**, vou somente listar quais os **INDEX**(indices) que deverão ser usados. Porem em outra “branch” desse repositório ou ainda dentro de outro markdown eu vou ter esse capricho de escrever esses índices o disponibilizando com o minimundo, em arquivos “**.sql**”.

---

## Cenário 1 (Q1)

> Desafio:
> *Listar Benchmark com seus Controles e o estado atual para uma Account.*

Como o desafio pede para apresentar a lógica de se criar o IDEX ideal para a situação, acredito eu que podemos seguir a seguinte lógica:

1. Poderíamos filtrar usando o ID da conta que queremos obter essas informações, a tabela `AccountBenchmark` já que ela possui os ID’s das contas, sabendo assim qual Benchmark ela tem acesso.
2. Buscamos dentro da tabela `Benchmark` a propriedade de `Name`.
3. Logo após poderíamos listar os ID’s dos controles pertencentes a cada Benchmark que aquela conta em acesso, buscando com estes ID’s os registros através da propriedade de chave-estrangeira `Benchmark_ID` da tabela `Controle`.
4. Podemos buscar por 2 propriedades dentro desta tabela `Name` e `EstadoAtual`
5. Gerando no final uma visualização mais ou menos assim:

| Benchmark (Name)                          | Controle (Name) | Estado Atual (EstadoAtual) |
| ----------------------------------------- | --------------- | -------------------------- |
| Teste de LGPD                             | Firewall Estado | OK                         |
| Teste de Escalabilidade                   | Estado Cluster  | OK                         |
| Teste da Conexão com Clound (Cenario AWS) | API Estado      | OK                         |

---

## Cenário 2 (Q2)

> Desafio:
> *Listar Benchmark com seus Controles e as mudanças de estado em um intervalo para uma Account.*

Vou seguir a mesma estrutura que eu fiz na minha resposta acima, pois creio que fique melhor para você ler! 😉

1. Utilizando o ID da conta para filtrar apenas os registros da tabela `HistoricoEstado` que estão vinculam.
2. Logo após definindo a data inicial e a data final que será filtrada desses valores pelo `timestamp` e `Estado`. 
3. Reservando essas informações de `timestamp`
4. Logo após obtendo as informações de `Controle_ID` dos registros da tabela, devemos filtrar todos os registros que estão vinculados a estes dados dentro da tabela `Controle`.
5. Reservando os valores de `Nome`.
6. E em seguida com as informações do `Benchmark_ID` podemos fazer uma seleção nesta tabela de somente estes Benchmarks.
7. Reservando também seus valores de ``Nome``
8. Gerando a seguinte visualização:

| Benchmark (name)        | Controle (name) | Estado (Estado) | Quando Utilizou (Timestamp) |
| ----------------------- | --------------- | --------------- | --------------------------- |
| Teste de Escalabilidade | Estado Cluster  | OK              | 2025-09-05 12:45:01         |

---

## Cenário 3 (Q3)

> Desafio:
> *Obter Benchmark com seus Controles e o estado em uma data/hora X (snapshot) para uma Account.*

1. Primeiro devemos ter 2 valores o `Timestamp` que queremos nos basear e o ID da Conta.
2. Devemos passar um filtro na tabela `HistoricoEstado` com esses dois valores.
3. Reservando as informações de `Estado` daqueles registros
4. Usando o `Controle_ID` buscamos na todos os controles daquele determinado tempo na tabela `Controle`
5. Reservamos a propriedade `Name`
6. Usamos a informação da chave-estrangeira `Benchmarck_ID` para realizar a buscar na tabela `Benchmark`
7. Reservamos a propriedade `nome`
8. E nossa visualização ficara organizada assim:

| Benchmark (nome)       | Controle (nome) | Estado no Momento X (Timestamp) |
| ---------------------- | --------------- | ------------------------------- |
| Teste de Performasse 1 | SSD Performasse | ALARM                           |


