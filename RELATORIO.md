# 📝 Relatório do Laboratório 2 - Chamadas de Sistema

---

## 1️⃣ Exercício 1a - Observação printf() vs 1b - write()

### 💻 Comandos executados:
```bash
strace -e write ./ex1a_printf
strace -e write ./ex1b_write
```

### 🔍 Análise

**1. Quantas syscalls write() cada programa gerou?**
- ex1a_printf: 9 syscalls
- ex1b_write: 7 syscalls

**2. Por que há diferença entre os dois métodos? Consulte o docs/printf_vs_write.md**

```
A diferença entre o printf e o write() é que voce utiliza o printf quando precisar formatar os dados, escrever de uma forma simples para o usuário e não for preciso alta performance, e o write() é o esqueleto que o printf utiliza, e voce utiliza o write() direto quando precisar ter total controle dos dados, alta performance e quiser um comportamento previsivel
```

**3. Qual método é mais previsível? Por quê você acha isso?**

```
O método mais previsivel é o do write, pois além dele ser o esqueleto que o printf utiliza, ele tem maior controle dos dados que ele envia, e além de para cada mensagem voce consegue controlar o acesso ao kernel, e consegue ter uma alta perfomance no seu programa.
```

---

## 2️⃣ Exercício 2 - Leitura de Arquivo

### 📊 Resultados da execução:
- File descriptor: 3
- Bytes lidos: 127

### 🔧 Comando strace:
```bash
strace -e openat,read,close ./ex2_leitura
```

### 🔍 Análise

**1. Qual file descriptor foi usado? Por que não começou em 0, 1 ou 2?**

```
Por que eles são os 3 primeiros files descriptors pré-definidos, e o file descriptor do código rodado foi 3 por que ele foi o primeiro livre dps do 0,1 e 2
 ```

**2. Como você sabe que o arquivo foi lido completamente?**

```
Tirando confiar no que está escrito, que é ultima linha, é contar os bytes lidos ou checar no próprio arquivo
```

**3. Por que verificar retorno de cada syscall?**

```
Para verificar que não teve alteração ou um resultado diferente do que esperado
```

---

## 3️⃣ Exercício 3 - Contador com Loop

### 📋 Resultados (BUFFER_SIZE = 64):
- Linhas: 25 (esperado: 25)
- Caracteres: 1300
- Chamadas read(): 21
- Tempo: 0.000147 segundos

### 🧪 Experimentos com buffer:

| Buffer Size | Chamadas read() | Tempo (s) |
|-------------|-----------------|-----------|
| 16          |     82          |  0.000197 |
| 64          |    21           |0.000147   |
| 256         |    6            |0.000066   |
| 1024        |     2           |0.000068   |

### 🔍 Análise

**1. Como o tamanho do buffer afeta o número de syscalls?**

```
Quanto maior o buffer, menos syscalls são necessárias para a conclusão do programa.
```

**2. Todas as chamadas read() retornaram BUFFER_SIZE bytes? Discorra brevemente sobre**

```
Todas menos a ultima, por que o read() sempre tentara ler o todo o seu buffer size, mas caso o ultimo não tenha a quantidade de bytes do buffer por ser o final do arquivo, ele retornara o valor total que leu, 
```

**3. Qual é a relação entre syscalls e performance?**

```
Quanto menos syscalls são realizadas, menos interrupções teram que ser feitas, então menos tempo será gasto para realizar o programa.
```

---

## 4️⃣ Exercício 4 - Cópia de Arquivo

### 📈 Resultados:
- Bytes copiados: 1364
- Operações: 6
- Tempo: 0.000200 segundos
- Throughput:6660.16 KB/s

### ✅ Verificação:
```bash
diff dados/origem.txt dados/destino.txt
```
Resultado: [X] Idênticos [ ] Diferentes

### 🔍 Análise

**1. Por que devemos verificar que bytes_escritos == bytes_lidos?**

```
Por que pode ter realizado alguma variação ou erro no código ou na escrita/leitura
```

**2. Que flags são essenciais no open() do destino?**

```
O_WRONLY: Abre o arquivo para escrita, O_CREAT: Garante que o arquivo seja criado se ele ainda não existir, O_TRUNC:** Se o arquivo já existir, esta flag o trunca para um tamanho de zero bytes, garantindo que o novo conteúdo não seja anexado ao antigo.
```

**3. O número de reads e writes é igual? Por quê?**

```
Sim, ocorre a mesma quantidade de write() e read() porque a chamada `write()` está diretamente dentro do loop `while`, e o loop é executado uma vez para cada chamada `read()` que retorna um número de bytes maior que zero.
```

**4. Como você saberia se o disco ficou cheio?**

```
O programa saberia que o disco ficou cheio através do valor de retorno da syscall `write()`. Se o disco estiver cheio, a chamada `write()` falhará e retornará `-1`.
```

**5. O que acontece se esquecer de fechar os arquivos?**

```
Se o programa esquecer de fechar os arquivos, ocorrerá um file descriptor leak
```

---

## 🎯 Análise Geral

### 📖 Conceitos Fundamentais

**1. Como as syscalls demonstram a transição usuário → kernel?**

```
As syscalls são a forma que ocorre a transição do modo usuário para o modo kernel. Quando um programa no modo usuário precisa de um serviço privilegiado do sistema operacional, ele não pode executá-lo diretamente. Em vez disso, ele faz uma chamada de sistema.
```

**2. Qual é o seu entendimento sobre a importância dos file descriptors?**

```
File descriptors (FDs) são inteiros não-negativos que o kernel do sistema operacional usa para representar uma abstração de um recurso de E/S
```

**3. Discorra sobre a relação entre o tamanho do buffer e performance:**

```
O tamanho do buffer é crucial para a performance de operações de E/S, pois ele determina a frequência com que as syscalls são realizadas. quanto maior o buffer, melhor a performance
```

### ⚡ Comparação de Performance

```bash
# Teste seu programa vs cp do sistema
time ./ex4_copia
time cp dados/origem.txt dados/destino_cp.txt
```

**Qual foi mais rápido?** user

**Por que você acha que foi mais rápido?**

```
Por que o user tinha menos ações para realizar
```

---

## 📤 Entrega
Certifique-se de ter:
- [x] Todos os códigos com TODOs completados
- [x] Traces salvos em `traces/`
- [x] Este relatório preenchido como `RELATORIO.md`

```bash
strace -e write -o traces/ex1a_trace.txt ./ex1a_printf
strace -e write -o traces/ex1b_trace.txt ./ex1b_write
strace -o traces/ex2_trace.txt ./ex2_leitura
strace -c -o traces/ex3_stats.txt ./ex3_contador
strace -o traces/ex4_trace.txt ./ex4_copia
```
# Bom trabalho!
