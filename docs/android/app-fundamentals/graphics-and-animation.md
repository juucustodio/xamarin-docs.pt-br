---
title: Elementos gráficos e animação
description: Android fornece uma estrutura muito avançada e diversificada para dar suporte a animações e gráficos 2D. Este tópico apresenta essas estruturas e discute como criar gráficos personalizados e animações para uso em um aplicativo xamarin. Android.
ms.prod: xamarin
ms.assetid: 80086318-6FE4-4711-9A71-5C8F8C28C754
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 7f4f7fd3af1e90307a84037f01ddf8e52b1ee030
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2019
ms.locfileid: "57669044"
---
# <a name="graphics-and-animation"></a>Elementos gráficos e animação

_Android fornece uma estrutura muito avançada e diversificada para dar suporte a animações e gráficos 2D. Este tópico apresenta essas estruturas e discute como criar gráficos personalizados e animações para uso em um aplicativo xamarin. Android._


## <a name="overview"></a>Visão geral

Apesar em execução em dispositivos que são tradicionalmente de energia limitada, os aplicativos móveis classificados de maior geralmente têm uma sofisticada experiência de UX (usuário), completo com gráficos de alta qualidade e animações que fornecem uma aparência intuitiva, responsiva e dinâmica. Como os aplicativos móveis recebem mais e mais sofisticados, os usuários começaram a esperar mais de aplicativos.

Felizmente para nós, plataformas móveis modernos têm estruturas muito poderosas para a criação de animações sofisticadas e elementos gráficos personalizados, mantendo a facilidade de uso. Isso permite aos desenvolvedores adicionar interatividade rica com muito pouco esforço.

Estruturas de API de interface do usuário no Android aproximadamente podem ser divididas em duas categorias: Gráficos e animação.

Elementos gráficos adicionais são divididos em diferentes abordagens para fazer gráficos 2D e 3D. Gráficos 3D estão disponíveis por meio de um número de estruturas como OpenGL ES (uma móvel versão específica do OpenGL) e estruturas de terceiros, como o MonoGame (uma plataforma cruzada Kit de ferramentas compatível com o Kit de ferramentas do XNA) internos. Embora os gráficos 3D não estão dentro do escopo deste artigo, examinaremos as técnicas de desenho 2D internos.

O Android fornece dois API diferente para a criação de gráficos 2D. Uma é uma abordagem declarativa de alto nível e a outra uma API programática de baixo nível:

-   **Recursos desenháveis** &ndash; eles são usados para criar elementos gráficos personalizados por meio de programação ou (normalmente) inserindo instruções de desenho em arquivos XML. Recursos desenháveis geralmente são definidos como arquivos XML que contêm instruções ou ações para o Android renderizar um elemento de gráfico 2D. 

-   **Tela** &ndash; esta é uma API de nível inferior que envolve desenhando diretamente em um bitmap subjacente. Ele fornece controle bastante refinado sobre o que é exibido. 

Além dessas técnicas de gráficos 2D, o Android também fornece várias maneiras diferentes de criar animações:

-   **Animações drawable** &ndash; Android também dá suporte a animações de quadro a quadro, conhecidas como *animação Desenháveis*. Essa é a API de animação mais simples. Android sequencialmente carrega e exibe recursos Desenháveis na sequência (muito parecido com um desenho animado).

-   **Exibir animações** &ndash; *animações de modo de exibição* são original da API de animação no Android e estão disponíveis em todas as versões do Android. Essa API é limitado em que ele só funcionará com objetos de exibição e só pode executar transformações simples sobre esses modos de exibição.
    Animações de modo de exibição geralmente são definidas no arquivo XML encontrado na `/Resources/anim` pasta.

-   **Propriedade animações** &ndash; Android 3.0 introduziu um novo conjunto de animação da API, conhecida como *animações de propriedade*. Esses nova API introduziu um sistema flexível e extensível que pode ser usado para animar as propriedades de qualquer objeto, não apenas exibir os objetos. Essa flexibilidade permite que as animações a ser encapsulada em classes distintas que torna mais fácil de compartilhamento de código.


Animações de modo de exibição são mais adequadas para aplicativos que precisam dar suporte a mais antiga pré-Android 3.0 da API (API nível 11). Caso contrário, os aplicativos devem usar a mais nova propriedade de animação API pelas razões mencionadas acima.

