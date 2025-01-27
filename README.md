# Function SQL

No contexto de SQL, uma função é um bloco de código reutilizável armazenado no banco de dados que realiza uma operação específica e retorna um valor. As funções ajudam a encapsular a lógica para realizar cálculos ou manipular dados, simplificando consultas e melhorando a organização do código.

## Tipos de Funções em SQL

As funções podem ser classificadas em dois tipos principais:

1. Funções Predefinidas (Built-in Functions)

São fornecidas pelo banco de dados e abrangem diversas operações comuns. Exemplos:

- Funções de agregação:
SUM(), AVG(), MAX(), MIN(), COUNT().

- Funções de string:
CONCAT(), UPPER(), LOWER(), SUBSTRING(), LENGTH().

- Funções de data e hora:
NOW(), CURDATE(), DATE_ADD(), DATEDIFF().

- Funções matemáticas:
ROUND(), FLOOR(), CEIL(), ABS().

2. Funções Definidas pelo Usuário (User-Defined Functions - UDFs)

Criadas pelo desenvolvedor para executar operações específicas e personalizadas. Exemplos:

- Uma função que calcula o desconto em um pedido.
- Uma função que formata e retorna nomes completos.
- Uma função que converte valores monetários.
- Estrutura de uma Função no SQL
- Uma função definida pelo usuário (UDF) geralmente segue esta estrutura básica:

Definir o nome da função.<br>
Declarar os parâmetros de entrada.<br>
Definir o tipo de dado de retorno.<br>
Escrever o bloco de código para executar a lógica.<br>
Especificar o valor de retorno.<br>
Exemplo genérico de uma função no MySQL:

````sql

DELIMITER $$

CREATE FUNCTION nome_da_funcao (parametro1 TIPO1, parametro2 TIPO2)
RETURNS TIPO_RETORNO
DETERMINISTIC
BEGIN
    -- Lógica da função
    RETURN valor; -- Resultado da função
END $$

DELIMITER ;

````

### Características de Funções em SQL

- Entrada e Saída: Funções recebem argumentos de entrada (parâmetros). Sempre retornam exatamente um valor.

- Deterministicidade:

DETERMINISTIC: Retorna sempre o mesmo valor para os mesmos inputs.<br>
NOT DETERMINISTIC: Pode retornar resultados diferentes dependendo de fatores externos (ex.: data/hora atual).<br>
Uso em Consultas:

Funções podem ser usadas diretamente em instruções SELECT, WHERE, ORDER BY e outras.

Exemplo:

````sql
SELECT nome_da_funcao(10, 20) AS resultado;
````

### Benefícios de Usar Funções

- Reutilização: Evita a repetição de código ao encapsular lógica comum.
- Organização: Facilita a manutenção e clareza no banco de dados.
- Eficiência: Pode ser otimizado pelo banco de dados, dependendo do contexto.

## Exemplos

Passo 01: Criar o banco de dados

````sql
CREATE DATABASE exemplo_funcao;
USE exemplo_funcao;
````

CREATE DATABASE exemplo_funcao: Cria um banco de dados chamado exemplo_funcao.<br>
USE exemplo_funcao: Seleciona esse banco para ser usado nos comandos subsequentes.

Passo 02: Criar a função dentro do banco <br>
Após selecionar o banco, você pode criar a função como antes. Por exemplo:

````sql
DELIMITER $$

CREATE FUNCTION soma (num1 INT, num2 INT)
RETURNS INT
DETERMINISTIC
BEGIN
    RETURN num1 + num2;
END $$

DELIMITER ;
````

Passo 03: Testar a função<br>
Agora, você pode chamar a função usando o comando SELECT:

````sql
SELECT soma(10, 20) AS resultado;
````

O Mysql retornará:

````diff
+-----------+
| resultado |
+-----------+
|        30 |
+-----------+
````

### Exemplo com a função sum()

Exemplo de uma função no MySQL que utiliza a função agregadora SUM() para calcular o total de valores em uma coluna específica de uma tabela. Suponha que temos uma tabela chamada vendas com a seguinte estrutura:

#### Estrutura da Tabela vendas

![Tabela vendas](assets/function%20png.png)

````sql
CREATE TABLE vendas (
    id_venda INT AUTO_INCREMENT PRIMARY KEY, -- Identificador único para cada venda
    cliente_id INT NOT NULL,                -- Identificador do cliente (relacionamento)
    valor DECIMAL(10, 2) NOT NULL,          -- Valor da venda, com 2 casas decimais
    data_venda DATE NOT NULL                -- Data em que a venda foi realizada
);

INSERT INTO vendas (cliente_id, valor, data_venda)
VALUES
    (101, 150.00, '2025-01-01'),
    (102, 200.00, '2025-01-02'),
    (101, 300.00, '2025-01-03'),
    (103, 100.00, '2025-01-04');
````

#### Função: Total de Vendas por Cliente

A função abaixo recebe um cliente_id como parâmetro e retorna o total de vendas desse cliente utilizando SUM().

````sql
DELIMITER $$

CREATE FUNCTION total_vendas_cliente (id_cliente INT)
RETURNS DECIMAL(10, 2)
DETERMINISTIC
BEGIN
    DECLARE total DECIMAL(10, 2);

    -- Calcula a soma dos valores de vendas para o cliente específico
    SELECT SUM(valor) 
    INTO total
    FROM vendas
    WHERE cliente_id = id_cliente;

    -- Retorna o total calculado
    RETURN total;
END $$

DELIMITER ;
````

### Explicação da Função

#### Parâmetro:

- id_cliente: Identifica o cliente cujas vendas devem ser somadas.

#### Variável:

- total: Armazena temporariamente a soma calculada.

#### Query:

- SELECT SUM(valor) INTO total: Soma os valores da coluna valor para o cliente_id informado.

#### Retorno:

- Retorna o valor calculado da variável total.

#### Testando a Função

Para testar a função, use o comando SELECT:

````sql
SELECT total_vendas_cliente(101) AS total_vendas;
````

#### Resultado:

````diff
+--------------+
| total_vendas |
+--------------+
|       450.00 |
+--------------+
````

Este comando calcula o total de vendas (150.00 + 300.00 = 450.00) para o cliente com cliente_id = 101.


