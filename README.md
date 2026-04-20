https://github.com/LubiiiCZ/DevQuickie.git

Memory Game

Este projeto é sobre um jogo simples de memória desenvolvido em Monogame. No jogo, o jogador escolhe a sua dificuldade e depois tem de virar pares de cartas, se as cartas forem diferentes elas voltam a ficar ao contrário, se forem iguais, elas são removidas do tabuleiro. Este loop continua até o jogador remover todas as cartas do tabuleiro ou ficar sem tempo.
_________________________________________________________________________________
Grupo

34982 João Alves

34987 Gustavo Pontes

34979 Gonçalo Vasconcelos
_________________________________________________________________________________
Requisitos

Microsoft.Xna.Framework;

Microsoft.Xna.Framework.Graphics;

Microsoft.Xna.Framework.Input;
_________________________________________________________________________________
Funcionalidades

Contém 3 dificuldades

Distribuição aleatória das cartas

Recomeço

Controles "Point and Click"

Vitória
_________________________________________________________________________________
Como jogar

Escolher a dificuldade que quer jogar, apoós isso o jogador tem de virar pares de cartas dentro de um certo tempo limite, se as cartas forem diferentes elas são resetadas e voltam a ficar ao contrário, se forem iguais, elas são removidas do tabuleiro e o jogador ganha algum tempo de volta. Este loop continua até o jogador remover todas as cartas do tabuleiro ou ficar sem tempo.
_________________________________________________________________________________
Estrutura do Código

O projeto está divido em várias classes, cada uma responsável por um aspecto específico do jogo. 

Algumas das mais importantes:

• Game1: Classe principal do jogo que gere a lógica do jogo. Contém a inicialização, carregamento de conteúdo, atualização e métodos de desenho, e carrega o primeiro ecrã do jogo.

• Card: Classe que gere as cartas do tabuleiro. Inclui as mecânicas das cartas serem viradas e resetadas e ainda as suas posições e texturas. 

• Difficulty: Classe que gere a dificuldade do jogo. Contém todas as dificuldades que o jogo aprensenta.

• Board: Classe que gere o próprio tabuleiro. Inclui a quantidade de cartas que este deve ter baseado na dificuldade previamente escolhida, e a mecânica de resetar o tabuleiro
_________________________________________________________________________________
Análise da Organização das Pastas do Jogo

A organização das pastas do jogo está estruturada da seguinte forma:

A pasta Content contém 3 outras pastas:

• Cards: Inclui as imagens que aparecem nas cartas quando estas são viradas
  
• Menu: Contém a fonte usada para todo o texto do jogo e as diversas imagens presentes no menu

• Sound: Inclui os diversos sons usados durante o jogo
  
As pastas _Models e _Managers:

• Contém todas as classes e processos necessários á execução do jogo 
_________________________________________________________________________________
Análise do Código

• Board

A classe Board representa o próprio tabuleiro do jogo que contém as várias cartas necessárias ao gameplay, e calcula a sua distribuição dependendo do tamanho da janela.

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

A função SetDifficulty(Difficulty difficulty) vai olhar para a dificuldade escolhida pelo jogador antes do início de jogo para determinar quantas cartas devem ser colocadas no tabuleiro

A função GetClickedCard() tem como objetivo determinar qual carta é que o jogador clicou recorrendo á classe Card 

Se ambas as cartas tiverem imagens iguais, então será utilizada a função Collect(Card c1, Card c2), que tem como objetivo remover ambas as cartas selecionadas

A função Reset() é utilizada para repor todas as cartas do tabuleiro

A função Shuffle() tem como objetivo randomizar a ordem em que as cartas são colocadas para que todos os jogos tenham uma ordem diferenete


• Card

A classe Card representa as diversas cartas presentes em cima do tabuleiro de jogo e controla todo sobre elas incluindo a imagem que elas apresentam e a mecânica de as virar ao contrário

Código:

    public class Card : Sprite
    {
        private readonly Texture2D _back;
        private readonly Texture2D _front;
        public bool Flipped { get; private set; }
        public Rectangle CardRectangle => new((int)(Position.X - origin.X), (int)(Position.Y - origin.Y),
                                              Texture.Width, Texture.Height);
        public int Id { get; }
        public bool Visible { get; set; }
        private readonly float _flipTime;
        private float _flipTimeLeft;
        public bool Flipping { get; private set; }
        private int _flippingDir;
    
        public Card(int id, Texture2D back, Texture2D front, Vector2 position) : base(back, position)
        {
            Id = id;
            _back = back;
            _front = front;
            _flipTime = 0.1f;
            Reset();
        }
    
        public void Reset()
        {
            Texture = _back;
            Visible = true;
            Flipped = false;
            Flipping = false;
            scale = Vector2.One;
            _flippingDir = -1;
            _flipTimeLeft = _flipTime;
        }
    
        public void Flip()
        {
            Flipping = true;
            Flipped = !Flipped;
            _flippingDir = -1;
            _flipTimeLeft = _flipTime;
            SoundManager.PlayFlipFx();
        }
    
        public void Update()
        {
            if (Flipping)
            {
                _flipTimeLeft += Globals.Time * _flippingDir;
                scale.X = _flipTimeLeft / _flipTime;
    
                if (_flipTimeLeft <= 0)
                {
                    _flippingDir = 1;
                    Texture = Flipped ? _front : _back;
                }
                else if (_flipTimeLeft > _flipTime)
                {
                    _flippingDir = -1;
                    Flipping = false;
                    scale = Vector2.One;
                }
            }
        }
    
        public override void Draw()
        {
            if (Visible) base.Draw();
        }
    }

