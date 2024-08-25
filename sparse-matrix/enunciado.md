## Matriz Esparsa

Uma matriz é **esparsa** se tem proporcionalmente muitos zeros. "Muitos" não é uma quantidade definida claramente, mas em geral se uma matriz com m linhas e n colunas tem um número de entradas não-zero da ordem de m+n então ela é considerada esparsa.
Quando uma matriz esparsa é colocada na memória, uma parte significativa do espaço é usada para armazenar zeros, que em muitos casos é simplesmente ausência de informação.

Por exemplo, suponha uma matriz de 10.000 x 30.000 inteiros que tenha 3.000 entradas não-zero. Se um int ocupar 4 bytes então a matriz ocupará 1.200.000.000 bytes na memória, mas apenas 12.000 bytes não serão zeros.

Estruturas de dados para matrizes esparsas não armazenam os zeros. Elas sacrificam o tempo de acesso constante a qualquer elemento oferecido pelas matrizes para usar menos memória. Algumas estruturas permitem recuperação eficiente dos elementos da matriz, mas são ineficientes na atualização. Outras permitem atualização mais eficiente, mas usam mais memória.

Uma dessas representações é o vetor-de-coordenadas: cada elemento não-zero `M[i,j] = x` é colocado em um vetor em que cada posição armazena uma tripla `(i,j,x)`.

Por exemplo, a matriz

	0 0 0 0
	7 0 0 0
	0 0 9 0  
	0 0 0 0
	0 8 0 5

pode ser representada em um vetor-de-coordenadas da forma:

	| 1,0,7 | 2,2,9 | 4,1,8 | 4,3,5 |

A recuperação dos elementos da matriz não é muito eficiente: é necessário percorrer o vetor-de-coordenadas desde o início para determinar se um elemento da matriz está ou não armazenado nele. Para melhorar um pouco a eficiência da recuperação, o vetor pode ser mantido ordenado por `i` e, para o mesmo `i`, por `j`. Por outro lado, se a matriz for atualizada e algum elemento deixar de ser zero ou passar a ser zero, a atualização do vetor-de-coordenadas mantendo-o ordenado é ainda mais custosa.

Uma outra forma de representação de matrizes esparsas que permite recuperação mais rápida que o vetor-de-coordenadas é a CSR (compressed sparse row).

Seja `M` uma matriz `m` <span style="font-size:0.8em;">x</span> `n` com `k` elementos diferentes de zero, com linhas e colunas indexadas a partir de 0. A representação CSR usa 3 vetores:

`A`, de tamanho `k`, armazenas os elementos não-zero de `M` por linhas.
`C`, de tamanho `k`, armazena a coluna de cada elemento de A.
`R`, de tamanho `m+1`, armazena a posição em `A` do primeiro elemento em cada linha não-vazia de `M`. `R[m]` é igual a `k`. Se a linha i estiver vazia então `R[i]` será igual a `R[t]`, onde `t` é a primeira linha depois da linha `i` que não é vazia. Se não houver tal `t` então `R[i] = R[m]`. `R[i+1]-R[i]` é o número de elementos não-zero na linha `i`.
Um elemento `M[i,j]` pode ser recuperado da seguinte forma:

Inspecionam-se `R[i]` e `R[i+1]` para determinar o intervalo ocupado pelos elementos da linha `i` em `C` (e também em `A`).
Se `R[i]` é igual a `R[i+1]` então a linha i tem apenas zeros, e `M[i,j]` é igual a zero. Senão `j` é procurado em `C[R[i]], ..., C[R[i+1]-1]`.
Se `j` existe em `C[R[i]], ..., C[R[i+1]-1]` então o valor de `A` na mesma posição de `C` em que `j` foi encontrado é `M[i,j]`. Senão `M[i,j]` é igual a zero.

Por exemplo, a matriz `M` 4 <span style="font-size:0.8em;">x</span> 8

	0  0  0  0  2  0  6  5
	4  0  0  0  0  1  0  0
	0  0  0  0  0  0  0  0
	0  0  0  0  3  0  8  1

é representada da seguinte forma em CSR

	A  =  2  6  5  4  1  3  8  1
	C  =  4  6  7  0  5  4  6  7
	R  =  0  3  5  5  8       

Para recuperar o elemento `M[3,4]`, verificam se `R[3] = 5` e `R[4] = 8` e como `8-5` é igual a 3, há três elementos não-zero na linha `M[3]`. Eles estão armazenados no subvetor `A[5], ..., A[7]`. Percorre-se o subvetor `C[5], ..., C[7]`. A coluna 4 ocorre em `C[5]` e então `M[3,4]` é igual a `A[5] = 3`.

Para recuperar o elemento `M[1,6]`, verificam se `R[1] = 3` e `R[2] = 5` e como `5-3` é igual a 2, há dois elementos não-zero na linha `M[1]`. Eles estão armazenados no subvetor `A[3], A[4]`. Percorre-se o subvetor `C[3],C[4]`. A coluna 6 não ocorre e então `M[1,6]` é igual a 0.

Escreva um programa para recuperar elementos de uma matriz esparsa de inteiros `M`. Inicialmente seu programa deve ler um conjunto de `k` triplas `(i,j,x)` e armazená-las em um vetor-de-coordenadas. Depois deve converter o vetor-de-coordenadas em CSR. Finalmente, o programa deve usar a representação CSR para recuperar entradas da matriz esparsa.

**Entrada**

A entrada inicia-se com uma linha contendo o valor inteiro `k`, `k>0`. Depois seguem-se `k` linhas contendo três inteiros `i`, `j` e `x`, sendo `i`,`j ≥ 0`.
Cada linha seguinte tem dois inteiros `i` e `j` cada, `i`,`j ≥ 0`, índices de um elemento que deve ser recuperado da matriz. A seqüência de consultas é terminada por valores -1.

Para cada linha de consulta o programa deve imprimir o valor do elemento correspondente, formatado como no exemplo abaixo.

**Saída** 

Exemplos:<br>
Entrada

	9
	4 4 9
	4 5 8
	2 3 6
	2 4 5
	0 4 3
	0 5 7
	1 0 1
	2 2 2
	4 1 4
	3 3
	1 1
	5 5
	6 6
	2 2
	4 4
	7 7
	-1 -1

Saída

	(3,3) = 0
	(1,1) = 0
	(5,5) = 0
	(6,6) = 0
	(2,2) = 2
	(4,4) = 9
	(7,7) = 0

<br/>**Requisitos adicionais**

Não pode haver qualquer variável global. Uma variável é global se estiver declarada fora de alguma função (variáveis declaradas dentro da main não são globais, são locais à função main).
Seu programa deve usar apenas 4 vetores.
Toda memória alocada dinamicamente deve ser liberada antes do fim do programa.

**Sugestões**

Comece fazendo um programa que apenas lê a entrada e imprime. Depois adicione as demais funcionalidades.

**Sobre organização do código e comentários**

Faça um programa organizado, bem indentado e que seja fácil de ler.
Adicione comentários que vão ser úteis para entender o programa se você for relê-lo daqui a alguns anos: comentar cada linha vai ser redundante; documentar blocos de código e a estratégia usada na solução vai ser muito útil.

