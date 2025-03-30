# O truque  $\sqrt{n}$

Em vários problemas que envolvem vetores, é possível fazer uma otimização do tempo de execução simples de implementar, usando alguma memória extra.  

Por exemplo, vamos supor que queremos usar um vetor para construir uma estrutura de dados que armazena uma sequência de inteiros com duas operações:  

- **somar($i$, $j$)**, que calcula $S_i + S_{i+1} + \dots + S_j$.  
- **atualizar($i$, $x$)**, que faz $S_i = x$.  

Usando-se apenas um vetor $V$ de tamanho $n$ para armazenar os elementos de $S$, a operação **somar** realizará $j - i + 1$ acessos a $V$ e $j - i$ adições. A operação **atualizar** realizará apenas um acesso a $V$.  

Com o truque, usam-se dois vetores:  
- Um vetor $V$ guarda os $n$ elementos de $S$.  
- O outro vetor $R$ tem tamanho $\lceil \sqrt{n} \rceil$.  

Vamos denotar $r = \lceil \sqrt{n} \rceil$. O vetor $R$ guarda as somas dos elementos de blocos de $V$ de tamanho $r$, de tal forma que:  

$R[0] = V[0] + V[1] + \dots + V[r-1]$
\
$R[1] = V[r] + V[r+1] + \dots + V[2r-1]$
\
$R[2] = V[2r] + V[2r+1] + \dots + V[3r-1]$


e assim por diante.  

Observe que se $n$ não for da forma $k \cdot r$, então os últimos elementos de $R$ corresponderão a blocos com menos que $r$ elementos, mas isso não é um problema.  

Por exemplo, suponha um vetor $V$ de tamanho 22. O vetor $R$ terá tamanho 5.  

### Operações  

Para realizar **somar($i$, $j$)**:  
- Somam-se os elementos de $R$ correspondentes aos blocos totalmente contidos em $[i, j]$.  
- Somam-se individualmente os elementos à direita de $i$ e à esquerda de $j$ que forem necessários.  

Exemplo: para calcular **somar($3$, $20$)** nos vetores da figura, fazemos:  


$R[1] + R[2] + R[3] + V[3] + V[4] + V[20]$


Sem usar o truque, seriam necessárias 18 adições.  

Dessa forma, a operação **somar** fará no máximo $3\sqrt{n} - 4$ acessos ao vetor e $3\sqrt{n} - 3$ adições.  

Para realizar **atualizar($i$, $x$)**, é necessário atualizar $V[i]$ e $R$, com 2 acessos ao vetor (e algumas operações aritméticas).  

---

## Entrada  

- A primeira linha contém um número inteiro $n$, representando a quantidade de elementos em $S$.  
- A segunda linha contém os $n$ elementos de $S$, números inteiros, separados por espaço.
- As próximas linhas contêm uma das duas operações:  

  - `s i j`: onde $i$ e $j$ são inteiros entre $0$ e $n-1$. O programa deve imprimir a soma dos elementos no intervalo $[i, j]$ em uma linha.  
  - `a i x`: onde $i$ é um inteiro entre $0$ e $n-1$ e $x$ é um inteiro qualquer. O programa deve atualizar o elemento $S[i]$ para o valor $x$.  

## Saída  

A saída do programa deve conter as respostas para as operações `s`, uma por linha.  

### Exemplo  

#### Entrada  
```
22  
8 3 1 4 1 0 8 0 5 1 2 7 9 3 4 3 4 2 1 1 2 7  
s 3 20  
s 7 12  
a 5 13  
a 6 4  
s 4 19  
```

#### Saída  
```
57  
24  
60  
```

---

## Sugestões  

- Comece fazendo um programa que apenas lê a entrada e imprime. Depois, adicione as demais funcionalidades.  

### FAQ  

**P.: Se eu resolver com apenas um vetor, sem usar o truque, perco pontos?**  
**R.: Sim, todos.**  

---

## Boas práticas  

- Faça um programa organizado, bem indentado e fácil de ler.  
- Adicione comentários úteis para entender o programa no futuro.  
- Não comente cada linha individualmente, mas documente os blocos de código e a estratégia usada. 