O código começa por defenir o tamanho, a posição e a imagem que a carta deve ter quando está virada para cima e para baixo

A função Reset() serve para rodar as cartas de volta para baixo após uma má escolha

A função Flip() é utilizada quando o jogador clica numa carta, atualizando as suas varáiveis para poder ser virada

A função Update() tem como objetivo detetar se a carta está no processo de ser virada ou não, controlando o tempo que esta demora a realizar a animação

• Button

A classe Button representa um botão clicável na tela sob os srpites. Ela vem de Sprite, por isso pode ser inserida como qualquer outro sprite.

Código:

    public class Button : Sprite
    {
        private readonly Rectangle _rectangle;
        public bool Disabled { get; set; }

        public Button(Texture2D tex, Vector2 pos) : base(tex, pos)
        {
            _rectangle = new((int)(pos.X - origin.X), (int)(pos.Y - origin.Y), tex.Width, tex.Height);
        }

        public event EventHandler OnClick;

        public void Update()
        {
            color = Color.White;

            if (_rectangle.Contains(InputManager.MouseRectangle))
            {
                color = Color.DarkGray;

                if (InputManager.MouseClicked)
                {
                    OnClick?.Invoke(this, EventArgs.Empty);
                }
            }

            if (Disabled) color *= 0.3f;
        }
    }

São gerados botões para que possam ser clicáveis e escolhida a dificuldade

A função Update() deteta se o rato está por cima do botão e muda a sua cor

• Difficulty

A classe Difficulty representa o nível de dificuldade do jogo de memória

Código:

    public enum Difficulty
    {
        Easy,
        Medium,
        Hard
    }

Este código apenas tem como função guardar a escolha do jogador sobre qual dificuldade quer jogar, sendo esta depois usada pela classe Board para determinar quantas cartas é que devem ser postas no tabuleiro


• CardPart

A classe CardPart tem como função animar as cartas a desaparecer

Código:

    public enum CardDirection
    {
        TopLeft,
        TopRight,
        BottomLeft,
        BottomRight
    }
    
    public class CardPart : Sprite
    {
        private readonly Vector2 _direction;
        public float Lifespan { get; private set; }
        private readonly Rectangle _rectangle;
        private const float _lifespan = 0.5f;
        private float _scale = 1f;
        private readonly float _speed = 175f;
        private float _rotation;
    
        public CardPart(Texture2D tex, CardDirection dir, Vector2 pos) : base(tex, pos)
        {
            origin /= 2;
            var w = tex.Width / 2;
            var h = tex.Height / 2;
    
            switch (dir)
            {
                case CardDirection.TopLeft:
                    _direction = new(-1, -1);
                    _rectangle = new(0, 0, w, h);
                    break;
                case CardDirection.TopRight:
                    _direction = new(1, -1);
                    _rectangle = new(w, 0, w, h);
                    break;
                case CardDirection.BottomLeft:
                    _direction = new(-1, 1);
                    _rectangle = new(0, w, w, h);
                    break;
                case CardDirection.BottomRight:
                    _direction = new(1, 1);
                    _rectangle = new(w, h, w, h);
                    break;
            }
    
            Vector2 shift = new(w / 2, h / 2);
            Position += _direction * shift;
            Lifespan = _lifespan;
        }
    
        public void Update()
        {
            Lifespan -= Globals.Time;
            _scale = Lifespan / _lifespan;
            Position += _direction * _speed * _scale * Globals.Time;
            _rotation += 10f * Globals.Time;
        }
    
        public override void Draw()
        {
            Globals.SpriteBatch.Draw(Texture, Position, _rectangle, Color.White * _scale, _rotation, origin, _scale, SpriteEffects.None, 1f);
        }
    }
A função CardPart(), serve para animar a carta a dividir-se em 4 fragmentos, explodir e desaparecer

Ainda temos a pasta States que contem mais algumas funções e classes importantes ao funcionamento do jogo

• GameState

Código:

    public abstract class GameState
    {
        public abstract void Update(GameManager gm);
        public abstract void Draw(GameManager gm);
    }

Classe que quando corrida corre as funções Update e Draw do game manager

• WinState

