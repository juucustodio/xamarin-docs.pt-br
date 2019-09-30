---
title: Parte 1. Introdução ao XAML
description: Em um aplicativo xamarin. Forms, o XAML é mais usado para definir o conteúdo visual de uma página e trabalha em conjunto com um arquivo code-behind.
ms.prod: xamarin
ms.assetid: 9073FA0E-BD5A-4492-8A93-54C466F6EDB9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/30/2019
ms.openlocfilehash: a35a3750957073168bca4903e1cef79ed53a3de9
ms.sourcegitcommit: bb6a103de620386187a08151a4cdc29a4dc53a10
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679971"
---
# <a name="part-1-getting-started-with-xaml"></a>Parte 1. Introdução ao XAML

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_Em um aplicativo xamarin. Forms, o XAML é usado principalmente para definir o conteúdo visual de uma página e funciona em conjunto com um C# arquivo code-behind._

O arquivo code-behind fornece suporte de código para a marcação. Juntos, esses dois arquivos contribuem para uma nova definição de classe que inclui modos de exibição filho e inicialização de propriedade. Dentro do arquivo XAML, classes e propriedades são referenciadas com atributos e elementos XML e links entre a marcação e código são estabelecidas.

## <a name="creating-the-solution"></a>Criando a solução

Para começar a editar seu primeiro arquivo XAML, use o Visual Studio ou Visual Studio para Mac para criar uma nova solução xamarin. Forms. (Selecione a guia abaixo correspondentes ao seu ambiente.)

<!-- markdownlint-disable MD001 -->

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

No Windows, inicie o Visual Studio 2019 e, na janela iniciar, clique em **criar um novo projeto** para criar um novo projeto:

![Nova janela de solução](get-started-with-xaml-images/win/new-solution-2019.png)

Na janela **criar um novo projeto** , selecione **móvel** na lista suspensa **tipo de projeto** , selecione o modelo **aplicativo móvel (Xamarin. Forms)** e clique no botão **Avançar** :

![Janela novo projeto](get-started-with-xaml-images/win/new-project-2019.png)

Na janela **configurar seu novo projeto** , defina o **nome do projeto** como **XamlSamples** (ou qualquer que seja sua preferência) e clique no botão **criar** .

Na caixa de diálogo **novo aplicativo de plataforma cruzada** , clique em **em branco**e clique no botão **OK** :

![Caixa de diálogo novo aplicativo](get-started-with-xaml-images/win/new-cross-platform-app.png)

Quatro projetos são criados na solução: o **XamlSamples** .NET Standard library, **XamlSamples.Android**, **XamlSamples.iOS**e a plataforma Universal do Windows solução, **XamlSamples.UWP**.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

No Visual Studio para Mac, selecione **arquivo > nova solução** no menu. No **novo projeto** caixa de diálogo, selecione **multiplataforma > aplicativo** à esquerda, e **aplicativo de formulários em branco** (*não* **aplicativo Forms** ) na lista de modelos:

![Caixa de diálogo novo projeto 1](get-started-with-xaml-images/mac/newprojectdialog1.png)

Pressione **próxima**.

Na próxima caixa de diálogo, dê ao projeto um nome de **XamlSamples** (ou tudo o que você prefere). Certifique-se de que o **Use .NET Standard** botão de opção está selecionado:

![Caixa de diálogo novo projeto 2](get-started-with-xaml-images/mac/newprojectdialog2.png)

Pressione **próxima**.

Na caixa de diálogo seguinte, você pode selecionar um local para o projeto:

![Caixa de diálogo novo projeto 3](get-started-with-xaml-images/mac/newprojectdialog3.png)

Pressione **criar**

Três projetos são criados na solução: o **XamlSamples** .NET Standard library, **XamlSamples.Android**, e **XamlSamples.iOS**.

-----

Depois de criar o **XamlSamples** solução, você talvez queira testar seu ambiente de desenvolvimento, selecionando os vários projetos de plataforma como o projeto de inicialização da solução e criar e implantar o aplicativo simples criado por o modelo de projeto em emuladores de telefone ou em dispositivos reais.

A menos que você precisa escrever código específico da plataforma, compartilhado **XamlSamples** projeto de biblioteca .NET Standard é onde você vai gastar praticamente todo seu tempo programação. Estes artigos não serão se aventurar fora desse projeto.

