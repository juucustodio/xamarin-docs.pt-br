---
ms.assetid: 1BB412D1-FC3D-4E69-8B01-B976A3DB6328
title: 'WPF x. Xamarin. Forms: Semelhanças e diferenças'
description: Este documento compara e contrasta WPF ao xamarin. Forms. Ele aborda modelos de controle, XAML, infra-estrutura de ligação, modelos de dados, ItemsControl, UserControl, navegação e navegação de URL.
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: 43eadacebf7ed877dfa28381ad77117d5988ca4b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50119808"
---
# <a name="wpf-vs-xamarinforms-similarities--differences"></a>WPF x. Xamarin. Forms: Semelhanças e diferenças

## <a name="control-templates"></a>Modelos de controle

WPF suporta o conceito de *modelos de controle* que fornece as instruções de visualização para um controle (`Button`, `ListBox`, etc.). Conforme mencionado acima, o xamarin. Forms usa concreto _renderização_ classes para isso que interagem com a plataforma nativa (iOS, Android, etc) para visualizar o controle.

No entanto, xamarin. Forms _faz_ têm um `ControlTemplate` tipo - ela é usada para temas `Page` objetos. Ele fornece uma definição para um `Page` que fornece conteúdo consistente, mas permite que o usuário da página Alterar as cores, fontes, etc. e até mesmo adicionar elementos para torná-lo exclusivo para o aplicativo.

Usos comuns para isso são coisas como caixas de diálogo de autenticação, prompts e fornecer uma página padronizados, porém themable aparência que podem ser personalizada dentro do aplicativo. Como parte desse suporte, muitos controles WPF nomeadas familiares são usados:

1. `ContentPage`
2. `ContentView`
3. `ContentPresenter`
4. `TemplateBinding`

Mas é importante saber que esses são _não_ atendendo a mesma finalidade no xamarin. Forms. Para obter mais informações sobre esse recurso, confira a [página de documentação](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md).

## <a name="xaml"></a>XAML

XAML é usado como a linguagem de marcação declarativa para xamarin. Forms e WPF. Na maior parte, a sintaxe é idêntica – a principal diferença é que os objetos que são definidos/criados nos gráficos de XAML.

- Xamarin. Forms dá suporte a [especificação XAML 2009](/dotnet/framework/xaml-services/xaml-2009-language-features/); isso torna mais fácil definir os dados, como `string`s, `int`s, etc., bem como definir tipos de genéricos e passando argumentos para construtores.

