# MondotSyn — README

Este repositório contém informações sobre a sintaxe da linguagem de programação desenvolvida e planejada por mim.

A linguagem tem como paradigma **imperativo com suporte a orientação a objetos leve e funções sobrecarregadas**. Ela combina características de programação procedural, manipulação de estruturas de dados complexas e métodos de objetos, permitindo que o programador organize código de forma modular e reutilizável.

Você pode ver a implementação em progresso da linguagem em [mondot](https://github.com/soloverdrive/mondot).

## Sumário

* [Estruturas principais da linguagem](#estruturas-principais-da-linguagem)
* [Statements](#statements)
* [Operadores](#operadores)
* [Diagnóstico da linguagem](#diagnóstico-da-linguagem)
* [Especificações](#especificações)

## Estruturas principais da linguagem

### Units

O código é organizado em **units**, que funcionam como módulos, no qual o programa bytecode ou projeto mondot inteiro pode ser iniciado a partir da unit passada. Cada unit pode conter:

* Declarações de **funções**
* Declarações de **propriedades** (tipos compostos/objetos/valores)
* Função de entrada **main**

Programa mondot básico:

```mon
unit myproj.file.program:

on void main()
    number x = 100000
    print("start")
    
    while (x > 0)
        print(x)
        x = x - 1
    end

    print("end")
end
```

# Statements

* **units**
    ```
    unit file.unitname:
    body
    ```
    or
    ```
    unit file.unitname{
        body
    }
    ```

* **variáveis**
    ```
    T name = value
    ```

* **condicionais**
    ```
    if (cond)
        body-if
    or (cond2)
        body-else-if
    or
        body-else
    end
    ```

* **loops**
    ```
    while (cond)
        body
    end

    for (iterator: T in values)
        body
    end
    ```

* **manipulação de erros**
    ```
    try
        danger-body
    catch error-type
        handle-body
    finally
        defer-body
    end
    ```

* **instanciamento de item**
    ```
    item name
        T value
        T value2
        ...
    end
    ```

* **funções**
    ```
    on T funcname(args: T)
        body
    end

    funcname() -- call
    ```

# Operadores

* Aritméticos
    * `+` `-` `*` `/` `%`
* Lógicos
    *  `!` `==` `!=` `<=` `>=` `|`  `&`  `<`  `>`
* Atribuição
    *  `+=`  `-=`  `|=` `&=`

---

# Diagnóstico da linguagem

Mondot foi projetada com foco em **simplicidade, previsibilidade e desempenho** — especialmente adequada para cenários embarcados e engines onde controle de memória e determinismo são cruciais.

# Especificações

## Modelo de tipos

* **Tipagem**: Estática com conversões controladas em runtime quando any está envolvido
* **Tipo especial**: `any` — resolvido em runtime, contagioso (qualquer operação envolvendo `any` produz `any`).
* **Tipos primitivos**: `number`, `string`, `void`, `bool` (implícito)
* **Compostos**: `table` (lista heterogênea), `list`
* **Registro leve**: `item` — record mutável e leve, passado por referência implícita.
* **Tipo função**: `function`, valores de primeira classe (podem ser armazenados e invocados), *sem captura* de variáveis externas.
> [!NOTE]
> item representa um registro leve e previsível, enquanto table é uma estrutura dinâmica e mais pesada, adequada para dados heterogêneos e scripts.

## Sintaxe de blocos

* Blocos terminam com `end`.
* Não há `{}` ou indentação obrigatória para delimitar blocos, `{}` podem ser usados apenas para delimitar units.

## Units e visibilidade

* Todos os símbolos declarados em uma `unit` são **públicos**.
* Convenção: nomes que começam com `_` indicam uso interno (apenas warnings em ferramentas).

## Semântica de memória

* **Lifetime por escopo**: tudo alocado em um escopo é destruído quando o escopo termina.
* Modelo implementável com frames de stack + arenas regionais por escopo.

## Passagem de parâmetros

* `number`, `string` e primitivos: **passados por valor**.
* `list`, `table` e `item`: **mutáveis, passados por referência implícita**.

## Funções e sobrecarga

* Overload resolvido **em tempo de compilação** com base nas assinaturas (exceto quando `any` está presente).
* Chamada para função com `any` envolve despacho dinâmico no runtime.

## Convenção de chamada

* Parâmetros até N são passados via registradores (r0..rN), extras via stack frame.
* Retorno em r0.
* `item`/`list`/`table` passam referência (registrador contém pointer/handle para arena).

## Resolução de sobrecargas

* Determinação por lista de tipos exata.
* Se múltiplas overloads aplicáveis, preferir a mais específica (menor uso de `any`/coerções).
* Se ambíguo, erro de compilação.

## Semântica do `any`

* `any` carrega tags de tipo em runtime (number|string|table|item|function|nil|...).
* Operações em `any` usam despachos: por exemplo `+` chama rotina dyn_add(tag1, tag2).
* Expressões com `any` resultam em `any`.

## Exceções

* `try` / `catch (error e)` / `finally` suportados.
* Stack unwinding garante destruição de valores do scope.

## Bibliotecas nativas

* `std`
    * `print()` — saída do console
    * `input()` — entrada do console
    * `flush()` — flush do console
    * ... 
* `std.extensions`
    * `append()` — para listas e tables
    * `insert()` — para tables
    * `len()` — para listas e tables
    * `create()` — construtor de `item` e coleções
    * ... 
* `std.math`
    * `sin()` — 
    * `cos()` — 
    * `tg()` — 
    * ... 
