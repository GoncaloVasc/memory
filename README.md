https://github.com/LubiiiCZ/DevQuickie.git

Memory Game

Este projeto é sobre um jogo simples de memória desenvolvido em Monogame. No jogo, o jogador escolhe a sua dificuldade e depois tem de virar pares de cartas, se as cartas forem diferentes elas voltam a ficar ao contrário, se forem iguais, elas são removidas do tabuleiro. Este loop continua até o jogador remover todas as cartas do tabuleiro ou ficar sem tempo.

Grupo

34982 João Alves
34987 Gustavo Pontes
34979 Gonçalo Vasconcelos

Requisitos

Microsoft.Xna.Framework;
Microsoft.Xna.Framework.Graphics;
Microsoft.Xna.Framework.Input;

Funcionalidades

Contém 3 dificuldades
Controles "Point and Click"

Como jogar

Escolher a dificuldade que quer jogar, apoós isso o jogador tem de virar pares de cartas dentro de um certo tempo limite, se as cartas forem diferentes elas são resetadas e voltam a ficar ao contrário, se forem iguais, elas são removidas do tabuleiro e o jogador ganha algum tempo de volta. Este loop continua até o jogador remover todas as cartas do tabuleiro ou ficar sem tempo.

Estrutura do Código

O projeto está divido em várias classes, cada uma responsável por um aspecto específico do jogo. Algumas das mais importantes:

1.Game1: Classe principal do jogo que gere a lógica do jogo. Contém a inicialização, carregamento de conteúdo, atualização e métodos de desenho, e carrega o primeiro ecrã do jogo.
2.Card: Classe que gere as cartas do tabuleiro. Inclui as mecânicas das cartas serem viradas e resetadas. 
3.Diifficulty: Classe que gere a dificuldade do jogo. Contém todas as dificuldades que o jogo aprensenta.
4.Board: Classe que gere o próprio tabuleiro. Inclui a quantidade de cartas que este deve ter baseado na dificuldade previamente escolhida, e a mecânica de resetar o tabuleiro

Análise da Organização das Pastas do Jogo

A organização das pastas do jogo está estruturada da seguinte forma:
A pasta Content contém 3 outras pastas:
  Cards: Inclui as imagens que aparecem nas cartas quando estas são viradas
  Menu: Contém a fonte usada para todo o texto do jogo e as diversas imagens presentes no menu
  Sound: Inclui os diversos sons usados durante o jogo
As pastas _Models e _Managers: 
  Contém todas as classes e processos necessários á execução do jogo

Análise do Código

