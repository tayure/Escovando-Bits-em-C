# [00] O Ambiente Hostil: Terminal, Vim e GCC

Quando você precisa acessar um servidor remoto para gerenciar uma intranet, cadastrar usuários ou subir regras em um firewall de rede, você não encontra uma interface gráfica bonitinha com um mouse à disposição. Você encontra uma tela preta piscando. 

Aprender a programar dentro desse ambiente não é masoquismo, é treinamento de sobrevivência. Se você se acostuma a escrever e compilar código direto no ferro, você domina qualquer servidor. 

Neste laboratório, nós não usamos botões de "Play". Nossa trindade de ferramentas é puramente baseada em linha de comando:

---

## 1. O Terminal (A Verdadeira Interface)
Esqueça as IDEs (como VSCode ou CodeBlocks) que escondem a sujeira debaixo do tapete. O terminal do Linux é onde o sistema operacional realmente vive. É por aqui que vamos criar nossos arquivos, navegar pelas pastas e invocar o nosso compilador. O terminal te obriga a entender a estrutura de diretórios e onde as coisas estão fisicamente alocadas no disco.

## 2. Vim: O Editor das Trincheiras
Por que usar um editor de texto criado nos anos 70 que nem sequer aceita o clique do mouse? 

Porque o **Vim** (ou o Vi) está nativamente instalado em praticamente **todos os servidores Unix/Linux do planeta**. Se um serviço de rede cai de madrugada e você precisa entrar no servidor via SSH para editar um arquivo de configuração, o Vim estará lá te esperando. Aprender seus atalhos básicos constrói uma fluência indispensável para a infraestrutura.

**Comandos de Sobrevivência no Vim:**
* `vim arquivo.c` : Abre (ou cria) o arquivo.
* `i` : Entra no modo **Insert** (para você poder digitar o código).
* `Esc` : Sai do modo de inserção e volta para o modo de comandos.
* `:wq` + `Enter` : Salva (Write) e sai (Quit) do arquivo.

## 3. GCC: A Forja do Binário
O **GNU Compiler Collection (GCC)** é o tradutor. Computadores não entendem a linguagem C; eles só entendem zeros e uns (código de máquina). 

Em linguagens modernas, esse processo é invisível. Aqui, nós invocamos o GCC manualmente. Quando fazemos isso, vemos exatamente como ele junta as peças, engole nossos arquivos de texto (`.c`) e cospe um arquivo binário pronto para ser executado pelo processador.

---

## 🔬 O Ciclo de Trabalho (A Prática)

Todo experimento neste repositório vai seguir exatamente o mesmo ciclo de vida brutal:

1. **Escrever:** Criamos o arquivo com `vim hello.c` e digitamos o código fonte.
2. **Compilar:** Rodamos o GCC apontando para o nosso texto e definindo o nome da saída com a flag `-o`.
   ```bash
   gcc hello.c -o meu_programa
