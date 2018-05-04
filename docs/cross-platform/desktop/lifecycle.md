---
ms.assetid: 7C132A7C-4973-4B2D-98DC-3661C08EA33F
title: WPF vs. Ciclo de vida de aplicativo xamarin. Forms
description: Noções básicas sobre o processo de inicialização do aplicativo e lidar com os estados de plano de fundo
ms.technology: xamarin-crossplatform
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: dfcc00fa34d3bd5026653a4550e16634bd2c6238
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/03/2018
---
# <a name="wpf-vs-xamarinforms-app-lifecycle"></a>WPF vs. Ciclo de vida de aplicativo xamarin. Forms

Xamarin. Forms exige muita diretrizes de design das estruturas baseadas em XAML antes dele, particularmente WPF. No entanto, outras maneiras ele desvia significativamente que pode ser um ponto Autoadesivas pessoas tentando migrar sobre. Este documento tenta identificar alguns desses problemas e fornece orientações sempre que possível, a ponte de dados de conhecimento do WPF para xamarin. Forms.

## <a name="app-lifecycle"></a>Ciclo de vida do aplicativo

O ciclo de vida do aplicativo entre WPF e xamarin. Forms é semelhante. Start no código externo (plataforma) e inicie a interface do usuário por meio de uma chamada de método. A diferença é que o xamarin. Forms sempre começa em um conjunto específico de plataforma que inicializa e cria a interface do usuário para o aplicativo.

**WPF**

- `Main method > App > MainWindow`

> [!NOTE]
> O `Main` método é, por padrão, automaticamente, gerado e não é visível no código.

**Xamarin.Forms**

- **iOS** &ndash; `Main method > AppDelegate > App > ContentPage`
- **Android** &ndash; `MainActivity > App > ContentPage`
- **UWP** &ndash; `Main method > App(UWP) > MainPage(UWP) > App > ContentPage`

### <a name="application-class"></a>Classe do aplicativo

WPF e xamarin. Forms têm um `Application` classe que é criado como um singleton. Na maioria dos casos, aplicativos serão derivar desta classe para fornecer um aplicativo personalizado, embora isso não é estritamente necessário no WPF. Expõem um `Application.Current` propriedade para localizar o singleton criado.

### <a name="global-properties--persistence"></a>Propriedades globais + persistência

WPF e xamarin. Forms têm um `Application.Properties` dicionário disponível onde você pode armazenar objetos de nível de aplicativo global que podem ser acessados em qualquer lugar no aplicativo. A principal diferença é que será xamarin. Forms _persistir_ qualquer tipos primitivos armazenados na coleção quando o aplicativo está suspenso e recarregá-las quando for relançada. WPF não suporta automaticamente esse comportamento - em vez disso, a maioria dos desenvolvedores baseava-se no armazenamento isolado ou utilizado interno `Settings` suporte.

## <a name="defining-pages-and-the-visual-tree"></a>Definindo páginas e a árvore Visual

O WPF usa o `Window` como elemento raiz de qualquer elemento visual de nível superior. Isso define um HWND no mundo do Windows para exibir informações. Você pode criar e exibir janelas tantos simultaneamente como desejar no WPF.

Xamarin. Forms, o visual de nível superior sempre é definido pela plataforma - por exemplo, no iOS, é um `UIWindow`. Xamarin. Forms renderiza conteúdo nessas representações de plataforma nativo usando um `Page` classe. Cada `Page` xamarin. Forms representa uma "página" exclusiva no aplicativo, onde somente uma é visível por vez.

Ambos os WPFs `Window` e xamarin. Forms `Page` incluem um `Title` propriedade para influenciar o título exibido e ambos têm um `Icon` propriedade para exibir um ícone específico para a página (**Observação** que o título e o ícone não são sempre visíveis no xamarin. Forms). Além disso, você pode alterar as propriedades comuns de visual em ambos, como a cor de plano de fundo ou imagem.

É tecnicamente possível renderizar a dois modos de exibição de plataforma separadas (por exemplo, definir dois `UIWindow` objetos e que o processamento de um segundo para um monitor ou externo AirPlay), não requer código específico da plataforma para fazer isso e não é um recurso com suporte diretamente do Xamarin. Forms em si.

