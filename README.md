
# *Structs* na linguagem C

## Pedro Figueirêdo, Sandoval Veríssimo e Thiago Wesley


### Roteiro da aula:
1. Introdução
1. Declaração
1. Inicialização
1. Acesso a membros
1. Structs como argumento e retorno de funções
    * *Structs* e *arrays*
    * Manipulação de *structs* por registradores
1. Ponteiros para struct
    * Operador "->"

## Introdução

<p><div style="text-align: justify">*Structs* também conhecida como registro, é um bloco que armazena diversas informações, ou seja, permite o armazenamento de diferentes tipos de dados na mesma estrutura, diferentemente dos vetores e matrizes que trabalham apenas com elementos do mesmo tipo. Desta forma, ao declarar uma struct está se declarando um novo tipo de dado.</div></p>
<p><div style="text-align: justify">Como todos os tipos de dados primitivos têm tamanho definido, com *structs* não é diferente. Entretanto, o seu tamanho é definido pelos seus atributos. O tamanho de um tipo *struct* será a soma dos tamanhos de seus campos. Caso esse tamanho não seja ideal para a arquitetura do processador é feito, automaticamente, um *padding* (alocação de bytes não-signicativos) que serve para facilitar o trabalho de leitura de memória e colocação em registradores. Por isso, nem sempre uma *struct* tem tamanho igual à soma de seus atributos. Tal fatalidade é combatida pela diretiva de compilação *pragma*, que informa ao compilador que não é necessário realizar o *padding*.</div></p>
<p><div style="text-align: justify">A importância de se usar *struct* é pelo fato de as vezes o programador precisar trabalhar com elementos que possuem várias características, desta forma, usando esse recurso da linguagem C, permite a manipulação desses elementos de forma mais prática, eficiente e organizada.</div></p>


## Declaração

Para declarar-se uma *struct*, segue-se uma fórmula genérica:

```C
struct tag_name {
    type member1;
    type member2;
    // Pode-se declarar quantos membros forem desejados
}
```

Há também a possibilidade de usar a palavra reservada *typedef* a qual define um novo tipo, eliminando a necessidade do termo *struct* sempre que se deseja referenciar esse tipo.

```C
typedef struct{
    type member1;
    type member2;
    // Pode-se declarar quantos membros forem desejados
} tag_name;
```

Como um exemplo, pode-se ver, abaixo, a declaração da *struct* Pessoa, a qual será usada ao longo da aula para explicar futuros conceitos.
```C
typedef struct{
    char* nome;
    int idade;
} Pessoa;
```

### <font color='red'>Alerta de Erro</font>
C não permite que se use, como atributo, uma nova instância do mesmo tipo sendo declarado.


```c
typedef struct{
    char* nome;
    int idade;
    struct Pessoa parent;
} Pessoa;

int main(){ return 0;}
```

    /Users/PedroFigueiredo/anaconda/lib/python3.6/site-packages/jupyter_c_kerneltt1z6pti/0.c:4:19: error: field has incomplete type 'struct Pessoa'
        struct Pessoa parent;
                      ^
    /Users/PedroFigueiredo/anaconda/lib/python3.6/site-packages/jupyter_c_kerneltt1z6pti/0.c:4:12: note: forward declaration of 'struct Pessoa'
        struct Pessoa parent;
               ^
    1 error generated.
    [C kernel] GCC exited with code 1, the executable will not be executed

Como resolver? Será visto no tópico sobre ponteiros para *struct*.

## Inicialização

Utilizando o exemplo da *struct* Pessoa:

```C
typedef struct{
    char* nome;
    int idade;
} Pessoa;
```

Há três formas de se inicializar uma *struct* em C:
```C
// Inicializar os primeiros dois membros em ordem disposta na *struct*:
Pessoa p = {"Pedro", 19};
    
// Inicializar usando os identificadores das variáveis da *struct*:
Pessoa s = {.idade = 20, .nome = "Sandoval"};
    
// Inicializar utilizando a cópia de atributos de outra instância
Pessoa t = p;
```

OBS: Para as duas primeiras maneiras de inicialização, não é necessário informar todos os parâmetros. Os que forem omitidos serão iniciados com **zero**.

Para a inicialização de um *array* de tipos de estruturas, basta aninhar as inicializações de cada elemento:
```C
Pessoa amigos[3] = {{"Pedro", 19},{.idade = 20, .nome = "Sandoval"}, t};
```

### <font color='red'>Alerta de Erro</font>
Não se pode inicializar os atributos na declaração de uma *struct*.