Código:

    public class WinState : GameState
    {
        private static Texture2D _texture;
        private static Vector2 _position;
        private static SpriteFont _font;
        private static Vector2 _textPosition;
        private static string _text = "";
    
        public WinState()
        {
            _texture = Globals.Content.Load<Texture2D>("Menu/win");
            _font = Globals.Content.Load<SpriteFont>("Menu/font");
            _position = new((Globals.Bounds.X - _texture.Width) / 2, (Globals.Bounds.Y - _texture.Height) / 2);
        }
    
        public override void Update(GameManager gm)
        {
            if (InputManager.MouseClicked)
            {
                gm.ChangeState(GameStates.Menu);
            }
    
            _text = ScoreManager.Score.ToString();
            var size = _font.MeasureString(_text);
            _textPosition = new((Globals.Bounds.X - size.X) / 2, _position.Y + (_texture.Height / 4));
        }
    
        public override void Draw(GameManager gm)
        {
            Globals.SpriteBatch.Draw(_texture, _position, Color.White);
            Globals.SpriteBatch.DrawString(_font, _text, _textPosition, Color.Black);
        }
    }

Classe de vitória, começa por ir buscar os graficos usados e posiciona-os na tela, de seguida verifica se o jogador clica e se esse o fizer muda o estado de jogo para o estado de menu


• FlipFirstCardState e FlipSecondCardState

Classes do playstate dedicadas á ação do jogador ao escolher as cartas que quer revelar durante o decorrer do jogo. Trabalhando respetivamente a primeira e segunda carta escolhidas.

Código:

FlipFirstCardState:

    public class FlipFirstCardState : PlayState
    {
        public override void Update(GameManager gm)
        {
            base.Update(gm);
    
            var card = gm.Board.GetClickedCard();
    
            if (card is not null)
            {
                card.Flip();
                gm.FirstCard = card;
                gm.ChangeState(GameStates.FlipSecondCard);
                ScoreManager.Start();
            }
        }
    }

A função começa por correr um update

Depois guarda na variavél card o valor da carta em que o jogador clica ( var card = gm.Board.GetClickedCard(); )

Verifica se o valor da carta não é nulo, ou seja se o jogador clicou numa carta valida.
Se o jogador selecionou uma carta, esta é virada para cima (card.Flip();), é guardada o seu valor na variavel gm.FirstCard e troca o estado de jogo para a função FlipSecondCard (gm.ChangeState(GameStates.FlipSecondCard);
)
E dá inicio ao score manager (ScoreManager.Start();)

FlipSecondCardState:

    public class FlipSecondCardState : PlayState
    {
        public override void Update(GameManager gm)
        {
            base.Update(gm);
    
            var card = gm.Board.GetClickedCard();
    
            if (card is not null && card != gm.FirstCard)
            {
                card.Flip();
                gm.SecondCard = card;
                gm.ChangeState(GameStates.ResolveTurn);
            }
        }
    }

A função começa por correr um update e como a primeira, guarda na variavél card o valor da carta que o jogador escolhe, e verifica se esse não é nulo e se é diferente do escolhido anteriormente (card is not null && card != gm.FirstCard)

Se se verificar essa condições é virada a carta (card.Flip();) e guardada o seu valor na variavél gm.SecondCard e finaliza passando para o estado de jogo ResolveTurn

• ResolveTurnState

Código:

    public class ResolveTurnState : PlayState
    {
        public override void Update(GameManager gm)
        {
            base.Update(gm);
    
            if (gm.FirstCard.Id == gm.SecondCard.Id && !gm.FirstCard.Flipping && !gm.SecondCard.Flipping)
            {
                gm.Board.Collect(gm.FirstCard, gm.SecondCard);
                ScoreManager.NextTurn();
    
                if (gm.Board.CardsLeft <= 0)
                {
                    gm.ChangeState(GameStates.Win);
                    ScoreManager.Stop();
                    ScoreManager.SaveScores();
                    SoundManager.PlayVictoryFX();
                }
                else
                {
                    gm.ChangeState(GameStates.FlipFirstCard);
                }
            }
    
            if (InputManager.MouseClicked && gm.FirstCard.Id != gm.SecondCard.Id)
            {
                gm.FirstCard.Flip();
                gm.SecondCard.Flip();
                ScoreManager.Miss();
                gm.ChangeState(GameStates.FlipFirstCard);
            }
        }
    }

Começa por verificar se ambas as cartas são iguais

Se sim coleta ambas as cartas (gm.Board.Collect(gm.FirstCard, gm.SecondCard);) e passa o score manager pro proximo turno (ScoreManager.NextTurn();) e verifica se a quantidade de cartas no tabuleiro de jogo é menor ou igual que zero
se for é dado o jogo como terminado e é mudado o estado de jogo para o estado de vitória (gm.ChangeState(GameStates.Win);), é parado o score manager (ScoreManager.Stop();) e a pontuação obtida é guardada (ScoreManager.SaveScores();)  e são tocados os efeitos de vitória (SoundManager.PlayVictoryFX();).
