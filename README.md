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

Board

A classe Board representa o próprio tabuleiro do jogo que contém as várias cartas necessárias ao gameplay, aqui temos uma análise do código desta classe:

Código:

public class Board
{
    private const int MAX_CARDS = 18;
    private Point _dimensions;
    private const int CARD_SPACING = 10;
    private readonly Point _cardDistance;
    public List<Card> Cards { get; } = [];
    public int CardsLeft { get; private set; }
    private readonly Texture2D _textureBack;
    public static readonly Texture2D[] CardTextures = new Texture2D[MAX_CARDS];

    public Board()
    {
        _textureBack = Globals.Content.Load<Texture2D>("Cards/back");
        _cardDistance = new(_textureBack.Width + CARD_SPACING, _textureBack.Height + CARD_SPACING);

        for (int i = 0; i < MAX_CARDS; i++)
        {
            CardTextures[i] = Globals.Content.Load<Texture2D>($"Cards/{i+1}");
        }
    }

    public void SetDifficulty(Difficulty difficulty)
    {
        switch (difficulty)
        {
            case Difficulty.Easy:
                _dimensions = new(4, 4);
                break;
            case Difficulty.Medium:
                _dimensions = new(6, 4);
                break;
            case Difficulty.Hard:
                _dimensions = new(6, 6);
                break;
        }

        Point boardSize = new((_cardDistance.X * _dimensions.X) - CARD_SPACING, (_cardDistance.Y * _dimensions.Y) - CARD_SPACING);
        Point boardSpacing = new((Globals.Bounds.X - boardSize.X + _textureBack.Width) / 2, (Globals.Bounds.Y - boardSize.Y + _textureBack.Height) / 2);

        var cardsCount = _dimensions.X * _dimensions.Y;
        CardsLeft = cardsCount;
        Cards.Clear();

        for (int i = 0; i < cardsCount; i++)
        {
            var id = i / 2;
            var front = CardTextures[id];
            var x = (_cardDistance.X * (i % _dimensions.X)) + boardSpacing.X;
            var y = (_cardDistance.Y * (i / _dimensions.X)) + boardSpacing.Y;
            Cards.Add(new(id, _textureBack, front, new(x, y)));
        }
    }

    public Card GetClickedCard()
    {
        if (!InputManager.MouseClicked) return null;

        foreach (Card card in Cards)
        {
            if (!card.Visible) continue;
            if (card.Flipping) continue;

            if (card.CardRectangle.Intersects(InputManager.MouseRectangle))
            {
                return card;
            }
        }

        return null;
    }

    public void Collect(Card c1, Card c2)
    {
        c1.Visible = false;
        c2.Visible = false;
        CardsLeft -= 2;
        CardPartsManager.AddParts(c1);
        CardPartsManager.AddParts(c2);
        SoundManager.PlayTearFX();
    }

    public void Reset()
    {
        CardsLeft = _dimensions.X * _dimensions.Y;

        foreach (Card card in Cards)
        {
            card.Reset();
        }

        Shuffle();
    }

    public void Shuffle()
    {
        Random rand = new();

        for (int i = Cards.Count - 1; i > 0; i--)
        {
            int j = rand.Next(i + 1);
            (Cards[j].Position, Cards[i].Position) = (Cards[i].Position, Cards[j].Position);
        }
    }

    public void Update()
    {
        foreach (Card card in Cards)
        {
            card.Update();
        }
    }

    public void Draw()
    {
        foreach (Card card in Cards)
        {
            card.Draw();
        }
    }
}


