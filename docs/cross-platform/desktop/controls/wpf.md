---
ms.assetid: 1BB412D1-FC3D-4E69-8B01-B976A3DB6328
title: 'WPF vs. Xamarin. Forms: Semelhanças & diferenças'
description: Este documento compara e contrasta o WPF para Xamarin. Forms. Ele aborda modelos de controle, XAML, infraestrutura de associação, modelos de dados, ItemsControl, UserControl, navegação e navegação de URL.
author: conceptdev
ms.author: crdun
ms.date: 04/26/2017
ms.openlocfilehash: d23b449382183b0385eac38c0b9205e48dbe0a34
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290404"
---
# <a name="wpf-vs-xamarinforms-similarities--differences"></a>WPF vs. Xamarin. Forms: Semelhanças & diferenças

## <a name="control-templates"></a>Modelos de controle

O WPF dá suporte ao conceito de *modelos de controle* que fornecem as instruções de visualização para`Button`um `ListBox`controle (, etc.). Conforme mencionado acima, o Xamarin. Forms usa classes de _renderização_ concretas para isso, que interagem com a plataforma nativa (Ios, Android etc.) para visualizar o controle.

No entanto, _o_ Xamarin. Forms `ControlTemplate` tem um tipo-ele é `Page` usado para os objetos deles. Ele fornece uma definição para um `Page` que fornece conteúdo consistente, mas permite que o usuário da página altere cores, fontes, etc. e até mesmo adicione elementos para torná-lo exclusivo ao aplicativo.

Usos comuns para isso são coisas como caixas de diálogo de autenticação, prompts e fornecer uma aparência de página padronizada, mas que pode ser orientada, que podem ser personalizadas no aplicativo. Como parte desse suporte, muitos controles nomeados com WPF conhecidos são usados:

1. `ContentPage`
2. `ContentView`
3. `ContentPresenter`
4. `TemplateBinding`

Mas é importante saber que eles _não_ atendem à mesma finalidade no Xamarin. Forms. Para obter mais informações sobre esse recurso, confira a [página de documentação](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md).

## <a name="xaml"></a>XAML

O XAML é usado como a linguagem de marcação declarativa para WPF e Xamarin. Forms. Na maior parte, a sintaxe é idêntica – a principal diferença são os objetos definidos/criados pelos grafos XAML.

- O Xamarin. Forms dá suporte à [especificação XAML 2009](/dotnet/framework/xaml-services/xaml-2009-language-features/); Isso facilita a definição de dados como `string`s, `int`s, etc., bem como a definição de tipos genéricos e a passagem de argumentos para construtores.