### <a name="anatomy-of-a-xaml-file"></a>Anatomia de um arquivo XAML

Dentro de **XamlSamples** biblioteca .NET Standard são um par de arquivos com os seguintes nomes:

- **App. XAML**, o arquivo XAML; e
- **App.XAML.CS**, um C# *de lógica* arquivo associado ao arquivo XAML.

Você precisará clicar na seta ao lado **App. XAML** para ver o arquivo code-behind.

Ambos **App. XAML** e **App.xaml.cs** contribuem para uma classe denominada `App` que deriva de `Application`. A maioria das outras classes com arquivos XAML contribuem para uma classe que deriva de `ContentPage`; esses arquivos usam XAML para definir o conteúdo visual de uma página inteira. Isso é verdadeiro para os outros dois arquivos na **XamlSamples** projeto:

- **MainPage. XAML**, o arquivo XAML; e
- **MainPage.xaml.cs**, o C# arquivo code-behind.

O **MainPage. XAML** arquivo tem esta aparência (embora a formatação pode ser um pouco diferente):

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples"
             x:Class="XamlSamples.MainPage">

    <StackLayout>
        <!-- Place new controls here -->
        <Label Text="Welcome to Xamarin Forms!"
               VerticalOptions="Center"
               HorizontalOptions="Center" />
    </StackLayout>

</ContentPage>
```

As duas declarações de namespace`xmlns`XML () referem-se a URIs, o primeiro aparentemente no site do Xamarin e o segundo na Microsoft. Não se preocupe em que ponto os URIs para a verificação. Não há nada. Eles são simplesmente URIs pertencentes a Xamarin e a Microsoft e eles basicamente funcionam como identificadores de versão.

A primeira declaração de namespace XML significa que marcas definidas dentro do arquivo XAML sem prefixo se referem às classes no xamarin. Forms, por exemplo `ContentPage`. A segunda declaração de namespace define um prefixo de `x`. Isso é usado para vários elementos e atributos que são intrínsecos para XAML em si e que são compatíveis com outras implementações de XAML. No entanto, esses elementos e atributos são ligeiramente diferentes dependendo do ano inserido no URI. Xamarin. Forms dá suporte à especificação do XAML 2009, mas nem tudo dela.

O `local` declaração de namespace permite que você acesse outras classes do projeto de biblioteca do .NET Standard.

No final da primeira marca, o `x` prefixo é usado para um atributo chamado `Class`. Porque o uso deste `x` prefixo é praticamente universal para o namespace XAML, atributos XAML, como `Class` quase sempre são chamados de `x:Class`.

O `x:Class` atributo especifica um nome de classe totalmente qualificado do .NET: o `MainPage` classe o `XamlSamples` namespace. Isso significa que esse arquivo XAML define uma nova classe chamada `MainPage` no `XamlSamples` namespace que deriva `ContentPage`— a marca na qual o `x:Class` atributo é exibido.

O `x:Class` atributo só pode aparecer no elemento raiz de um arquivo XAML para definir um derivada C# classe. Esta é a classe somente novo definido no arquivo XAML. Em vez disso, tudo o que aparece no arquivo XAML é instanciado a partir de classes existentes-las simplesmente e inicializado.

O **MainPage.xaml.cs** arquivo tem esta aparência (além de não utilizados `using` diretivas):

```csharp
using Xamarin.Forms;