Todas essas estruturas são viáveis de opções, no entanto sempre que possível, preferência deve ser fornecida a animações de propriedade, pois é uma API mais flexível para trabalhar com. Permitem animações de propriedade para a lógica de animação a ser encapsulada em classes distintas que torna mais fácil de compartilhamento de código e simplifica a manutenção do código.


## <a name="accessibility"></a>Acessibilidade

Gráficos e animações ajudam a tornar os aplicativos Android atraente e divertidos de usar; No entanto, é importante lembrar-se de que algumas interações ocorrem por meio de leitores de tela, dispositivos de entrada alternativos, ou com o zoom assistido.
Além disso, algumas interações podem ocorrer sem os recursos de áudio.

Os aplicativos são mais utilizáveis nessas situações, se elas foram projetadas com acessibilidade em mente: fornecer dicas e assistência de navegação na interface do usuário e garantir que não há conteúdo de texto ou descrições para elementos pictográficos da interface do usuário.

Consulte a [guia de acessibilidade do Google](https://developer.android.com/guide/topics/ui/accessibility/) para obter mais informações sobre como utilizar as APIs de acessibilidade do Android.



## <a name="2d-graphics"></a>Gráficos 2D

Recursos desenháveis são uma técnica popular em aplicativos Android. Assim como com outros recursos, recursos Desenháveis declarativos &ndash; são definidas em arquivos XML. Essa abordagem permite uma separação clara entre código de recursos. Isso pode simplificar o desenvolvimento e manutenção porque não é necessário alterar o código para atualizar ou alterar os elementos gráficos em um aplicativo Android. No entanto, enquanto recursos Desenháveis são úteis para muitos requisitos gráficos simples e comuns, eles não têm a potência e o controle sobre a API de Canvas.

A técnica, usando o [Canvas](https://developer.xamarin.com/api/type/Android.Graphics.Canvas/) de objeto, é muito semelhante a outras estruturas de API tradicionais, como System. Drawing ou desenho de núcleo do iOS. Usando o objeto de tela fornece mais controle de gráficos 2D como são criados. Ele é adequado para situações em que um recurso Desenháveis não funcionará, ou será difícil de trabalhar com. Por exemplo, pode ser necessário desenhar um controle deslizante personalizado cuja aparência será alterado com base em cálculos relacionados ao valor do controle deslizante.

Vamos examinar os recursos Desenháveis pela primeira vez. Eles são mais simples e abrangem os casos mais comuns de desenho personalizados.


### <a name="drawable-resources"></a>Recursos desenháveis

Recursos desenháveis são definidos em um arquivo XML no diretório `/Resources/drawable`. Ao contrário de incorporação PNG ou do JPEG, não é necessário fornecer versões específicas de densidade de recursos Desenháveis.
Em tempo de execução, um aplicativo do Android carregará esses recursos e use as instruções contidas nesses arquivos XML para criar gráficos 2D.
Android define vários tipos diferentes de recursos Desenháveis:

-   [ShapeDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Shape) &ndash; esse é um objeto Desenhável que desenha uma forma geométrica primitiva e aplica um conjunto limitado de efeitos gráficos nessa forma. Eles são muito úteis para coisas como personalizando os botões ou definir o plano de fundo de TextViews. Veremos um exemplo de como usar um `ShapeDrawable` mais adiante neste artigo.

-   [StateListDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#StateList) &ndash; esse é um recurso Desenháveis que irá alterar a aparência de acordo com o estado de um controle de widget /. Por exemplo, um botão pode alterar sua aparência dependendo se ele é pressionado ou não.

-   [LayerDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList) &ndash; este recurso Desenháveis que serão empilhados de várias outras desenháveis um sobre o outro. Um exemplo de uma *LayerDrawable* é mostrado na seguinte captura de tela:

    ![Exemplo de LayerDrawable](graphics-and-animation-images/image1.png)

-   [TransitionDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Transition) &ndash; esse é um *LayerDrawable* , mas com uma diferença. Um *TransitionDrawable* é capaz de animar uma camada aparecendo sobre outro.

-   [LevelListDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#LevelList) &ndash; isso é muito semelhante a um *StateListDrawable* em que ele exibirá uma imagem com base em determinadas condições. No entanto, ao contrário de um *StateListDrawable*, o *LevelListDrawable* exibe uma imagem com base em um valor inteiro. Um exemplo de uma *LevelListDrawable* seria exibir a intensidade do sinal de Wi-Fi. Como a força das alterações de sinal Wi-Fi, a desenháveis que é exibido mudará adequadamente.

-   [ScaleDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Scale)/[ClipDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Clip) &ndash; como seu nome implica, essas Desenháveis fornecem dimensionamento e funcionalidade de recorte. O *ScaleDrawable* dimensionará para outra, quando Desenhável, o *ClipDrawable* recortará Drawable outro.

-   [InsetDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Inset) &ndash; este Desenháveis aplicará inserções nos lados do outro recurso Desenhável. Ele é usado quando uma exibição precisa de um plano de fundo é menor do que os limites de real da exibição.

-   XML [BitmapDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Bitmap) &ndash; esse arquivo é um conjunto de instruções, em XML, que devem ser executadas em um bitmap real. Algumas ações que pode ser executadas pelo Android são aplicar lado a lado, pontilhado e suavização. Um dos usos muito comuns disso é um bitmap divididas, o plano de fundo de um layout.


#### <a name="drawable-example"></a>Exemplo de desenhável

Vamos examinar um exemplo de como criar um gráfico 2D usando um `ShapeDrawable`. Um `ShapeDrawable` pode definir uma das quatro formas básicas: oval, retângulo, linha e anel. Também é possível aplicar efeitos básicos, como o gradiente, cor e tamanho. O XML a seguir é um `ShapeDrawable` que podem ser encontrados na *AnimationsDemo* projeto complementar (no arquivo `Resources/drawable/shape_rounded_blue_rect.xml`).
Ele define um retângulo com um plano de fundo gradiente roxo e cantos arredondados:

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

Podemos pode fazer referência a esse recurso Desenháveis declarativamente em um Layout ou outro Drawable conforme mostrado no seguinte XML:

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

Recursos desenháveis também podem ser aplicados por meio de programação. O trecho de código a seguir mostra como definir o plano de fundo de um TextView programaticamente:

```csharp
TextView tv = FindViewById<TextView>(Resource.Id.shapeDrawableTextView);
tv.SetBackgroundResource(Resource.Drawable.shape_rounded_blue_rect);
```

Para ver o que seria a aparência disso, execute as *AnimationsDemo* do projeto e selecione o item de forma Desenháveis no menu principal. Devemos ver algo semelhante à seguinte captura de tela:

![TextView com um plano de fundo personalizado, desenhável com um cantos arredondados e gradiente](graphics-and-animation-images/image1.png)

Para obter mais detalhes sobre os elementos XML e a sintaxe de recursos Desenháveis, consulte [documentação do Google](https://developer.android.com/guide/topics/resources/drawable-resource.html#Shape).


### <a name="using-the-canvas-drawing-api"></a>Usando a API de desenho da tela

Desenháveis são poderosas, mas têm suas limitações. Determinadas coisas estão não é possível ou muito complexos (por exemplo: aplicando um filtro em uma imagem que foi feita por uma câmera no dispositivo). Seria muito difícil aplicar redução de olhos vermelhos, usando um recurso Desenháveis.
Em vez disso, a API de Canvas permite que um aplicativo tenha um controle bastante refinado seletivamente alterar cores em uma parte específica da imagem.

Uma classe que é comumente usada com a tela é o [Paint](https://developer.xamarin.com/api/type/Android.Graphics.Paint/) classe. Essa classe contém informações de cor e estilo sobre como desenhar. Ele é usado para fornecer as coisas, uma cor e transparência.

A API de Canvas usa o *modelo de pincel* para desenhar gráficos 2D.
As operações são aplicadas em camadas sucessivas umas sobre as outras. Cada operação abordará alguma área do bitmap subjacente. Quando a área se sobrepõe a uma área pintada anteriormente, a nova pintura funcionará parcialmente ou obscurecer completamente o antigo. Isso é da mesma forma que funcionam de muitas outras APIs de desenho, como System. Drawing e gráficos de núcleo do iOS.

Há duas maneiras de obter um `Canvas` objeto. A primeira maneira envolve a definição de um [Bitmap](https://developer.xamarin.com/api/type/Android.Graphics.Bitmap/) objeto e, em seguida, criando uma instância de um `Canvas` objeto com ele. Por exemplo, o trecho de código a seguir cria uma nova tela com um bitmap subjacente:

```csharp
Bitmap bitmap = Bitmap.CreateBitmap(100, 100, Bitmap.Config.Argb8888);
Canvas canvas = new Canvas(b);
```

A outra maneira de obter um `Canvas` objeto é o [OnDraw](https://developer.xamarin.com/api/member/Android.Views.View.OnDraw/) método de retorno de chamada que é fornecido o [exibição](https://developer.xamarin.com/api/type/Android.Views.View/) classe base. Android chama esse método quando ele decide em uma exibição precisa desenhar a mesmo e passa um `Canvas` objeto para trabalhar com o modo de exibição.

A classe de Canvas expõe métodos para fornecer programaticamente as instruções de desenho. Por exemplo:

-   [Canvas.DrawPaint](https://developer.xamarin.com/api/member/Android.Graphics.Canvas.DrawPaint/p/Android.Graphics.Paint/) &ndash; preenche o bitmap da tela inteira com o paint especificado.

-   [Canvas.DrawPath](https://developer.xamarin.com/api/member/Android.Graphics.Canvas.DrawPath/p/Android.Graphics.Path/Android.Graphics.Paint/) &ndash; desenha a forma geométrica especificada usando o paint especificado.

-   [Canvas.DrawText](https://developer.xamarin.com/api/member/Android.Graphics.Canvas.DrawText/p/System.String/System.Single/System.Single/Android.Graphics.Paint/) &ndash; desenha o texto na tela com a cor especificada. O texto é desenhado no local `x,y` .



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

O código acima cria primeiro uma pintura vermelha e um objeto de pintura verde. Ele preenche o conteúdo da tela com vermelho e, em seguida, instrui a tela para desenhar um retângulo verde que é 25% da largura da tela. Um exemplo disso pode ser visto na `AnimationsDemo` projeto que está incluído com o código-fonte para este artigo. Iniciar o aplicativo e selecionando o item de desenho no menu principal, devemos uma tela semelhante à seguinte:

![Com objetos de pintura verde e vermelha pintura de tela](graphics-and-animation-images/image3.png)


## <a name="animation"></a>Animação

Os usuários, como as coisas que se movem em seus aplicativos. As animações são uma ótima maneira de melhorar a experiência do usuário de um aplicativo e destacá-lo. As animações recomendadas são aqueles que os usuários não percebem porque eles se sintam naturais. Android fornece a seguinte três API para animações:

-   **Exibir a animação** &ndash; essa é a API original. Essas animações são vinculadas a uma exibição específica e podem executar transformações simples no conteúdo da exibição. Devido à simplicidade de TI, essa API ainda é útil para itens, como animações alfabéticos, rotações e assim por diante.

-   **Propriedade de animação** &ndash; animações de propriedade foram introduzidas no Android 3.0. Eles permitem que um aplicativo animar quase tudo. As animações de propriedade podem ser usadas para alterar qualquer propriedade de qualquer objeto, mesmo se esse objeto não é visível na tela.

-   **Animação drawable** &ndash; isso um recurso de Desenhável especial que é usado para aplicar uma animação muito simples em vigor para layouts.

Em geral, a animação da propriedade é o sistema preferencial a ser usado conforme ele é mais flexível e oferece mais recursos.


### <a name="view-animations"></a>Animações de modo de exibição

Animações de modo de exibição são limitadas a modos de exibição e só podem executar animações em valores, como o início e pontos de extremidade, tamanho, rotação e transparência.
Esses tipos de animações são geralmente denominados *animações de interpolação*. Animações de modo de exibição podem ser definidas de duas maneiras de &ndash; programaticamente no código ou usando arquivos XML. Arquivos XML são a maneira preferencial para declarar o modo de exibição de animações, pois eles são mais legível e fácil de manter.

Os arquivos XML de animação serão armazenados no `/Resources/anim` diretório de um projeto xamarin. Android. Esse arquivo deve ter um dos seguintes elementos como o elemento raiz:

-   `alpha` &ndash; Uma animação de esmaecimento ou de fade in.

-   `rotate` &ndash; Uma animação de rotação.

-   `scale` &ndash; Uma animação de redimensionamento.

-   `translate` &ndash; Um movimento vertical e/ou horizontal.

-   `set` &ndash; Um contêiner que pode conter um ou mais dos outros elementos de animação.

Por padrão, todas as animações em um arquivo XML serão aplicadas ao mesmo tempo. Para fazer com que as animações ocorrem sequencialmente, defina o `android:startOffset` atributo em um dos elementos definidos acima.

É possível afetar a taxa de alteração em uma animação usando um *interpolador*. Um interpolador torna possível para efeitos de animação a ser acelerada, repetidas ou decelerated. A estrutura do Android fornece vários interpoladores imediatamente, como (mas não limitado a):

-   `AccelerateInterpolator` / `DecelerateInterpolator` &ndash; Esses interpoladores aumentam ou diminuir a taxa de alteração em uma animação.

-   `BounceInterpolator` &ndash; a alteração bounces no final.

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

Essa animação executará todas as animações ao mesmo tempo. A primeira animação de escala será esticar a imagem horizontalmente e reduzir verticalmente e, em seguida, a imagem será simultaneamente girada 45 graus no sentido anti-horário e reduzir, desaparecem da tela.

A animação pode ser aplicada por meio de programação para um modo de exibição, aumentando a animação e, em seguida, aplicá-la a um modo de exibição. O Android fornece a classe auxiliar `Android.Views.Animations.AnimationUtils` que serão inflar um recurso de animação e retornar uma instância de `Android.Views.Animations.Animation`. Esse objeto é aplicado a uma exibição chamando `StartAnimation` e passando o `Animation` objeto. O trecho de código a seguir mostra um exemplo disso:

```csharp
Animation myAnimation = AnimationUtils.LoadAnimation(Resource.Animation.MyAnimation);
ImageView myImage = FindViewById<ImageView>(Resource.Id.imageView1);
myImage.StartAnimation(myAnimation);
```

Agora que temos um entendimento fundamental de como funcionam as animações de modo de exibição, permite mover a animações de propriedade.


### <a name="property-animations"></a>Animações de propriedade

Animadores de propriedade são uma nova API que foi introduzida no Android 3.0.
Eles fornecem uma API mais extensível que pode ser usada para animar qualquer propriedade em qualquer objeto.

Todas as animações de propriedade são criadas por instâncias de [Animator](https://developer.xamarin.com/api/type/Android.Animation.Animator/) subclasse. Os aplicativos não usam essa classe diretamente, em vez disso, use uma das subclasses de TI:

-   [ValueAnimator](https://developer.xamarin.com/api/type/Android.Animation.ValueAnimator/) &ndash; essa classe é a classe mais importante na API de animação de propriedade de inteiro. Ele calcula os valores das propriedades que precisam ser alteradas. O `ViewAnimator` não atualiza diretamente esses valores; em vez disso, ela gera eventos que podem ser usados para atualizar objetos animados.

-   [ObjectAnimator](https://developer.xamarin.com/api/type/Android.Animation.ObjectAnimator/) &ndash; essa classe é uma subclasse de `ValueAnimator` . Ele destina-se para simplificar o processo de objetos, aceitando um objeto de destino e a propriedade a ser atualizada.

-   [AnimationSet](https://developer.xamarin.com/api/type/Android.Animation.AnimatorSet/) &ndash; essa classe é responsável pela coordenação como animações executados em relação umas às outras. As animações podem executar ao mesmo tempo, sequencialmente ou com um atraso especificado entre eles.


*Avaliadores* são classes especiais que são usadas pelo animadores para calcular os novos valores durante uma animação. Imediatamente, o Android fornece os avaliadores a seguir:

-   [IntEvaluator](https://developer.xamarin.com/api/type/Android.Animation.IntEvaluator/) &ndash; calcula valores para propriedades de inteiro.

-   [FloatEvaluator](https://developer.xamarin.com/api/type/Android.Animation.FloatEvaluator/) &ndash; calcula valores para propriedades de float.

-   [ArgbEvaluator](https://developer.xamarin.com/api/type/Android.Animation.ArgbEvaluator/) &ndash; calcula valores para propriedades de cor.

Se a propriedade que está sendo animada não é um `float`, `int` ou a cor, aplicativos podem criar seu próprios avaliador Implementando o `ITypeEvaluator` interface. (Implementar avaliadores personalizados está além do escopo deste tópico.)

#### <a name="using-the-valueanimator"></a>Usando o ValueAnimator

Há duas partes para qualquer animação: calculando valores animados e, em seguida, definir esses valores nas propriedades de um objeto. 
[ValueAnimator](https://developer.xamarin.com/api/type/Android.Animation.ValueAnimator/) apenas calculará os valores, mas ele não funcionará em objetos diretamente. Em vez disso, objetos sejam atualizados dentro de manipuladores de eventos que serão invocados durante o tempo de vida de animação. Esse design permite que várias propriedades para ser atualizado de um valor animado.

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

Mas o próprio, trecho de código acima não é muito útil &ndash; executará o animador, mas não há nenhum destino para o valor atualizado. O `Animator` classe irá gerar o evento de atualização quando ele decide o que é necessário informar ouvintes de um novo valor. Aplicativos podem fornecer um manipulador de eventos para responder a esse evento, conforme mostrado no trecho de código a seguir:

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

Agora que temos uma compreensão das `ValueAnimator`, permite que saiba mais sobre o `ObjectAnimator`.

#### <a name="using-the-objectanimator"></a>Usando o ObjectAnimator

[ObjectAnimator](https://developer.xamarin.com/api/type/Android.Animation.ObjectAnimator/) é uma subclasse de `ViewAnimator` que combina o cálculo de mecanismo e o valor de tempo do `ValueAnimator` com a lógica necessária para conectar manipuladores de eventos. O `ValueAnimator` requer que os aplicativos para conectar um manipulador de eventos explicitamente &ndash; `ObjectAnimator` cuidará desta etapa para nós.

A API para `ObjectAnimator` é muito semelhante à API para `ViewAnimator`, mas exige que você forneça o objeto e o nome da propriedade para atualizar. O exemplo a seguir mostra um exemplo de uso `ObjectAnimator`:

```csharp
MyCustomObject myObj = new MyCustomObject();
myObj.SomeIntegerValue = -1;

ObjectAnimator animator = ObjectAnimator.OfFloat(myObj, "SomeIntegerValue", 0, 100);
animator.SetDuration(1000);
animator.Start();
```

Como você pode ver no trecho de código anterior, `ObjectAnimator` pode reduzir e simplificar o código que é necessário para animar um objeto.


### <a name="drawable-animations"></a>Animações desenháveis

A API de animação final é a API de animação Desenháveis. Animações desenháveis carregar uma série de recursos Desenháveis um após o outro e exibi-los em sequência, semelhante a um desenho de flip-it.

Recursos desenháveis são definidos em um arquivo XML que tem um `<animation-list>` elemento como o elemento raiz e uma série de `<item>` elementos que definem cada quadro na animação. Esse arquivo XML é armazenado no `/Resource/drawable` pasta do aplicativo. O XML a seguir está um exemplo de uma animação de desenhável:

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

Essa animação será executada por meio de seis quadros. O `android:duration` atributo declara quanto tempo cada quadro será exibido. O próximo trecho de código mostra um exemplo de criação de uma animação Desenhável e iniciá-lo quando o usuário clica em um botão na tela:

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

Neste momento, abordamos as bases da animação APIs disponíveis em um aplicativo Android.


## <a name="summary"></a>Resumo

Este artigo apresentou um muitos conceitos novos e APIs ajudar a adicionar alguns elementos gráficos a um aplicativo Android. Primeiro, ele discutidas os vários gráficos 2D da API e demonstrado como Android permite que os aplicativos desenham diretamente na tela usando um objeto de tela. Também vimos algumas técnicas alternativas que permitem que os gráficos a ser criado declarativamente usando arquivos XML. Em seguida, entramos para discutir nova e antiga da API para a criação de animações no Android.



## <a name="related-links"></a>Links relacionados

- [Demonstração de animação (amostra)](https://developer.xamarin.com/samples/monodroid/AnimationDemo)
- [Animação e elementos gráficos](https://developer.android.com/guide/topics/graphics/index.html)
- [Usando animações para dar vida a seus aplicativos móveis](http://youtu.be/ikSk_ILg3d0)
- [AnimationDrawable](https://developer.xamarin.com/api/type/Android.Graphics.Drawables.AnimationDrawable/)
- [Tela](https://developer.xamarin.com/api/type/Android.Graphics.Canvas/)
- [Objeto Animator](https://developer.xamarin.com/api/type/Android.Animation.ObjectAnimator/)
- [Valor Animator](https://developer.xamarin.com/api/type/Android.Animation.ValueAnimator/)
