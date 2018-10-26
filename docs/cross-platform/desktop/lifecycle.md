---
ms.assetid: 7C132A7C-4973-4B2D-98DC-3661C08EA33F
title: WPF x. Ciclo de vida de aplicativo xamarin. Forms
description: Este documento compara as semelhanças e diferenças entre o ciclo de vida do aplicativo para aplicativos xamarin. Forms e WPF. Ele também analisa a árvore visual, gráficos, recursos e estilos.
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: cf25cf956fbf9fd566520d9067f0d98a9a7624aa
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50107099"
---
# <a name="wpf-vs-xamarinforms-app-lifecycle"></a>WPF x. Ciclo de vida de aplicativo xamarin. Forms

Xamarin. Forms usa muitas diretrizes de design das estruturas com base em XAML antes dele, particularmente o WPF. No entanto, de outras maneiras ele tiver um desvio significativamente que pode ser um ponto de adesivo para as pessoas tentando migrar sobre. Este documento tenta identificar alguns desses problemas e fornecer orientações sempre que possível, a ponte de dados de conhecimento do WPF para xamarin. Forms.

## <a name="app-lifecycle"></a>Ciclo de vida do aplicativo

O ciclo de vida do aplicativo entre o WPF e o xamarin. Forms é semelhante. Start em código externo (plataforma) e inicie a interface do usuário por meio de uma chamada de método. A diferença é que o xamarin. Forms sempre é iniciado em um assembly específico da plataforma que, em seguida, inicializa e cria a interface do usuário para o aplicativo.

**WPF**

- `Main method > App > MainWindow`

> [!NOTE]
> O `Main` método é, por padrão, automaticamente gerado e não são visível no código.

**Xamarin.Forms**

- **iOS** &ndash; `Main method > AppDelegate > App > ContentPage`
- **Android** &ndash; `MainActivity > App > ContentPage`
- **UWP** &ndash; `Main method > App(UWP) > MainPage(UWP) > App > ContentPage`

### <a name="application-class"></a>Classe de aplicativo

WPF e o xamarin. Forms têm uma `Application` classe que é criado como um singleton. Na maioria dos casos, aplicativos serão derivam dessa classe para fornecer um aplicativo personalizado, embora isso não é estritamente necessário no WPF. Expõem um `Application.Current` propriedade para localizar o singleton criado.

### <a name="global-properties--persistence"></a>Propriedades globais + persistência

WPF e o xamarin. Forms têm uma `Application.Properties` dicionário disponível onde você pode armazenar objetos de nível de aplicativo globais que podem ser acessados em qualquer lugar no aplicativo. A principal diferença é que serão de xamarin. Forms _persistir_ quaisquer tipos primitivos armazenados na coleção quando o aplicativo está suspenso e recarregá-los quando ele é relançado. WPF não suporta automaticamente esse comportamento – em vez disso, a maioria dos desenvolvedores contavam com armazenamento isolado ou utilizado interno `Settings` dão suporte.

## <a name="defining-pages-and-the-visual-tree"></a>Definindo páginas e a árvore Visual

O WPF usa o `Window` como o elemento raiz de qualquer elemento visual de nível superior. Isso define um HWND no mundo do Windows para exibir informações. Você pode criar e exibir quantas janelas ao mesmo tempo que desejar no WPF.

No xamarin. Forms, o visual de nível superior é sempre definido pela plataforma – por exemplo, no iOS, é um `UIWindow`. Processa de xamarin. Forms é conteúdo nessas representações de plataforma nativa usando um `Page` classe. Cada `Page` no xamarin. Forms representa uma "página" exclusiva no aplicativo, onde somente uma é visível por vez.

Ambos os WPFs `Window` e xamarin. Forms `Page` incluem um `Title` propriedade para influenciar o título exibido e ambos têm um `Icon` propriedade para exibir um ícone específico para a página (**Observação** que o título e o ícone não estão sempre visíveis no xamarin. Forms). Além disso, você pode alterar as propriedades comuns de visual em ambos, como a cor do plano de fundo ou imagem.

É tecnicamente possível renderizar a dois modos de exibição de plataforma separadas (por exemplo, defina dois `UIWindow` objetos e que o processamento em segundo lugar, um a um vídeo externo ou AirPlay), ele requer código específico da plataforma para fazer isso e não é um recurso com suporte diretamente do Xamarin. Forms em si.

### <a name="views"></a>Exibições

A hierarquia visual para ambas as estruturas é semelhante. O WPF é um pouco mais devido a seu suporte para documentos WYSIWYG.

**WPF**