namespace XamlSamples
{
    public partial class MainPage : ContentPage
    {
        public MainPage()
        {
            InitializeComponent();
        }
    }
}
```

O `MainPage` classe deriva `ContentPage`, mas observe o `partial` definição de classe. Isso sugere que deve haver outra definição de classe parcial para `MainPage`, mas onde ele está? E o que é isso `InitializeComponent` método?

Quando o Visual Studio compila o projeto, ele analisa o arquivo XAML para gerar um C# arquivo de código. Se você olhar o **XamlSamples\XamlSamples\obj\Debug** diretório, você encontrará um arquivo chamado **XamlSamples.MainPage.xaml.g.cs**. 'g' significa gerado. Isso é outra definição de classe parcial de `MainPage` que contém a definição do `InitializeComponent` método chamado do `MainPage` construtor. Esses dois parcial `MainPage` definições de classe podem ser compiladas juntos. Dependendo se o XAML é compilado ou não, o arquivo XAML ou um formato binário do arquivo XAML é incorporado no executável.

No tempo de execução de código nas chamadas de projeto de plataforma em particular uma `LoadApplication` método, passando a ele uma nova instância do `App` classe na biblioteca do .NET Standard. O `App` cria uma instância do construtor de classe `MainPage`. Chama o construtor da classe `InitializeComponent`, que chama o `LoadFromXaml` método que extrai o arquivo XAML (ou seu binário compilado) da biblioteca do .NET Standard. `LoadFromXaml` inicializa todos os objetos definidos no arquivo XAML, conecta tudo isso junto em relações pai-filho, anexa os manipuladores de eventos definidos no código aos eventos definido no arquivo XAML e define a árvore resultante de objetos, como o conteúdo da página.

Embora você normalmente não precisa gastar muito tempo com arquivos de código gerados, às vezes, tempo de execução exceções são geradas em código em arquivos gerados, portanto, você deve estar familiarizado com eles.

Quando você compilar e executar este programa, o `Label` elemento aparece no centro da página, como sugere o XAML:

[![Exibição padrão do Xamarin. Forms](get-started-with-xaml-images/xamlsamples.png)](get-started-with-xaml-images/xamlsamples-large.png#lightbox)

Para visuais mais interessantes, tudo o que você precisa é mais interessante de XAML.

## <a name="adding-new-xaml-pages"></a>Adição de novas páginas XAML

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Para adicionar outras classes `ContentPage` baseadas em XAML ao seu projeto, selecione o projeto de biblioteca **XamlSamples** .net Standard, clique com o botão direito do mouse e selecione **Adicionar > novo item...** . Na caixa de diálogo **Adicionar novo item** , **selecione C# itens visuais > Xamarin. Forms > página de conteúdo** (não a **página conteúdo (C#)** , que cria uma página somente código ou **exibição de conteúdo**, que não é uma página). Nomeie a página, por exemplo, **HelloXamlPage**:

![Caixa de diálogo Adicionar novo item](get-started-with-xaml-images/win/add-new-item-dialog-2019.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Para adicionar outros XAML baseado `ContentPage` classes ao seu projeto, selecione o **XamlSamples** biblioteca .NET Standard de projeto e invoque o **arquivo > novo arquivo** item de menu. Na parte esquerda dos **novo arquivo** caixa de diálogo, selecione **formulários** à esquerda, e **formulários ContentPage Xaml** (não **ContentPage do Forms**, que cria uma página somente de código, ou **exibição de conteúdo**, que não é uma página). Nomeie a página, por exemplo, **HelloXamlPage**:

![Caixa de diálogo novo arquivo](get-started-with-xaml-images/mac/newfiledialog.png)

-----

Dois arquivos são adicionados ao projeto, **HelloXamlPage.xaml** e o arquivo code-behind **HelloXamlPage.xaml.cs**.

## <a name="setting-page-content"></a>Conteúdo da página de configuração

Editar o **HelloXamlPage.xaml** arquivo para que as marcas somente são aqueles para `ContentPage` e `ContentPage.Content`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.HelloXamlPage">
    <ContentPage.Content>

    </ContentPage.Content>
</ContentPage>
```

O `ContentPage.Content` marcas fazem parte da sintaxe de XAML exclusivo. Primeiro, elas podem parecer ser um XML inválido, mas são válidas. O período não é um caractere especial em XML.

O `ContentPage.Content` marcas são chamadas *elemento de propriedade* marcas. `Content` é uma propriedade de `ContentPage`e geralmente é definido como um modo de exibição único ou um layout com modos de exibição filho. Normalmente propriedades tornam-se atributos em XAML, mas ele seria difícil de definir um `Content` de atributo para um objeto complexo. Por esse motivo, a propriedade é expressa como um elemento XML que consiste em nome de classe e o nome da propriedade separados por um período. Agora o `Content` propriedade pode ser definida entre o `ContentPage.Content` marcas, como este:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.HelloXamlPage"
             Title="Hello XAML Page">
    <ContentPage.Content>

        <Label Text="Hello, XAML!"
               VerticalOptions="Center"
               HorizontalTextAlignment="Center"
               Rotation="-15"
               IsVisible="true"
               FontSize="Large"
               FontAttributes="Bold"
               TextColor="Blue" />

    </ContentPage.Content>
