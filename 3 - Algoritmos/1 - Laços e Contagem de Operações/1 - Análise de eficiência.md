Pseudocódigo:
```
f1(v []int, tam, elem):
	i = 0
	enquanto (i < tam):
		se elem == v[1]:
		devolva 1
	i += 1
devolva 0
```

Go:
```go
func f1(v []int, tam int, elem int) int {
	for i := 0; i < tam; i++ {
		if elem == v[1] {
		return 1
		}
	}
	return 0
}
```

Esse é um código que basicamente checa se `elem` está dentro do array `v` na posição `1`.

Nesse código há um laço de repetição, onde a eficiência é O(tam), ou seja, o laço faz iterações proporcionais a variável `tam` (tendo em vista que `i` é incrementado de 1 em 1). O número de iterações é da Ordem de `tam` (O(tam)).

E a condicional que se encontra dentro do laço, é de eficiência O(1), pois ela independe da entrada. O desempenho do laço depende da Ordem de `tam`, já a eficiência da condicional é `constante`, não muda seu desempenho.

Um jeito de calcular o tempo total desse algoritmo é:
O algoritmo realiza `tam` operações, só que dentro do laço, leva um tempo `constante`.

Então:
![[Pasted image 20240104221151.png]]
_Obs.: em cima do ∑ é `tam-1` por que quando `i == tam`, ele termina o somatório (laço)._
_Obs2.: as contas levam sempre em consideração o pior caso de desempenho, nesse caso, percorrendo todos os valores de `tam`._

continuar em: https://youtu.be/1Uz8wXKdzcA?list=PL5TPkym335qztD0JofGEyVcdmlNf7arzE&t=500
