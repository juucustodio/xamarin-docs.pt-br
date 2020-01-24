---
title: Elementos gráficos e animação
description: O Android fornece uma estrutura muito rica e diversificada para dar suporte a gráficos 2D e animações. Este tópico apresenta essas estruturas e discute como criar gráficos personalizados e animações para uso em um aplicativo Xamarin. Android.
ms.prod: xamarin
ms.assetid: 80086318-6FE4-4711-9A71-5C8F8C28C754
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: eeee9b7d694d9380c653fb87c24171bcaf79389d
ms.sourcegitcommit: 9ab907e053c57fc96419149f83187bc3e8983a6b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2020
ms.locfileid: "75655273"
---
# <a name="android-graphics-and-animation"></a>Gráficos e animação do Android

_O Android fornece uma estrutura muito rica e diversificada para dar suporte a gráficos 2D e animações. Este tópico apresenta essas estruturas e discute como criar gráficos personalizados e animações para uso em um aplicativo Xamarin. Android._

## <a name="overview"></a>{1&gt;Visão Geral&lt;1}

Apesar de ser executado em dispositivos que são tradicionalmente limitados, os mais altos aplicativos móveis geralmente têm uma experiência de usuário sofisticada (UX), completa com gráficos de alta qualidade e animações que fornecem uma sensação intuitiva, responsiva e dinâmica. À medida que os aplicativos móveis ficam cada vez mais sofisticados, os usuários começaram a esperar cada vez mais dos aplicativos.

Felizmente, para nós, as plataformas móveis modernas têm estruturas muito poderosas para a criação de animações sofisticadas e gráficos personalizados, mantendo a facilidade de uso. Isso permite que os desenvolvedores adicionem interatividade avançada com muito pouco esforço.

Estruturas de API de interface do usuário no Android podem ser divididas aproximadamente em duas categorias: gráficos e animação.

Os gráficos são divididos em diferentes abordagens para fazer gráficos 2D e 3D. os gráficos 3D estão disponíveis por meio de várias estruturas internas, como OpenGL ES (uma versão específica para dispositivos móveis do OpenGL) e estruturas de terceiros, como o monogame (um kit de ferramentas de plataforma cruzada compatível com o kit de ferramentas do XNA). Embora os gráficos 3D não estejam dentro do escopo deste artigo, examinaremos as técnicas internas de desenho 2D.

O Android fornece duas APIs diferentes para a criação de gráficos 2D. Uma é uma abordagem declarativa de alto nível e a outra API de baixo nível programática:

- Os **recursos desenháveis** &ndash; eles são usados para criar gráficos personalizados de forma programática ou (mais geralmente) inserindo instruções de desenho em arquivos XML. Os recursos desenháveis normalmente são definidos como arquivos XML que contêm instruções ou ações para o Android renderizar um gráfico 2D. 

- **Canvas** &ndash; essa é uma API de nível baixo que envolve o desenho diretamente em um bitmap subjacente. Ele fornece um controle muito refinado sobre o que é exibido. 

Além dessas técnicas de gráficos 2D, o Android também fornece várias maneiras diferentes de criar animações:

- **Animações desenháveis** &ndash; Android também oferece suporte a animações quadro a quadro conhecidas como *animação desenhável*. Essa é a API de animação mais simples. O Android carrega e exibe os recursos desenháveis em sequência (muito parecido com um desenho).

- **Exibir animações** &ndash; *Exibir animações* são as APIs de animação originais no Android e estão disponíveis em todas as versões do Android. Essa API é limitada, pois só funcionará com objetos de exibição e só poderá executar transformações simples nessas exibições.
    As animações de exibição normalmente são definidas em arquivos XML encontrados na pasta `/Resources/anim`.

- As **animações de propriedade** &ndash; Android 3,0 introduziram um novo conjunto de APIs de animação conhecido como animações de *Propriedade*. Essas novas APIs introduziram um sistema extensível e flexível que pode ser usado para animar as propriedades de qualquer objeto, não apenas exibir objetos. Essa flexibilidade permite que as animações sejam encapsuladas em classes distintas, o que facilitará o compartilhamento de código.

As animações de exibição são mais adequadas para aplicativos que devem dar suporte às APIs anteriores do Android 3,0 (API nível 11). Caso contrário, os aplicativos devem usar as APIs de animação de propriedade mais recentes pelos motivos mencionados acima.