</ContentPage>
```

Observe também que um `Title` atributo foi definido na marca raiz.

Neste momento, a relação entre classes, propriedades e XML deve ser evidente: Uma classe Xamarin. Forms ( `ContentPage` como ou `Label`) aparece no arquivo XAML como um elemento XML. Propriedades da classe — incluindo `Title` na `ContentPage` e sete propriedades de `Label`— normalmente são exibidos como atributos XML.

Existem muitos atalhos para definir os valores dessas propriedades. Algumas propriedades são tipos de dados básicos: Por exemplo, as `Title` propriedades `Text` e são do tipo `String`, `Rotation` é do tipo `Double`e `IsVisible` (que é `true` por padrão e é definido aqui somente para ilustração) é do tipo `Boolean`.

O `HorizontalTextAlignment` propriedade é do tipo `TextAlignment`, que é uma enumeração. Para uma propriedade de qualquer tipo de enumeração, tudo o que você precisa fornecer é um nome de membro.

Para propriedades de tipos mais complexos, no entanto, conversores são usados para analisar o XAML. Essas são classes no xamarin. Forms que derivam de `TypeConverter`. Muitos são classes públicas, mas alguns não são. Para esse arquivo XAML específico, várias dessas classes desempenham um papel nos bastidores:

- `LayoutOptionsConverter` para o `VerticalOptions` propriedade
- `FontSizeConverter` para o `FontSize` propriedade
- `ColorTypeConverter` para o `TextColor` propriedade

Esses conversores regem a sintaxe permitida das configurações de propriedade.

O `ThicknessTypeConverter` pode lidar com um, dois ou quatro números separados por vírgulas. Se um número for fornecido, ele se aplica a todos os quatro lados. Com dois números, a primeira é left e right padding e o segundo é a parte superior e inferior. Quatro números estão em ordem esquerda, superior, direita e inferior.

O `LayoutOptionsConverter` pode converter os nomes dos campos estáticos públicos do `LayoutOptions` estrutura para valores do tipo `LayoutOptions`.

O `FontSizeConverter` pode lidar com um `NamedSize` membro ou um tamanho de fonte numérica.

O `ColorTypeConverter` aceita os nomes dos campos estáticos públicos do `Color` estrutura ou valores RGB hexadecimais, com ou sem um canal alfa, precedido por um sinal numérico (#). Aqui está a sintaxe sem um canal alfa:

 `TextColor="#rrggbb"`

Cada uma das letras pouco é um dígito hexadecimal. Aqui está como um canal alfa está incluído:

 `TextColor="#aarrggbb">`

Para o canal alfa, tenha em mente que FF é totalmente opaca e 00 é totalmente transparente.

Dois outros formatos permitem que você especifique apenas um dígito hexadecimal para cada canal:

 `TextColor="#rgb"` `TextColor="#argb"`

Nesses casos, o dígito é repetido para formar o valor. Por exemplo, #CF3 é a cor RGB FF-CC-33.

## <a name="page-navigation"></a>Navegação de página

Quando você executa o **XamlSamples** programa, o `MainPage` é exibida. Para ver o novo `HelloXamlPage` você pode definir que, como a inicialização de novo, página em de **App.xaml.cs** do arquivo ou navegue para a nova página de `MainPage`.

Para implementar a navegação, primeiro altere o código na **App.xaml.cs** construtor para que um `NavigationPage` objeto é criado:

```csharp
public App()
{
    InitializeComponent();
    MainPage = new NavigationPage(new MainPage());
}
```

No **MainPage.xaml.cs** construtor, você pode criar um simples `Button` e use o manipulador de eventos para navegar até `HelloXamlPage`:

```csharp
public MainPage()
{
    InitializeComponent();

    Button button = new Button
    {
        Text = "Navigate!",
        HorizontalOptions = LayoutOptions.Center,
        VerticalOptions = LayoutOptions.Center
    };

    button.Clicked += async (sender, args) =>
    {
        await Navigation.PushAsync(new HelloXamlPage());
    };

    Content = button;
}
```

Definindo o `Content` propriedade da página substitui a configuração do `Content` propriedade no arquivo XAML. Quando você compila e implanta a nova versão deste programa, um botão aparece na tela. Pressioná-lo navega para `HelloXamlPage`. Aqui está a página resultante no iPhone, Android e UWP:

[![Texto do rótulo girado](get-started-with-xaml-images/helloxaml1.png)](get-started-with-xaml-images/helloxaml1-large.png#lightbox)

Você pode navegar de volta para `MainPage` usando o **< volta** botão no iOS, usando a seta para a esquerda na parte superior da página ou na parte inferior do telefone no Android, ou na seta à esquerda na parte superior da página no Windows 10.

Fique à vontade fazer experiências com o XAML para diferentes maneiras renderizar o `Label`. Se você precisar inserir quaisquer caracteres Unicode em texto, você pode usar a sintaxe XML padrão. Por exemplo, para colocar a saudação aspas inglesas, use:

 `<Label Text="&#x201C;Hello, XAML!&#x201D;" … />`

Aqui está o que se parece com:

[![Texto do rótulo girado com caracteres Unicode](get-started-with-xaml-images/helloxaml2.png)](get-started-with-xaml-images/helloxaml2-large.png#lightbox)

## <a name="xaml-and-code-interactions"></a>Interações de código e XAML

O **HelloXamlPage** exemplo contém um único `Label` na página, mas isso é muito incomum. A maioria dos `ContentPage` derivativos conjunto a `Content` classificar de propriedade para um layout de alguns, como um `StackLayout`. O `Children` propriedade do `StackLayout` é definido para ser do tipo `IList<View>` mas é realmente um objeto do tipo `ElementCollection<View>`, e que a coleção pode ser preenchida com várias exibições ou outros layouts. No XAML, essas relações pai-filho são estabelecidas com a hierarquia XML normal. Aqui está um arquivo XAML para uma nova página chamada **XamlPlusCodePage**:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.XamlPlusCodePage"
             Title="XAML + Code Page">
    <StackLayout>
        <Slider VerticalOptions="CenterAndExpand" />

        <Label Text="A simple Label"
               Font="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Button Text="Click Me!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

Esse arquivo XAML é sintaticamente completo, e aqui está o que se parece com:

[![Vários controles em uma página](get-started-with-xaml-images/xamlpluscode1.png)](get-started-with-xaml-images/xamlpluscode1-large.png#lightbox)

No entanto, você provavelmente considere este programa seja funcionalmente deficientes. Talvez o `Slider` deve fazer com que o `Label` para exibir o valor atual e o `Button` provavelmente se destina a fazer algo dentro do programa.

Como você verá no [parte 4. Noções básicas de vinculação de dados](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md), o trabalho de exibição de um `Slider` valor usando um `Label` possam ser manipulados inteiramente em XAML com uma associação de dados. Mas é útil ver a solução de código pela primeira vez. Mesmo assim, tratando o `Button` clique definitivamente requer código. Isso significa que o arquivo code-behind para `XamlPlusCodePage` deve conter os manipuladores para o `ValueChanged` eventos da `Slider` e o `Clicked` evento do `Button`. Vamos adicioná-los:

```csharp
namespace XamlSamples
{
    public partial class XamlPlusCodePage
    {
        public XamlPlusCodePage()
        {
            InitializeComponent();
        }

