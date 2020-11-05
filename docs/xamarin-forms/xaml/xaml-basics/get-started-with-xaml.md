---
title: Parte 1. Introdução ao XAML
description: Em um Xamarin.Forms aplicativo, o XAML é usado principalmente para definir o conteúdo visual de uma página e funciona junto com um arquivo code-behind.
ms.prod: xamarin
ms.assetid: 9073FA0E-BD5A-4492-8A93-54C466F6EDB9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/30/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8992f2a649b1d3998a24a11cdf7e51d768a3cd09
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93371333"
---
# <a name="part-1-getting-started-with-xaml"></a>Parte 1. Introdução ao XAML

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/xamlsamples)

_Em um Xamarin.Forms aplicativo, o XAML é usado principalmente para definir o conteúdo visual de uma página e funciona junto com um arquivo code-behind em C#._

O arquivo code-behind fornece suporte de código para a marcação. Juntos, esses dois arquivos contribuem para uma nova definição de classe que inclui exibições filhas e inicialização de propriedade. Dentro do arquivo XAML, classes e propriedades são referenciadas com elementos e atributos XML, e os links entre a marcação e o código são estabelecidos.

## <a name="creating-the-solution"></a>Criando a solução

Para começar a editar seu primeiro arquivo XAML, use o Visual Studio ou Visual Studio para Mac para criar uma nova Xamarin.Forms solução. (Selecione a guia abaixo correspondente ao seu ambiente.)

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

No Windows, inicie o Visual Studio 2019 e, na janela iniciar, clique em **criar um novo projeto** para criar um novo projeto:

![Nova janela de solução](get-started-with-xaml-images/win/new-solution-2019.png)

Na janela **criar um novo projeto** , selecione **móvel** na lista suspensa **tipo de projeto** , selecione o modelo **aplicativo móvel ( Xamarin.Forms )** e clique no botão **Avançar** :

![Janela novo projeto](get-started-with-xaml-images/win/new-project-2019.png)

Na janela **configurar seu novo projeto** , defina o **nome do projeto** como **XamlSamples** (ou qualquer que seja sua preferência) e clique no botão **criar** .

Na caixa de diálogo **novo aplicativo de plataforma cruzada** , clique em **em branco** e clique no botão **OK** :

![Caixa de diálogo novo aplicativo](get-started-with-xaml-images/win/new-cross-platform-app.png)

Quatro projetos são criados na solução: o **XamlSamples** .net standard library, **XamlSamples. Android** , **XamlSamples. Ios** e a solução plataforma universal do Windows, **XamlSamples. UWP**.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Em Visual Studio para Mac, selecione **arquivo > nova solução** no menu. Na caixa de diálogo **novo projeto** , selecione **multiplataforma > aplicativo** à esquerda e **aplicativo de formulários em branco** ( *não* **aplicativo de formulários** ) na lista modelo:

![Caixa de diálogo novo projeto 1](get-started-with-xaml-images/mac/newprojectdialog1.png)

Pressione **Avançar**.

Na próxima caixa de diálogo, dê ao projeto um nome de **XamlSamples** (ou o que preferir). Verifique se o botão de opção **usar .net Standard** está selecionado:

![Caixa de diálogo novo projeto 2](get-started-with-xaml-images/mac/newprojectdialog2.png)

Pressione **Avançar**.

Na caixa de diálogo a seguir, você pode selecionar um local para o projeto:

![Caixa de diálogo novo projeto 3](get-started-with-xaml-images/mac/newprojectdialog3.png)

Pressione **criar**

Três projetos são criados na solução: o **XamlSamples** .net standard library, **XamlSamples. Android** e **XamlSamples. Ios**.

-----

Depois de criar a solução **XamlSamples** , talvez você queira testar seu ambiente de desenvolvimento selecionando os vários projetos da plataforma como o projeto de inicialização da solução e criando e implantando o aplicativo simples criado pelo modelo de projeto em um dos emuladores de telefone ou em dispositivos reais.

