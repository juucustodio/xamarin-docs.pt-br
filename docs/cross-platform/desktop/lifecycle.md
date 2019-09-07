---
ms.assetid: 7C132A7C-4973-4B2D-98DC-3661C08EA33F
title: WPF vs. Ciclo de vida do aplicativo no Xamarin.Forms
description: Este documento compara as semelhanças e diferenças entre o ciclo de vida do aplicativo para aplicativos Xamarin. Forms e WPF. Ele também examina a árvore visual, os gráficos, os recursos e os estilos.
author: conceptdev
ms.author: crdun
ms.date: 04/26/2017
ms.openlocfilehash: 1d1c207672baf37c91d39fca1d9f628d85971577
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70765900"
---
# <a name="wpf-vs-xamarinforms-app-lifecycle"></a>WPF vs. Ciclo de vida do aplicativo no Xamarin.Forms

O Xamarin. Forms leva muitas diretrizes de design das estruturas baseadas em XAML que vieram antes dela, especialmente o WPF. No entanto, de outras maneiras, ela se desvia significativamente, o que pode ser um ponto adesivo para as pessoas que tentam migrar. Este documento tenta identificar alguns desses problemas e fornece orientação quando possível para a ponte de conhecimento do WPF para Xamarin. Forms.

## <a name="app-lifecycle"></a>Ciclo de vida do aplicativo

O ciclo de vida do aplicativo entre o WPF e o Xamarin. Forms é semelhante. Ambos iniciam em código externo (plataforma) e iniciam a interface do usuário por meio de uma chamada de método. A diferença é que o Xamarin. Forms sempre inicia em um assembly específico da plataforma que inicializa e cria a interface do usuário para o aplicativo.

**WPF**

- `Main method > App > MainWindow`

> [!NOTE]
> O `Main` método é, por padrão, gerado automaticamente e não visível no código.

**Xamarin.Forms**

- **iOS** &ndash; `Main method > AppDelegate > App > ContentPage`
- **Android** &ndash; `MainActivity > App > ContentPage`
- **UWP** &ndash; `Main method > App(UWP) > MainPage(UWP) > App > ContentPage`

### <a name="application-class"></a>Classe de aplicativo

O WPF e o Xamarin. Forms têm `Application` uma classe que é criada como um singleton. Na maioria dos casos, os aplicativos serão derivados dessa classe para fornecer um aplicativo personalizado, embora isso não seja estritamente necessário no WPF. Ambos expõem `Application.Current` uma propriedade para localizar o singleton criado.

### <a name="global-properties--persistence"></a>Propriedades globais + persistência

O WPF e o Xamarin. Forms têm `Application.Properties` um dicionário disponível onde você pode armazenar objetos globais no nível do aplicativo que são acessíveis em qualquer lugar no aplicativo. A principal diferença é que o Xamarin. Forms _manterá_ todos os tipos primitivos armazenados na coleção quando o aplicativo for suspenso e os recarregará quando for reiniciado. O WPF não dá suporte automaticamente a esse comportamento – em vez disso, a maioria dos desenvolvedores conta com armazenamento isolado ou utilizou `Settings` o suporte interno.

## <a name="defining-pages-and-the-visual-tree"></a>Definindo páginas e a árvore visual

O WPF usa `Window` o como o elemento raiz para qualquer elemento visual de nível superior. Isso define um HWND no mundo do Windows para exibir informações. Você pode criar e exibir tantas janelas simultaneamente quanto desejar no WPF.

No Xamarin. Forms, o Visual de nível superior é sempre definido pela plataforma, por exemplo, no iOS, é um `UIWindow`. O Xamarin. Forms renderiza o conteúdo para essas representações de plataforma nativas usando uma `Page` classe. Cada `Page` um no Xamarin. Forms representa uma "página" exclusiva no aplicativo, em que apenas uma é visível por vez.

WPFs `Window` e Xamarin. Forms `Page` incluem uma `Title` propriedade para influenciar o título exibido, e ambos têm uma `Icon` propriedade para exibir um ícone específico para a página (**Observe** que o título e o ícone nem sempre são visível no Xamarin. Forms). Além disso, você pode alterar as propriedades visuais comuns, como a cor ou a imagem do plano de fundo.

