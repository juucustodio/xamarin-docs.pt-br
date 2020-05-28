---
Título: " Xamarin.Forms pesquisa do Shell" Descrição: " Xamarin.Forms os aplicativos do Shell podem usar a funcionalidade de pesquisa integrada que é fornecida por uma caixa de pesquisa que pode ser adicionada à parte superior de cada página."
MS. Prod: MS. AssetID: MS. Technology: autor: MS. Author: MS. Date: no-loc:
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

---

# <a name="xamarinforms-shell-search"></a>Xamarin.FormsPesquisa de Shell

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

Xamarin.FormsO Shell inclui a funcionalidade de pesquisa integrada que é fornecida pela `SearchHandler` classe. A funcionalidade de pesquisa pode ser adicionada a uma página definindo a propriedade anexada `Shell.SearchHandler` como um objeto `SearchHandler` da subclasse. Isso faz com que uma caixa de pesquisa seja adicionada na parte superior da página:

[![Captura de tela de um shell SearchHandler, no iOS e no Android](search-images/searchhandler.png "SearchHandler do Shell")](search-images/searchhandler-large.png#lightbox "SearchHandler do Shell")

Quando uma consulta é inserida na caixa de pesquisa, a propriedade `Query` é atualizada e, em cada atualização, o método `OnQueryChanged` é executado. Esse método pode ser substituído para preencher a área de sugestões de pesquisa com dados:

[![Captura de tela de resultados de uma pesquisa em um shell SearchHandler, no iOS e no Android](search-images/search-suggestions.png "Resultados da pesquisa do Shell SearchHandler")](search-images/search-suggestions-large.png#lightbox "Resultados da pesquisa do Shell SearchHandler")

Em seguida, quando um resultado é selecionado na área de sugestões de pesquisa, o método `OnItemSelected` é executado. Esse método pode ser substituído para responder apropriadamente, por exemplo, navegando para uma página de detalhes.

## <a name="create-a-searchhandler"></a>Criar um SearchHandler

A funcionalidade de pesquisa pode ser adicionada a um aplicativo Shell criando subclasses da classe `SearchHandler` e substituindo os métodos `OnQueryChanged` e `OnItemSelected`:

```csharp
public class MonkeySearchHandler : SearchHandler
{
    protected override void OnQueryChanged(string oldValue, string newValue)
    {
        base.OnQueryChanged(oldValue, newValue);

        if (string.IsNullOrWhiteSpace(newValue))
        {
            ItemsSource = null;
        }
        else
        {
            ItemsSource = MonkeyData.Monkeys
                .Where(monkey => monkey.Name.ToLower().Contains(newValue.ToLower()))
                .ToList<Animal>();
        }
    }

    protected override async void OnItemSelected(object item)
    {
        base.OnItemSelected(item);

        // Note: strings will be URL encoded for navigation (e.g. "Blue Monkey" becomes "Blue%20Monkey"). Therefore, decode at the receiver.
        await (App.Current.MainPage as Xamarin.Forms.Shell).GoToAsync($"monkeydetails?name={((Animal)item).Name}");
    }
}
```

A substituição de `OnQueryChanged` tem dois argumentos: `oldValue`, que contém a consulta de pesquisa anterior, e `newValue`, que contém a consulta de pesquisa atual. A área de sugestões de pesquisa pode ser atualizada, definindo-se a propriedade `SearchHandler.ItemsSource` como uma coleção `IEnumerable` que contém itens que correspondem à consulta de pesquisa atual.

Quando um resultado de pesquisa é selecionado pelo usuário, a substituição de `OnItemSelected` é executada, e a propriedade `SelectedItem` é definida. Nesse exemplo, o método navega para outra página que exibe os dados sobre o `Animal` selecionado. Para obter mais informações sobre navegação, consulte [ Xamarin.Forms navegação do Shell](navigation.md).

> [!NOTE]
> As propriedades `SearchHandler` adicionais podem ser definidas para controlar a aparência da caixa de pesquisa.

## <a name="consume-a-searchhandler"></a>Criar um SearchHandler

O `SearchHandler` com subclasse pode ser consumido definindo a propriedade anexada `Shell.SearchHandler` como um objeto do tipo de subclasse:

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:Xaminals.Controls">
    <Shell.SearchHandler>
        <controls:MonkeySearchHandler Placeholder="Enter search term"
                                      ShowsResults="true"
                                      DisplayMemberName="Name" />
    </Shell.SearchHandler>
    ...
</ContentPage>
```

Este é o código C# equivalente:

```csharp
Shell.SetSearchHandler(this, new MonkeySearchHandler
{
    Placeholder = "Enter search term",
    ShowsResults = true,
    DisplayMemberName = "Name"
});
```

O método `MonkeySearchHandler.OnQueryChanged` retorna um `List` de objetos `Animal`. A propriedade `DisplayMemberName` é definida como a propriedade `Name` de cada objeto `Animal` e, portanto, os dados exibidos na área de sugestões serão cada nome de animal.

A propriedade `ShowsResults` é definida como `true`, de modo que as sugestões de pesquisa são exibidas enquanto o usuário insere uma consulta de pesquisa:

[![Captura de tela dos resultados da pesquisa em um shell SearchHandler, no iOS e no Android](search-images/search-results.png "Resultados da pesquisa do Shell SearchHandler")](search-images/search-results-large.png#lightbox "Resultados da pesquisa do Shell SearchHandler")

À medida que a consulta de pesquisa muda, a área de sugestões da pesquisa é atualizada:

[![Captura de tela dos resultados da pesquisa em um shell SearchHandler, no iOS e no Android](search-images/search-results-change.png "Resultados da pesquisa do Shell SearchHandler")](search-images/search-results-change-large.png#lightbox "Resultados da pesquisa do Shell SearchHandler")

Quando um resultado de pesquisa é selecionado, ocorre a navegação até `MonkeyDetailPage`, e os dados sobre o macaco selecionado são exibidos:

[![Captura de tela de detalhes de macaco, no iOS e no Android](search-images/detailpage.png "Detalhes da macaco")](search-images/detailpage-large.png#lightbox "Detalhes da macaco")

## <a name="define-search-results-item-appearance"></a>Definir a aparência do item dos resultados da pesquisa

Além de exibir `string` dados nos resultados da pesquisa, a aparência de cada item de resultado de pesquisa pode ser definida definindo a `SearchHandler.ItemTemplate` propriedade como um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) :

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:Xaminals.Controls">    
    <Shell.SearchHandler>
        <controls:MonkeySearchHandler Placeholder="Enter search term"
                                      ShowsResults="true">
            <controls:MonkeySearchHandler.ItemTemplate>
                <DataTemplate>
                    <Grid Padding="10">
                        <Grid.ColumnDefinitions>
                            <ColumnDefinition Width="0.15*" />
                            <ColumnDefinition Width="0.85*" />
                        </Grid.ColumnDefinitions>
                        <Image Source="{Binding ImageUrl}"
                               Aspect="AspectFill"
                               HeightRequest="40"
                               WidthRequest="40" />
                        <Label Grid.Column="1"
                               Text="{Binding Name}"
                               FontAttributes="Bold" />
                    </Grid>
                </DataTemplate>
            </controls:MonkeySearchHandler.ItemTemplate>
       </controls:MonkeySearchHandler>
    </Shell.SearchHandler>
    ...
</ContentPage>
```

Este é o código C# equivalente:

```csharp
Shell.SetSearchHandler(this, new MonkeySearchHandler
{
    Placeholder = "Enter search term",
    ShowsResults = true,
    DisplayMemberName = "Name",
    ItemTemplate = new DataTemplate(() =>
    {
        Grid grid = new Grid { Padding = 10 };
        grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength(0.15, GridUnitType.Star) });
        grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength(0.85, GridUnitType.Star) });

        Image image = new Image { Aspect = Aspect.AspectFill, HeightRequest = 40, WidthRequest = 40 };
        image.SetBinding(Image.SourceProperty, "ImageUrl");
        Label nameLabel = new Label { FontAttributes = FontAttributes.Bold };
        nameLabel.SetBinding(Label.TextProperty, "Name");

        grid.Children.Add(image);
        grid.Children.Add(nameLabel, 1, 0);
        return grid;
    })
});
```

Os elementos especificados no [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) definem a aparência de cada item na área de sugestões. Neste exemplo, o layout dentro do `DataTemplate` é gerenciado por um [`Grid`](xref:Xamarin.Forms.Grid) . O `Grid` contém um [`Image`](xref:Xamarin.Forms.Image) objeto e um [`Label`](xref:Xamarin.Forms.Label) objeto que ambos se associam a propriedades de cada `Monkey` objeto.

As capturas de tela a seguir mostram o resultado dos modelos de cada item na área de sugestões:

[![Captura de tela dos resultados de pesquisa do modelo em um shell SearchHandler, no iOS e no Android](search-images/search-results-template.png "Resultados da pesquisa do modelo SearchHandler do Shell")](search-images/search-results-template-large.png#lightbox "Resultados da pesquisa do modelo SearchHandler do Shell")

Para obter mais informações sobre modelos de dados, consulte [ Xamarin.Forms modelos de dados](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="search-box-visibility"></a>Visibilidade da caixa de pesquisa

Quando um `SearchHandler` é adicionado na parte superior de uma página, por padrão, a caixa de pesquisa fica visível e totalmente expandida. No entanto, esse comportamento pode ser alterado definindo a propriedade `SearchHandler.SearchBoxVisibility` para um dos membros da enumeração `SearchBoxVisibility`:

- `Hidden` – a caixa de pesquisa não está visível ou acessível.
- `Collapsible` – a caixa de pesquisa está oculta até que o usuário execute uma ação para mostrá-la. No iOS, a caixa de pesquisa é revelada verticalmente com o toque do conteúdo da página e, no Android, a caixa de pesquisa é revelada tocando no ícone de ponto de interrogação.
- `Expanded` – a caixa de pesquisa está visível e totalmente expandida. Este é o valor padrão da propriedade `SearchHandler.SearchBoxVisibility`.

> [!IMPORTANT]
> No iOS, uma caixa de pesquisa recolhível requer o iOS 11 ou superior.

O exemplo a seguir mostra como ocultar a caixa de pesquisa:

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:Xaminals.Controls">
    <Shell.SearchHandler>
        <controls:AnimalSearchHandler SearchBoxVisibility="Hidden"
                                      ... />
    </Shell.SearchHandler>
    ...
</ContentPage>
```

## <a name="search-box-focus"></a>Foco na caixa de pesquisa

Tocar na caixa de pesquisa invoca o teclado na tela, e a caixa de pesquisa recebe o foco de entrada. Isso também pode ser feito de forma programática chamando o método `Focus`, que tenta definir o foco de entrada na caixa de pesquisa e retornará `true` se for bem-sucedido. Quando o foco passa para a caixa de pesquisa, o evento `Focus` é acionado, e o método `OnFocused` substituível é chamado.

Quando o foco de entrada estiver em uma caixa de pesquisa, tocar em qualquer parte da tela fará o teclado na tela desaparecer e a caixa de pesquisa perder o foco de entrada. Isso também poderá ser feito de forma programática chamando o método `Unfocus`. Quando a caixa de pesquisa perde o foco, o evento `Unfocused` é acionado, e o método `OnUnfocus` substituível é chamado.

O estado do foco de uma caixa de pesquisa poderá ser recuperado por meio da propriedade `IsFocused`, que retornará `true` se um `SearchHandler` estiver com o foco de entrada no momento.

## <a name="searchhandler-appearance"></a>Aparência do SearchHandler

A classe `SearchHandler` define as seguintes propriedades que afetam sua aparência:

- `BackgroundColor`, do tipo `Color`, é a cor do segundo plano do texto da caixa de pesquisa.
- `CancelButtonColor`, do tipo `Color`, é a cor do botão Cancelar.
- `CharacterSpacing`, do tipo `double` , é o espaçamento entre os caracteres do `SearchHandler` texto.
- `FontAttributes`, do tipo `FontAttributes`, indica se o texto da caixa de pesquisa está em negrito ou em itálico.
- `FontFamily`, do tipo `string`, é a família da fonte usada para o texto da caixa de pesquisa.
- `FontSize`, do tipo `double`, é o tamanho do texto da caixa de pesquisa.
- `HorizontalTextAlignment`, do tipo `TextAlignment`, é o alinhamento horizontal da caixa de pesquisa.
- `PlaceholderColor`, do tipo `Color`, é a cor do texto da caixa de pesquisa do espaço reservado.
- `TextColor`, do tipo `Color`, é a cor do texto da caixa de pesquisa.
- `VerticalTextAlignment`, do tipo `TextAlignment` , é o alinhamento vertical do texto da caixa de pesquisa.

## <a name="searchhandler-keyboard"></a>Teclado do SearchHandler

O teclado apresentado quando os usuários interagem com um `SearchHandler` pode ser definido programaticamente por meio da `Keyboard` propriedade, para uma das seguintes propriedades da [`Keyboard`](xref:Xamarin.Forms.Keyboard) classe:

- [`Chat`](xref:Xamarin.Forms.Keyboard.Chat)– usado para texto e locais em que emojis são úteis.
- [`Default`](xref:Xamarin.Forms.Keyboard.Default)– o teclado padrão.
- [`Email`](xref:Xamarin.Forms.Keyboard.Email)– usado ao inserir endereços de email.
- [`Numeric`](xref:Xamarin.Forms.Keyboard.Numeric)– usado ao inserir números.
- [`Plain`](xref:Xamarin.Forms.Keyboard.Plain)– usado ao inserir texto, sem nenhum [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags) especificado.
- [`Telephone`](xref:Xamarin.Forms.Keyboard.Telephone)– usado ao inserir números de telefone.
- [`Text`](xref:Xamarin.Forms.Keyboard.Text)– usado ao inserir texto.
- [`Url`](xref:Xamarin.Forms.Keyboard.Url)– usado para inserir caminhos de arquivo & endereços da Web.

Isso pode ser feito no XAML da seguinte maneira:

```xaml
<SearchHandler Keyboard="Email" />
```

Este é o código C# equivalente:

```csharp
SearchHandler searchHandler = new SearchHandler { Keyboard = Keyboard.Email };
```

A [`Keyboard`](xref:Xamarin.Forms.Keyboard) classe também tem um [`Create`](xref:Xamarin.Forms.Keyboard.Create*) método de fábrica que pode ser usado para personalizar um teclado especificando a capitalização, a verificação ortográfica e o comportamento de sugestão. [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags)os valores de enumeração são especificados como argumentos para o método, com um `Keyboard` retorno personalizado sendo retornado. A enumeração `KeyboardFlags` contém os seguintes valores:

- [`None`](xref:Xamarin.Forms.KeyboardFlags.None)– nenhum recurso é adicionado ao teclado.
- [`CapitalizeSentence`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeSentence)– indica que a primeira letra da primeira palavra de cada sentença inserida será automaticamente capitalizada.
- [`Spellcheck`](xref:Xamarin.Forms.KeyboardFlags.Spellcheck)– indica que a verificação ortográfica será executada no texto inserido.
- [`Suggestions`](xref:Xamarin.Forms.KeyboardFlags.Suggestions)– indica que as conclusões do Word serão oferecidas no texto inserido.
- [`CapitalizeWord`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeWord)– indica que a primeira letra de cada palavra será automaticamente capitalizada.
- [`CapitalizeCharacter`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeCharacter)– indica que cada caractere será colocado em maiúscula automaticamente.
- [`CapitalizeNone`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeNone)– indica que nenhuma capitalização automática ocorrerá.
- [`All`](xref:Xamarin.Forms.KeyboardFlags.All)– indica que a verificação ortográfica, as preenchimentos de palavras e a capitalização de frase ocorrerão no texto inserido.

O exemplo de código XAML a seguir mostra como personalizar o padrão [`Keyboard`](xref:Xamarin.Forms.Keyboard) para oferecer preenchimentos de palavras e colocar todos os caracteres inseridos em maiúsculas:

```xaml
<SearchHandler Placeholder="Enter search terms">
    <SearchHandler.Keyboard>
        <Keyboard x:FactoryMethod="Create">
            <x:Arguments>
                <KeyboardFlags>Suggestions,CapitalizeCharacter</KeyboardFlags>
            </x:Arguments>
        </Keyboard>
    </SearchHandler.Keyboard>
</SearchHandler>
```

Este é o código C# equivalente:

```csharp
SearchHandler searchHandler = new SearchHandler { Placeholder = "Enter search terms" };
searchHandler.Keyboard = Keyboard.Create(KeyboardFlags.Suggestions | KeyboardFlags.CapitalizeCharacter);
```

## <a name="searchhandler-reference"></a>Referência do SearchHandler

A classe `SearchHandler` define as seguintes propriedades que controlam sua aparência e seu comportamento:

- `BackgroundColor`, do tipo `Color`, é a cor do segundo plano do texto da caixa de pesquisa.
- `CancelButtonColor`, do tipo `Color`, é a cor do botão Cancelar.
- `ClearIcon`, do tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource) , o ícone exibido para limpar o conteúdo da caixa de pesquisa.
- `ClearIconHelpText`, do tipo `string`, o texto acessível de ajuda para o ícone de limpeza.
- `ClearIconName`, do tipo `string`, o nome do ícone de limpeza para uso com leitores de tela.
- `ICommand`, do tipo `ClearPlaceholderIcon`, que é executado quando `ClearPlaceholderCommand` é tocado.
- `ClearPlaceholderCommandParameter`, do tipo `object`, que é o parâmetro passado para `ClearPlaceholderCommand`.
- `ClearPlaceholderEnabled`, do tipo `bool`, que determina se o `ClearPlaceholderCommand` pode ser executado. O valor padrão é `true`.
- `ClearPlaceholderHelpText`, do tipo `string`, o texto acessível de ajuda para o ícone de espaço reservado de limpeza.
- `ClearPlaceholderIcon`, do tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource) , o ícone de espaço reservado claro exibido quando a caixa de pesquisa está vazia.
- `ClearPlaceholderName`, do tipo `string`, o nome do ícone de espaço reservado de limpeza para uso com leitores de tela.
- `Command`, do tipo `ICommand`, que é executado quando a consulta de pesquisa é confirmada.
- `CommandParameter`, do tipo `object`, que é o parâmetro passado para `Command`.
- `DisplayMemberName`, do tipo `string`, que representa o nome ou caminho da propriedade que é exibida para cada item de dados na coleção `ItemsSource`.
- `FontAttributes`, do tipo `FontAttributes`, indica se o texto da caixa de pesquisa está em negrito ou em itálico.
- `FontFamily`, do tipo `string`, é a família da fonte usada para o texto da caixa de pesquisa.
- `FontSize`, do tipo `double`, é o tamanho do texto da caixa de pesquisa.
- `HorizontalTextAlignment`, do tipo `TextAlignment`, é o alinhamento horizontal da caixa de pesquisa.
- `IsFocused`, do tipo `bool`, representando se um `SearchHandler` tem atualmente foco de entrada.
- `IsSearchEnabled`, do tipo `bool`, que representa o estado habilitado da caixa de pesquisa. O valor padrão é `true`.
- `ItemsSource`, do tipo `IEnumerable`, especifica a coleção de itens a serem exibidos na área de sugestão e tem um valor padrão de `null`.
- `ItemTemplate`, do tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , especifica o modelo a ser aplicado a cada item na coleção de itens a ser exibida na área de sugestão.
- `Keyboard`, do tipo `Keyboard`, é o teclado para o `SearchHandler`.
- `Placeholder`, do tipo `string`, o texto a ser exibido quando a caixa de pesquisa está vazia.
- `PlaceholderColor`, do tipo `Color`, é a cor do texto da caixa de pesquisa do espaço reservado.
- `Query`, do tipo `string`, o texto inserido pelo usuário na caixa de pesquisa.
- `QueryIcon`, do tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource) , o ícone usado para indicar ao usuário que a pesquisa está disponível.
- `QueryIconHelpText`, do tipo `string`, o texto de ajuda acessível para o ícone de consulta.
- `QueryIconName`, do tipo `string`, o nome do ícone de consulta para uso com leitores de tela.
- `SearchBoxVisibility`, do tipo `SearchBoxVisibility`, a visibilidade da caixa de pesquisa. Por padrão, a caixa de pesquisa fica visível e totalmente expandida.
- `SelectedItem`, do tipo `object`, o item selecionado nos resultados da pesquisa. Essa propriedade é somente leitura e tem um valor padrão de `null`.
- `ShowsResults`, do tipo `bool`, indica se os resultados da pesquisa devem ser esperados na área de sugestão na entrada de texto. O valor padrão é `false`.
- `TextColor`, do tipo `Color`, é a cor do texto da caixa de pesquisa.

Todas essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que as propriedades podem ser destinos de associações de dados.

Além disso, a classe `SearchHandler` fornece os seguintes métodos substituíveis:

- `OnClearPlaceholderClicked`, que é chamado sempre que `ClearPlaceholderIcon` é tocado.
- `OnItemSelected`, que é chamado sempre que um resultado de pesquisa é selecionado pelo usuário.
- `OnFocused`, que é chamado quando um `SearchHandler` adquire o foco de entrada.
- `Query`, que é chamado quando a propriedade `OnQueryChanged` é alterada.
- `OnQueryConfirmed`, que é chamado sempre que o usuário pressiona ou confirma a consulta na caixa de pesquisa.
- `OnUnfocus`, que é chamado quando um `SearchHandler` fecha o foco de entrada.

## <a name="related-links"></a>Links relacionados

- [Xaminals (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [Xamarin.FormsNavegação do Shell](navigation.md)
