---
ms.assetid: 1BB412D1-FC3D-4E69-8B01-B976A3DB6328
title: 'WPF vs. Xamarin. Forms: Semelhanças e diferenças'
ms.technology: xamarin-crossplatform
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: e72297963b6be50572e4bb539263065592737c42
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/03/2018
---
# <a name="wpf-vs-xamarinforms-similarities--differences"></a>WPF vs. Xamarin. Forms: Semelhanças e diferenças

## <a name="control-templates"></a>Modelos de controle

WPF oferece suporte ao conceito de *modelos de controle* que fornecem as instruções de visualização para um controle (`Button`, `ListBox`, etc.). Conforme mencionado acima, xamarin. Forms usa concreto _renderização_ classes para isso que interagem com a plataforma nativo (iOS, Android, etc) para visualizar o controle.

No entanto, xamarin. Forms _does_ tem um `ControlTemplate` tipo - ele é usado para temas `Page` objetos. Ele fornece uma definição para um `Page` que fornece conteúdo consistente, mas permite que o usuário da página Alterar cores, fontes, etc. e até mesmo adicionar elementos para torná-lo exclusivo para o aplicativo.

Usos comuns para isso são as coisas, como caixas de diálogo de autenticação, avisos e para fornecer uma página padronizado, mas themable aparência que podem ser personalizada no aplicativo. Como parte desse suporte, muitos controles WPF com nome familiares são usados:

1. `ContentPage`
2. `ContentView`
3. `ContentPresenter`
4. `TemplateBinding`

Mas é importante saber que esses são _não_ atendendo a mesma finalidade no xamarin. Forms. Para obter mais informações sobre esse recurso, confira o [página de documentação](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md).

## <a name="xaml"></a>XAML

XAML é usado como a linguagem de marcação declarativa para WPF e xamarin. Forms. A maior parte do tempo, a sintaxe é idêntica - a principal diferença é que os objetos que são definidas/criadas nos gráficos XAML.

- Xamarin. Forms oferece suporte a [especificação XAML 2009](/dotnet/framework/xaml-services/xaml-2009-language-features/); isso facilita definir os dados como `string`s, `int`s, etc. tipos genéricos, bem como definir e passando argumentos para construtores.

