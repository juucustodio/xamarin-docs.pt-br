---
title: "Animação e elementos gráficos"
description: "Android fornece uma estrutura muito diferentes e avançada para dar suporte a animações e gráficos 2D. Este tópico apresenta estas estruturas e discute como criar gráficos personalizados e animações para uso em um aplicativo xamarin."
ms.topic: article
ms.prod: xamarin
ms.assetid: 80086318-6FE4-4711-9A71-5C8F8C28C754
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 90a2eb219ae1189e7a48e60cde9761e3e9e93e0b
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="graphics-and-animation"></a>Animação e elementos gráficos

_Android fornece uma estrutura muito diferentes e avançada para dar suporte a animações e gráficos 2D. Este tópico apresenta estas estruturas e discute como criar gráficos personalizados e animações para uso em um aplicativo xamarin._

<a name="Overview" />

## <a name="overview"></a>Visão geral

Apesar de executados em dispositivos que são tradicionalmente de energia limitada, os aplicativos móveis classificados de maior geralmente têm uma sofisticada usuário experiência (UX), completo com gráficos de alta qualidade e animações que fornecem uma funcionalidade intuitiva, capacidade de resposta, dinâmica. Como os aplicativos móveis get mais sofisticados, os usuários começaram a esperar mais de aplicativos.

Felizmente para nós, plataformas móveis modernas tem as estruturas muito poderosas para a criação de animações sofisticadas e elementos gráficos personalizados mantendo a facilidade de uso. Isso permite aos desenvolvedores adicionar interatividade rica com muito pouco esforço.

Estruturas de API de interface do usuário no Android aproximadamente podem ser divididas em duas categorias: animação e elementos gráficos.

Elementos gráficos adicionais são divididos em diferentes abordagens para fazer gráficos 2D e 3D. Gráficos 3D estão disponíveis por meio de um número de estruturas como OpenGL ES (uma móvel versão específica do OpenGL) e estruturas de terceiros, como MonoGame (uma plataforma cruzada Kit de ferramentas compatível com o Kit de ferramentas do XNA) internos. Embora os gráficos 3D não estão dentro do escopo deste artigo, vamos examinar as técnicas de desenho 2D internas.

Android fornece dois API diferente para criar gráficos 2D. Um é uma abordagem declarativa de alto nível e a outra uma API de nível inferior através de programação:

-   **Recursos drawable** &ndash; eles são usados para criar gráficos personalizados programaticamente ou (normalmente) inserindo instruções de desenho em arquivos XML. Recursos drawable costumam ser definidos como arquivos XML que contêm instruções ou ações para o Android renderizar um elemento de gráfico 2D. 

-   **Tela** &ndash; esta é uma API de nível inferior que envolve desenhando diretamente em um bitmap subjacente. Ele fornece um controle refinado muito sobre o que é exibido. 

Além dessas técnicas de gráficos 2D, Android também fornece várias maneiras diferentes de criar animações:

-   **Animações drawable** &ndash; Android também dá suporte a animações por quadro conhecidas como *Drawable animação*. Esta é a API de animação mais simples. Android sequencialmente carrega e exibe recursos Drawable em sequência (muito parecido com uma personagem de desenho).

-   **Exibir animações** &ndash; *exibição animações* são a animação original da API do Android e estão disponíveis em todas as versões do Android. Essa API é limitada em que ele só funciona com objetos de exibição e só pode realizar transformações simples esses modos de exibição.
    Exibir animações geralmente são definidas em arquivos XML encontrados no `/Resources/anim` pasta.

-   **Propriedade animações** &ndash; 3.0 Android introduziu um novo conjunto de animação da API conhecido como *animações de propriedade*. Essas APIs novo introduziu um sistema flexível e extensível que pode ser usado para animar as propriedades de qualquer objeto, exiba não apenas os objetos. Essa flexibilidade permite animações a ser encapsulada em classes distintas que tornará mais fácil de compartilhamento de código.


