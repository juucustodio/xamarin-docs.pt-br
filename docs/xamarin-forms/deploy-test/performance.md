---
title: Melhorar o Xamarin.Forms desempenho do aplicativo
description: Há muitas técnicas para aumentar o desempenho dos Xamarin.Forms aplicativos. Coletivamente, essas técnicas podem reduzir de forma considerável a quantidade de trabalho que está sendo executado por uma CPU e a quantidade de memória consumida por um aplicativo.
ms.prod: xamarin
ms.assetid: 0be84c56-6698-448d-be5a-b4205f1caa9f
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/27/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 96b5939fd1f8448d45d1398fd56770f9032de083
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84139107"
---
# <a name="improve-xamarinforms-app-performance"></a>Melhorar o Xamarin.Forms desempenho do aplicativo

> [!VIDEO https://youtube.com/embed/RZvdql3Ev0E]

**Evolua 2016: Otimizando o desempenho do aplicativo comXamarin.Forms**

O baixo desempenho de aplicativo se apresenta de várias maneiras. Ele pode fazer com que o aplicativo pareça não responder, deixar a rolagem lenta e reduzir a vida útil da bateria do dispositivo. No entanto, a otimização do desempenho engloba mais do que apenas a implementação de um código eficiente. A experiência do usuário quanto ao desempenho do aplicativo também deve ser considerada. Por exemplo, garantir que as operações sejam executadas sem impedir o usuário de realizar outras atividades pode ajudar a melhorar a experiência do usuário.

Há muitas técnicas para aumentar o desempenho e desempenho percebido dos Xamarin.Forms aplicativos. Coletivamente, essas técnicas podem reduzir de forma considerável a quantidade de trabalho que está sendo executado por uma CPU e a quantidade de memória consumida por um aplicativo.

> [!NOTE]
>  Antes de ler esse artigo, você deve primeiro ler [Desempenho de plataforma cruzada](~/cross-platform/deploy-test/memory-perf-best-practices.md), que discute técnicas específicas sem plataforma para melhorar o uso de memória e o desempenho de aplicativos criados usando a plataforma Xamarin.

## <a name="enable-the-xaml-compiler"></a>Habilitar o compilador de XAML

Opcionalmente, XAML pode ser compilado direto na IL (linguagem intermediária) com o compilador XAML (XAMLC). XAMLC oferece vários benefícios:

- Executa verificação de tempo de compilação de XAML, notificando o usuário de quaisquer erros.
- Elimina parte da carga e do tempo de instanciação para elementos XAML.
- Ajuda a reduzir o tamanho do arquivo do assembly final não incluindo mais arquivos .XAML.

O XAMLC é habilitado por padrão em novas Xamarin.Forms soluções. No entanto, talvez seja necessário habilitá-lo em soluções mais antigas. Para saber mais, consulte [Compilação de XAML](~/xamarin-forms/xaml/xamlc.md).

## <a name="use-compiled-bindings"></a>Usar associações compiladas

Associações compiladas melhoram o desempenho de vinculação de dados em Xamarin.Forms aplicativos resolvendo expressões de associação em tempo de compilação, em vez de em tempo de execução com reflexão. A compilação de uma expressão de associação gera código compilado que normalmente resolve uma associação 8 a 20 vezes mais rápido do que ao usar uma associação clássica. Para saber mais, confira [Associações compiladas](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md).

## <a name="reduce-unnecessary-bindings"></a>Reduzir associações desnecessárias

Não use associações para conteúdo que pode ser facilmente definido estaticamente. Não há nenhuma vantagem em associar dados que não precisam ser associados, pois associações não são econômicas. Por exemplo, a configuração `Button.Text = "Accept"` tem menos sobrecarga do que [`Button.Text`](xref:Xamarin.Forms.Button.Text) a associação a uma propriedade ViewModel com o `string` valor "Accept".

## <a name="use-fast-renderers"></a>Usar renderizadores rápidos

Renderizadores rápidos reduzem os custos de inflação e renderização de Xamarin.Forms controles no Android, nivelando a hierarquia de controle nativo resultante. Isso aprimora o desempenho criando menos objetos, o que resulta em uma árvore visual menos complexa e em menos uso de memória.

De Xamarin.Forms 4,0 em diante, todos os aplicativos destinados `FormsAppCompatActivity` a usar renderizadores rápidos por padrão. Para obter mais informações, veja [Renderizadores Rápidos](~/xamarin-forms/internals/fast-renderers.md).

## <a name="enable-startup-tracing-on-android"></a>Habilitar o rastreamento de inicialização no Android

A compilação AOT (Ahead of Time) no Android minimiza a sobrecarga e o uso de memória da inicialização de aplicativo JIT (Just-in-Time), com o custo de criar um APK muito maior. Uma alternativa é usar o rastreamento de inicialização, que proporciona uma compensação entre o tamanho do APK do Android e o tempo de inicialização, quando comparado à compilação AOT convencional.

Em vez de compilar o máximo possível do aplicativo para código não-gerenciado, o rastreamento de inicialização compila apenas o conjunto de métodos gerenciados que representam as partes mais caras da inicialização do aplicativo em um Xamarin.Forms aplicativo em branco. Essa abordagem resulta na redução do tamanho do APK, quando comparado à compilação AOT convencional, enquanto ainda fornece melhorias de inicialização semelhantes.

## <a name="enable-layout-compression"></a>Habilitar a compactação de layout

A compactação de layout remove os layouts especificados da árvore visual, em uma tentativa de melhorar o desempenho de renderização da página. O benefício de desempenho que isso oferece varia dependendo da complexidade de uma página, da versão do sistema operacional que está sendo usado e do dispositivo no qual o aplicativo está sendo executado. No entanto, os maiores ganhos de desempenho serão observados em versões mais antigas. Para obter mais informações, confira [Layout de Compactação](~/xamarin-forms/user-interface/layouts/layout-compression.md).

## <a name="choose-the-correct-layout"></a>Escolher o layout correto

Um layout que é capaz de exibir vários filhos, mas que tem apenas um único filho, é um desperdício. Por exemplo, o exemplo de código a seguir mostra um [`StackLayout`](xref:Xamarin.Forms.StackLayout) com um único filho:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DisplayImage.HomePage">
    <StackLayout>
        <Image Source="waterfront.jpg" />
    </StackLayout>
</ContentPage>
```

Isso é um desperdício e o [`StackLayout`](xref:Xamarin.Forms.StackLayout) elemento deve ser removido, conforme mostrado no exemplo de código a seguir:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DisplayImage.HomePage">
    <Image Source="waterfront.jpg" />
</ContentPage>
```

Além disso, não tente reproduzir a aparência de um layout específico usando combinações de outros layouts, pois isso resulta na execução de cálculos de layout desnecessários. Por exemplo, não tente reproduzir um [`Grid`](xref:Xamarin.Forms.Grid) layout usando uma combinação de [`StackLayout`](xref:Xamarin.Forms.StackLayout) instâncias. O código a seguir mostra um exemplo dessa má prática:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Details.HomePage"
             Padding="0,20,0,0">
    <StackLayout>
        <StackLayout Orientation="Horizontal">
            <Label Text="Name:" />
            <Entry Placeholder="Enter your name" />
        </StackLayout>
        <StackLayout Orientation="Horizontal">
            <Label Text="Age:" />
            <Entry Placeholder="Enter your age" />
        </StackLayout>
        <StackLayout Orientation="Horizontal">
            <Label Text="Occupation:" />
            <Entry Placeholder="Enter your occupation" />
        </StackLayout>
        <StackLayout Orientation="Horizontal">
            <Label Text="Address:" />
            <Entry Placeholder="Enter your address" />
        </StackLayout>
    </StackLayout>
</ContentPage>
```

Isso é um desperdício porque cálculos de layout desnecessário são executados. Em vez disso, o layout desejado pode ser melhor obtido usando um [`Grid`](xref:Xamarin.Forms.Grid) , conforme mostrado no exemplo de código a seguir:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Details.HomePage"
             Padding="0,20,0,0">
    <Grid>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="100" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>
        <Grid.RowDefinitions>
            <RowDefinition Height="30" />
            <RowDefinition Height="30" />
            <RowDefinition Height="30" />
            <RowDefinition Height="30" />
        </Grid.RowDefinitions>
        <Label Text="Name:" />
        <Entry Grid.Column="1" Placeholder="Enter your name" />
        <Label Grid.Row="1" Text="Age:" />
        <Entry Grid.Row="1" Grid.Column="1" Placeholder="Enter your age" />
        <Label Grid.Row="2" Text="Occupation:" />
        <Entry Grid.Row="2" Grid.Column="1" Placeholder="Enter your occupation" />
        <Label Grid.Row="3" Text="Address:" />
        <Entry Grid.Row="3" Grid.Column="1" Placeholder="Enter your address" />
    </Grid>
</ContentPage>
```

## <a name="optimize-layout-performance"></a>Otimizar o desempenho do layout

Para obter o melhor desempenho possível do layout, siga estas diretrizes:

- Reduza a profundidade das hierarquias de layout especificando [`Margin`](xref:Xamarin.Forms.View.Margin) valores de propriedade, permitindo a criação de layouts com menos exibições de quebra. Para saber mais, consulte [Margens e preenchimento](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).
- Ao usar um [`Grid`](xref:Xamarin.Forms.Grid) , tente garantir que o menor número de linhas e colunas possível esteja definido como [`Auto`](xref:Xamarin.Forms.GridLength.Auto) tamanho. Cada linha ou coluna dimensionada automaticamente fará o mecanismo de layout realizar cálculos de layout adicionais. Em vez disso, use linhas e colunas de tamanho fixo, se possível. Como alternativa, defina linhas e colunas para ocupar uma quantidade proporcional de espaço com o valor de [`GridUnitType.Star`](xref:Xamarin.Forms.GridUnitType.Star) enumeração, desde que a árvore pai Siga estas diretrizes de layout.
- Não defina as [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) [`HorizontalOptions`](xref:Xamarin.Forms.View.VerticalOptions) Propriedades e de um layout, a menos que necessário. Os valores padrão de [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill) e [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) permitem a melhor otimização de layout. Alterar essas propriedades tem um custo e consome memória, mesmo ao configurá-las como os valores padrão.
- Evite usar um [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) sempre que possível. Isso resultará em a CPU precisar realizar significativamente mais trabalho.
- Ao usar um [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) , evite usar a [`AbsoluteLayout.AutoSize`](xref:Xamarin.Forms.AbsoluteLayout.AutoSize) propriedade sempre que possível.
- Ao usar um [`StackLayout`](xref:Xamarin.Forms.StackLayout) , verifique se apenas um filho está definido como [`LayoutOptions.Expands`](xref:Xamarin.Forms.LayoutOptions.Expands) . Essa propriedade garante que o filho especificado ocupe o maior espaço que o `StackLayout` pode dar a ele e é um desperdício executar esses cálculos mais de uma vez.
- Evite chamar qualquer um dos métodos da [`Layout`](xref:Xamarin.Forms.Layout) classe, pois eles resultam na execução de cálculos de layout caros. Em vez disso, é provável que o comportamento de layout desejado possa ser obtido definindo [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) as [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) Propriedades e. Como alternativa, subclasse a [`Layout<View>`](xref:Xamarin.Forms.Layout`1) classe para obter o comportamento de layout desejado.
- Não atualize nenhuma [`Label`](xref:Xamarin.Forms.Label) instância com mais frequência do que o necessário, pois a alteração do tamanho do rótulo pode resultar em todo o layout da tela sendo recalculado.
- Não defina a [`Label.VerticalTextAlignment`](xref:Xamarin.Forms.Label.VerticalTextAlignment) propriedade, a menos que seja necessário.
- Defina o [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) de qualquer [`Label`](xref:Xamarin.Forms.Label) instância para [`NoWrap`](xref:Xamarin.Forms.LineBreakMode.NoWrap) sempre que possível.

## <a name="use-asynchronous-programming"></a>usar programação assíncrona

A capacidade de resposta geral do seu aplicativo pode ser aprimorada e os gargalos de desempenho geralmente são evitados usando a programação assíncrona. No .NET, o [padrão assíncrono baseado em tarefa (toque)](/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap) é o padrão de design recomendado para operações assíncronas. No entanto, o uso incorreto do TAP pode resultar em aplicativos não executados. Portanto, as diretrizes a seguir devem ser seguidas ao usar o toque.

### <a name="fundamentals"></a>Conceitos básicos

- Entenda o ciclo de vida da tarefa, que é representado pela `TaskStatus` enumeração. Para obter mais informações, consulte [o significado de TaskStatus](https://devblogs.microsoft.com/pfxteam/the-meaning-of-taskstatus/) e [status da tarefa](/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap#task-status).
- Use o `Task.WhenAll` método para aguardar assincronamente que várias operações assíncronas sejam concluídas, em vez de `await` uma série de operações assíncronas individualmente. Para obter mais informações, consulte [Task. WhenAll](/dotnet/standard/asynchronous-programming-patterns/consuming-the-task-based-asynchronous-pattern#taskwhenall).
- Use o `Task.WhenAny` método para aguardar de forma assíncrona que uma de várias operações assíncronas seja concluída. Para obter mais informações, consulte [Task. WhenAny](/dotnet/standard/asynchronous-programming-patterns/consuming-the-task-based-asynchronous-pattern#taskwhenall).
- Use o `Task.Delay` método para produzir um `Task` objeto que termina após o tempo especificado. Isso é útil para cenários como sondagem de dados e atraso de manipulação de entrada do usuário para um tempo predeterminado. Para obter mais informações, consulte [Task. Delay](/dotnet/standard/asynchronous-programming-patterns/consuming-the-task-based-asynchronous-pattern#taskdelay).
- Execute operações de CPU síncrona intensivas no pool de threads com o `Task.Run` método. Esse método é um atalho para o `TaskFactory.StartNew` método, com os argumentos mais ideais definidos. Para obter mais informações, consulte [Task. Run](/dotnet/standard/asynchronous-programming-patterns/consuming-the-task-based-asynchronous-pattern#taskrun).
- Evite tentar criar construtores assíncronos. Em vez disso, use eventos de ciclo de vida ou lógica de inicialização separada para `await` qualquer inicialização corretamente. Para obter mais informações, consulte [construtores assíncronos](https://blog.stephencleary.com/2013/01/async-oop-2-constructors.html) em blog.stephencleary.com.
- Use o padrão de tarefa lenta para evitar esperar que operações assíncronas sejam concluídas durante a inicialização do aplicativo. Para obter mais informações, consulte [AsyncLazy](https://devblogs.microsoft.com/pfxteam/asynclazyt/).
- Crie um wrapper de tarefa para operações assíncronas existentes, que não usam o TAP, criando `TaskCompletionSource<T>` objetos. Esses objetos têm os benefícios da `Task` programação e permitem controlar o tempo de vida e a conclusão do associado `Task` . Para obter mais informações, consulte [a natureza de TaskCompletionSource](https://devblogs.microsoft.com/pfxteam/the-nature-of-taskcompletionsourcetresult/).
 
- Retornar um `Task` objeto, em vez de retornar um `Task` objeto esperado, quando não for necessário processar o resultado de uma operação assíncrona. Isso é mais eficaz devido à execução de menos alternância de contexto.
- Use a biblioteca de Dataflow da TPL (biblioteca paralela de tarefas) em cenários como processamento de dados conforme ele se tornar disponível ou quando você tiver várias operações que devem se comunicar entre si de forma assíncrona. Para obter mais informações, consulte fluxo de dados [(biblioteca paralela de tarefas)](/dotnet/standard/parallel-programming/dataflow-task-parallel-library).

### <a name="ui"></a>UI

- Chamar uma versão assíncrona de uma API, se ela estiver disponível. Isso manterá o thread de interface do usuário desbloqueado, o que ajudará a melhorar a experiência do usuário com o aplicativo.
- Atualize os elementos da interface do usuário com dados de operações assíncronas no thread da interface do usuário, para evitar que exceções sejam geradas. No entanto, as atualizações para a `ListView.ItemsSource` propriedade serão automaticamente empacotadas para o thread da interface do usuário. Para obter informações sobre como determinar se o código está em execução no thread da interface do usuário, consulte [ Xamarin.Essentials : MainThread](~/essentials/main-thread.md?content=xamarin/xamarin-forms).

    > [!IMPORTANT]
    > Todas as propriedades de controle que são atualizadas por meio da ligação de dados serão automaticamente empacotadas para o thread da interface do usuário.

### <a name="error-handling"></a>Tratamento de erros

- Saiba mais sobre a manipulação de exceção assíncrona. As exceções sem tratamento que são geradas pelo código que está sendo executado de forma assíncrona são propagadas de volta para o thread de chamada, exceto em determinados cenários. Para obter mais informações, consulte [tratamento de exceção (biblioteca paralela de tarefas)](/dotnet/standard/parallel-programming/exception-handling-task-parallel-library).
- Evite criar `async void` métodos e, em vez disso, criar `async Task` métodos. Isso permite o tratamento de erros, a capacidade de composição e a possibilidade de teste mais fáceis. A exceção a essa diretriz são os manipuladores de eventos assíncronos, que devem retornar `void` . Para obter mais informações, consulte [evitar Async void](/archive/msdn-magazine/2013/march/async-await-best-practices-in-asynchronous-programming#avoid-async-void).
- Não misture o bloqueio e o código assíncrono chamando `Task.Wait` os `Task.Result` métodos, ou `GetAwaiter().GetResult` , pois eles podem resultar na ocorrência de deadlock. No entanto, se essa diretriz precisar ser violada, a abordagem preferida será chamar o `GetAwaiter().GetResult` método porque preserva as exceções da tarefa. Para obter mais informações, consulte [Async All The](/archive/msdn-magazine/2013/march/async-await-best-practices-in-asynchronous-programming#async-all-the-way) e [tratamento de exceção de tarefa no .NET 4,5](https://devblogs.microsoft.com/pfxteam/task-exception-handling-in-net-4-5/).
- Use o `ConfigureAwait` método sempre que possível, para criar código sem contexto. O código sem contexto tem um melhor desempenho para aplicativos móveis e é uma técnica útil para evitar o deadlock ao trabalhar com uma base de código parcialmente assíncrona. Para obter mais informações, consulte [Configurar contexto](/archive/msdn-magazine/2013/march/async-await-best-practices-in-asynchronous-programming#configure-context).
- Use *tarefas de continuação* para funcionalidades como a manipulação de exceções lançadas pela operação assíncrona anterior e a cancelamento de uma continuação antes que ela seja iniciada ou enquanto estiver em execução. Para obter mais informações, consulte [encadeando tarefas usando tarefas contínuas](/dotnet/standard/parallel-programming/chaining-tasks-by-using-continuation-tasks).
- Use uma implementação assíncrona `ICommand` quando operações assíncronas forem invocadas do `ICommand` . Isso garante que qualquer exceção na lógica de comando assíncrono possa ser tratada. Para obter mais informações, consulte [programação assíncrona: padrões para aplicativos MVVM assíncronos: comandos](/archive/msdn-magazine/2014/april/async-programming-patterns-for-asynchronous-mvvm-applications-commands).

## <a name="choose-a-dependency-injection-container-carefully"></a>Escolha o contêiner de injeção de dependência com cuidado

Contêineres de injeção de dependência introduzem restrições de desempenho adicionais em aplicativos móveis. Efetuar o registro e a resolução de tipos usando um contêiner tem um custo de desempenho devido ao uso da reflexão pelo contêiner para criar cada tipo, especialmente se as dependências estiverem sendo reconstruídas para cada navegação de página no aplicativo. Se houver muitas dependências ou se elas forem profundas, o custo da criação poderá aumentar significativamente. Além disso, o registro de tipo, que geralmente ocorre durante a inicialização do aplicativo, pode ter um impacto perceptível sobre o tempo de inicialização dependendo do contêiner que está sendo usado.

Como alternativa, a injeção de dependência pode se tornar mais eficaz por meio da implementação manual usando fábricas.

## <a name="create-shell-applications"></a>Criar aplicativos de Shell

Xamarin.FormsOs aplicativos de shell fornecem uma experiência de navegação conceituada com base em submenus e guias. Se a experiência do usuário do aplicativo puder ser implementada com Shell, será benéfico fazê-lo. Aplicativos de Shell ajudam a evitar uma experiência de inicialização ruim, pois as páginas são criadas sob demanda em resposta à navegação, e não na inicialização do aplicativo, o que ocorre com aplicativos que usam uma [`TabbedPage'](xref:Xamarin.Forms.TabbedPage). Para obter mais informações, consulte [ Xamarin.Forms shell](~/xamarin-forms/app-fundamentals/shell/index.md).

## <a name="use-collectionview-instead-of-listview"></a>Usar CollectionView em vez de ListView

[`CollectionView`](xref:Xamarin.Forms.CollectionView)é uma exibição para apresentar listas de dados usando especificações de layout diferentes. Ele fornece uma alternativa mais flexível e de alto desempenho para o [`ListView`](xref:Xamarin.Forms.ListView) . Para obter mais informações, consulte [ Xamarin.Forms CollectionView](~/xamarin-forms/user-interface/collectionview/index.md).

## <a name="optimize-listview-performance"></a>Otimizar o desempenho da ListView

Ao usar [`ListView`](xref:Xamarin.Forms.ListView) o, há várias experiências de usuário que devem ser otimizadas:

- **Inicialização** – o intervalo de tempo que começa quando o controle é criado e termina quando itens são mostrados na tela.
- **Rolagem** – a capacidade de rolar pela lista e garantir que a interface do usuário não fique atrasada com relação a gestos de toque.
- **Interação** para adicionar, excluir e selecionar itens.

O [`ListView`](xref:Xamarin.Forms.ListView) controle requer um aplicativo para fornecer dados e modelos de célula. Como isso é feito terá um grande impacto sobre o desempenho do controle. Para obter mais informações, consulte [Desempenho da ListView](~/xamarin-forms/user-interface/listview/performance.md).

## <a name="optimize-image-resources"></a>Otimizar os recursos de imagem

Exibir recursos de imagem pode aumentar significativamente o volume de memória de um aplicativo. Portanto, eles só devem ser criados quando necessário e devem ser liberados assim que o aplicativo não exigi-los mais. Por exemplo, se um aplicativo estiver exibindo uma imagem lendo seus dados de um fluxo, certifique-se de que esse fluxo seja criado somente quando necessário e liberado quando não for mais necessário. Isso pode ser feito criando o fluxo quando a página é criada, ou quando o [`Page.Appearing`](xref:Xamarin.Forms.Page.Appearing) evento é acionado e, em seguida, descartando o fluxo quando o [`Page.Disappearing`](xref:Xamarin.Forms.Page.Disappearing) evento é acionado.

Ao baixar uma imagem para exibição com o [`ImageSource.FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri)) método, armazene em cache a imagem baixada, assegurando que a [`UriImageSource.CachingEnabled`](xref:Xamarin.Forms.UriImageSource.CachingEnabled) Propriedade esteja definida como `true` . Para saber mais, consulte [Trabalhando com imagens](~/xamarin-forms/user-interface/images.md).

Para saber mais, consulte [Otimizar recursos de imagem](~/cross-platform/deploy-test/memory-perf-best-practices.md#optimizeimages).

## <a name="reduce-the-visual-tree-size"></a>Reduzir o tamanho da árvore visual

Reduzir o número de elementos em uma página tornará a renderização da página mais rápida. Há duas técnicas principais para realizar essa tarefa. A primeira é ocultar elementos que não estão visíveis. A [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible) propriedade de cada elemento determina se o elemento deve ser parte da árvore visual ou não. Portanto, se um elemento não estiver visível porque está oculto atrás de outros elementos, remova o elemento ou defina sua propriedade `IsVisible` como `false`.

A segunda técnica é remover elementos desnecessários. Por exemplo, o exemplo de código a seguir mostra um layout de página que contém vários [`Label`](xref:Xamarin.Forms.Label) objetos:

```xaml
<StackLayout>
    <StackLayout Padding="20,20,0,0">
        <Label Text="Hello" />
    </StackLayout>
    <StackLayout Padding="20,20,0,0">
        <Label Text="Welcome to the App!" />
    </StackLayout>
    <StackLayout Padding="20,20,0,0">
        <Label Text="Downloading Data..." />
    </StackLayout>
</StackLayout>
```

O mesmo layout da página poderá ser mantido com uma contagem de elementos reduzida, conforme mostrado no exemplo de código a seguir:

```xaml
<StackLayout Padding="20,35,20,20" Spacing="25">
  <Label Text="Hello" />
  <Label Text="Welcome to the App!" />
  <Label Text="Downloading Data..." />
</StackLayout>
```

## <a name="reduce-the-application-resource-dictionary-size"></a>Reduzir o tamanho do dicionário de recursos do aplicativo

Todos os recursos usados em todo o aplicativo devem ser armazenados no dicionário de recursos do aplicativo para evitar duplicação. Isso ajudará a reduzir a quantidade de XAML que precisa ser analisada em todo o aplicativo. O seguinte exemplo de código mostra o recurso `HeadingLabelStyle`, que é usado em todo o aplicativo e então é definido no dicionário de recursos do aplicativo:

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Resources.App">
     <Application.Resources>
         <ResourceDictionary>
            <Style x:Key="HeadingLabelStyle" TargetType="Label">
                <Setter Property="HorizontalOptions" Value="Center" />
                <Setter Property="FontSize" Value="Large" />
                <Setter Property="TextColor" Value="Red" />
            </Style>
         </ResourceDictionary>
     </Application.Resources>
</Application>
```

No entanto, o XAML específico de uma página não deve ser incluído no dicionário de recursos do aplicativo, uma vez que os recursos então serão analisados na inicialização do aplicativo, em vez de quando exigido por uma página. Se um recurso for usado por uma página que não seja a página de inicialização, ele deverá ser colocado no dicionário de recursos para essa página, ajudando, assim, a reduzir o XAML analisado quando o aplicativo é iniciado. O seguinte exemplo de código mostra o recurso `HeadingLabelStyle`, que está em apenas uma única página e então é definido no dicionário de recursos da página:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Test.HomePage"
             Padding="0,20,0,0">
    <ContentPage.Resources>
        <ResourceDictionary>
          <Style x:Key="HeadingLabelStyle" TargetType="Label">
              <Setter Property="HorizontalOptions" Value="Center" />
              <Setter Property="FontSize" Value="Large" />
              <Setter Property="TextColor" Value="Red" />
          </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

Para saber mais sobre os recursos de aplicativo, consulte [Estilos de XAML](~/xamarin-forms/user-interface/styles/xaml/index.md).

## <a name="use-the-custom-renderer-pattern"></a>Usar o padrão de renderizador personalizado

A maioria das Xamarin.Forms classes de renderizador expõe o `OnElementChanged` método, que é chamado quando um Xamarin.Forms controle personalizado é criado para renderizar o controle nativo correspondente. Então, classes de renderizador personalizadas em cada projeto da plataforma substituem esse método para instanciar e personalizar o controle nativo. O método `SetNativeControl` é usado para instanciar o controle nativo e esse método também atribuirá a referência de controle à propriedade `Control`.

No entanto, em algumas circunstâncias, o método `OnElementChanged` pode ser chamado várias vezes. Portanto, para evitar perdas de memória, que podem ter um impacto no desempenho, é necessário ter cuidado ao instanciar um novo controle nativo. A abordagem a ser usada ao instanciar um novo controle nativo em um renderizador personalizado é mostrada no exemplo de código a seguir:

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<NativeListView> e)
{
  base.OnElementChanged (e);

  if (e.OldElement != null)
  {
    // Unsubscribe from event handlers and cleanup any resources
  }

  if (e.NewElement != null)
  {
    if (Control == null)
    {
      // Instantiate the native control with the SetNativeControl method
    }
    // Configure the control and subscribe to event handlers
  }
}
```

Um novo controle nativo deve ser instanciado apenas uma vez, quando a propriedade `Control` é `null`. Além disso, o controle só deve ser criado, configurado e manipuladores de eventos assinados quando o renderizador personalizado é anexado a um novo Xamarin.Forms elemento. Da mesma forma, a inscrição de quaisquer manipuladores de evento inscritos só deve ser cancelada quando o elemento ao qual o renderizador está anexado for alterado. Adotar essa abordagem ajudará a criar um renderizador personalizado de desempenho eficiente que não sofra perdas de memória.

> [!IMPORTANT]
> O método `SetNativeControl` só deverá ser invocado se a propriedade `e.NewElement` não for `null` e a propriedade `Control` for `null`.

Para obter mais informações sobre renderizadores personalizados, consulte [Como personalizar controles em cada plataforma](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).

## <a name="related-links"></a>Links relacionados

- [Desempenho de plataforma cruzada](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [Compilando XAML](~/xamarin-forms/xaml/xamlc.md)
- [Associações compiladas](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md)
- [Renderizadores Rápidos](~/xamarin-forms/internals/fast-renderers.md)
- [Compactação de Layout](~/xamarin-forms/user-interface/layouts/layout-compression.md)
- [Xamarin.FormsShell](~/xamarin-forms/app-fundamentals/shell/index.md)
- [Xamarin.FormsCollectionView](~/xamarin-forms/user-interface/collectionview/index.md)
- [Desempenho de ListView](~/xamarin-forms/user-interface/listview/performance.md)
- [Otimizar recursos de imagem](~/cross-platform/deploy-test/memory-perf-best-practices.md#optimizeimages)
- [Estilos de XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
- [Personalizando controles em cada plataforma](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
