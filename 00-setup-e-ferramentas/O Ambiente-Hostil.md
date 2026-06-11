# [00] O Ambiente Hostil: Terminal, Vim e GCC

Minha ideia inicial ao começar a programar e compilar C via terminal, nasceu de uma curiosidade de saber como cada "engrenagem" funciona. Essa curiosidade nasceu ao jogar um wargame chamado "OverTheWire: Bandit", onde a fase 12 foca em engenharia reversa de um hex dump, e se consolidou ao assistir ao vídeo "Qual a REAL diferença entre Arquivos Binário e Texto??" do Fabio Akita.

A partir dessas referências, ficou claro que ambientes de desenvolvimento integrados (IDEs) abstraem excessivamente o processo. Para entender a anatomia de um executável e o papel da memória, é necessário operar as ferramentas em seu nível mais fundamental, interagindo diretamente com o sistema operacional.

---

## 1. O Editor: Vim e a Infraestrutura Unix
Se para entender a anatomia de um binário precisamos abrir mão das IDEs, para editar esse código a lógica é a mesma. É aqui que entra o Vim. A escolha por ele não é estética, mas uma necessidade de infraestrutura. Em servidores headless (sem monitor), roteadores de rede ou ambientes acessados remotamente via SSH, não existe interface gráfica. Os editores da família vi/vim são parte do padrão POSIX e estão presentes nativamente em praticamente qualquer distribuição Unix/Linux.

Quando decidi compilar C no terminal, percebi que usar um editor gráfico externo quebraria o propósito de entender as "engrenagens". Aprender a manipular arquivos de texto, códigos-fonte e configurações diretamente pelo terminal "sem tirar as mãos do teclado" remove as distrações visuais e nos força a entender o sistema de arquivos real. 

O Vim roda diretamente na memória do terminal, não exige renderização de gráficos e consome recursos mínimos. Isso o torna ideal tanto para a máquina local quanto para servidores com restrição de hardware ou conexões remotas de alta latência, consolidando a mentalidade de que o terminal é o seu verdadeiro ambiente de desenvolvimento.

**Comandos básicos do Vim:**
* `vim arquivo.c` : Abre (ou cria) o arquivo.
* `i` : Entra no modo **Insert** (para você poder digitar o código).
* `Esc` : Sai do modo de inserção e volta para o modo de comandos.
* `:wq` + `Enter` : Salva (Write) e sai (Quit) do arquivo.

---

## 2. O Compilador: O Pipeline do GCC
Na prática, um código em C é apenas um arquivo de texto puro (ASCII ou UTF-8). O processador, no entanto, é agnóstico a linguagens de alto nível, ele executa estritamente instruções em código de máquina (binário). O papel do GCC (GNU Compiler Collection) é atuar como o tradutor dessa cadeia. Acionar o compilador manualmente via linha de comando, em vez de clicar em um botão de "Run" em uma IDE, nos permite visualizar e intervir nos quatro estágios exatos de transformação do código:

**a. Pré-processador (gcc -E):**
Antes de analisar a sintaxe do seu código, o GCC aciona o Pré-processador. O papel dele é puramente manipulação de texto: ele remove todos os comentários e processa as diretivas que começam com #. Ao encontrar o `#include <stdio.h>`, ele localiza o arquivo de cabeçalho no sistema, copia todo o seu conteúdo e o cola no topo do seu arquivo.

- O Comando: `gcc -E hello.c -o hello.i`

- O Resultado: Se você abrir o "hello.i" no Vim, tomará um susto. Um código inicial de 6 linhas transforma-se em um arquivo gigante com centenas de linhas. Isso acontece porque o cabeçalho inteiro do "stdio.h" com as assinaturas de funções foi injetado ali dentro.

**b. Compilador Real (gcc -S):**
Agora, o GCC pega o arquivo expandido e traduz a linguagem C (feita para humanos) para a linguagem Assembly (específica da arquitetura da sua CPU). É aqui que as variáveis amigáveis desaparecem e você começa a interagir diretamente com a infraestrutura física do processador.

- O Comando: `gcc -S hello.i -o hello.s` (passando o arquivo pré-processado para seguir o fluxo)

- O Resultado: Ao ler o "hello.s" no Vim, você verá instruções puras de baixo nível, como `pushq`, `movq` e `call`. A anatomia lógica do seu algoritmo fica exposta no osso.

**c. Montador ou Assembler (gcc -c):**
A CPU não lê palavras como `movq`, ela responde a instruções binárias codificadas em bytes (que representam os pulsos elétricos de nível lógico alto e baixo). A flag `-c` chama o Assembler para converter o código Assembly em código de máquina puro, gerando o chamado arquivo objeto.

- O Comando: `gcc -c hello.s -o hello.o`

- O Resultado: Se você tentar abrir o "hello.o" com o comando cat ou no Vim, o terminal tentará interpretar bytes puros como caracteres de texto, exibindo uma sequência incompreensível de interrogações e losangos. Deixou de ser texto, agora é um arquivo binário estruturado (ELF no Linux). É exatamente aqui que as ferramentas de Hex Dump (como o `xxd` ou `hexdump`), fundamentais no wargame Bandit, entram em ação para nos permitir ler a estrutura real do binário.

**d. Linker (Linking):**
O arquivo `.o` gerado no passo anterior ainda não é um executável independente. O motivo? Ele sabe que você chamou a função `printf`, mas o código que ensina a CPU a cuspir caracteres na tela não está ali dentro. O Linker (ld) realiza o passo final que ocorre por padrão quando não passamos flags de interrupção ao GCC. Ele pega o seu "hello.o" e amarra as referências pendentes às bibliotecas dinâmicas do sistema operacional (como a "libc.so"), gerando o binário executável final `./hello`.

## 3. Metodologia de Trabalho
Para começar, como diz o Gustavo Guanabara: *"Vamos printar um 'Hello World' na tela para nos livrar da maldição."*

**Edição:** 
Criação e modificação do arquivo fonte diretamente no editor de terminal.
```bash
vim hello.c
```
[Clique aqui para ver o código fonte do hello.c](https://github.com/tayure/Escovando-Bits-em-C/blob/48ea2fb708459f2ab4fc04b19bd19ff86d90e087/00-setup-e-ferramentas/hello.c)

**Compilação:**
Invocação manual do GCC, instruindo o compilador a processar o arquivo de texto e gerar um artefato binário (flag -o).
```bash
gcc hello.c -o meu_programa
```
**Execução:**
Invocação direta do binário recém-compilado no diretório atual, delegando a execução ao kernel do Linux.
```bash
./meu_programa
```