Exibir animações são mais adequadas para aplicativos que precisam dar suporte a mais antiga pré-Android 3.0 da API (API nível 11). Caso contrário, os aplicativos devem usar a API mais recente propriedade animação pelas razões mencionadas acima.

Todas essas estruturas são viáveis opções, no entanto sempre que possível, preferência deve ser fornecida para animações de propriedade, como é uma API mais flexível para trabalhar com. Permitem animações de propriedade para a lógica de animação a ser encapsulada em classes distintas que torna mais fácil de compartilhamento de código e simplifica a manutenção do código.


## <a name="accessibility"></a>Acessibilidade

Gráficos e animações ajudam a tornar aplicativos Android atraente e divertido usar; No entanto, é importante lembrar-se de que algumas interações ocorrem por meio de screenreaders, dispositivos de entrada alternativos, ou com zoom assistido.
Além disso, algumas interações podem ocorrer sem os recursos de áudio.

Aplicativos são mais utilizáveis nessas situações, se eles foram criados com acessibilidade em mente: fornecendo dicas e assistência de navegação na interface do usuário e verificando se há conteúdo de texto ou descrições para ilustrada elementos da interface do usuário.

Consulte [guia de acessibilidade do Google](http://developer.android.com/guide/topics/ui/accessibility/) para obter mais informações sobre como utilizar as APIs de acessibilidade do Android.


<a name="2D_Graphics" />

## <a name="2d-graphics"></a>Gráficos 2D

Recursos drawable são uma técnica popular em aplicativos Android. Assim como com outros recursos, recursos Drawable declarativos &ndash; serem definidos em arquivos XML. Essa abordagem permite uma separação limpa de código de recursos. Isso pode simplificar o desenvolvimento e manutenção, porque ele não é necessário alterar o código para atualizar ou alterar os elementos gráficos em um aplicativo do Android. No entanto, enquanto Drawable recursos são úteis para muitos requisitos gráficos simples e comuns, eles não têm o poder e o controle da tela de API.

A técnica, usando o [tela](https://developer.xamarin.com/api/type/Android.Graphics.Canvas/) de objeto, é muito semelhante a outras estruturas de API tradicionais, como System. Drawing ou desenho de núcleo do iOS. Usando o objeto de tela oferece mais controle dos gráficos 2D como são criados. Ela é adequada para situações em que um recurso Drawable não funcionará ou será difícil trabalhar com. Por exemplo, pode ser necessário desenhar o controle deslizante personalizado cuja aparência será alterado com base nos cálculos relacionados ao valor do controle deslizante.

Primeiro, vamos examinar os recursos Drawable. Eles são mais simples e aborda os casos mais comuns de desenho personalizados.

<a name="Drawable Resources" />

### <a name="drawable-resources"></a>Recursos drawable

Recursos drawable são definidos em um arquivo XML no diretório `/Resources/drawable`. Ao contrário de incorporação PNG ou do JPEG, não é necessário fornecer versões específicas de densidade de recursos Drawable.
Em tempo de execução, um aplicativo do Android carregará esses recursos e use as instruções contidas nesses arquivos XML para criar gráficos 2D.
Android define vários tipos diferentes de recursos Drawable:

-   [ShapeDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#Shape) &ndash; é um objeto Drawable que desenha uma forma geométrica primitivo e aplica um conjunto limitado de efeitos de gráficos na forma. Eles são muito úteis para coisas como personalizar botões ou definir o plano de fundo do TextViews. Veremos um exemplo de como usar um `ShapeDrawable` posteriormente neste artigo.

-   [StateListDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#StateList) &ndash; esse é um recurso Drawable que irá alterar a aparência de acordo com o estado de um controle de widget /. Por exemplo, um botão pode alterar sua aparência, dependendo se ela está pressionada ou não.

-   [LayerDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList) &ndash; este recurso Drawable que serão empilhados vários outros drawables um sobre o outro. Um exemplo de um *LayerDrawable* é mostrado na seguinte captura de tela:

    ![Exemplo de LayerDrawable](graphics-and-animation-images/image1.png)

-   [TransitionDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#Transition) &ndash; este é um *LayerDrawable* , mas com uma diferença. Um *TransitionDrawable* é possível animar uma camada aparecendo sobre outro.

-   [LevelListDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#LevelList) &ndash; isso é muito semelhante a um *StateListDrawable* em que ela será exibida uma imagem com base em determinadas condições. No entanto, ao contrário de um *StateListDrawable*, o *LevelListDrawable* exibe uma imagem com base em um valor inteiro. Um exemplo de um *LevelListDrawable* seria exibir a intensidade do sinal de Wi-Fi. Como o nível das alterações de sinal de Wi-Fi, o drawable que é exibido mudará adequadamente.

-   [ScaleDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#Scale)/[ClipDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#Clip) &ndash; como seu nome implica, essas Drawables fornecer dimensionamento e funcionalidade de recorte. O *ScaleDrawable* será dimensionado para outra, quando Drawable, o *ClipDrawable* recortará Drawable outro.

-   [InsetDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#Inset) &ndash; este Drawable aplicará inserções nos lados de outro recurso Drawable. Ele é usado quando uma exibição precisa de um plano de fundo é menor do que os limites reais do modo de exibição.

-   XML [BitmapDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#Bitmap) &ndash; esse arquivo é um conjunto de instruções, XML, que devem ser executadas em um bitmap real. Algumas ações que pode ser executadas Android são aplicar lado a lado, pontilhado e suavização. Um dos usos muito comuns disso é bloco um bitmap em plano de fundo de um layout.


#### <a name="drawable-example"></a>Exemplo drawable

Vejamos um exemplo rápido de como criar um gráfico 2D usando um `ShapeDrawable`. Um `ShapeDrawable` pode definir uma das quatro formas básicas: oval, retângulo, linha e anel. Também é possível aplicar efeitos básicos, como tamanho, cor e gradiente. O XML a seguir é um `ShapeDrawable` que podem ser encontrados no *AnimationsDemo* projeto complementar (no arquivo `Resources/drawable/shape_rounded_blue_rect.xml`).
Ele define um retângulo com um plano de fundo do gradiente roxo e cantos arredondados:

```xml
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android" android:shape="rectangle">
<!-- Specify a gradient for the background -->
<gradient android:angle="45"
          android:startColor="#55000066"
          android:centerColor="#00000000"
          android:endColor="#00000000"
          android:centerX="0.75" />

<padding android:left="5dp"
          android:right="5dp"
          android:top="5dp"
          android:bottom="5dp" />

<corners android:topLeftRadius="10dp"
          android:topRightRadius="10dp"
          android:bottomLeftRadius="10dp"
          android:bottomRightRadius="10dp" />
</shape>
```

Podemos fazer referência a esse recurso Drawable declarativamente em um Layout ou outros Drawable, conforme mostrado no seguinte XML:

```xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:background="#33000000">
    <TextView android:layout_width="wrap_content"
              android:layout_height="wrap_content"
              android:layout_centerInParent="true"
              android:background="@drawable/shape_rounded_blue_rect"
              android:text="@string/message_shapedrawable" />
</RelativeLayout>
```

Recursos drawable também podem ser aplicados por meio de programação. O trecho de código a seguir mostra como definir o plano de fundo de um TextView:

```csharp
TextView tv = FindViewById<TextView>(Resource.Id.shapeDrawableTextView);
tv.SetBackgroundResource(Resource.Drawable.shape_rounded_blue_rect);
```

Para ver como isso seria, execute o *AnimationsDemo* do projeto e selecione o item de forma Drawable no menu principal. Devemos ver algo semelhante à captura de tela a seguir:

![TextView com um plano de fundo personalizado, drawable com um gradiente e arredondado cantos](graphics-and-animation-images/image1.png)

Para obter mais detalhes sobre a sintaxe Drawable recursos e elementos XML, consulte [documentação do Google](http://developer.android.com/guide/topics/resources/drawable-resource.html#Shape).

<a name="Using the Canvas Drawing API" />

### <a name="using-the-canvas-drawing-api"></a>Usando a API de desenho da tela

Drawables são eficientes, mas têm suas limitações. Certas coisas são impossível ou muito complexa (por exemplo: aplicando um filtro a uma imagem que foi feita por uma câmera do dispositivo). Seria muito difícil aplicar redução de olhos vermelhos, usando um recurso Drawable.
Em vez disso, a API de tela permite que um aplicativo tenha controle rigoroso seletivamente alterar as cores em uma parte específica da imagem.

É uma classe que é comumente usada com a tela de [pintura](https://developer.xamarin.com/api/type/Android.Graphics.Paint/) classe. Essa classe contém informações de cor e estilo sobre como desenhar. Ele é usado para fornecer coisas uma cor e transparência.

Usa a API de tela de *modelo de pintor* para desenhar gráficos 2D.
As operações são aplicadas em camadas sucessivas uns sobre os outros. Cada operação abordará algumas área do bitmap subjacente. Quando a área se sobrepõe a uma área de pintura anteriormente, a nova pintura será parcialmente ou obscurecer completamente o antigo. Isso é da mesma forma que funcionam de muitas outras APIs de desenho, como System. Drawing e gráficos de núcleo do iOS.

Há duas maneiras de obter um `Canvas` objeto. O primeiro modo envolve a definição de um [Bitmap](https://developer.xamarin.com/api/type/Android.Graphics.Bitmap/) objeto e, em seguida, criando um `Canvas` objeto com ele. Por exemplo, o trecho de código a seguir cria uma nova tela com um bitmap subjacente:

```csharp
Bitmap bitmap = Bitmap.CreateBitmap(100, 100, Bitmap.Config.Argb8888);
Canvas canvas = new Canvas(b);
```

A outra maneira de obter um `Canvas` objeto é o [OnDraw](https://developer.xamarin.com/api/member/Android.Views.View.OnDraw/) método de retorno de chamada que é fornecido o [exibição](https://developer.xamarin.com/api/type/Android.Views.View/) classe base. Android chama este método quando ele decide uma exibição precisa desenhar a próprio e passa um `Canvas` objeto para trabalhar com o modo de exibição.

A classe de tela expõe métodos para fornecer programaticamente as instruções de desenho. Por exemplo:

-   [Canvas.DrawPaint](https://developer.xamarin.com/api/member/Android.Graphics.Canvas.DrawPaint/p/Android.Graphics.Paint/) &ndash; preenche o bitmap da tela inteira com o paint especificado.

-   [Canvas.DrawPath](https://developer.xamarin.com/api/member/Android.Graphics.Canvas.DrawPath/p/Android.Graphics.Path/Android.Graphics.Paint/) &ndash; desenha a forma geométrica especificada usando o paint especificado.

-   [Canvas.DrawText](https://developer.xamarin.com/api/member/Android.Graphics.Canvas.DrawText/p/System.String/System.Single/System.Single/Android.Graphics.Paint/) &ndash; desenha o texto na tela com a cor especificada. O texto é desenhado no local `x,y` .


<a name="Drawing with the Canvas API" />

#### <a name="drawing-with-the-canvas-api"></a>Com a API de tela de desenho

Vamos ver um exemplo da tela de API em ação. O trecho de código a seguir mostra como desenhar um modo de exibição:

```csharp
public class MyView : View
{
    protected override void OnDraw(Canvas canvas)
    {
        base.OnDraw(canvas);
        Paint green = new Paint {
            AntiAlias = true,
            Color = Color.Rgb(0x99, 0xcc, 0),
        };
        green.SetStyle(Paint.Style.FillAndStroke);

        Paint red = new Paint {
            AntiAlias = true,
            Color = Color.Rgb(0xff, 0x44, 0x44)
        };
        red.SetStyle(Paint.Style.FillAndStroke);

        float middle = canvas.Width * 0.25f;
        canvas.DrawPaint(red);
        canvas.DrawRect(0, 0, middle, canvas.Height, green);
    }
}
```

Esse código acima cria primeiro um paint vermelho e um objeto de pintura verde. Ela preenche o conteúdo da tela com vermelho e, em seguida, instrui a tela para desenhar um retângulo verde que é 25% da largura da tela. Um exemplo disso pode ser visto na `AnimationsDemo` projeto que está incluído com o código-fonte para este artigo. Iniciar o aplicativo e selecionando o item de desenho no menu principal, é necessário uma tela semelhante à seguinte:

![Tela com pintura vermelha e objetos de pintura verde](graphics-and-animation-images/image3.png)

<a name="Animation" />

## <a name="animation"></a>Animação

Os usuários como coisas que se movem em seus aplicativos. Animações são uma ótima maneira de melhorar a experiência do usuário de um aplicativo e destacá-lo. As melhor animações são aqueles que os usuários não percebem porque acham naturais. Android fornece a seguinte três API para animações:

-   **Visualizar a animação** &ndash; essa é a API original. Essas animações são associadas a um modo de exibição específico e podem executar transformações simples no conteúdo da exibição. Devido à sua simplicidade, essa API ainda é útil para coisas como animações alfa, rotações e assim por diante.

-   **Propriedade animação** &ndash; animações de propriedade foram introduzidas no Android 3.0. Elas permitem que um aplicativo animar quase tudo. Animações de propriedade podem ser usadas para alterar qualquer propriedade de qualquer objeto, mesmo se esse objeto não é visível na tela.

-   **Animação drawable** &ndash; esse efeito de um recurso Drawable especial que é usado para aplicar uma animação muito simple para layouts.

Em geral, animação de propriedade é o sistema preferencial a ser usado como ele é mais flexível e oferece mais recursos.

<a name="View Animations" />

### <a name="view-animations"></a>Exibir animações

Exibir animações são limitadas a modos de exibição e só podem executar animações em valores como iniciar e pontos de extremidade, tamanho, rotação e transparência.
Esses tipos de animações são geralmente chamados de *animações de interpolação*. Animações de exibição podem ser definidas duas maneiras &ndash; programaticamente no código ou usando arquivos XML. Arquivos XML são a melhor maneira de declarar animações de exibição, como eles são mais legível e mais fáceis de manter.

Os arquivos XML de animação serão armazenados no `/Resources/anim` diretório de um projeto xamarin. Esse arquivo deve ter um dos seguintes elementos como o elemento raiz:

-   `alpha` &ndash; Uma animação fade in ou fade-out.

-   `rotate` &ndash; Uma animação de rotação.

-   `scale` &ndash; Uma animação de redimensionamento.

-   `translate` &ndash; Um movimento vertical e/ou horizontal.

-   `set` &ndash; Um contêiner que pode conter um ou mais dos elementos de animação.

Por padrão, todas as animações em um arquivo XML serão aplicadas ao mesmo tempo. Para tornar animações ocorrem em sequência, defina o `android:startOffset` atributo em um dos elementos definidos acima.

É possível afetar a taxa de alteração de uma animação usando um *interpolador*. Um interpolador possibilita que os efeitos da animação ser aceleradas, repetidas ou decelerated. A estrutura do Android fornece vários interpoladores fora da caixa, como (mas não limitado a):

-   `AccelerateInterpolator` / `DecelerateInterpolator` &ndash; Essas interpoladores aumentam ou diminuir a taxa de alteração de uma animação.

-   `BounceInterpolator` &ndash; a alteração de devoluções no final.

-   `LinearInterpolator` &ndash; a taxa de alterações é constante.


O XML a seguir mostra um exemplo de um arquivo de animação que combina alguns desses elementos:

```xml
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android=http://schemas.android.com/apk/res/android
     android:shareInterpolator="false">

    <scale android:interpolator="@android:anim/accelerate_decelerate_interpolator"
           android:fromXScale="1.0"
           android:toXScale="1.4"
           android:fromYScale="1.0"
           android:toYScale="0.6"
           android:pivotX="50%"
           android:pivotY="50%"
           android:fillEnabled="true"
           android:fillAfter="false"
           android:duration="700" />

    <set android:interpolator="@android:anim/accelerate_interpolator">
        <scale android:fromXScale="1.4"
               android:toXScale="0.0"
               android:fromYScale="0.6"
               android:toYScale="0.0"
               android:pivotX="50%"
               android:pivotY="50%"
               android:fillEnabled="true"
               android:fillBefore="false"
               android:fillAfter="true"
               android:startOffset="700"
               android:duration="400" />

        <rotate android:fromDegrees="0"
                android:toDegrees="-45"
                android:toYScale="0.0"
                android:pivotX="50%"
                android:pivotY="50%"
                android:fillEnabled="true"
                android:fillBefore="false"
                android:fillAfter="true"
                android:startOffset="700"
                android:duration="400" />
    </set>
</set>
```

A animação executará todas as animações simultaneamente. A primeira animação de escala será Alongar a imagem horizontalmente e reduzir verticalmente, e, em seguida, a imagem será simultaneamente girados 45 graus no sentido anti-horário e reduzir, desaparecendo da tela.

A animação pode ser aplicada por meio de programação para um modo de exibição, aumentando a animação e, em seguida, aplicá-la a um modo de exibição. Android fornece a classe auxiliar `Android.Views.Animations.AnimationUtils` que irá aumentar um recurso de animação e retornar uma instância de `Android.Views.Animations.Animation`. Esse objeto é aplicado a uma exibição chamando `StartAnimation` e passando o `Animation` objeto. O trecho de código a seguir mostra um exemplo disso:

```csharp
Animation myAnimation = AnimationUtils.LoadAnimation(Resource.Animation.MyAnimation);
ImageView myImage = FindViewById<ImageView>(Resource.Id.imageView1);
myImage.StartAnimation(myAnimation);
```

Agora que temos um entendimento fundamental do funcionamento de animações de exibição, permite mover a propriedade animações.

<a name="Property Animations" />

### <a name="property-animations"></a>Propriedade animações

Animadores de propriedade são uma nova API que foi introduzida no Android 3.0.
Eles fornecem uma API mais extensível que pode ser usada para animar qualquer propriedade em qualquer objeto.

Todas as animações de propriedade são criadas por instâncias de [Animator](https://developer.xamarin.com/api/type/Android.Animation.Animator/) subclasse. Aplicativos não usam essa classe diretamente, em vez disso, eles utilizam uma das suas subclasses:

-   [ValueAnimator](https://developer.xamarin.com/api/type/Android.Animation.ValueAnimator/) &ndash; esta classe é a classe mais importante na API de animação de propriedade de inteiro. Ela calcula os valores das propriedades que precisam ser alteradas. O `ViewAnimator` não atualizar diretamente esses valores; em vez disso, ele gera eventos que podem ser usados para atualizar objetos animados.

-   [ObjectAnimator](https://developer.xamarin.com/api/type/Android.Animation.ObjectAnimator/) &ndash; essa classe é uma subclasse de `ValueAnimator` . Ele foi feito para simplificar o processo de objetos de animação, aceitando um objeto de destino e a propriedade para atualizar.

-   [AnimationSet](https://developer.xamarin.com/api/type/Android.Animation.AnimatorSet/) &ndash; essa classe é responsável para orquestrar como animações executado em relação um ao outro. Animações podem executar ao mesmo tempo, em sequência ou com um atraso especificado entre eles.


*Avaliadores* são classes especiais que são usados por animadores para calcular os novos valores de durante uma animação. Predefinido Android fornece os avaliadores a seguir:

-   [IntEvaluator](https://developer.xamarin.com/api/type/Android.Animation.IntEvaluator/) &ndash; calcula os valores para propriedades de inteiro.

-   [FloatEvaluator](https://developer.xamarin.com/api/type/Android.Animation.FloatEvaluator/) &ndash; calcula os valores das propriedades de ponto flutuante.

-   [ArgbEvaluator](https://developer.xamarin.com/api/type/Android.Animation.ArgbEvaluator/) &ndash; calcula os valores para propriedades de cor.

Se a propriedade que está sendo animada não é um `float`, `int` ou cores, aplicativos podem criar seu próprios avaliador Implementando o `ITypeEvaluator` interface. (Implementar avaliadores personalizados está além do escopo deste tópico.)

#### <a name="using-the-valueanimator"></a>Usando o ValueAnimator

Há duas partes para qualquer animação: cálculo de valores animados e, em seguida, definir esses valores em propriedades em um objeto. 
[ValueAnimator](https://developer.xamarin.com/api/type/Android.Animation.ValueAnimator/) somente calculará os valores, mas ele não funcionará em objetos diretamente. Em vez disso, objetos sejam atualizados dentro de manipuladores de eventos que serão invocados durante o tempo de vida de animação. Esse design permite que várias propriedades a serem atualizadas de um valor animado.

Obtenha uma instância de `ValueAnimator` chamando um dos seguintes métodos de fábrica:

-  `ValueAnimator.OfInt`
-  `ValueAnimator.OfFloat`
-  `ValueAnimator.OfObject`

Quando terminar, o `ValueAnimator` instância deve ter sua duração definida e, em seguida, ele pode ser iniciado. O exemplo a seguir mostra como animar um valor de 0 a 1 no decorrer de 1000 milissegundos:

```csharp
ValueAnimator animator = ValueAnimator.OfInt(0, 100);
animator.SetDuration(1000);
animator.Start();
```

Mas, o trecho de código acima não é muito útil &ndash; o animator será executado mas não há nenhum destino para o valor atualizado. O `Animator` classe irá gerar o evento de atualização quando ela decide o que é necessário informar os ouvintes de um novo valor. Aplicativos podem fornecer um manipulador de eventos para responder a esse evento, conforme mostrado no trecho de código a seguir:

```csharp
MyCustomObject myObj = new MyCustomObject();
myObj.SomeIntegerValue = -1;

animator.Update += (object sender, ValueAnimator.AnimatorUpdateEventArgs e) =>
{
    int newValue = (int) e.Animation.AnimatedValue;
    // Apply this new value to the object being animated.
    myObj.SomeIntegerValue = newValue;
};
```

Agora que temos uma compreensão de `ValueAnimator`, permite saber mais sobre o `ObjectAnimator`.

#### <a name="using-the-objectanimator"></a>Usando o ObjectAnimator

[ObjectAnimator](https://developer.xamarin.com/api/type/Android.Animation.ObjectAnimator/) é uma subclasse de `ViewAnimator` que combina o cálculo de mecanismo e o valor de tempo do `ValueAnimator` com a lógica necessária para conectar manipuladores de eventos. O `ValueAnimator` exige a transmissão explicitamente um manipulador de eventos de aplicativos &ndash; `ObjectAnimator` será responsável por essa etapa para nós.

A API para `ObjectAnimator` é muito semelhante para a API do `ViewAnimator`, mas exige que você forneça o objeto e o nome da propriedade para atualizar. O exemplo a seguir mostra um exemplo de uso `ObjectAnimator`:

```csharp
MyCustomObject myObj = new MyCustomObject();
myObj.SomeIntegerValue = -1;

ObjectAnimator animator = ObjectAnimator.OfFloat(myObj, "SomeIntegerValue", 0, 100);
animator.SetDuration(1000);
animator.Start();
```

Como você pode ver no trecho de código anterior, `ObjectAnimator` pode reduzir e simplificar o código que é necessário para animar um objeto.

<a name="Drawable Animations" />

### <a name="drawable-animations"></a>Animações drawable

A API de animação final é a API de animação Drawable. Animações drawable carregar uma série de recursos Drawable um após o outro e exibi-los em sequência, semelhante a uma personagem de desenho flip-it.

Recursos drawable são definidos em um arquivo XML que tem um `<animation-list>` elemento como o elemento raiz e uma série de `<item>` elementos que definem cada quadro na animação. Esse arquivo XML é armazenado no `/Resource/drawable` pasta do aplicativo. O XML a seguir é um exemplo de uma animação drawable:

```xml
<animation-list xmlns:android="http://schemas.android.com/apk/res/android">
  <item android:drawable="@drawable/asteroid01" android:duration="100" />
  <item android:drawable="@drawable/asteroid02" android:duration="100" />
  <item android:drawable="@drawable/asteroid03" android:duration="100" />
  <item android:drawable="@drawable/asteroid04" android:duration="100" />
  <item android:drawable="@drawable/asteroid05" android:duration="100" />
  <item android:drawable="@drawable/asteroid06" android:duration="100" />
</animation-list>
```

A animação será executado por meio de seis quadros. O `android:duration` atributo declara quanto tempo cada quadro será exibido. O trecho de código seguinte mostra um exemplo de criação de uma animação Drawable e iniciá-lo quando o usuário clica em um botão na tela:

```csharp
AnimationDrawable _asteroidDrawable;

protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);

    _asteroidDrawable = (Android.Graphics.Drawables.AnimationDrawable)
    Resources.GetDrawable(Resource.Drawable.spinning_asteroid);

    ImageView asteroidImage = FindViewById<ImageView>(Resource.Id.imageView2);
    asteroidImage.SetImageDrawable((Android.Graphics.Drawables.Drawable) _asteroidDrawable);

    Button asteroidButton = FindViewById<Button>(Resource.Id.spinAsteroid);
    asteroidButton.Click += (sender, e) =>
    {
        _asteroidDrawable.Start();
    };
}
```

Agora nós abordamos os fundamentos da animação APIs disponíveis em um aplicativo do Android.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo introduziu vários novos conceitos e da API ajudar a adicionar alguns gráficos para um aplicativo do Android. Primeiro, ele discutidos os vários gráficos 2D da API e demonstrou como Android permite que os aplicativos desenhar diretamente na tela usando um objeto da tela. Também vimos algumas técnicas alternativas que permitem que os gráficos a serem criados declarativamente usando arquivos XML. Em seguida, fomos discutir as antigas e novas APIs para criar animações no Android.



## <a name="related-links"></a>Links relacionados

- [Demonstração de animação (exemplo)](https://developer.xamarin.com/samples/monodroid/AnimationDemo)
- [Animação e elementos gráficos](http://developer.android.com/guide/topics/graphics/index.html)
- [Usando animações para dar vida a seus aplicativos móveis](http://youtu.be/ikSk_ILg3d0)
- [AnimationDrawable](https://developer.xamarin.comhttps://developer.xamarin.com/api/type/Android.Graphics.Drawables.AnimationDrawable/)
- [Tela](https://developer.xamarin.comhttps://developer.xamarin.com/api/type/Android.Graphics.Canvas/)
- [Objeto Animator](https://developer.xamarin.comhttps://developer.xamarin.com/api/type/Android.Animation.ObjectAnimator/)
- [Value Animator](https://developer.xamarin.comhttps://developer.xamarin.com/api/type/Android.Animation.ValueAnimator/)