A menos que você precise escrever código específico da plataforma, o projeto de biblioteca de .NET Standard compartilhado **XamlSamples** é onde você gastará praticamente todo o seu tempo de programação. Esses artigos não se aventurarão fora desse projeto.

### <a name="anatomy-of-a-xaml-file"></a>Anatomia de um arquivo XAML

Na biblioteca de .NET Standard **XamlSamples** , há um par de arquivos com os seguintes nomes:

- **App. XAML** , o arquivo XAML; e
- **App.XAML.cs** , um arquivo *code-behind em* C# associado ao arquivo XAML.

Você precisará clicar na seta ao lado de **app. XAML** para ver o arquivo code-behind.

Tanto o **app. XAML** quanto o **app.XAML.cs** contribuem para uma classe chamada `App` derivada de `Application` . A maioria das outras classes com arquivos XAML contribuem para uma classe derivada de `ContentPage` ; esses arquivos usam XAML para definir o conteúdo visual de uma página inteira. Isso é verdadeiro nos outros dois arquivos no projeto **XamlSamples** :

- **MainPage. XAML** , o arquivo XAML; e
- **MainPage.XAML.cs** , o arquivo code-behind do C#.

O arquivo **MainPage. XAML** tem esta aparência (embora a formatação possa ser um pouco diferente):

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

As duas declarações de namespace XML ( `xmlns` ) referem-se a URIs, o primeiro aparentemente no site do Xamarin e o segundo na Microsoft. Não se preocupe em verificar o que esses URIs apontam. Não há nada. Eles são simplesmente URIs de Propriedade do Xamarin e da Microsoft e, basicamente, funcionam como identificadores de versão.

A primeira declaração de namespace de XML significa que as marcas definidas no arquivo XAML sem nenhum prefixo se referem a classes em Xamarin.Forms , por exemplo `ContentPage` . A segunda declaração de namespace define um prefixo de `x` . Isso é usado para vários elementos e atributos que são intrínsecos ao próprio XAML e que têm suporte de outras implementações de XAML. No entanto, esses elementos e atributos são ligeiramente diferentes dependendo do ano inserido no URI. Xamarin.Forms dá suporte à especificação XAML 2009, mas não a todos.

A `local` declaração de namespace permite que você acesse outras classes do projeto de biblioteca .net Standard.

No final dessa primeira marca, o `x` prefixo é usado para um atributo chamado `Class` . Como o uso desse `x` prefixo é praticamente universal para o namespace XAML, os atributos XAML, como `Class` são quase sempre chamados de `x:Class` .

O `x:Class` atributo especifica um nome de classe .NET totalmente qualificado: a `MainPage` classe no `XamlSamples` namespace. Isso significa que esse arquivo XAML define uma nova classe chamada `MainPage` no `XamlSamples` namespace que deriva de `ContentPage` — a marca na qual o `x:Class` atributo é exibido.

O `x:Class` atributo só pode aparecer no elemento raiz de um arquivo XAML para definir uma classe C# derivada. Essa é a única nova classe definida no arquivo XAML. Em vez disso, todo o resto que aparece no arquivo XAML é simplesmente instanciado de classes existentes e inicializado.

O arquivo **MainPage.XAML.cs** é semelhante a este (exceto pelas diretivas não utilizadas `using` ):

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

A `MainPage` classe deriva de `ContentPage` , mas observe a `partial` definição de classe. Isso sugere que deve haver outra definição de classe parcial para `MainPage` , mas onde é? E o que é esse `InitializeComponent` método?

Quando o Visual Studio cria o projeto, ele analisa o arquivo XAML para gerar um arquivo de código C#. Se você olhar no diretório **XamlSamples\XamlSamples\obj\Debug** , encontrará um arquivo chamado **XamlSamples.MainPage.XAML.g.cs**. O ' g ' significa gerado. Essa é a outra definição de classe parcial de `MainPage` que contém a definição do `InitializeComponent` método chamado a partir do `MainPage` Construtor. Essas duas `MainPage` definições de classe parcial podem então ser compiladas juntas. Dependendo se o XAML é compilado ou não, o arquivo XAML ou um formato binário do arquivo XAML é inserido no executável.