        void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
        {

        }

        void OnButtonClicked(object sender, EventArgs args)
        {

        }
    }
}
```

Esses manipuladores de eventos não é necessário para o público.

Novamente no arquivo XAML, o `Slider` e `Button` marcas precisam incluir os atributos para o `ValueChanged` e `Clicked` que fazem referência a esses manipuladores de eventos:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.XamlPlusCodePage"
             Title="XAML + Code Page">
    <StackLayout>
        <Slider VerticalOptions="CenterAndExpand"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="A simple Label"
               Font="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Button Text="Click Me!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                Clicked="OnButtonClicked" />
    </StackLayout>
</ContentPage>
```

Observe que a atribuição de um manipulador a um evento tem a mesma sintaxe atribuindo um valor a uma propriedade.

Se o manipulador para o `ValueChanged` eventos do `Slider` usará o `Label` para exibir o valor atual, o manipulador precisa fazer referência a esse objeto de código. O `Label` precisa de um nome, o que é especificado com o `x:Name` atributo.

```xaml
<Label x:Name="valueLabel"
       Text="A simple Label"
       Font="Large"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand" />
```

O `x` prefixo do `x:Name` atributo indica que esse atributo é intrínseco ao XAML.

O nome atribuído para o `x:Name` atributo tem as mesmas regras que C# nomes de variável. Por exemplo, ele deve começar com uma letra ou sublinhado e não conter espaços.