- Atualmente, não há nenhuma maneira de carregar dinamicamente XAML, como o `XamlReader`WPF, pode com. No entanto, você pode obter a mesma funcionalidade básica com um [pacote NuGet](https://www.nuget.org/packages/Xamarin.Forms.Dynamic/) .

### <a name="markup-extensions"></a>Extensões de marcação

O Xamarin. Forms dá suporte à extensão de XAML por meio de extensões de marcação, assim como o WPF. Ele tem os mesmos blocos de construção básicos:

1. `{x:Array}`
2. `{Binding}`
3. `{DynamicResource}`
4. `{x:Null}`
5. `{x:Static}`
6. `{StaticResource}`
7. `{x:Type}`

Além disso, ele inclui `{x:Reference}` da especificação XAML 2009 e uma `{TemplateBinding}` extensão de marcação que é usada para a versão especializada do `ControlTemplate` suportada pelo Xamarin. Forms.

> [!WARNING]
> O `ControlTemplate` suporte não é o mesmo, embora tenha o mesmo nome.

O Xamarin. Forms também dá suporte a extensões de marcação personalizadas, mas a implementação é um pouco diferente. No WPF, você deve derivar `MarkupExtension` de uma classe base abstrata. No Xamarin. Forms, que é substituído por uma interface `IMarkupExtension` ou `IMarkupExtension<T>` que é mais flexível.

Assim como o WPF, o único método necessário é `ProvideValue` um método para retornar o valor da extensão de marcação.

## <a name="binding-infrastructure"></a>Infraestrutura de associação

Um dos principais conceitos transferidos é uma infra-estrutura de ligação de dados para conectar propriedades visuais a propriedades de dados do .NET. Isso permite padrões de arquitetura como MVVM. O design básico é idêntico-você tem uma classe base vinculável [vinculobject](xref:Xamarin.Forms.BindableObject), no WPF, essa é a classe [DependencyObject](xref:System.Windows.DependencyObject) . Essa classe base é usada como o ancestral raiz para todos os objetos que participarão como destinos na associação de dados. As classes derivadas expõem os objetos [vinculproperty](xref:Xamarin.Forms.BindableProperty) que atuam como o armazenamento de backup para valores de propriedade (eles são definidos como objetos [DependencyProperty](xref:System.Windows.DependencyProperty) no WPF).

### <a name="defining-bindable-properties"></a>Definindo propriedades vinculáveis

A definição de uma propriedade vinculável no Xamarin. Forms é a mesma do WPF:
1. O objeto deve derivar `BindableObject`de.
2. Deve haver um campo estático público do tipo `BindableProperty` declarado para definir a chave de armazenamento de backup para a propriedade.
3. Deve haver um wrapper de propriedades de instância pública que `GetValue` usa `SetValue` e para recuperar e alterar o valor das propriedades.

Para obter um exemplo completo, consulte [propriedades vinculáveis no Xamarin. Forms](~/xamarin-forms/xaml/bindable-properties.md).

### <a name="attached-properties"></a>Propriedades anexadas

As propriedades anexadas são um subconjunto da propriedade vinculável e funcionam da mesma maneira que no WPF. A principal diferença é que o wrapper de propriedade é ommitted nesse caso e substituído por um conjunto de métodos get/set estáticos na classe proprietária. Consulte [Propriedades anexadas no Xamarin. Forms](~/xamarin-forms/xaml/attached-properties.md) para obter mais informações.

### <a name="using-the-binding-engine"></a>Usando o mecanismo de associação

O processo para usar o mecanismo de associação é o mesmo que no WPF. Ele pode ser utilizado no code-behind criando um `Binding` objeto vinculado a um objeto de origem (qualquer tipo .net) e um valor de propriedade opcional (se ommitted, ele trata o objeto de origem como a própria propriedade, assim como o WPF). Você pode usar `SetBinding` o em qualquer `BindableObject` para associar a associação a um `BindableProperty`.

Como alternativa, você pode definir a relação de associação em XAML usando `BindingExtension`o. Ele tem os mesmos valores básicos que a extensão no WPF.

O suporte e o mecanismo de ligação são mais semelhantes à implementação do Silverlight do que o WPF. Há vários recursos ausentes que não foram implementados no Xamarin. Forms:

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
  - Coleção de ValidationRules
  - XPath
  - XmlNamespaceManager

#### <a name="relativesource"></a>RelativeSource

Não há suporte para `RelativeSource` associações. No WPF, eles permitem que você se vincule a outros elementos visuais definidos em XAML. No Xamarin. Forms, esse mesmo recurso pode ser obtido usando a `{x:Reference}` extensão de marcação. Por exemplo, supondo que tenhamos um controle com o nome "otherControl" que tem uma propriedade Text, podemos associar a ele da seguinte maneira:

**WPF**

```xaml
Text={Binding RelativeSource={RelativeSource otherControl}, Path=Text}
```

**Xamarin.Forms**

```xaml
Text={Binding Source={x:Reference otherControl}, Path=Text}
```

O mesmo recurso pode ser usado para o `{RelativeSource Self}` recurso. No entanto, não há suporte para localizar ancestrais por`{RelativeSource FindAncestor}`tipo ().

#### <a name="binding-context"></a>Contexto de associação

No WPF, você pode definir um `DataContext` valor de propriedade que reprents a fonte de associação padrão. Se a origem de uma associação não for definida, esse valor de propriedade será usado. O valor é herdado para baixo na árvore visual, permitindo que ele seja definido em um nível mais alto e, em seguida, usado por filhos.

No Xamarin. Forms, esse mesmo recurso é disponíveis, mas o nome da propriedade `BindingContext`é.

#### <a name="value-converters"></a>Conversores de valor

Os conversores de valor têm suporte total no Xamarin. Forms, assim como o WPF. A mesma forma de interface é usada, mas o Xamarin. Forms tem a interface definida `Xamarin.Forms` no namespace.

### <a name="model-view-viewmodel"></a>Model-View-ViewModel

O MVVM é totalmente suportado pelo WPF e Xamarin. Forms.

O WPF inclui um interno `RoutedCommand` , que às vezes é usado; O Xamarin. Forms não tem suporte de comando interno além da definição `ICommand` da interface. Você pode incluir uma variedade de estruturas MVVM para adicionar as classes base necessárias para implementar o MVVM.

#### <a name="inotifypropertychanged-and-inotifycollectionchanged"></a>INotifyPropertyChanged e INotifyCollectionChanged

Ambas as interfaces têm suporte total em associações do Xamarin. Forms. Ao contrário de muitas estruturas baseadas em XAML, as notificações de alteração de propriedade podem ser geradas em threads em segundo plano no Xamarin. Forms (assim como o WPF) e o mecanismo de associação fará a transição apropriada para o thread da interface do usuário.

Além disso, os dois ambientes `SynchronziationContext` dão `async` suporte e / `await` para realizar o marshaling adequado de threads. O WPF inclui `Dispatcher` a classe em todos os elementos visuais, o Xamarin. Forms tem `Device.BeginInvokeOnMainThread` um método estático que também pode ser `SynchronizationContext` usado (embora seja preferencial para codificação de plataforma cruzada).

- O Xamarin. Forms inclui `ObservableCollection<T>` um que oferece suporte a notificações de alteração de coleção.
- Você pode usar `BindingBase.EnableCollectionSynchronization` o para habilitar atualizações entre threads para uma coleção. A API é ligeiramente diferente da variação do WPF, [Verifique os detalhes de uso dos documentos](xref:Xamarin.Forms.BindingBase.EnableCollectionSynchronization*).

## <a name="data-templates"></a>Modelos de dados

Modelos de dados têm suporte no Xamarin. Forms para personalizar a renderização de `ListView` uma linha (célula). Ao contrário do WPF, `DataTemplate`que pode utilizar s para qualquer controle orientado a conteúdo, o Xamarin. Forms `ListView`atualmente só os usa para o. A definição do modelo pode ser definida como embutida ( `ItemTemplate` atribuída à propriedade) ou como um recurso em `ResourceDictionary`um.

Além disso, eles não são tão flexíveis quanto seus equivalentes do WPF.

1. O elemento raiz do `DataTemplate` _sempre_ deve ser um `ViewCell` objeto.
2. Os gatilhos de dados têm suporte total em um modelo de dados, `DataType` mas devem incluir uma propriedade que indique o tipo da propriedade à qual o gatilho está associado.
3. `DataTemplateSelector`também tem suporte, mas deriva de `DataTemplate` e, portanto, é atribuído diretamente `ItemTemplate` à propriedade (vs. `ItemTemplateSelector` no WPF).

## <a name="itemscontrol"></a>ItemsControl

Não há nenhum equivalente interno a um `ItemsControl` no xamarin. Forms; mas há um [personalizado para o Xamarin. Forms disponível aqui](https://github.com/xamarinhq/xamu-infrastructure/blob/master/src/XamU.Infrastructure/Controls/ItemsControl.cs).

## <a name="user-controls"></a>Controles de usuário

No WPF, `UserControl`os s são usados para fornecer uma seção da interface do usuário que tem comportamento associado. No Xamarin. Forms, usamos o `ContentView` para a mesma finalidade. Ambos dão suporte à associação e à inclusão em XAML.

## <a name="navigation"></a>Navegação

O WPF inclui um raramente `NavigationService` usado, que poderia ser usado para fornecer um recurso de navegação "como navegador". A maioria dos aplicativos não se preocupa com isso no entanto e, em vez disso, usava elementos diferentes `Window` ou seções diferentes da janela para exibir dados.

Em dispositivos de telefone, _telas_ diferentes geralmente são a solução e, portanto, o Xamarin. Forms inclui suporte para várias formas de navegação:

| Estilo de navegação | Tipo de página |
|--- |--- |
|Baseado em pilha (Push/pop)|NavigationPage|
|Mestre/Detalhes|MasterDetailPage|
|Tabulações|TabbedPage|
|Passar o dedo para a esquerda/direita|CarouselView|

O `NavigationPage` é a abordagem mais comum, e cada página tem uma `Navigation` propriedade que pode ser usada para enviar por Push ou pop-up na pilha de navegação. Esse é o equivalente mais próximo ao `NavigationService` encontrado no WPF.

### <a name="url-navigation"></a>Navegação de URL

O WPF é uma tecnologia orientada a desktops e pode aceitar parâmetros de linha de comando para direcionar o comportamento de inicialização. O Xamarin. Forms pode usar a [vinculação de URL profunda](https://blog.xamarin.com/deep-link-content-with-xamarin-forms-url-navigation/) para saltar para uma página na inicialização.