### <a name="views"></a>Exibições

A hierarquia visual para ambas as estruturas é semelhante. WPF é um pouco mais profundo devido ao seu suporte para documentos WYSIWYG.

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

### <a name="view-lifcycle"></a>Modo de exibição Lifcycle

Xamarin. Forms principalmente é voltado para cenários móveis. Dessa forma, os aplicativos são _ativado_, _suspenso_, e _reativado_ conforme o usuário interage com eles. Isso é semelhante a clicar fora do `Window` em um aplicativo WPF e há um conjunto de métodos e eventos correspondentes, você pode substituir ou conectar-se ao monitorar esse comportamento.

| Finalidade | Método WPF | Método xamarin. Forms |
|--- |--- |--- |
|Ativação inicial|construtor + Window.OnLoaded|construtor + Page.OnStart|
|Mostrado|Window.IsVisibleChanged|Page.Appearing|
|Hidden|Window.IsVisibleChanged|Page.Disapearing|
|Suspender/perdidos foco|Window.OnDeactivated|Page.OnSleep|
|Ativado/obteve foco|Window.OnActivated|Page.OnResume|
|Closed|Window.OnClosing + Window.OnClosed|N/D|


Ambos os suporte mostrando/ocultando controles filho, em WPF é uma propriedade de três estados `IsVisible` (visível, oculto e recolhido). Em xamarin. Forms, é apenas visível ou oculto por meio de `IsVisible` propriedade.

### <a name="layout"></a>Layout

Layout de página ocorre na mesma 2-fase (medida/organizar) que ocorre no WPF. Você pode acessar o layout de página com a substituição dos seguintes métodos no xamarin. Forms `Page` classe:

| Método | Finalidade |
|--- |--- |
|OnChildMeasureInvalidated|Tamanho preferido da filho foi alterado.|
|OnSizeAllocated|Página recebeu uma largura/altura.|
|Evento LayoutChanged|Layout ou o tamanho da página foi alterada.|

Há um evento de layout global que é chamado de hoje, nem há global `CompositionTarget.Rendering` evento como encontrado em WPF.

#### <a name="common-layout-properties"></a>Propriedades comuns de layout

WPF e xamarin. Forms oferecem suporte a `Margin` para espaçamento de controle em torno de um elemento, e `Padding` para controle espaçamento _dentro de_ um elemento. Além disso, a maioria dos modos de exibição de layout xamarin. Forms têm propriedades para controlar o espaçamento (por exemplo, linha ou coluna).

Além disso, a maioria dos elementos têm propriedades para influenciar como eles são posicionados no contêiner pai:

| WPF | Xamarin.Forms | Finalidade |
|--- |--- |--- |
|HorizontalAlignment|HorizontalOptions|Opções de esquerda/Center/direito/Stretch|
|VerticalAlignment|VerticalOptions|Opções de início/Center/inferior/Stretch|

> [!NOTE]
> A interpretação real dessas propriedades depende do contêiner pai.

#### <a name="layout-views"></a>Modos de exibição de layout

WPF e xamarin. Forms usam controles de layout para posicionar elementos filho. Na maioria dos casos, esses são muito próximos uns dos outros em termos de funcionalidade.

| WPF | Xamarin.Forms | Estilo de layout |
|--- |--- |--- |
|StackPanel|StackLayout|Empilhamento infinito esquerda para a direita ou de cima para baixo|
|Grade|Grade|Formato de tabela (linhas e colunas)|
|DockPanel|N/D|As bordas da janela de encaixe|
|Tela|AbsoluteLayout|Posicionamento de pixel/coordenada|
|WrapPanel|N/D|Quebra automática de pilha|
|N/D|RelativeLayout|Com base em regra de posicionamento relativo|

> [!NOTE]
> Xamarin. Forms não oferece suporte a um `GridSplitter`.

Usam as duas plataformas _propriedades anexadas_ para ajustar os filhos.

### <a name="rendering"></a>Renderização