Agora o `ValueChanged` manipulador de eventos pode definir o `Label` para exibir a nova `Slider` valor. O novo valor está disponível de argumentos do evento:

```csharp
void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
{
    valueLabel.Text = args.NewValue.ToString("F3");
}
```

Ou, o manipulador foi possível obter o `Slider` objeto que está gerando esse evento do `sender` argumento e obter o `Value` propriedade de que:

```csharp
void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
{
    valueLabel.Text = ((Slider)sender).Value.ToString("F3");
}
```

Quando você executa o programa, o `Label` não exibe as `Slider` valor porque o `ValueChanged` evento ainda não foi acionado. Mas qualquer manipulação do `Slider` faz com que o valor a ser exibido:

[![Valor do controle deslizante exibido](get-started-with-xaml-images/xamlpluscode2.png)](get-started-with-xaml-images/xamlpluscode2-large.png#lightbox)

Agora, para o `Button`. Vamos simular uma resposta a um `Clicked` evento exibindo um alerta com o `Text` do botão. O manipulador de eventos pode seguramente os `sender` argumento para um `Button` e, em seguida, acessar suas propriedades:

```csharp
async void OnButtonClicked(object sender, EventArgs args)
{
    Button button = (Button)sender;
    await DisplayAlert("Clicked!",
        "The button labeled '" + button.Text + "' has been clicked",
        "OK");
}
```

O método é definido como `async` porque o `DisplayAlert` método é assíncrono e devem ser precedido com o `await` operador, que retorna quando o método for concluído. Porque esse método obtém o `Button` acionar o evento a partir de `sender` argumento, o mesmo manipulador pode ser usado para vários botões.

Você já viu que um objeto definido em XAML pode acionar um evento que é processado no arquivo code-behind, e que o arquivo code-behind pode acessar um objeto definido em XAML usando o nome atribuído a ele com o `x:Name` atributo. Essas são as duas maneiras fundamentais que interagem de código e XAML.

Algumas informações adicionais em como funciona o XAML pode ser obtida, examinando o recém-gerado **arquivo XamlPlusCode.xaml.g.cs**, que agora inclui qualquer nome atribuído a qualquer `x:Name` atributo como um campo particular. Aqui está uma versão simplificada do arquivo:

```csharp
public partial class XamlPlusCodePage : ContentPage {

    private Label valueLabel;

    private void InitializeComponent() {
        this.LoadFromXaml(typeof(XamlPlusCodePage));
        valueLabel = this.FindByName<Label>("valueLabel");
    }
}
```

A declaração desse campo permite que a variável para ser usados livremente em qualquer lugar dentro do `XamlPlusCodePage` arquivo de classe parcial em sua jurisdição. Em tempo de execução, o campo é atribuído depois que o XAML foi analisado. Isso significa que o `valueLabel` campo é `null` quando o `XamlPlusCodePage` construtor começa válido, mas depois `InitializeComponent` é chamado.

Depois de `InitializeComponent` retorna controle volta para o construtor, os elementos visuais da página foram construídos como se eles tinham instanciados e inicializados no código. O arquivo XAML não desempenha qualquer função na classe. Você pode manipular esses objetos na página de qualquer forma que desejar, por exemplo, adicionando modos de exibição para o `StackLayout`, configuração ou o `Content` propriedade da página para algo mais inteiramente. Você pode "percorrer a árvore" Examinando o `Content` propriedade da página e os itens a `Children` coleções de layouts. Você pode definir propriedades em modos de exibição acessados dessa maneira, ou atribuir manipuladores de eventos a elas dinamicamente.

Fique à vontade. É sua página e o XAML é apenas uma ferramenta para criar seu conteúdo.

## <a name="summary"></a>Resumo

Com essa introdução, você viu como um arquivo XAML e o arquivo de código contribuem para uma definição de classe, e como os arquivos XAML e código interagem. Mas XAML também tem seus próprios recursos exclusivos de sintáticos que permitem que ele seja usado de maneira muito flexível. Você pode começar a explorar em [parte 2. Sintaxe XAML essencial](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md).

## <a name="related-links"></a>Links relacionados

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [Parte 2. Sintaxe essencial de XAML](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Parte 3. Extensões de Marcação XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Parte 4. Conceitos básicos da associação de dados](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [Parte 5. De associação de dados a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