- Não há atualmente nenhuma maneira de carga dyanmically XAML WPF pode com `XamlReader`. Você pode obter a mesma funcionalidade básica com uma [pacote NuGet](https://www.nuget.org/packages/Xamarin.Forms.Dynamic/) embora.

### <a name="markup-extensions"></a>Extensões de marcação

Xamarin. Forms oferece suporte à extensão XAML por meio de extensões de marcação, bem como WPF. Fora da caixa, ela tem os mesmo blocos de construção básicos:

1. `{x:Array}`
2. `{Binding}`
3. `{DynamicResource}`
4. `{x:Null}`
5. `{x:Static}`
6. `{StaticResource}`
7. `{x:Type}`

Além disso, ele inclui `{x:Reference}` da especificação de XAML 2009 e um `{TemplateBinding}` extensão de marcação que é usado para a versão especializada do `ControlTemplate` com o suporte de xamarin. Forms.

> [!WARNING]
> O `ControlTemplate` suporte não é o mesmo - mesmo que ela tenha o mesmo nome.

Xamarin. Forms oferece suporte a extensões de marcação personalizadas também - mas a implementação é ligeiramente diferente. No WPF, você deve derivar de `MarkupExtension` -uma classe base abstrata. Xamarin. Forms, que é substituída por uma interface `IMarkupExtension` ou `IMarkupExtension<T>` que é mais flexível.

Assim como WPF, o único método necessário é um `ProvideValue` método para retornar o valor da extensão de marcação.

## <a name="binding-infrastructure"></a>Infraestrutura de associação

Um dos principais conceitos transportados é uma infraestrutura de associação de dados para conectar-se propriedades visuais para propriedades de dados do .NET. Isso permite que os padrões de arquitetura como MVVM. O design básico é idêntico - você tem uma classe base associável [BindableObject](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/), no WPF, esse é o [DependencyObject](https://msdn.microsoft.com/en-us/library/system.windows.dependencyobject(v=vs.110).aspx) classe. Esta classe base é usada como o ancestral de raiz para todos os objetos que participarão como destinos de vinculação de dados. As classes derivadas exponham [BindableProperty](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) objetos que atuam como o armazenamento de backup para valores de propriedade (eles são definidos como [DependencyProperty](https://msdn.microsoft.com/library/system.windows.dependencyproperty(v=vs.110).aspx) objetos no WPF).

### <a name="defining-bindable-properties"></a>Definindo propriedades vinculáveis

A definição de uma propriedade ligável no xamarin. Forms é o mesmo que o WPF:
1. O objeto deve ser derivado de `BindableObject`.
2. Deve haver um campo estático público do tipo `BindableProperty` declarado para definir a chave de armazenamento de backup para a propriedade.
3. Deve haver um wrapper de propriedade de instância pública que usa `GetValue` e `SetValue` para recuperar e altere o valor de propriedades.

Para obter um exemplo completo, consulte [propriedades vinculáveis no xamarin. Forms](~/xamarin-forms/xaml/bindable-properties.md).

### <a name="attached-properties"></a>Propriedades anexadas

Propriedades anexadas são um subconjunto da propriedade associável e funcionam da mesma maneira que no WPF. A principal diferença é que o wrapper de propriedade é omitido nesse caso e substituído por um conjunto de métodos get/set estático na classe de propriedade. Consulte [propriedades anexadas no xamarin. Forms](~/xamarin-forms/xaml/attached-properties.md) para obter mais informações.

### <a name="using-the-binding-engine"></a>Usando o mecanismo de associação

O processo para usar o mecanismo de associação é o mesmo no WPF. Ele pode ser utilizado no code-behind, criando uma `Binding` objeto associado a um objeto de origem (qualquer tipo .NET) e um valor de propriedade opcional (se omitido, ele tratará o objeto de origem como a propriedade – assim como WPF). Você pode usar `SetBinding` em qualquer `BindableObject` para associar a associação a um `BindableProperty`.

Como alternativa, você pode definir a relação de associação em XAML usando o `BindingExtension`. Ele tem os mesmos valores básicos que a extensão no WPF.

O mecanismo e o suporte à vinculação são mais semelhantes à implementação do Silverlight WPF. Há vários recursos ausentes que não foram implementados no xamarin. Forms:

- Não há nenhum suporte para os seguintes recursos em associações:
    - BindingGroupName
    - BindsDirectlyToSource
    - IsAsync
    - MultiBinding
    - NotifyOnSourceUpdated
    - NotifyOnTargetUpdated
    - NotifyOnValidationError
    - UpdateSourceTrigger
    - UpdateSourceExceptionFilter
    - ValidatesOnDataErrors
    - ValidatesOnExceptions
    - Coleção ValidationRules
    - XPath
    - XmlNamespaceManager
- `Binding.Mode` não oferece suporte a `OneTime`, em vez disso, basta usar `OneWay`.

#### <a name="relativesource"></a>RelativeSource

Não há suporte para `RelativeSource` associações. No WPF, elas permitem que você vincular a outros elementos visuais definidos em XAML. No xamarin. Forms, esse mesmo recurso pode ser obtido usando o `{x:Reference}` extensão de marcação. Por exemplo, supondo que temos um controle com o nome "otherControl" que tem uma propriedade de texto, podemos associar a ele como esta:

**WPF**

```xaml
Text={Binding RelativeSource={RelativeSource otherControl}, Path=Text}
```

**Xamarin.Forms**

```xaml
Text={Binding Source={x:Reference otherControl}, Path=Text}
```

A mesma funcionalidade pode ser usada para o `{RelativeSource Self}` recurso. No entanto, há nenhum suporte para localizar os ancestrais por tipo (`{RelativeSource FindAncestor}`).

#### <a name="binding-context"></a>Contexto de associação

No WPF, você pode definir um `DataContext` valor de propriedade que reprents o padrão de associação de origem. Se a fonte de uma associação não é definida, o valor da propriedade será usado. O valor é herdado abaixo da árvore visual, permitindo que ele seja definido em um nível superior e, em seguida, usado por filhos.

Xamarin. Forms, esse mesmo recurso é avaialable, mas o nome da propriedade é `BindingContext`.

#### <a name="value-converters"></a>Conversores de valor

Conversores de valor têm suporte total no xamarin. Forms - como WPF. Da mesma forma de interface é usada, mas xamarin. Forms tem a interface definida no `Xamarin.Forms` namespace.

### <a name="model-view-viewmodel"></a>Model-View-ViewModel

MVVM tem suporte completo WPF e xamarin. Forms.

WPF inclui um built em `RoutedCommand` que às vezes é usado; Xamarin. Forms tem suporte comando interna além de `ICommand` definição de interface. Você pode incluir uma variedade de estruturas MVVM para adicionar as classes base necessárias para implementar MVVM.

#### <a name="inotifypropertychanged-and-inotifycollectionchanged"></a>INotifyPropertyChanged e INotifyCollectionChanged

Ambas as interfaces são totalmente suportadas nas associações xamarin. Forms. Ao contrário de muitas estruturas baseadas em XAML, as notificações de alteração de propriedade podem ser geradas em threads de plano de fundo em xamarin. Forms (assim como WPF) e o mecanismo de associação corretamente fará a transição para o thread de interface do usuário.

Além disso, suportam a ambos os ambientes `SynchronziationContext` e `async` / `await` fazer o empacotamento de segmento adequado. O WPF inclui o `Dispatcher` classe em todos os elementos visuais, xamarin. Forms tem um método estático `Device.BeginInvokeOnMainThread` que também pode ser usado (embora `SynchronizationContext` é preferido para codificação de plataforma cruzada).

- Xamarin. Forms inclui um `ObservableCollection<T>` que dá suporte a notificações de alteração de coleção.
- Você pode usar `BindingBase.EnableCollectionSynchronization` para habilitar atualizações entre threads para uma coleção. A API é ligeiramente diferente da variação WPF, [Verifique a documentação para obter detalhes de uso](https://developer.xamarin.com/api/member/Xamarin.Forms.BindingBase.EnableCollectionSynchronization/).

## <a name="data-templates"></a>Modelos de dados

Modelos de dados têm suporte no xamarin. Forms para personalizar a renderização de um `ListView` linha (célula). Ao contrário do WPF que pode utilizar `DataTemplate`s para qualquer controle orientado por conteúdo, xamarin. Forms atualmente usa apenas para `ListView`. A definição de modelo pode ser definidas embutidas (atribuído para o `ItemTemplate` propriedade), ou como um recurso em um `ResourceDictionary`.

Além disso, eles não são muito flexíveis sua contraparte WPF.

1. O elemento raiz da `DataTemplate` deve _sempre_ ser um `ViewCell` objeto.
2. Gatilhos de dados têm suporte total em um modelo de dados, mas deve incluir um `DataType` propriedade que indica o tipo da propriedade que o gatilho está associado.
3. `DataTemplateSelector` também é suportado, mas deriva `DataTemplate` e apenas, portanto, é atribuído diretamente para o `ItemTemplate` propriedade (VS. `ItemTemplateSelector` no WPF).

## <a name="itemscontrol"></a>ItemsControl

Não há nenhum equivelent interno para uma `ItemsControl` em xamarin. Forms; mas não há um [um personalizado para xamarin. Forms disponível aqui](https://github.com/xamarinhq/xamu-infrastructure/blob/master/src/XamU.Infrastructure/Controls/ItemsControl.cs).

## <a name="user-controls"></a>Controles de usuário

No WPF, `UserControl`s são usados para fornecer uma seção da interface do usuário que associou comportamento. Xamarin. Forms, usamos o `ContentView` para a mesma finalidade. Oferecem suporte a associação e inclusão em XAML.

## <a name="navigation"></a>Navegação

O WPF inclui usados raramente `NavigationService` que pode ser usado para fornecer um recurso de navegação "navegador-like". A maioria dos aplicativos não se preocupar com isso porém e em vez disso, usados diferentes `Window` elementos ou seções diferentes da janela para exibir dados.

Em dispositivos de telefone, diferentes _telas_ geralmente são a solução e então xamarin. Forms inclui suporte para várias formas de navegação:

| Estilo de navegação | Tipo de página |
|--- |--- |
|Baseado em pilha (push/pop)|NavigationPage|
|Detalhes/mestre|MasterDetailPage|
|Tabulações|TabbedPage|
|Passe o dedo para esquerda/direita|CarouselView|

O `NavigationPage` é a abordagem mais comum, e cada página tem uma `Navigation` propriedade que pode ser usada para enviar por push ou pop páginas e desativar a pilha de navegação. Este é o mais próximo equivelent para o `NavigationService` encontrado no WPF.

### <a name="url-navigation"></a>Navegação de URL

O WPF é uma tecnologia orientada a área de trabalho e pode aceitar parâmetros de linha de comando para direcionar o comportamento de inicialização. Xamarin. Forms pode usar [vinculação profunda de URL](https://blog.xamarin.com/deep-link-content-with-xamarin-forms-url-navigation/) para ir para uma página na inicialização.