```c
typedef struct{
    char* nome = "Pedro";
    int idade = 19;
} Pessoa;

int main(){ return 0;}
```

    /Users/PedroFigueiredo/anaconda/lib/python3.6/site-packages/jupyter_c_kerneltt1z6pti/0.c:2:15: error: expected ';' at end of declaration list
        char* nome = "Pedro";
                  ^
                  ;
    /Users/PedroFigueiredo/anaconda/lib/python3.6/site-packages/jupyter_c_kerneltt1z6pti/0.c:3:14: error: expected ';' at end of declaration list
        int idade = 19;
                 ^
                 ;
    2 errors generated.
    [C kernel] GCC exited with code 1, the executable will not be executed

## Acesso a membros

O operador "**.**" é usado para fazer o acesso a atributos de uma *struct*.


```c
#include <stdio.h>
typedef struct{
    char* nome;
    int idade;
} Pessoa;

int main(){
    // Inicializa-se apenas o primeiro atributo de Pessoa
    Pessoa t = {"Thiago"};
    
    printf("Idade de %s: %d\n", t.nome, t.idade);
    
    // Altera-se o valor do campo idade de 0 para 23
    t.idade = 23;
    
    printf("Idade de %s: %d\n", t.nome, t.idade);
    
    return 0;
}
```

    Idade de Thiago: 0
    Idade de Thiago: 23


## *Structs* como argumento e retorno de funções

As *structs*, assim como os outros tipos primitivos de C, podem ser passadas e retornadas por valor, quando em uma função. 

### *Structs* e *arrays*
<p><div style="text-align: justify">Uma dúvida frequente é: **por que é possível passar como parâmetro e retornar uma *struct* por valor, mas não se pode fazer o mesmo com *arrays*?**. Isso acontece, pois um *array*, na verdade, é interpretado como um espaço de memória e não um tipo de dados. Para esse espaço de memória ser acessado, usa-se o endereço do seu primeiro elemento (que é representado pelo seu próprio identificador). Logo, quando se invoca uma função com um *array* como parâmetro, recebe-se apenas o endereço para seu primeiro elemento, e não o *array* em sua totalidade, o mesmo valendo para quando é retornado, o que implica que a função terá como parâmetro formal, ou tipo de retorno, um ponteiro para o tipo dos elementos do *array*. Já a *struct* é um tipo de dados como os primitivos e, assim, tem seus atributos copiados na passagem por parâmetro e no retorno.</div></p>

<p><div style="text-align: justify">**Já que uma *struct* pode ser passada como parâmetro e retornada por valor, como é feita a manipulação desse tipo de dados pelos registradores?**</div></p>

### Manipulação de *structs* por registradores
Uma *struct* pode ser passada como valor ou endereço. Para analisar o custo de cada uma das opções de passagem por parâmetro e retorno, foi feito um teste com o seguinte trecho de código:

```C
struct Pessoa{
  int idade_;
  double peso_;
  double altura_;
  int nIrmaos_;
    
};

struct Pessoa f(struct Pessoa s){ 
  return s;
}

```

Como pode-se ver, a *struct* *s* será passada por parâmetro para a função f() por valor. Para comparar a eficiência, gera-se o assembly correspondente:

```Assembly
   struct Pessoa f(struct Pessoa s){
   0x0000000000400546 <+0>:    push   %rbp
   0x0000000000400547 <+1>:    mov    %rsp,%rbp
   0x000000000040054a <+4>:    mov    %rdi,-0x8(%rbp)

   return s;
   0x000000000040054e <+8>:     mov    -0x8(%rbp),%rax
   0x0000000000400552 <+12>:    mov    0x10(%rbp),%rdx
   0x0000000000400556 <+16>:    mov    %rdx,(%rax)
   0x0000000000400559 <+19>:    mov    0x18(%rbp),%rdx
   0x000000000040055d <+23>:    mov    %rdx,0x8(%rax)
   0x0000000000400561 <+27>:    mov    0x20(%rbp),%rdx
   0x0000000000400565 <+31>:    mov    %rdx,0x10(%rax)
   0x0000000000400569 <+35>:    mov    0x28(%rbp),%rdx
   0x000000000040056d <+39>:    mov    %rdx,0x18(%rax)

   }
   0x0000000000400571 <+43>:    mov    -0x8(%rbp),%rax
   0x0000000000400575 <+47>:    pop    %rbp
   0x0000000000400576 <+48>:    retq
```

Pôde-se ver que foram feitas muitas operações com os registradores de uso geral no momento do retorno da função f(). Isso ocorre devido às cópias por valor dos atributos para que fosse retornado à *main* um novo objeto *struct* Pessoa idêntico ao passado por parâmetro.