```
DependencyObject - base class for all bindable things
   Visual - rendering mechanics
      UIElement - common events + interactions
         FrameworkElement - adds layout
            Shape - 2D graphics
            Control - interactive controls
```

**Xamarin.Forms**

```
BindableObject - base class for all bindable things
   Element - basic parent/child support + resources + effects
      VisualElement - adds visual rendering properties (color, fonts, transforms, etc.)
         View - layout + gesture support
```

### <a name="view-lifecycle"></a>Ciclo de vida de exibição

Xamarin. Forms está voltada principalmente para cenários móveis. Dessa forma, os aplicativos são _ativados_, _suspenso_, e _reativado_ conforme o usuário interage com eles. Isso é semelhante a clicar fora do `Window` em um aplicativo WPF e há um conjunto de métodos e eventos correspondentes, você pode substituir ou se conectar ao monitorar esse comportamento.

| Finalidade | Método WPF | Método de xamarin. Forms |
|--- |--- |--- |
|Ativação inicial|ctor + Window.OnLoaded|ctor + Page.OnStart|
|Mostrado|Window.IsVisibleChanged|Page.Appearing|
|Hidden|Window.IsVisibleChanged|Page.Disapearing|
|Foco de suspender/perdidos|Window.OnDeactivated|Page.OnSleep|
|Ativado/recebido foco|Window.OnActivated|Page.OnResume|
|Closed|Window.OnClosing + Window.OnClosed|N/D|


Ambos os suporte ocultando/mostrando controles filho também, no WPF é uma propriedade de três estados `IsVisible` (visível, oculto e recolhido). No xamarin. Forms, é apenas visível ou oculto por meio de `IsVisible` propriedade.

### <a name="layout"></a>Layout

Layout de página ocorre no mesmo 2-passo (Arrange/medida) que acontece no WPF. Você pode conectar o layout da página, substituindo os métodos a seguir no xamarin. Forms `Page` classe:

| Método | Finalidade |
|--- |--- |
|OnChildMeasureInvalidated|O tamanho preferencial de um filho foi alterado.|
|OnSizeAllocated|Página foi atribuída uma largura/altura.|
|Evento LayoutChanged|Layout ou o tamanho da página foi alterado.|

Existe um evento de layout global que é chamado de hoje, nem há um global `CompositionTarget.Rendering` evento, como encontrado no WPF.

#### <a name="common-layout-properties"></a>Propriedades de layout comuns

Oferecem suporte de xamarin. Forms e WPF `Margin` para controle de espaçamento em torno de um elemento, e `Padding` para controle de espaçamento _dentro de_ um elemento. Além disso, a maioria dos modos de exibição de layout de xamarin. Forms tem propriedades para controlar o espaçamento (por exemplo, linha ou coluna).

Além disso, a maioria dos elementos têm propriedades para influenciar como eles são posicionados no contêiner pai:

| WPF | Xamarin.Forms | Finalidade |
|--- |--- |--- |
|HorizontalAlignment|HorizontalOptions|Opções de esquerda/Center/direita/Stretch|
|VerticalAlignment|Propriedades VerticalOptions|Opções de início/Center/inferior/Stretch|

> [!NOTE]
> A interpretação real dessas propriedades depende do contêiner pai.

#### <a name="layout-views"></a>Modos de exibição de layout

WPF e o xamarin. Forms usam controles de layout para posicionar elementos filho. Na maioria dos casos, essas são muito perto uns dos outros em termos de funcionalidade.

| WPF | Xamarin.Forms | Estilo de layout |
|--- |--- |--- |
|StackPanel|StackLayout|Empilhamento infinito esquerda para a direita ou para cima para baixo|
|Grade|Grade|Formato de tabela (linhas e colunas)|
|DockPanel|N/D|Encaixar às bordas da janela|
|Tela|AbsoluteLayout|Posicionamento/coordenada de pixel|
|WrapPanel|N/D|Quebra automática de pilha|
|N/D|RelativeLayout|Posicionamento relativo baseado em regras|

> [!NOTE]
> Xamarin. Forms não oferece suporte a um `GridSplitter`.

Ambas as plataformas usam _propriedades anexadas_ para ajustar os filhos.

### <a name="rendering"></a>Renderização

O mecanismo de renderização para xamarin. Forms e WPF é radicalmente diferente. No WPF, os controles que você criar diretamente renderizem conteúdo pixels na tela. WPF mantém dois grafos de objeto (_árvores_) para representá-lo - as _árvore lógica_ representa os controles, conforme definido no código ou XAML e o _árvore visual_ representa o processamento real ocorre na tela que é executado diretamente pelo elemento visual (por meio de um método draw virtual) ou por meio de um XAML definido `ControlTemplate` que podem ser substituído ou personalizado. Normalmente, a árvore visual é mais complexa, pois isso inclui como bordas em torno de controles, os rótulos para conteúdo implícito, etc. O WPF inclui um conjunto de APIs (`LogicalTreeHelper` e `VisualTreeHelper`) para examinar esses dois gráficos de objetos.