Em tempo de execução, o código no projeto de plataforma específico chama um `LoadApplication` método, passando a ele uma nova instância da `App` classe na biblioteca de .net Standard. O `App` Construtor de classe instancia `MainPage` . O construtor dessa classe chama `InitializeComponent` , que então chama o `LoadFromXaml` método que extrai o arquivo XAML (ou seu binário compilado) da biblioteca de .net Standard. `LoadFromXaml` Inicializa todos os objetos definidos no arquivo XAML, conecta-os juntos em relações pai-filho, anexa manipuladores de eventos definidos no código a eventos definidos no arquivo XAML e define a árvore resultante de objetos como o conteúdo da página.

Embora você normalmente não precise gastar muito tempo com arquivos de código gerados, às vezes as exceções de tempo de execução são geradas no código nos arquivos gerados, portanto, você deve estar familiarizado com eles.

Quando você compila e executa esse programa, o `Label` elemento é exibido no centro da página como sugere o XAML:

[![Padrão::: não-Loc (Xamarin. Forms)::: Display](get-started-with-xaml-images/xamlsamples.png)](get-started-with-xaml-images/xamlsamples-large.png#lightbox)

Para visuais mais interessantes, tudo o que você precisa é de um XAML mais interessante.

## <a name="adding-new-xaml-pages"></a>Adicionando novas páginas XAML

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Para adicionar outras classes baseadas em XAML `ContentPage` ao seu projeto, selecione o projeto de biblioteca **XamlSamples** .net Standard, clique com o botão direito do mouse e selecione **Adicionar > novo item...**. Na caixa de diálogo **Adicionar novo item** , selecione **itens do Visual C# > Xamarin.Forms > página conteúdo** (não a **página de conteúdo (C#)** , que cria uma página somente código ou exibição de **conteúdo** , que não é uma página). Dê um nome à página, por exemplo, **HelloXamlPage** :

![Caixa de diálogo Adicionar novo item](get-started-with-xaml-images/win/add-new-item-dialog-2019.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Para adicionar outras classes baseadas em XAML `ContentPage` ao seu projeto, selecione o projeto de biblioteca **XamlSamples** .net Standard e invoque o arquivo > item de menu **novo arquivo** . À esquerda da caixa de diálogo **novo arquivo** , selecione **formulários** à esquerda e forma de **XAML de Contentpage** (não **forma Contentpage** , que cria uma página somente código ou exibição de **conteúdo** , que não é uma página). Dê um nome à página, por exemplo, **HelloXamlPage** :

![Caixa de diálogo novo arquivo](get-started-with-xaml-images/mac/newfiledialog.png)

-----

Dois arquivos são adicionados ao projeto, **HelloXamlPage. XAML** e o arquivo code-behind **HelloXamlPage.XAML.cs**.

## <a name="setting-page-content"></a>Configurando conteúdo da página

Edite o arquivo **HelloXamlPage. XAML** para que as únicas marcas sejam as `ContentPage` para `ContentPage.Content` e:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.HelloXamlPage">
    <ContentPage.Content>

    </ContentPage.Content>
</ContentPage>
```

As `ContentPage.Content` marcas fazem parte da sintaxe exclusiva do XAML. A princípio, eles podem parecer XML inválidos, mas são legais. O período não é um caractere especial em XML.

As `ContentPage.Content` marcas são chamadas de marcas de *elemento de propriedade* . `Content` é uma propriedade de `ContentPage` e é geralmente definida como uma exibição única ou um layout com exibições filho. Normalmente, as propriedades se tornam atributos em XAML, mas seria difícil definir um `Content` atributo como um objeto complexo. Por esse motivo, a propriedade é expressa como um elemento XML que consiste no nome da classe e no nome da propriedade separados por um ponto. Agora `Content` , a propriedade pode ser definida entre as `ContentPage.Content` marcas, desta forma:

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

Neste momento, a relação entre classes, propriedades e XML deve ser evidente: uma Xamarin.Forms classe (como `ContentPage` ou `Label` ) aparece no arquivo XAML como um elemento XML. As propriedades dessa classe, incluindo `Title` `ContentPage` as sete Propriedades de `Label` , geralmente aparecem como atributos XML.

Existem muitos atalhos para definir os valores dessas propriedades. Algumas propriedades são tipos de dados básicos: por exemplo, `Title` as `Text` Propriedades e são do tipo `String` , `Rotation` é do tipo `Double` , e `IsVisible` (que é `true` por padrão e é definido aqui somente para ilustração) é do tipo `Boolean` .

A `HorizontalTextAlignment` propriedade é do tipo `TextAlignment` , que é uma enumeração. Para uma propriedade de qualquer tipo de enumeração, tudo o que você precisa fornecer é um nome de membro.

No entanto, para propriedades de tipos mais complexos, os conversores são usados para analisar o XAML. Essas são classes Xamarin.Forms que derivam de `TypeConverter` . Muitas são classes públicas, mas algumas não são. Para esse arquivo XAML específico, várias dessas classes desempenham uma função em segundo plano:

- `LayoutOptionsConverter` para a  `VerticalOptions` Propriedade
- `FontSizeConverter` para a  `FontSize` Propriedade
- `ColorTypeConverter` para a  `TextColor` Propriedade

Esses conversores regem a sintaxe permitida das configurações de propriedade.

O `ThicknessTypeConverter` pode manipular um, dois ou quatro números separados por vírgulas. Se um número for fornecido, ele se aplicará a todos os quatro lados. Com dois números, o primeiro é o preenchimento esquerdo e direito e o segundo é superior e inferior. Quatro números estão na ordem esquerda, superior, direita e inferior.

O `LayoutOptionsConverter` pode converter os nomes dos campos públicos estáticos da `LayoutOptions` estrutura em valores do tipo `LayoutOptions` .

O `FontSizeConverter` pode manipular um `NamedSize` membro ou um tamanho de fonte numérico.

O `ColorTypeConverter` aceita os nomes de campos públicos estáticos da `Color` estrutura ou valores RGB hexadecimais, com ou sem um canal alfa, precedido por um sinal numérico (#). Aqui está a sintaxe sem um canal alfa:

 `TextColor="#rrggbb"`

Cada uma das pequenas letras é um dígito hexadecimal. Veja como um canal alfa é incluído:

 `TextColor="#aarrggbb">`

Para o canal alfa, tenha em mente que FF é totalmente opaco e 00 é totalmente transparente.

Dois outros formatos permitem que você especifique apenas um único dígito hexadecimal para cada canal:

 `TextColor="#rgb"` `TextColor="#argb"`

Nesses casos, o dígito é repetido para formar o valor. Por exemplo, #CF3 é a cor RGB CC-FF-33.

## <a name="page-navigation"></a>Navegação de página

Quando você executa o programa **XamlSamples** , o `MainPage` é exibido. Para ver o novo `HelloXamlPage` , você pode defini-lo como a nova página de inicialização no arquivo **app.XAML.cs** ou navegar até a nova página de `MainPage` .

Para implementar a navegação, primeiro altere o código no construtor **app.XAML.cs** para que um `NavigationPage` objeto seja criado:

```csharp
public App()
{
    InitializeComponent();
    MainPage = new NavigationPage(new MainPage());
}
```

No construtor **MainPage.XAML.cs** , você pode criar um simples `Button` e usar o manipulador de eventos para navegar até `HelloXamlPage` :

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

Definir a `Content` propriedade da página substitui a configuração da `Content` propriedade no arquivo XAML. Quando você compila e implanta a nova versão deste programa, um botão é exibido na tela. Pressioná-lo navega para `HelloXamlPage` . Esta é a página resultante no iPhone, Android e UWP:

[![Texto do rótulo girado](get-started-with-xaml-images/helloxaml1.png)](get-started-with-xaml-images/helloxaml1-large.png#lightbox)

Você pode navegar de volta para `MainPage` usar o botão de **voltar<** no Ios, usando a seta para a esquerda na parte superior da página ou na parte inferior do telefone no Android, ou usando a seta para a esquerda na parte superior da página no Windows 10.

Sinta-se à vontade para experimentar o XAML para diferentes maneiras de renderizar o `Label` . Se você precisar inserir caracteres Unicode no texto, poderá usar a sintaxe XML padrão. Por exemplo, para colocar a saudação entre aspas inteligentes, use:

 `<Label Text="&#x201C;Hello, XAML!&#x201D;" … />`

A aparência é a seguinte:

[![Texto do rótulo girado com caracteres Unicode](get-started-with-xaml-images/helloxaml2.png)](get-started-with-xaml-images/helloxaml2-large.png#lightbox)

## <a name="xaml-and-code-interactions"></a>XAML e interações de código

O exemplo **HelloXamlPage** contém apenas um único `Label` na página, mas isso é muito incomum. A maioria dos `ContentPage` derivativos define a `Content` propriedade como um layout de algum tipo, como um `StackLayout` . A `Children` propriedade de `StackLayout` é definida como sendo do tipo, `IList<View>` mas é, na verdade, um objeto do tipo `ElementCollection<View>` , e essa coleção pode ser preenchida com várias exibições ou outros layouts. Em XAML, essas relações pai-filho são estabelecidas com a hierarquia XML normal. Aqui está um arquivo XAML para uma nova página chamada **XamlPlusCodePage** :

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

Este arquivo XAML está sintaticamente concluído e aqui está o que ele parece:

[![Vários controles em uma página](get-started-with-xaml-images/xamlpluscode1.png)](get-started-with-xaml-images/xamlpluscode1-large.png#lightbox)

No entanto, é provável que você considere esse programa para ser funcionalmente deficientes. Talvez o `Slider` deva fazer com que o `Label` exiba o valor atual, e o `Button` provavelmente se destina a fazer algo dentro do programa.

Como você verá na [parte 4. Noções básicas de ligação de dados](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md), o trabalho de exibir um `Slider` valor usando um `Label` pode ser manipulado inteiramente em XAML com uma associação de dados. Mas é útil ver a solução de código primeiro. Mesmo assim, manipular o `Button` clique definitivamente requer código. Isso significa que o arquivo code-behind para `XamlPlusCodePage` deve conter manipuladores para o `ValueChanged` evento do `Slider` e o `Clicked` evento do `Button` . Vamos adicioná-los:

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

Esses manipuladores de eventos não precisam ser públicos.

De volta ao arquivo XAML, as `Slider` `Button` marcas e precisam incluir atributos para os `ValueChanged` eventos e `Clicked` que fazem referência a esses manipuladores:

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

Observe que a atribuição de um manipulador a um evento tem a mesma sintaxe que atribuir um valor a uma propriedade.

Se o manipulador para o `ValueChanged` evento do `Slider` estiver usando o `Label` para exibir o valor atual, o manipulador precisará fazer referência a esse objeto a partir do código. O `Label` precisa de um nome, que é especificado com o `x:Name` atributo.

```xaml
<Label x:Name="valueLabel"
       Text="A simple Label"
       Font="Large"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand" />
```

O `x` prefixo do `x:Name` atributo indica que esse atributo é INTRÍNSECO ao XAML.

O nome que você atribui ao `x:Name` atributo tem as mesmas regras que os nomes de variável do C#. Por exemplo, ele deve começar com uma letra ou sublinhado e não conter espaços inseridos.

Agora o `ValueChanged` manipulador de eventos pode definir o `Label` para exibir o novo `Slider` valor. O novo valor está disponível nos argumentos do evento:

```csharp
void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
{
    valueLabel.Text = args.NewValue.ToString("F3");
}
```

Ou, o manipulador pode obter o `Slider` objeto que está gerando esse evento a partir do `sender` argumento e obter a `Value` propriedade desse:

```csharp
void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
{
    valueLabel.Text = ((Slider)sender).Value.ToString("F3");
}
```

Quando você executa o programa pela primeira vez, o `Label` não exibe o `Slider` valor porque o `ValueChanged` evento ainda não foi acionado. Mas qualquer manipulação do `Slider` faz com que o valor seja exibido:

[![Valor do controle deslizante exibido](get-started-with-xaml-images/xamlpluscode2.png)](get-started-with-xaml-images/xamlpluscode2-large.png#lightbox)

Agora para o `Button` . Vamos simular uma resposta a um `Clicked` evento exibindo um alerta com o `Text` do botão. O manipulador de eventos pode converter com segurança o `sender` argumento para um `Button` e, em seguida, acessar suas propriedades:

```csharp
async void OnButtonClicked(object sender, EventArgs args)
{
    Button button = (Button)sender;
    await DisplayAlert("Clicked!",
        "The button labeled '" + button.Text + "' has been clicked",
        "OK");
}
```

O método é definido como `async` porque o `DisplayAlert` método é assíncrono e deve ser precedido com o `await` operador, que retorna quando o método é concluído. Como esse método obtém o `Button` acionamento do evento do `sender` argumento, o mesmo manipulador poderia ser usado para vários botões.

Você viu que um objeto definido em XAML pode acionar um evento que é manipulado no arquivo code-behind e que o arquivo code-behind pode acessar um objeto definido em XAML usando o nome atribuído a ele com o `x:Name` atributo. Essas são as duas maneiras fundamentais pelas quais o código e o XAML interagem.

Algumas informações adicionais sobre como o XAML funciona podem ser obtidas examinando o **arquivo XamlPlusCode.XAML.g.cs** recém-gerado, que agora inclui qualquer nome atribuído a qualquer `x:Name` atributo como um campo privado. Veja uma versão simplificada desse arquivo:

```csharp
public partial class XamlPlusCodePage : ContentPage {

    private Label valueLabel;

    private void InitializeComponent() {
        this.LoadFromXaml(typeof(XamlPlusCodePage));
        valueLabel = this.FindByName<Label>("valueLabel");
    }
}
```

A declaração desse campo permite que a variável seja usada livremente em qualquer lugar dentro do `XamlPlusCodePage` arquivo de classe parcial em sua jurisdição. Em tempo de execução, o campo é atribuído depois que o XAML é analisado. Isso significa que o `valueLabel` campo é `null` quando o `XamlPlusCodePage` Construtor começa, mas válido após `InitializeComponent` é chamado.

Depois de `InitializeComponent` retornar o controle ao construtor, os elementos visuais da página foram construídos como se tivessem sido instanciados e inicializados no código. O arquivo XAML não desempenha mais nenhuma função na classe. Você pode manipular esses objetos na página de qualquer forma que desejar, por exemplo, adicionando exibições ao `StackLayout` ou definindo a `Content` propriedade da página para algo totalmente diferente. Você pode "percorrer a árvore" examinando a `Content` propriedade da página e os itens nas `Children` coleções de layouts. Você pode definir propriedades em modos de exibição acessados dessa forma ou atribuir manipuladores de eventos a eles dinamicamente.

Fique à vontade. É sua página, e o XAML é apenas uma ferramenta para criar seu conteúdo.

## <a name="summary"></a>Resumo

Com essa introdução, você viu como um arquivo XAML e um arquivo de código contribuem para uma definição de classe e como os arquivos XAML e de código interagem. Mas o XAML também tem seus próprios recursos sintáticos exclusivos que permitem que ele seja usado de maneira muito flexível. Você pode começar a explorá-los na [parte 2. Sintaxe XAML essencial](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md).

## <a name="related-links"></a>Links Relacionados

- [XamlSamples](/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [Parte 2. Sintaxe XAML essencial](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Parte 3. Extensões de marcação XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Parte 4. Noções básicas de ligação de dados](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [Parte 5. Da vinculação de dados com o MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)