É tecnicamente possível renderizar para duas exibições de plataforma separadas (por exemplo, `UIWindow` definir dois objetos e fazer com que o segundo seja renderizado para uma exibição externa ou Airplay), ele requer o código específico da plataforma para fazer isso e não é um recurso com suporte direto do Xamarin. Forms em si.

### <a name="views"></a>Exibições

A hierarquia visual para ambas as estruturas é semelhante. O WPF é um pouco mais profundo devido ao seu suporte a documentos WYSIWYG.

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

O Xamarin. Forms é orientado principalmente em cenários móveis. Assim, os aplicativos são _ativados_, _suspensos_e _reativados_ à medida que o usuário interage com eles. Isso é semelhante a clicar fora do `Window` em um aplicativo do WPF e há um conjunto de métodos e eventos correspondentes que você pode substituir ou conectar para monitorar esse comportamento.

| Finalidade | Método WPF | Método Xamarin. Forms |
|--- |--- |--- |
|Ativação inicial|Construtor + janela. pré-carregado|ctor + Page. OnStart|
|Hubs|Window.IsVisibleChanged|Página. aparecendo|
|Hidden|Window.IsVisibleChanged|Página. desaparecendo|
|Suspender/perder foco|Janela. OnActivated|Page.OnSleep|
|Ativado/obtido com foco|Window.OnActivated|Page.OnResume|
|Closed|Janela. onfechando + janela. oncloseed|N/D|

Ambos dão suporte à ocultação/exibição de controles filho também, no WPF, é uma propriedade `IsVisible` de três Estados (visível, oculta e recolhida). No Xamarin. Forms, ele está apenas visível ou oculto pela `IsVisible` propriedade.

### <a name="layout"></a>Layout

O layout de página ocorre na mesma 2 passagens (medida/organização) que ocorre no WPF. Você pode conectar-se ao layout da página substituindo os seguintes métodos na classe Xamarin `Page` . Forms:

| Método | Finalidade |
|--- |--- |
|OnChildMeasureInvalidated|O tamanho preferencial de um filho foi alterado.|
|OnSizeAllocated|A página recebeu uma largura/altura.|
|Evento layoutchanged|Layout/tamanho da página alterado.|

Não há evento de layout global chamado hoje, nem há um evento global `CompositionTarget.Rendering` como encontrado no WPF.

#### <a name="common-layout-properties"></a>Propriedades de layout comuns

O WPF e o Xamarin. Forms `Margin` têm suporte para controlar o espaçamento em `Padding` um elemento e para controlar o espaçamento _dentro_ de um elemento. Além disso, a maioria dos modos de exibição de layout do Xamarin. Forms tem propriedades para controlar o espaçamento (por exemplo, linha ou coluna).

Além disso, a maioria dos elementos tem propriedades para influenciar como eles são posicionados no contêiner pai:

| WPF | Xamarin.Forms | Finalidade |
|--- |--- |--- |
|HorizontalAlignment|HorizontalOptions|Opções esquerda/central/direita/alongar|
|VerticalAlignment|VerticalOptions|Opções superior/central/inferior/ampliação|

> [!NOTE]
> A interpretação real dessas propriedades depende do contêiner pai.

#### <a name="layout-views"></a>Exibições de layout

O WPF e o Xamarin. Forms usam controles de layout para posicionar elementos filho. Na maioria dos casos, eles são muito próximos um do outro em termos de funcionalidade.

| WPF | Xamarin.Forms | Estilo do layout |
|--- |--- |--- |
|StackPanel|StackLayout|Empilhamento infinito da esquerda para a direita ou de cima para baixo|
|Grade|Grade|Formato de tabela (linhas e colunas)|
|DockPanel|N/D|Encaixar nas bordas da janela|
|Tela|AbsoluteLayout|Posicionamento de pixel/coordenado|
|WrapPanel|N/D|Pilha de encapsulamento|
|N/D|RelativeLayout|Posicionamento relativo baseado em regra|

> [!NOTE]
> O Xamarin. Forms não dá suporte `GridSplitter`a um.

Ambas as plataformas usam _Propriedades anexadas_ para ajustar os filhos.

### <a name="rendering"></a>Renderização