No xamarin. Forms, os controles que você define em um `Page` são objetos de dados realmente bem simples. Eles são semelhantes a representação de árvore lógica, mas nunca renderizam conteúdo por conta própria. Em vez disso, eles são os _modelo de dados_ que influencia a renderização de elementos. O processamento real é feito por uma [separar o conjunto de _renderizadores visual_ que é mapeado para cada tipo de controle](~/xamarin-forms/app-fundamentals/custom-renderer/index.md). Esses processadores são registrados em cada um dos projetos específicos da plataforma por conjuntos de módulos específicos da plataforma xamarin. Forms. Você pode ver uma lista [aqui](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md). Além de substituir ou estender o renderizador, xamarin. Forms também tem suporte para [efeitos](~/xamarin-forms/app-fundamentals/effects/index.md) que pode ser usado para influenciar a renderização nativa em uma base por plataforma.

#### <a name="the-logicalvisual-tree"></a>A árvore lógica/Visual

Há uma API exposta para percorrer a árvore lógica no xamarin. Forms - mas você pode usar reflexão para obter as mesmas informações. Por exemplo, [aqui está um método que pode enumerar os filhos lógicos](https://github.com/xamarinhq/xamu-infrastructure/blob/master/src/XamU.Infrastructure/Extensions/ElementExtensions.cs#L108) com reflexão.

## <a name="graphics"></a>Gráficos

Xamarin. Forms não inclui um sistema de elementos gráficos para primitivos, além de um retângulo simples (`BoxView`). Você pode incluir como bibliotecas de terceiros 3ª [SkiaSharp](~/graphics-games/skiasharp/index.md) para obter o desenho 2D de plataforma cruzada, ou [UrhoSharp](~/graphics-games/urhosharp/index.md) para 3D.

## <a name="resources"></a>Recursos

Xamarin. Forms e WPF têm o conceito de recursos e dicionários de recursos. Você pode colocar qualquer tipo de objeto em uma `ResourceDictionary` com uma chave e, em seguida, procurá-lo com `{StaticResource}` para coisas que não serão alterado, ou `{DynamicResource}` para coisas que podem ser alterado no dicionário em tempo de execução. O uso e a mecânica é os mesmos, com uma diferença: xamarin. Forms requer que você defina as `ResourceDictionary` para atribuir ao `Resources` propriedade enquanto o WPF cria previamente um e atribui-lo para você.

Por exemplo, consulte a definição abaixo:

**WPF**

```xaml
<Window.Resources>
   <Color x:Key="redColor">#ff0000</Color>
   ...
</Window.Resources>
```

**Xamarin.Forms**

```xaml
<ContentPage.Resources>
   <ResourceDictionary>
      <Color x:Key="redColor">#ff0000</Color>
      ...
   </ResourceDictionary>
</ContentPage.Resources>
```

Se você não definir o `ResourceDictionary`, será gerado um erro de tempo de execução.

## <a name="styles"></a>Estilos

Estilos também tem total suporte no xamarin. Forms e pode ser usado para o tema os elementos de xamarin. Forms que compõem a interface do usuário. Eles oferecem suporte a herança de gatilhos (propriedades, eventos e dados), por meio de `BasedOn`e as pesquisas de recursos para valores. Os estilos são aplicados aos elementos qualquer explicitamente por meio de `Style` propriedade, ou implicitamente ao não fornecer uma chave de recurso – assim como o WPF.

### <a name="device-styles"></a>Estilos de dispositivo

O WPF tem um conjunto de propriedades predefinidas (armazenados como valores estáticos em um conjunto de classes estáticas, como `SystemColors`) que determinam o sistema de sites que cores, fontes e métricas na forma de valores e chaves de recurso. Xamarin. Forms é semelhante, mas define um conjunto de [estilos de dispositivo](~/xamarin-forms/user-interface/styles/device.md) para representar as mesmas coisas. Esses estilos são fornecidos pela frameowrk e definidos como valores com base no ambiente de tempo de execução (por exemplo, acessibilidade).

**WPF**

```xaml
<Label Text="Title" Foreground="{DynamicResource {x:Static SystemColors.DesktopBrushKey}}" />
```

**Xamarin.Forms**

```xaml
<Label Text="Title" Style="{DynamicResource TitleStyle}" />
```