- Atualmente, não há nenhuma maneira para carregar XAML dinamicamente como WPF pode fazer com `XamlReader`. Você pode obter a mesma funcionalidade básica com um [pacote do NuGet](https://www.nuget.org/packages/Xamarin.Forms.Dynamic/) embora.

### <a name="markup-extensions"></a>Extensões de marcação

Xamarin. Forms dá suporte à extensão XAML por meio de extensões de marcação, muito parecido com o WPF. Uso imediato, ele tem os mesmos blocos de construção básicos:

1. `{x:Array}`
2. `{Binding}`
3. `{DynamicResource}`
4. `{x:Null}`
5. `{x:Static}`
6. `{StaticResource}`
7. `{x:Type}`

Além disso, ele inclui `{x:Reference}` da especificação XAML 2009 e uma `{TemplateBinding}` extensão de marcação que é usado para a versão especializada do `ControlTemplate` com suporte pelo xamarin. Forms.

> [!WARNING]
> O `ControlTemplate` suporte não é o mesmo – mesmo que ela tenha o mesmo nome.

Xamarin. Forms dá suporte a extensões de marcação personalizadas também – mas a implementação é ligeiramente diferente. No WPF, você deve derivar de `MarkupExtension` -uma classe base abstrata. No xamarin. Forms, que é substituído por uma interface `IMarkupExtension` ou `IMarkupExtension<T>` que é mais flexível.

Assim como o WPF, o único método necessário é um `ProvideValue` método para retornar o valor de extensão de marcação.

## <a name="binding-infrastructure"></a>Infraestrutura de associação

Um dos principais conceitos transferidos é uma infra-estrutura de ligação de dados para conectar-se propriedades visuais às propriedades de dados do .NET. Isso permite que os padrões de arquitetura como o MVVM. O design básico é idêntico - você possui uma classe base associável [BindableObject](xref:Xamarin.Forms.BindableObject), no WPF, esse é o [DependencyObject](https://msdn.microsoft.com/en-us/library/system.windows.dependencyobject(v=vs.110).aspx) classe. Essa classe base é usada como o predecessor de raiz para todos os objetos que participarão como destinos de vinculação de dados. As classes derivadas, em seguida, exponham [BindableProperty](xref:Xamarin.Forms.BindableProperty) objetos que atuam como o armazenamento de backup para valores de propriedade (eles são definidos como [DependencyProperty](https://msdn.microsoft.com/library/system.windows.dependencyproperty(v=vs.110).aspx) objetos no WPF).

### <a name="defining-bindable-properties"></a>Definindo propriedades vinculáveis

A definição de uma propriedade vinculável na xamarin. Forms é o mesmo que o WPF:
1. O objeto deve derivar de `BindableObject`.
2. Deve haver um campo estático público do tipo `BindableProperty` declarado para definir a chave de armazenamento de backup para a propriedade.
3. Deve haver um wrapper de propriedade de instância pública que usa `GetValue` e `SetValue` para recuperar e altere o valor de propriedades.

Para obter um exemplo completo, consulte [propriedades vinculáveis no xamarin. Forms](~/xamarin-forms/xaml/bindable-properties.md).

### <a name="attached-properties"></a>Propriedades anexadas

Propriedades anexadas são um subconjunto da propriedade associável e eles funcionam da mesma maneira que no WPF. A principal diferença é que o wrapper de propriedade é omitido nesse caso e substituído por um conjunto de métodos estáticos get/set na classe proprietária. Ver [propriedades anexadas no xamarin. Forms](~/xamarin-forms/xaml/attached-properties.md) para obter mais informações.

### <a name="using-the-binding-engine"></a>Usando o mecanismo de associação

O processo para usar o mecanismo de associação é o mesmo que no WPF. Ele pode ser utilizado no code-behind, criando um `Binding` objeto vinculado a um objeto de origem (qualquer tipo .NET) e um valor de propriedade opcional (se omitido, ele trata o objeto de origem como a propriedade em si – assim como WPF). Você pode usar `SetBinding` em qualquer `BindableObject` para associar a associação a um `BindableProperty`.

Como alternativa, você pode definir a relação de vinculação em XAML usando o `BindingExtension`. Ele tem os mesmos valores básicos que a extensão no WPF.

O mecanismo e o suporte à associação são mais semelhantes à implementação do Silverlight que WPF. Há vários recursos ausentes que não foram implementados no xamarin. Forms:

- Não há suporte para os seguintes recursos em associações:
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

#### <a name="relativesource"></a>RelativeSource

Não há suporte para `RelativeSource` associações. No WPF, eles permitem que você associar a outros elementos visuais definidos em XAML. No xamarin. Forms, essa mesma funcionalidade pode ser obtida usando o `{x:Reference}` extensão de marcação. Por exemplo, supondo que temos um controle com o nome "otherControl" que tem uma propriedade de texto, podemos fazer a ligação a ele como esta:

**WPF**

```xaml
Text={Binding RelativeSource={RelativeSource otherControl}, Path=Text}
```

**Xamarin.Forms**

```xaml
Text={Binding Source={x:Reference otherControl}, Path=Text}
```

A mesma funcionalidade pode ser usada para o `{RelativeSource Self}` recurso. No entanto não há suporte para a localização de ancestrais por tipo (`{RelativeSource FindAncestor}`).

#### <a name="binding-context"></a>Contexto de associação

No WPF, você pode definir um `DataContext` valor da propriedade que reprents o padrão de origem da associação. Se a fonte para uma associação não estiver definida, esse valor de propriedade é usado. O valor é herdado abaixo da árvore visual, permitindo que ele seja definido em um nível superior e, em seguida, usado por filhos.

No xamarin. Forms, esse mesmo recurso é disponíveis, mas é o nome da propriedade `BindingContext`.

#### <a name="value-converters"></a>Conversores de valor

Conversores de valor são totalmente compatíveis no xamarin. Forms - como o WPF. Da mesma forma da interface é usada, mas o xamarin. Forms tem a interface definida no `Xamarin.Forms` namespace.

### <a name="model-view-viewmodel"></a>Model-View-ViewModel

MVVM completamente é compatível com WPF e o xamarin. Forms.

O WPF inclui um built em `RoutedCommand` que às vezes é usado; Não há suporte interno de comando além do xamarin. Forms tem o `ICommand` definição de interface. Você pode incluir uma variedade de estruturas MVVM para adicionar as classes base necessárias para implementar o MVVM.

#### <a name="inotifypropertychanged-and-inotifycollectionchanged"></a>INotifyPropertyChanged e INotifyCollectionChanged

Ambas as interfaces são totalmente compatíveis em associações do xamarin. Forms. Ao contrário de muitas estruturas baseadas em XAML, as notificações de alteração de propriedade podem ser geradas em threads em segundo plano no xamarin. Forms (assim como o WPF) e o mecanismo de associação corretamente fará a transição para o thread de interface do usuário.

Além disso, dar suporte a ambos os ambientes `SynchronziationContext` e `async` / `await` fazer o marshalling de thread adequado. O WPF inclui o `Dispatcher` classe em todos os elementos visuais, xamarin. Forms tem um método estático `Device.BeginInvokeOnMainThread` que também pode ser usado (embora `SynchronizationContext` é preferencial para a codificação de plataforma cruzada).

- Xamarin. Forms inclui um `ObservableCollection<T>` que dá suporte a notificações de alteração de coleção.
- Você pode usar `BindingBase.EnableCollectionSynchronization` para habilitar atualizações de thread cruzado para uma coleção. A API é ligeiramente diferente do que a variação do WPF, [verificar os documentos para obter detalhes de uso](xref:Xamarin.Forms.BindingBase.EnableCollectionSynchronization*).

## <a name="data-templates"></a>Modelos de dados

Modelos de dados têm suporte no xamarin. Forms para personalizar a renderização de um `ListView` linha (célula). Ao contrário do WPF que pode utilizar `DataTemplate`s para qualquer controle orientados por conteúdo, o xamarin. Forms atualmente usa apenas-los para `ListView`. A definição de modelo pode ser definidas embutidas (atribuído para o `ItemTemplate` propriedade), ou como um recurso em um `ResourceDictionary`.

Além disso, eles não são bastante tão flexíveis quanto sua contraparte do WPF.

1. O elemento raiz do `DataTemplate` devem _sempre_ ser um `ViewCell` objeto.
2. Gatilhos de dados têm suporte completo em um modelo de dados, mas deve incluir um `DataType` propriedade que indica o tipo da propriedade que o gatilho está associado.
3. `DataTemplateSelector` também é suportado, mas deriva `DataTemplate` e apenas, portanto, é atribuído diretamente para o `ItemTemplate` propriedade (versus `ItemTemplateSelector` no WPF).

## <a name="itemscontrol"></a>ItemsControl

Não há nenhum equivelent interno para um `ItemsControl` no xamarin. Forms; mas não há uma [um personalizado para xamarin. Forms disponível aqui](https://github.com/xamarinhq/xamu-infrastructure/blob/master/src/XamU.Infrastructure/Controls/ItemsControl.cs).

## <a name="user-controls"></a>Controles de usuário

No WPF, `UserControl`s são usadas para fornecer uma seção da interface do usuário que associou o comportamento. No xamarin. Forms, usamos o `ContentView` para a mesma finalidade. Ambos dão suporte a associação e inclusão em XAML.

## <a name="navigation"></a>Navegação

O WPF inclui usados raramente `NavigationService` que poderia ser usado para fornecer um recurso de navegação do "tipo navegador". A maioria dos aplicativos não se preocupar com isso no entanto e em vez disso, usados diferentes `Window` elementos ou diferentes seções da janela para exibir dados.

Em dispositivos de telefone, diferentes _telas_ geralmente são a solução e então, o xamarin. Forms inclui suporte para várias formas de navegação:

| Estilo de navegação | Tipo de página |
|--- |--- |
|Baseada em pilha por push/pop)|NavigationPage|
|Mestre/detalhes|MasterDetailPage|
|Tabulações|TabbedPage|
|Passe o dedo para esquerda/direita|CarouselView|

O `NavigationPage` é a abordagem mais comum, e cada página tem um `Navigation` propriedade que pode ser usada para enviar por push ou pop-páginas e desativar a pilha de navegação. Esse é o mais próximo equivelent para o `NavigationService` encontrado no WPF.

### <a name="url-navigation"></a>Navegação de URL

WPF é uma tecnologia orientada a área de trabalho e pode aceitar parâmetros de linha de comando para direcionar o comportamento de inicialização. Pode usar o xamarin. Forms [vinculação profunda de URL](https://blog.xamarin.com/deep-link-content-with-xamarin-forms-url-navigation/) para saltar para uma página na inicialização.