Adaptou-se o código para passar o objeto *s* por endereço, como pode ser visto abaixo:

```C
struct Pessoa* f(struct Pessoa* s){
  return s;
}
```

Então, gerou-se o assembly correspondente:

```Assembly
   struct Pessoa* f(struct Pessoa* s){
   0x0000000000400546 <+0>:    push   %rbp
   0x0000000000400547 <+1>:    mov    %rsp,%rbp
   0x000000000040054a <+4>:    mov    %rdi,-0x8(%rbp)
   
   return s;
   0x000000000040054e <+8>:    mov    -0x8(%rbp),%rax

   }
   0x0000000000400552 <+12>:    pop    %rbp
   0x0000000000400553 <+13>:    retq
```

Vê-se que é feita apenas uma instrução *mov*, a qual transfere o valor do ponteiro que armazena o endereço da *struct* *s* para o registrador eax, retornando-o à *main*.

Portanto, ao examinar os dois códigos assembly, conclui-se que apesar de uma struct poder ser passada por valor, é muito mais eficiente passá-la por endereço, o qual é armazenado em um ponteiro para *struct*, que será explicado a seguir.


## Ponteiros para *struct*

<p><div style="text-align: justify">Utilizar ponteiros para armazenar endereços de *structs* é um artifício muito útil na linguagem C, já que é possível referenciar todos os atributos de uma *struct*, apenas com seu endereço, não sendo necessária uma cópia. Por isso, na prática, faz muito mais sentido passar-se o endereço de uma *struct* para uma função como parâmetro, ao invés de uma cópia, a qual é muito mais custosa, como visto anteriormente.</div></p>
<p><div style="text-align: justify">OBS: Há de se tomar cuidado com o acesso a *structs* por meio de ponteiros, já que estes permitem a alteração dos dados. Caso se queira garantir que os dados de uma *struct* passada por endereço para uma função não sejam modificados, pode-se usar a palavra reservada *const* **antes** do tipo do ponteiro.</div></p>

```C
// Nesse caso, o objeto apontado por p não poderá ser alterado
extern void f(const Pessoa * p);
```

<p><div style="text-align: justify">A solução do problema citado no tópico "Declaração" é utilizar um ponteiro para a estrutura em sua declaração, visto que o tamanho para alocar-se um ponteiro de tipo qualquer é fixo, portanto, o compilador não necessita de mais nada para definir o tamanho necessário de tal tipo, como se pode ver na declaração abaixo:</div></p>


```c
typedef struct{
    char* nome;
    int idade;
    struct Pessoa* parent;
} Pessoa;

int main(){ return 0;}
```

### Operador "->"
Para acessar um atributo da *struct* apontado por um ponteiro, normalmente se recuperaria o objeto apontado por meio do operador de indireção e, só depois, utilizaria-se o operador "." para acessar o campo.

```C
Pessoa* p;
// Atribui a idade da pessoa referenciada pelo ponteiro p à variàvel idadeAux
int idadeAux = (*p).idade;
```

Essa sequência de passos pode resultar em erros de programação, visto que, pelo o operador "." ter precedência maior que o operador de indireção, se tentarmos acessar sem o uso de parênteses, obeteremos:


```c
typedef struct{
    char* nome;
    int idade;
    struct Pessoa* parent;
}Pessoa;

int main(){ 
    Pessoa* pParent;
    /* Como a precedência do operador "." é maior que o operador de indireção,
    *  a instrução abaixo se torna equivalente a: int idadePai = *(pParent.idade);
    */
    int idadePai = *pParent.idade;
    return 0;
}
```

    /Users/PedroFigueiredo/anaconda/lib/python3.6/site-packages/jupyter_c_kerneltt1z6pti/0.c:12:28: error: member reference type 'Pessoa *' is a pointer; did you mean to use '->'?
        int idadePai = *pParent.idade;
                        ~~~~~~~^
                               ->
    /Users/PedroFigueiredo/anaconda/lib/python3.6/site-packages/jupyter_c_kerneltt1z6pti/0.c:12:20: error: indirection requires pointer operand ('int' invalid)
        int idadePai = *pParent.idade;
                       ^~~~~~~~~~~~~~
    2 errors generated.
    [C kernel] GCC exited with code 1, the executable will not be executed

Para diminuir o risco de erros de sintaxe, foi introduzido, à linguaguem C, o operador "->". Este é equivalente a usar o operador de indireção + operador ".", na sequência correta, a qual foi apresentada anteriormente.
```C
Pessoa* p;
// Atribui a idade da pessoa referenciada pelo ponteiro p à variàvel idadeAux
int idadeAux = p->idade;
```