Todas essas estruturas são opções viáveis, no entanto, sempre que possível, a preferência deve ser dada às animações de propriedade, pois é uma API mais flexível com a qual trabalhar. As animações de propriedade permitem que a lógica de animação seja encapsulada em classes distintas que facilitam o compartilhamento de código e simplifica a manutenção do código.

## <a name="accessibility"></a>Acessibilidade

Os gráficos e as animações ajudam a tornar os aplicativos Android atraentes e divertidos de usar; no entanto, é importante lembrar que algumas interações ocorrem por meio de leitores, dispositivos de entrada alternativos ou com zoom assistido.
Além disso, algumas interações podem ocorrer sem recursos de áudio.

Os aplicativos são mais utilizáveis nessas situações se tiverem sido projetados com a acessibilidade em mente: fornecendo dicas e assistência de navegação na interface do usuário e garantindo que há conteúdo de texto ou descrições para elementos de ilustração da interface do usuário.

Consulte o [Guia de acessibilidade do Google](https://developer.android.com/guide/topics/ui/accessibility/) para obter mais informações sobre como utilizar as APIs de acessibilidade do Android.

## <a name="2d-graphics"></a>Gráficos 2D

Os recursos desenháveis são uma técnica popular em aplicativos Android. Assim como ocorre com outros recursos, os recursos desenháveis são declarativos &ndash; eles são definidos em arquivos XML. Essa abordagem permite uma separação limpa do código dos recursos. Isso pode simplificar o desenvolvimento e a manutenção, pois não é necessário alterar o código para atualizar ou alterar os elementos gráficos em um aplicativo Android. No entanto, embora os recursos desenháveis sejam úteis para muitos requisitos gráficos simples e comuns, eles não têm a potência e o controle da API Canvas.

A outra técnica, usando o objeto [Canvas](xref:Android.Graphics.Canvas) , é muito semelhante a outras estruturas de API tradicionais, como o desenho principal System. Drawing ou Ios. O uso do objeto Canvas fornece o maior controle de como os gráficos 2D são criados. É apropriado para situações em que um recurso que desenha não funcionará ou será difícil de trabalhar com ele. Por exemplo, pode ser necessário desenhar um controle Slider personalizado cuja aparência será alterada com base nos cálculos relacionados ao valor do controle deslizante.

Vamos examinar os recursos desenháveis primeiro. Elas são mais simples e abrangem os casos de desenho personalizados mais comuns.

### <a name="drawable-resources"></a>Recursos de desenho

Os recursos desenháveis são definidos em um arquivo XML no diretório `/Resources/drawable`. Ao contrário da incorporação de PNG ou JPEG, não é necessário fornecer versões específicas de densidade de recursos desenháveis.
Em tempo de execução, um aplicativo Android carregará esses recursos e usará as instruções contidas nesses arquivos XML para criar gráficos 2D.
O Android define vários tipos diferentes de recursos de empate:

- [ShapeDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Shape) &ndash; esse é um objeto que desenha uma forma geométrica primitiva e aplica um conjunto limitado de efeitos gráficos nessa forma. Eles são muito úteis para coisas como personalizar botões ou definir o plano de fundo de textviews. Veremos um exemplo de como usar uma `ShapeDrawable` mais adiante neste artigo.

- [StateListDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#StateList) &ndash; esse é um recurso que vai ser desenhado, que alterará a aparência com base no estado de um widget/controle. Por exemplo, um botão pode alterar sua aparência dependendo se ele é pressionado ou não.

- [LayerDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList) &ndash; esse recurso que poderá ser desenhado, que empilhará várias outras drawables, uma na parte superior de outra. Um exemplo de um *LayerDrawable* é mostrado na seguinte captura de tela:

    ![Exemplo de LayerDrawable](graphics-and-animation-images/image1.png)

- [TransitionDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Transition) &ndash; isso é um *LayerDrawable* , mas com uma diferença. Um *TransitionDrawable* é capaz de animar uma camada mostrando acima da outra.

- [LevelListDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#LevelList) &ndash; isso é muito semelhante a um *StateListDrawable* , pois ele exibirá uma imagem com base em determinadas condições. No entanto, ao contrário de um *StateListDrawable*, o *LevelListDrawable* exibe uma imagem com base em um valor inteiro. Um exemplo de um *LevelListDrawable* seria exibir a força de um sinal WiFi. À medida que a força do sinal de WiFi é alterada, o empate que é exibido será alterado de acordo.

- [ScaleDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Scale)/[ClipDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Clip) &ndash; como o nome indica, esses drawables fornecem funcionalidade de dimensionamento e recorte. O *ScaleDrawable* dimensionará outro para ser desenhado, enquanto o *ClipDrawable* cortará outro empate.

- [InsetDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Inset) &ndash; esse empate aplicará indefinições nos lados de outro recurso que poderá ser desenhado. Ele é usado quando uma exibição precisa de um plano de fundo menor do que os limites reais da exibição.

- XML [BitmapDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Bitmap) &ndash; este arquivo é um conjunto de instruções, em XML, que devem ser executadas em um bitmap real. Algumas ações que o Android pode executar são divisão, pontilhamento e suavização de serrilhado. Um dos usos muito comuns disso é colocar um bitmap em bloco no plano de fundo de um layout.

#### <a name="drawable-example"></a>Exemplo de empate

Vejamos um exemplo rápido de como criar um gráfico 2D usando um `ShapeDrawable`. Uma `ShapeDrawable` pode definir uma das quatro formas básicas: retângulo, oval, linha e anel. Também é possível aplicar efeitos básicos, como gradiente, cor e tamanho. O XML a seguir é um `ShapeDrawable` que pode ser encontrado no projeto do *AnimationsDemo* Companion (no arquivo `Resources/drawable/shape_rounded_blue_rect.xml`).
Ele define um retângulo com uma fundo de gradiente roxo e cantos arredondados:

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

Podemos referenciar esse recurso com empate declarativamente em um layout ou outro que pode ser desenhado, conforme mostrado no XML a seguir:

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

Os recursos desenháveis também podem ser aplicados programaticamente. O trecho de código a seguir mostra como definir programaticamente o plano de fundo de um TextView:

```csharp
TextView tv = FindViewById<TextView>(Resource.Id.shapeDrawableTextView);
tv.SetBackgroundResource(Resource.Drawable.shape_rounded_blue_rect);
```

Para ver como isso seria, execute o projeto *AnimationsDemo* e selecione o item de forma que pode ser extraído no menu principal. Devemos ver algo semelhante à captura de tela a seguir:

[![TextView com um plano de fundo personalizado, desenhável com um gradiente e cantos arredondados](graphics-and-animation-images/image2-sml.png)](graphics-and-animation-images/image2.png#lightbox)

Para obter mais detalhes sobre os elementos XML e a sintaxe de recursos de desenho, consulte a [documentação do Google](https://developer.android.com/guide/topics/resources/drawable-resource.html#Shape).

### <a name="using-the-canvas-drawing-api"></a>Usando a API de desenho de tela

Drawables são poderosas, mas têm suas limitações. Certas coisas não são possíveis ou muito complexas (por exemplo: aplicação de um filtro a uma imagem que foi tomada por uma câmera no dispositivo). Seria muito difícil aplicar a redução de olhos vermelhos usando um recurso que pode ser desenhado.
Em vez disso, a API Canvas permite que um aplicativo tenha controle refinado para alterar seletivamente as cores em uma parte específica da imagem.

Uma classe que é comumente usada com a tela é a classe [Paint](xref:Android.Graphics.Paint) . Essa classe contém informações de cor e estilo sobre como desenhar. Ele é usado para fornecer coisas como cor e transparência.

A API Canvas usa o *modelo de pintor* para desenhar gráficos 2D.
As operações são aplicadas em camadas sucessivas sobre as outras. Cada operação abrangerá alguma área do bitmap subjacente. Quando a área se sobrepõe a uma área pintada anteriormente, a nova pintura irá parcialmente ou completamente obscurecer o antigo. Essa é a mesma maneira que muitas outras APIs de desenho, como os principais gráficos do System. Drawing e do iOS, funcionam.

Há duas maneiras de obter um objeto `Canvas`. A primeira maneira envolve a definição de um objeto [bitmap](xref:Android.Graphics.Bitmap) e a instanciação de um objeto `Canvas` com ele. Por exemplo, o trecho de código a seguir cria uma nova tela com um bitmap subjacente:

```csharp
Bitmap bitmap = Bitmap.CreateBitmap(100, 100, Bitmap.Config.Argb8888);
Canvas canvas = new Canvas(b);
```

A outra maneira de obter um objeto `Canvas` é pelo método de retorno de chamada [OnDraw](xref:Android.Views.View.OnDraw*) que é fornecido na classe base [View](xref:Android.Views.View) . O Android chama esse método quando decide que uma exibição precisa se desenhar e passa um objeto `Canvas` para que a exibição funcione.

A classe Canvas expõe métodos para fornecer programaticamente as instruções Draw. Por exemplo:

- [Canvas. DrawPaint](xref:Android.Graphics.Canvas.DrawPaint*) &ndash; preenche o bitmap de tela inteira com o Paint especificado.

- [Canvas. DrawPath](xref:Android.Graphics.Canvas.DrawPath*) &ndash; desenha a forma geométrica especificada usando o Paint especificado.

- [Canvas. DrawText](xref:Android.Graphics.Canvas.DrawText*) &ndash; desenha o texto na tela com a cor especificada. O texto é desenhado no local `x,y`.

#### <a name="drawing-with-the-canvas-api"></a>Desenho com a API Canvas

Aqui está um exemplo da API Canvas em ação. O trecho de código a seguir mostra como desenhar uma exibição:

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

Esse código acima cria primeiro uma pintura vermelha e um objeto de pintura verde. Ele preenche o conteúdo da tela com vermelho e, em seguida, instrui a tela a desenhar um retângulo verde que é 25% da largura da tela. Um exemplo disso pode ser visto pelo no projeto `AnimationsDemo` incluído com o código-fonte deste artigo. Ao iniciar o aplicativo e selecionar o item de desenho no menu principal, devemos uma tela semelhante à seguinte:

[Tela de ![com objetos de pintura vermelha e de pintura verde](graphics-and-animation-images/image3-sml.png)](graphics-and-animation-images/image3.png#lightbox)

## <a name="animation"></a>{1&gt;Animação&lt;1}

Usuários como coisas que se movem em seus aplicativos. As animações são uma ótima maneira de melhorar a experiência do usuário de um aplicativo e ajudá-lo a se destacar. As melhores animações são aquelas que os usuários não percebem porque se sentem naturais. O Android fornece as três seguintes APIs para animações:

- **Exibir a animação** &ndash; esta é a API original. Essas animações são vinculadas a uma exibição específica e podem executar transformações simples no conteúdo da exibição. Por causa da simplicidade, essa API ainda é útil para coisas como animações alfa, rotações e assim por diante.

- A **animação de propriedade** &ndash; animações de propriedade foram introduzidas no Android 3,0. Eles permitem que um aplicativo anime quase tudo. As animações de propriedade podem ser usadas para alterar qualquer propriedade de qualquer objeto, mesmo que esse objeto não esteja visível na tela.

- **Animação desenhável** &ndash; esse recurso de desenho especial que é usado para aplicar um efeito de animação muito simples aos layouts.

Em geral, a animação de propriedade é o sistema preferencial a ser usado, pois é mais flexível e oferece mais recursos.

### <a name="view-animations"></a>Exibir animações

As animações de exibição são limitadas a exibições e só podem executar animações em valores como pontos de início e fim, tamanho, rotação e transparência.
Esses tipos de animações são normalmente chamados de *animações de interpolação*. As animações de exibição podem ser definidas de duas maneiras &ndash; programaticamente no código ou usando arquivos XML. Os arquivos XML são a maneira preferida de declarar animações de exibição, pois elas são mais legíveis e mais fáceis de manter.

Os arquivos XML de animação serão armazenados no diretório `/Resources/anim` de um projeto Xamarin. Android. Esse arquivo deve ter um dos seguintes elementos como o elemento raiz:

- `alpha` &ndash; uma animação desaparecer ou esmaecer.

- `rotate` &ndash; uma animação de rotação.

- `scale` &ndash; uma animação de redimensionamento.

- `translate` &ndash; um movimento horizontal e/ou vertical.

- `set` &ndash; um contêiner que pode conter um ou mais dos outros elementos de animação.

Por padrão, todas as animações em um arquivo XML serão aplicadas simultaneamente. Para fazer as animações ocorrerem em sequência, defina o atributo `android:startOffset` em um dos elementos definidos acima.

É possível afetar a taxa de alteração em uma animação usando um *interpolador*. Um interpolador possibilita que os efeitos da animação sejam acelerados, repetidos ou desacelerados. A estrutura do Android fornece vários interpoladores prontos para uso, como (mas não se limitando a):

- `AccelerateInterpolator` / `DecelerateInterpolator` &ndash; esses interpoladores aumentam ou diminuem a taxa de alteração em uma animação.

- `BounceInterpolator` &ndash; a alteração salta no final.

- `LinearInterpolator` &ndash; a taxa de alterações é constante.

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

Essa animação executará todas as animações simultaneamente. A primeira animação de escala alongará a imagem horizontalmente e a reduzirá verticalmente e, em seguida, a imagem será girada simultaneamente em 45 graus no sentido horário e redução, desaparecendo da tela.

A animação pode ser aplicada programaticamente a uma exibição por meio da reformatação da animação e, em seguida, a aplicação de uma exibição. O Android fornece a classe auxiliar `Android.Views.Animations.AnimationUtils` que ativará um recurso de animação e retornará uma instância de `Android.Views.Animations.Animation`. Esse objeto é aplicado a uma exibição chamando `StartAnimation` e passando o objeto `Animation`. O trecho de código a seguir mostra um exemplo disso:

```csharp
Animation myAnimation = AnimationUtils.LoadAnimation(Resource.Animation.MyAnimation);
ImageView myImage = FindViewById<ImageView>(Resource.Id.imageView1);
myImage.StartAnimation(myAnimation);
```

Agora que temos uma compreensão fundamental de como exibir animações funcionam, vamos mudar para animações de propriedade.

### <a name="property-animations"></a>Animações de propriedade

A propriedade Animators é uma nova API que foi introduzida no Android 3,0.
Eles fornecem uma API mais extensível que pode ser usada para animar qualquer propriedade em qualquer objeto.

Todas as animações de propriedade são criadas por instâncias da subclasse [Animator](xref:Android.Animation.Animator) . Os aplicativos não usam essa classe diretamente, em vez disso, eles usam uma das subclasses:

- [ValueAnimator](xref:Android.Animation.ValueAnimator) &ndash; essa classe é a classe mais importante na API de animação de propriedade inteira. Ele calcula os valores das propriedades que precisam ser alteradas. O `ViewAnimator` não atualiza diretamente esses valores; em vez disso, ele gera eventos que podem ser usados para atualizar objetos animados.

- [ObjectAnimator](xref:Android.Animation.ObjectAnimator) &ndash; essa classe é uma subclasse de `ValueAnimator`. Ele destina-se a simplificar o processo de animação de objetos aceitando um objeto de destino e uma propriedade para atualizar.

- O [animationset](xref:Android.Animation.AnimatorSet) &ndash; essa classe é responsável por orquestrar como as animações são executadas em relação umas às outras. As animações podem ser executadas simultaneamente, sequencialmente ou com um atraso especificado entre elas.

Os *avaliadores* são classes especiais que são usadas pelo Animators para calcular os novos valores durante uma animação. Para uso pronto, o Android fornece os seguintes avaliadores:

- [IntEvaluator](xref:Android.Animation.IntEvaluator) &ndash; calcula valores para propriedades de inteiro.

- [FloatEvaluator](xref:Android.Animation.FloatEvaluator) &ndash; calcula valores para propriedades float.

- [ArgbEvaluator](xref:Android.Animation.ArgbEvaluator) &ndash; calcula valores para propriedades de cor.

Se a propriedade que está sendo animada não for uma `float`, `int` ou cor, os aplicativos poderão criar seu próprio avaliador implementando a interface `ITypeEvaluator`. (A implementação de avaliadores personalizados está além do escopo deste tópico.)

#### <a name="using-the-valueanimator"></a>Usando o ValueAnimator

Há duas partes para qualquer animação: calcular valores animados e, em seguida, definir esses valores em Propriedades em algum objeto. 
[ValueAnimator](xref:Android.Animation.ValueAnimator) calculará apenas os valores, mas não funcionará diretamente nos objetos. Em vez disso, os objetos serão atualizados nos manipuladores de eventos que serão invocados durante o ciclo de vida da animação. Esse design permite que várias propriedades sejam atualizadas de um valor animado.

Você Obtém uma instância do `ValueAnimator` chamando um dos seguintes métodos de fábrica:

- `ValueAnimator.OfInt`
- `ValueAnimator.OfFloat`
- `ValueAnimator.OfObject`

Depois disso, a instância de `ValueAnimator` deve ter sua duração definida e, em seguida, pode ser iniciada. O exemplo a seguir mostra como animar um valor de 0 a 1 acima do intervalo de 1000 milissegundos:

```csharp
ValueAnimator animator = ValueAnimator.OfInt(0, 100);
animator.SetDuration(1000);
animator.Start();
```

Mas, em si, o trecho de código acima não é muito útil &ndash; o Animator será executado, mas não há nenhum destino para o valor atualizado. A classe `Animator` gerará o evento Update quando decidir que é necessário informar os ouvintes de um novo valor. Os aplicativos podem fornecer um manipulador de eventos para responder a esse evento, conforme mostrado no seguinte trecho de código:

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

Agora que temos uma compreensão de `ValueAnimator`, vamos saber mais sobre o `ObjectAnimator`.

#### <a name="using-the-objectanimator"></a>Usando o ObjectAnimator

[ObjectAnimator](xref:Android.Animation.ObjectAnimator) é uma subclasse de `ViewAnimator` que combina o mecanismo de tempo e o cálculo de valor do `ValueAnimator` com a lógica necessária para conectar manipuladores de eventos. O `ValueAnimator` exige que os aplicativos conectem explicitamente um manipulador de eventos &ndash; `ObjectAnimator` cuidará desta etapa para nós.

A API para `ObjectAnimator` é muito semelhante à API para `ViewAnimator`, mas requer que você forneça o objeto e o nome da propriedade a ser atualizada. O exemplo a seguir mostra um exemplo de como usar `ObjectAnimator`:

```csharp
MyCustomObject myObj = new MyCustomObject();
myObj.SomeIntegerValue = -1;

ObjectAnimator animator = ObjectAnimator.OfFloat(myObj, "SomeIntegerValue", 0, 100);
animator.SetDuration(1000);
animator.Start();
```

Como você pode ver no trecho de código anterior, `ObjectAnimator` pode reduzir e simplificar o código necessário para animar um objeto.

### <a name="drawable-animations"></a>Animações desenháveis

A API de animação final é a API de animação desenhável. Animações desenháveis carregam uma série de recursos desenháveis um após o outro e os exibem sequencialmente, semelhante a um desenho de inversão de ti.

Os recursos desenháveis são definidos em um arquivo XML que tem um elemento `<animation-list>` como o elemento raiz e uma série de elementos `<item>` que definem cada quadro na animação. Esse arquivo XML é armazenado na pasta `/Resource/drawable` do aplicativo. O XML a seguir é um exemplo de uma animação desenhável:

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

Essa animação será executada em seis quadros. O atributo `android:duration` declara quanto tempo cada quadro será exibido. O próximo trecho de código mostra um exemplo de como criar uma animação desenhável e iniciá-la quando o usuário clica em um botão na tela:

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

Neste ponto, abordamos as bases das APIs de animação disponíveis em um aplicativo Android.

## <a name="summary"></a>Resumo

Este artigo apresentou muitos conceitos e APIs novos para ajudar a adicionar alguns elementos gráficos a um aplicativo Android. Primeiro, discutiu as várias APIs de gráficos 2D e demonstrou como o Android permite que os aplicativos desenhem diretamente na tela usando um objeto Canvas. Também vimos algumas técnicas alternativas que permitem que os gráficos sejam criados declarativamente usando arquivos XML. Em seguida, fizemos uma discussão sobre as APIs antigas e novas para a criação de animações no Android.

## <a name="related-links"></a>Links Relacionados

- [Demonstração de animação (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/animationdemo)
- [Animação e gráficos](https://developer.android.com/guide/topics/graphics/index.html)
- [Usando animações para dar vida aos seus aplicativos móveis](https://youtu.be/ikSk_ILg3d0)
- [AnimationDrawable](xref:Android.Graphics.Drawables.AnimationDrawable)
- [Tela](xref:Android.Graphics.Canvas)
- [Animator de objeto](xref:Android.Animation.ObjectAnimator)
- [Valor Animator](xref:Android.Animation.ValueAnimator)