A mecânica de renderização para WPF e Xamarin. Forms é radicalmente diferente. No WPF, os controles criados diretamente processam conteúdo para pixels na tela. O WPF mantém dois grafos de objeto (_árvores_) para representar isso – a _árvore lógica_ representa os controles conforme definidos no código ou XAML, e a _árvore visual_ representa a renderização real que ocorre na tela que é executada diretamente pelo elemento visual (por meio de um método de desenho virtual) ou por meio de um `ControlTemplate` XAML definido que pode ser substituído ou personalizado. Normalmente, a árvore visual é mais complexa, pois inclui itens como bordas em torno de controles, rótulos para conteúdo implícito, etc. O WPF inclui um conjunto de APIs`LogicalTreeHelper` ( `VisualTreeHelper`e) para examinar esses dois gráficos de objeto.

No Xamarin. Forms, os controles que você define em `Page` um são, na verdade, apenas objetos de dados simples. Eles são semelhantes à representação de árvore lógica, mas nunca processam o conteúdo por conta própria. Em vez disso, eles são o _modelo de dados_ que influencia a renderização de elementos. A renderização real é feita por um [conjunto separado de _renderizadores visuais_ que são mapeados para cada tipo de controle](~/xamarin-forms/app-fundamentals/custom-renderer/index.md). Esses renderizadores são registrados em cada um dos projetos específicos da plataforma por assemblies Xamarin. Forms específicos da plataforma. Você pode ver uma lista [aqui](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md). Além de substituir ou estender o renderizador, o Xamarin. Forms também tem suporte para [efeitos](~/xamarin-forms/app-fundamentals/effects/index.md) que podem ser usados para influenciar a renderização nativa em uma base por plataforma.

#### <a name="the-logicalvisual-tree"></a>A árvore lógica/Visual

Não há API exposta para percorrer a árvore lógica no Xamarin. Forms-mas você pode usar a reflexão para obter as mesmas informações. Por exemplo, [aqui está um método que pode enumerar filhos lógicos](https://github.com/xamarinhq/xamu-infrastructure/blob/master/src/XamU.Infrastructure/Extensions/ElementExtensions.cs#L108) com reflexão.

## <a name="graphics"></a>Gráficos

O Xamarin. Forms não inclui um sistema gráfico para primitivos além de um retângulo simples`BoxView`(). Você pode incluir bibliotecas de terceiros, como [SkiaSharp](~/graphics-games/skiasharp/index.md) , para obter desenho 2D de plataforma cruzada ou [UrhoSharp](~/graphics-games/urhosharp/index.md) para 3D.

## <a name="resources"></a>Recursos

O WPF e o Xamarin. Forms têm o conceito de recursos e dicionários de recursos. Você pode inserir qualquer tipo de objeto em `ResourceDictionary` um com uma chave e, em seguida, `{StaticResource}` procurar por coisas que não serão alteradas `{DynamicResource}` ou para coisas que podem ser alteradas no dicionário em tempo de execução. O uso e a mecânica são os mesmos com uma diferença: O Xamarin. Forms exige que você defina `ResourceDictionary` o para atribuir à `Resources` Propriedade, ao passo que o WPF cria um e o atribui para você.

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

Se você não definir o `ResourceDictionary`, um erro de tempo de execução será gerado.

## <a name="styles"></a>Estilos

Os estilos também têm suporte total no Xamarin. Forms e podem ser usados para aplicar temas aos elementos Xamarin. Forms que compõem a interface do usuário. Eles dão suporte a gatilhos (Propriedade, evento e dados) `BasedOn`, herança por meio do e pesquisas de recursos para valores. Os estilos são aplicados aos elementos explicitamente por meio `Style` da propriedade ou implicitamente não fornecendo uma chave de recurso, assim como o WPF.

### <a name="device-styles"></a>Estilos de dispositivo

O WPF tem um conjunto de propriedades predefinidas (armazenadas como valores estáticos em um conjunto de `SystemColors`classes estáticas, como) que ditam cores do sistema, fontes e métricas na forma de valores e chaves de recurso. O Xamarin. Forms é semelhante, mas define um conjunto de [estilos de dispositivo](~/xamarin-forms/user-interface/styles/device.md) para representar as mesmas coisas. Esses estilos são fornecidos pela estrutura e definidos para valores com base no ambiente de tempo de execução (por exemplo, acessibilidade).

**WPF**

```xaml
<Label Text="Title" Foreground="{DynamicResource {x:Static SystemColors.DesktopBrushKey}}" />
```

**Xamarin.Forms**

```xaml
<Label Text="Title" Style="{DynamicResource TitleStyle}" />
```
