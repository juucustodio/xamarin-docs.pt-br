---
title: Melhorar o Desempenho do Aplicativo Xamarin.Forms
description: Há muitas técnicas para aumentar o desempenho de aplicativos Xamarin.Forms. Coletivamente, essas técnicas podem reduzir de forma considerável a quantidade de trabalho que está sendo executado por uma CPU e a quantidade de memória consumida por um aplicativo.
ms.prod: xamarin
ms.assetid: 0be84c56-6698-448d-be5a-b4205f1caa9f
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/27/2019
ms.openlocfilehash: 4427d347723284a2f8897612f10857270c9631bf
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "79305209"
---
# <a name="improve-xamarinforms-app-performance"></a>Melhorar o Desempenho do Aplicativo Xamarin.Forms

> [!VIDEO https://youtube.com/embed/RZvdql3Ev0E]

**Evoluir 2016: como otimizar o desempenho do aplicativo com o Xamarin.Forms**

O baixo desempenho de aplicativo se apresenta de várias maneiras. Ele pode fazer com que o aplicativo pareça não responder, deixar a rolagem lenta e reduzir a vida útil da bateria do dispositivo. No entanto, a otimização do desempenho engloba mais do que apenas a implementação de um código eficiente. A experiência do usuário quanto ao desempenho do aplicativo também deve ser considerada. Por exemplo, garantir que as operações sejam executadas sem impedir o usuário de realizar outras atividades pode ajudar a melhorar a experiência do usuário.

Há muitas técnicas para aumentar o desempenho, bem como o desempenho percebido, de aplicativos Xamarin.Forms. Coletivamente, essas técnicas podem reduzir de forma considerável a quantidade de trabalho que está sendo executado por uma CPU e a quantidade de memória consumida por um aplicativo.

> [!NOTE]
>  Antes de ler esse artigo, você deve primeiro ler [Desempenho de plataforma cruzada](~/cross-platform/deploy-test/memory-perf-best-practices.md), que discute técnicas específicas sem plataforma para melhorar o uso de memória e o desempenho de aplicativos criados usando a plataforma Xamarin.

## <a name="enable-the-xaml-compiler"></a>Habilitar o compilador de XAML

Opcionalmente, XAML pode ser compilado direto na IL (linguagem intermediária) com o compilador XAML (XAMLC). XAMLC oferece vários benefícios:

- Executa verificação de tempo de compilação de XAML, notificando o usuário de quaisquer erros.
- Elimina parte da carga e do tempo de instanciação para elementos XAML.
- Ajuda a reduzir o tamanho do arquivo do assembly final não incluindo mais arquivos .XAML.

O XAMLC é habilitado por padrão em novas soluções de Xamarin.Forms. No entanto, talvez seja necessário habilitá-lo em soluções mais antigas. Para saber mais, consulte [Compilação de XAML](~/xamarin-forms/xaml/xamlc.md).

## <a name="use-compiled-bindings"></a>Usar associações compiladas

Associações compiladas melhoram o desempenho da associação de dados em aplicativos Xamarin.Forms resolvendo expressões de associação em tempo de compilação, e não em runtime com reflexão. A compilação de uma expressão de associação gera código compilado que normalmente resolve uma associação 8 a 20 vezes mais rápido do que ao usar uma associação clássica. Para saber mais, confira [Associações compiladas](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md).

## <a name="reduce-unnecessary-bindings"></a>Reduzir associações desnecessárias

Não use associações para conteúdo que pode ser facilmente definido estaticamente. Não há nenhuma vantagem em associar dados que não precisam ser associados, pois associações não são econômicas. Por exemplo, `Button.Text = "Accept"` a configuração [`Button.Text`](xref:Xamarin.Forms.Button.Text) tem menos `string` sobrecarga do que vincular a uma propriedade de modelo de exibição com o valor "Aceitar".

## <a name="use-fast-renderers"></a>Usar renderizadores rápidos

Os renderizadores rápidos reduzem os custos de renderização e inflação dos controles Xamarin.Forms no Android nivelando a hierarquia resultante de controle nativo. Isso aprimora o desempenho criando menos objetos, o que resulta em uma árvore visual menos complexa e em menos uso de memória.

Do Xamarin.Forms 4.0 em diante, todos os aplicativos destinados a `FormsAppCompatActivity` usam renderizadores rápidos por padrão. Para obter mais informações, veja [Renderizadores Rápidos](~/xamarin-forms/internals/fast-renderers.md).

## <a name="enable-startup-tracing-on-android"></a>Habilitar o rastreamento de inicialização no Android

A compilação AOT (Ahead of Time) no Android minimiza a sobrecarga e o uso de memória da inicialização de aplicativo JIT (Just-in-Time), com o custo de criar um APK muito maior. Uma alternativa é usar o rastreamento de inicialização, que proporciona uma compensação entre o tamanho do APK do Android e o tempo de inicialização, quando comparado à compilação AOT convencional.

Em vez de compilar o máximo possível do aplicativo para código não gerenciado, o rastreamento de inicialização compila apenas o conjunto de métodos gerenciados que representam as partes mais caras da inicialização do aplicativo em um aplicativo Xamarin.Forms em branco. Essa abordagem resulta na redução do tamanho do APK, quando comparado à compilação AOT convencional, enquanto ainda fornece melhorias de inicialização semelhantes.

## <a name="enable-layout-compression"></a>Habilitar a compactação de layout

A compactação de layout remove os layouts especificados da árvore visual, em uma tentativa de melhorar o desempenho de renderização da página. O benefício de desempenho que isso oferece varia dependendo da complexidade de uma página, da versão do sistema operacional que está sendo usado e do dispositivo no qual o aplicativo está sendo executado. No entanto, os maiores ganhos de desempenho serão observados em versões mais antigas. Para obter mais informações, confira [Layout de Compactação](~/xamarin-forms/user-interface/layouts/layout-compression.md).

## <a name="choose-the-correct-layout"></a>Escolher o layout correto

Um layout que é capaz de exibir vários filhos, mas que tem apenas um único filho, é um desperdício. Por exemplo, o exemplo [`StackLayout`](xref:Xamarin.Forms.StackLayout) de código a seguir mostra um com um único filho:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DisplayImage.HomePage">
    <StackLayout>
        <Image Source="waterfront.jpg" />
    </StackLayout>
</ContentPage>
```

Isso é um [`StackLayout`](xref:Xamarin.Forms.StackLayout) desperdício e o elemento deve ser removido, como mostrado no exemplo de código a seguir:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DisplayImage.HomePage">
    <Image Source="waterfront.jpg" />
</ContentPage>
```

Além disso, não tente reproduzir a aparência de um layout específico usando combinações de outros layouts, pois isso resulta na execução de cálculos de layout desnecessários. Por exemplo, não tente reproduzir [`Grid`](xref:Xamarin.Forms.Grid) um layout usando [`StackLayout`](xref:Xamarin.Forms.StackLayout) uma combinação de instâncias. O código a seguir mostra um exemplo dessa má prática:

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

Isso é um desperdício porque cálculos de layout desnecessário são executados. Em vez disso, o layout desejado [`Grid`](xref:Xamarin.Forms.Grid)pode ser melhor alcançado usando um , como mostrado no exemplo de código a seguir:

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

- Reduza a profundidade das hierarquias de layout especificando [`Margin`](xref:Xamarin.Forms.View.Margin) valores de propriedade, permitindo a criação de layouts com menos visualizações de embrulho. Para saber mais, consulte [Margens e preenchimento](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).
- Ao usar [`Grid`](xref:Xamarin.Forms.Grid)um , tente garantir que o máximo de [`Auto`](xref:Xamarin.Forms.GridLength.Auto) linhas e colunas possíveis sejam definidos como tamanho. Cada linha ou coluna dimensionada automaticamente fará o mecanismo de layout realizar cálculos de layout adicionais. Em vez disso, use linhas e colunas de tamanho fixo, se possível. Alternativamente, defina linhas e colunas para ocupar [`GridUnitType.Star`](xref:Xamarin.Forms.GridUnitType.Star) uma quantidade proporcional de espaço com o valor de enumeração, desde que a árvore-mãe siga essas diretrizes de layout.
- Não defina [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) as [`HorizontalOptions`](xref:Xamarin.Forms.View.VerticalOptions) propriedades de um layout a menos que seja necessário. Os valores [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill) [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) padrão de e permitem a melhor otimização de layout. Alterar essas propriedades tem um custo e consome memória, mesmo ao configurá-las como os valores padrão.
- Evite usar [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) um sempre que possível. Isso resultará em a CPU precisar realizar significativamente mais trabalho.
- Ao usar [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)um , [`AbsoluteLayout.AutoSize`](xref:Xamarin.Forms.AbsoluteLayout.AutoSize) evite usar a propriedade sempre que possível.
- Ao usar [`StackLayout`](xref:Xamarin.Forms.StackLayout)um , certifique-se [`LayoutOptions.Expands`](xref:Xamarin.Forms.LayoutOptions.Expands)de que apenas uma criança está definida para . Essa propriedade garante que o filho especificado ocupe o maior espaço que o `StackLayout` pode dar a ele e é um desperdício executar esses cálculos mais de uma vez.
- Evite chamar qualquer um dos [`Layout`](xref:Xamarin.Forms.Layout) métodos da classe, pois eles resultam em cálculos de layout caros sendo realizados. Em vez disso, é provável que o comportamento de [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) layout [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) desejado possa ser obtido definindo as propriedades e as propriedades. Alternativamente, subclassificar a [`Layout<View>`](xref:Xamarin.Forms.Layout`1) classe para alcançar o comportamento de layout desejado.
- Não atualize [`Label`](xref:Xamarin.Forms.Label) nenhuma instância com mais freqüência do que o necessário, pois a mudança de tamanho do rótulo pode resultar na recálculo do layout da tela.
- Não defina [`Label.VerticalTextAlignment`](xref:Xamarin.Forms.Label.VerticalTextAlignment) a propriedade a menos que seja necessário.
- Defina [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) as [`Label`](xref:Xamarin.Forms.Label) instâncias sempre [`NoWrap`](xref:Xamarin.Forms.LineBreakMode.NoWrap) que possível.

## <a name="use-asynchronous-programming"></a>usar programação assíncrona

A capacidade de resposta geral do seu aplicativo pode ser aprimorada, e gargalos de desempenho muitas vezes evitados, usando programação assíncrona. Em .NET, o [Padrão Assíncrono (TAP) baseado em tarefas](/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap) é o padrão de design recomendado para operações assíncronas. No entanto, o uso incorreto do TAP pode resultar em aplicativos não performáticos. Portanto, as seguintes orientações devem ser seguidas ao usar o TAP.

### <a name="fundamentals"></a>Conceitos básicos

- Entenda o ciclo de vida da `TaskStatus` tarefa, que é representado pela enumeração. Para obter mais informações, consulte [O significado de TaskStatus](https://devblogs.microsoft.com/pfxteam/the-meaning-of-taskstatus/) e [Task status](/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap#task-status).
- Use `Task.WhenAll` o método para esperar assíncronamente por várias operações `await` assíncronas para terminar, em vez de individualmente uma série de operações assíncronas. Para obter mais informações, consulte [Task.WhenAll](/dotnet/standard/asynchronous-programming-patterns/consuming-the-task-based-asynchronous-pattern#taskwhenall).
- Use `Task.WhenAny` o método para esperar assíncronamente por uma das múltiplas operações assíncronas para terminar. Para obter mais informações, consulte [Task.WhenAny](/dotnet/standard/asynchronous-programming-patterns/consuming-the-task-based-asynchronous-pattern#taskwhenall).
- Use `Task.Delay` o método `Task` para produzir um objeto que termine após o tempo especificado. Isso é útil para cenários como a pesquisa de dados e o atraso no manuseio da entrada do usuário por um tempo pré-determinado. Para obter mais informações, consulte [Task.Delay](/dotnet/standard/asynchronous-programming-patterns/consuming-the-task-based-asynchronous-pattern#taskdelay).
- Execute operações intensivas de CPU síncrona no pool de segmentos com o `Task.Run` método. Este método é um `TaskFactory.StartNew` atalho para o método, com os argumentos mais ideais definidos. Para obter mais informações, consulte [Task.Run](/dotnet/standard/asynchronous-programming-patterns/consuming-the-task-based-asynchronous-pattern#taskrun).
- Evite tentar criar construtores assíncronos. Em vez disso, use eventos do ciclo `await` de vida ou lógica de inicialização separada para corretamente qualquer inicialização. Para obter mais informações, consulte [Async Constructors](https://blog.stephencleary.com/2013/01/async-oop-2-constructors.html) on blog.stephencleary.com.
- Use o padrão de tarefa preguiçoso para evitar esperar que as operações assíncronas se completem durante a inicialização do aplicativo. Para obter mais informações, consulte [AsyncLazy](https://devblogs.microsoft.com/pfxteam/asynclazyt/).
- Crie um invólucro de tarefa para operações assíncronas `TaskCompletionSource<T>` existentes, que não use o TAP, criando objetos. Esses objetos ganham `Task` os benefícios da programabilidade e permitem controlar `Task`a vida útil e a conclusão dos associados. Para obter mais informações, consulte [A Natureza da Conclusão de TarefasFonte](https://devblogs.microsoft.com/pfxteam/the-nature-of-taskcompletionsourcetresult/).
 
- Devolva um `Task` objeto, em `Task` vez de retornar um objeto esperado, quando não há necessidade de processar o resultado de uma operação assíncrona. Isso é mais performático devido à menor mudança de contexto sendo realizada.
- Use a biblioteca tpl (Task Parallel Library) Dataflow em cenários como o processamento de dados à medida que ele se torna disponível ou quando você tiver várias operações que devem se comunicar assíncronamente. Para obter mais informações, consulte [Dataflow (Task Parallel Library)](/dotnet/standard/parallel-programming/dataflow-task-parallel-library).

### <a name="ui"></a>UI

- Chame uma versão assíncrona de uma API, se estiver disponível. Isso manterá o thread de interface do usuário desbloqueado, o que ajudará a melhorar a experiência do usuário com o aplicativo.
- Atualize elementos de UI com dados de operações assíncronas no segmento de IA, para evitar que exceções sejam lançadas. No entanto, `ListView.ItemsSource` as atualizações da propriedade serão automaticamente empacotadas para o segmento de ida e futura. Para obter informações sobre como determinar se o código está sendo executado no segmento de ia de usuário, consulte [Xamarin.Essentials: MainThread](~/essentials/main-thread.md?content=xamarin/xamarin-forms).

    > [!IMPORTANT]
    > Quaisquer propriedades de controle atualizadas via vinculação de dados serão automaticamente empacotadas para o segmento de IA.

### <a name="error-handling"></a>Tratamento de erros

- Saiba mais sobre o manuseio de exceções assíncronas. Exceções não manuseadas que são lançadas por código que está sendo executado assíncronamente são propagadas de volta para o segmento de chamada, exceto em certos cenários. Para obter mais informações, consulte [O manuseio de exceção (Biblioteca Paralela de Tarefas)](/dotnet/standard/parallel-programming/exception-handling-task-parallel-library).
- Evite `async void` criar métodos e, em vez disso, crie `async Task` métodos. Isso permite uma manipulação mais fácil de erros, composibilidade e testabilidade. A exceção a esta diretriz são os manipuladores `void`de eventos assíncronos, que devem retornar . Para obter mais informações, consulte [Evite O Vazio Assincrático](/archive/msdn-magazine/2013/march/async-await-best-practices-in-asynchronous-programming#avoid-async-void).
- Não misture o bloqueio e o código `Task.Wait`assíncrono chamando os métodos `Task.Result`, ou `GetAwaiter().GetResult` métodos, pois eles podem resultar em impasse ocorrendo. No entanto, se essa diretriz deve ser violada, a abordagem preferida é chamar o `GetAwaiter().GetResult` método porque preserva as exceções da tarefa. Para obter mais informações, consulte [Async All the Way](/archive/msdn-magazine/2013/march/async-await-best-practices-in-asynchronous-programming#async-all-the-way) e [Task Exception Handling em .NET 4.5](https://devblogs.microsoft.com/pfxteam/task-exception-handling-in-net-4-5/).
- Use `ConfigureAwait` o método sempre que possível, para criar código livre de contexto. O código sem contexto tem melhor desempenho para aplicativos móveis e é uma técnica útil para evitar impasse ao trabalhar com uma base de código parcialmente assíncrona. Para obter mais informações, consulte [Configurar contexto](/archive/msdn-magazine/2013/march/async-await-best-practices-in-asynchronous-programming#configure-context).
- Use *tarefas de continuação* para funcionalidade, como lidar com exceções lançadas pela operação assíncrona anterior e cancelar uma continuação antes de começar ou enquanto estiver em execução. Para obter mais informações, consulte [Tarefas de encadeamento usando tarefas contínuas](/dotnet/standard/parallel-programming/chaining-tasks-by-using-continuation-tasks).
- Use uma implementação `ICommand` assíncrona quando as operações assíncronas forem invocadas a partir do `ICommand`. Isso garante que quaisquer exceções na lógica de comando assíncrono possam ser tratadas. Para obter mais informações, consulte [Programação Assincronte: Padrões para aplicações Assíncronas MVVM: Comandos](/archive/msdn-magazine/2014/april/async-programming-patterns-for-asynchronous-mvvm-applications-commands).

## <a name="choose-a-dependency-injection-container-carefully"></a>Escolha o contêiner de injeção de dependência com cuidado

Contêineres de injeção de dependência introduzem restrições de desempenho adicionais em aplicativos móveis. Efetuar o registro e a resolução de tipos usando um contêiner tem um custo de desempenho devido ao uso da reflexão pelo contêiner para criar cada tipo, especialmente se as dependências estiverem sendo reconstruídas para cada navegação de página no aplicativo. Se houver muitas dependências ou se elas forem profundas, o custo da criação poderá aumentar significativamente. Além disso, o registro de tipo, que geralmente ocorre durante a inicialização do aplicativo, pode ter um impacto perceptível sobre o tempo de inicialização dependendo do contêiner que está sendo usado.

Como alternativa, a injeção de dependência pode se tornar mais eficaz por meio da implementação manual usando fábricas.

## <a name="create-shell-applications"></a>Criar aplicativos de Shell

Aplicativos do Xamarin.Forms em Shell fornecem uma experiência de navegação obstinada, com base em guias e submenus. Se a experiência do usuário do aplicativo puder ser implementada com Shell, será benéfico fazê-lo. Aplicativos de Shell ajudam a evitar uma experiência de inicialização ruim, pois as páginas são criadas sob demanda em resposta à navegação, e não na inicialização do aplicativo, o que ocorre com aplicativos que usam uma [`TabbedPage'](xref:Xamarin.Forms.TabbedPage). Para obter mais informações, consulte [Xamarin.Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md).

## <a name="use-collectionview-instead-of-listview"></a>Usar CollectionView em vez de ListView

[`CollectionView`](xref:Xamarin.Forms.CollectionView)é uma exibição para apresentar listas de dados usando diferentes especificações de layout. Ele fornece uma alternativa mais flexível [`ListView`](xref:Xamarin.Forms.ListView)e performática para . Para obter mais informações, confira [CollectionView de Xamarin.Forms](~/xamarin-forms/user-interface/collectionview/index.md).

## <a name="optimize-listview-performance"></a>Otimizar o desempenho da ListView

Ao [`ListView`](xref:Xamarin.Forms.ListView)usar, há uma série de experiências de usuário que devem ser otimizadas:

- **Inicialização** – o intervalo de tempo que começa quando o controle é criado e termina quando itens são mostrados na tela.
- **Rolagem** – a capacidade de rolar pela lista e garantir que a interface do usuário não fique atrasada com relação a gestos de toque.
- **Interação** para adicionar, excluir e selecionar itens.

O [`ListView`](xref:Xamarin.Forms.ListView) controle requer um aplicativo para fornecer dados e modelos de celular. Como isso é feito terá um grande impacto sobre o desempenho do controle. Para obter mais informações, consulte [Desempenho da ListView](~/xamarin-forms/user-interface/listview/performance.md).

## <a name="optimize-image-resources"></a>Otimizar os recursos de imagem

Exibir recursos de imagem pode aumentar significativamente o volume de memória de um aplicativo. Portanto, eles só devem ser criados quando necessário e devem ser liberados assim que o aplicativo não exigi-los mais. Por exemplo, se um aplicativo estiver exibindo uma imagem lendo seus dados de um fluxo, certifique-se de que esse fluxo seja criado somente quando necessário e liberado quando não for mais necessário. Isso pode ser alcançado criando o fluxo quando a [`Page.Appearing`](xref:Xamarin.Forms.Page.Appearing) página é criada, ou quando o [`Page.Disappearing`](xref:Xamarin.Forms.Page.Disappearing) evento é acionado e, em seguida, eliminando o fluxo quando o evento é acionado.

Ao baixar uma imagem para [`ImageSource.FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri)) exibição com o método, cacheie a imagem baixada, garantindo que a [`UriImageSource.CachingEnabled`](xref:Xamarin.Forms.UriImageSource.CachingEnabled) propriedade esteja definida como `true`. Para saber mais, consulte [Trabalhando com imagens](~/xamarin-forms/user-interface/images.md).

Para saber mais, consulte [Otimizar recursos de imagem](~/cross-platform/deploy-test/memory-perf-best-practices.md#optimizeimages).

## <a name="reduce-the-visual-tree-size"></a>Reduzir o tamanho da árvore visual

Reduzir o número de elementos em uma página tornará a renderização da página mais rápida. Há duas técnicas principais para realizar essa tarefa. A primeira é ocultar elementos que não estão visíveis. A [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible) propriedade de cada elemento determina se o elemento deve fazer parte da árvore visual ou não. Portanto, se um elemento não estiver visível porque está oculto atrás de outros elementos, remova o elemento ou defina sua propriedade `IsVisible` como `false`.

A segunda técnica é remover elementos desnecessários. Por exemplo, o exemplo de código a [`Label`](xref:Xamarin.Forms.Label) seguir mostra um layout de página contendo vários objetos:

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

A maioria das classes de renderizador do Xamarin.Forms expõe o método `OnElementChanged`, que é chamado quando um controle personalizado do Xamarin.Forms é criado para renderizar o controle nativo correspondente. Então, classes de renderizador personalizadas em cada projeto da plataforma substituem esse método para instanciar e personalizar o controle nativo. O método `SetNativeControl` é usado para instanciar o controle nativo e esse método também atribuirá a referência de controle à propriedade `Control`.

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

Um novo controle nativo deve ser instanciado apenas uma vez, quando a propriedade `Control` é `null`. Além disso, o controle só deve ser criado e configurado e os manipuladores de eventos devem ser inscritos apenas quando o renderizador personalizado for anexado a um novo elemento Xamarin.Forms. Da mesma forma, a inscrição de quaisquer manipuladores de evento inscritos só deve ser cancelada quando o elemento ao qual o renderizador está anexado for alterado. Adotar essa abordagem ajudará a criar um renderizador personalizado de desempenho eficiente que não sofra perdas de memória.

> [!IMPORTANT]
> O método `SetNativeControl` só deverá ser invocado se a propriedade `e.NewElement` não for `null` e a propriedade `Control` for `null`.

Para obter mais informações sobre renderizadores personalizados, consulte [Como personalizar controles em cada plataforma](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).

## <a name="related-links"></a>Links relacionados

- [Desempenho de plataforma cruzada](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [Compilando XAML](~/xamarin-forms/xaml/xamlc.md)
- [Associações compiladas](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md)
- [Renderizadores Rápidos](~/xamarin-forms/internals/fast-renderers.md)
- [Compactação de Layout](~/xamarin-forms/user-interface/layouts/layout-compression.md)
- [Shell do Xamarin.Forms](~/xamarin-forms/app-fundamentals/shell/index.md)
- [CollectionView de Xamarin.Forms](~/xamarin-forms/user-interface/collectionview/index.md)
- [Desempenho de ListView](~/xamarin-forms/user-interface/listview/performance.md)
- [Otimizar recursos de imagem](~/cross-platform/deploy-test/memory-perf-best-practices.md#optimizeimages)
- [Estilos de XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
- [Personalizando controles em cada plataforma](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