O mecanismo de renderização para WPF e xamarin. Forms é radicalmente diferente. No WPF, os controles que você cria diretamente renderizam o conteúdo de pixels na tela. WPF mantém dois gráficos de objeto (_árvores_) para representar esse - o _árvore lógica_ representa os controles, conforme definido no código ou XAML e o _árvore visual_ representa o processamento real ocorre na tela que é executado diretamente pelo elemento visual (por meio de um método virtual desenho), ou por meio de um definido em XAML `ControlTemplate` que podem ser substituído ou personalizado. Normalmente, a árvore visual é mais complexa, pois isso inclui como bordas em torno de controles, rótulos para conteúdo implícita, etc. O WPF inclui um conjunto de APIs (`LogicalTreeHelper` e `VisualTreeHelper`) para examinar esses dois gráficos de objeto.

No xamarin. Forms, os controles que você define em um `Page` são objetos de dados realmente simples. Eles são semelhantes para a representação de árvore lógica, mas nunca renderizam o conteúdo por conta própria. Em vez disso, eles são o _modelo de dados_ que influencia a renderização de elementos. O processamento real é feito um [separar o conjunto de _renderizadores visual_ que é mapeado para cada tipo de controle](~/xamarin-forms/app-fundamentals/custom-renderer/index.md). Esses processadores são registrados em cada um dos projetos específicos de plataforma por assemblies do xamarin. Forms específico da plataforma. Você pode ver uma lista [aqui](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md). Além de substituição ou estendendo o processador, xamarin. Forms também tem suporte para [efeitos](~/xamarin-forms/app-fundamentals/effects/index.md) que pode ser usado para influenciar a renderização nativo em uma base por plataforma.

#### <a name="the-logicalvisual-tree"></a>A árvore lógica/Visual

Há uma API exposta para percorrer a árvore lógica no xamarin. Forms - mas você pode usar a reflexão para obter as mesmas informações. Por exemplo, [aqui é um método que pode enumerar os filhos lógicos](https://github.com/xamarinhq/xamu-infrastructure/blob/master/src/XamU.Infrastructure/Extensions/ElementExtensions.cs#L108) com reflexão.

## <a name="graphics"></a>Gráficos

Xamarin. Forms não inclui um sistema de elementos gráficos para tipos primitivos, além de um retângulo simple (`BoxView`). Você pode incluir 3º bibliotecas de terceiros, como [SkiaSharp](~/graphics-games/skiasharp/index.md) obter desenho 2D de plataforma cruzada, ou [UrhoSharp](~/graphics-games/urhosharp/index.md) para 3D.

## <a name="resources"></a>Recursos

WPF e xamarin. Forms têm o conceito de recursos e dicionários de recursos. Você pode colocar qualquer tipo de objeto em um `ResourceDictionary` com uma chave e, em seguida, procure com `{StaticResource}` para ações que não serão alterado, ou `{DynamicResource}` para coisas que podem ser alteradas no dicionário em tempo de execução. O uso e a mecânica é os mesmos, com uma diferença: xamarin. Forms requer que você defina o `ResourceDictionary` para atribuir ao `Resources` propriedade enquanto o WPF cria um previamente e atribui-lo para você.

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

Estilos também totalmente compatível com xamarin. Forms e pode ser usado para o tema xamarin. Forms elementos que compõem a interface do usuário. Eles oferecem suporte a herança de gatilhos (propriedade, eventos e dados), por meio de `BasedOn`e as pesquisas de recursos para valores. Os estilos são aplicados aos elementos ou explicitamente por meio de `Style` propriedade ou implicitely ao não fornecer uma chave de recurso - como WPF.

### <a name="device-styles"></a>Estilos de dispositivo

WPF tem um conjunto de propriedades predefinidas (armazenados como valores estáticos em um conjunto de classes estáticas, como `SystemColors`) que ditam sistema cores, fontes e métricas na forma de valores e chaves de recurso. Xamarin. Forms é semelhante, mas define um conjunto de [estilos de dispositivo](~/xamarin-forms/user-interface/styles/device.md) representar as mesmas coisas. Esses estilos são fornecidos pelo frameowrk e definidos como valores com base no ambiente de tempo de execução (por exemplo, acessibilidade).

**WPF**

```xaml
<Label Text="Title" Foreground="{DynamicResource {x:Static SystemColors.DesktopBrushKey}}" />
```

**Xamarin.Forms**

```xaml
<Label Text="Title" Style="{DynamicResource TitleStyle}" />
```
