---
title: Progresso e indicadores de atividade
ms.topic: article
ms.prod: xamarin
ms.assetid: 7AA887E4-51F7-4867-82C5-A8D2EA48AE07
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 07/11/2017
ms.openlocfilehash: b91c0d7504b391630beded7a52e240a0d043152f
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="progress-and-activity-indicators"></a>Progresso e indicadores de atividade

É provável que o seu aplicativo precisa executar longa executando tarefas como carregar ou processamento de dados e que esse atraso pode causar um atraso ao atualizar a interface do usuário. Durante esse tempo, você sempre deve usar um indicador de progresso para garantir que o usuário que o sistema está ocupado realizando um trabalho. Isso fornece o controle de usuário que o aplicativo está funcionando em sua solicitação, que não está aguardando sua entrada, e pode fornecer um meio de detalhando exatamente quanto tempo eles precisam esperar.

iOS fornece duas maneiras de fornecer essa indicação de progresso em seu aplicativo: indicadores de atividade (incluindo um determinado _rede_ indicador de atividade) e barras de progresso.

## <a name="activity-indicator"></a>Indicador de atividade

Indicadores de atividade devem ser mostrados quando seu aplicativo estiver executando um processo longo, mas você não souber o comprimento exato de tempo que exigirá que a tarefa.

Apple tem as seguintes sugestões para trabalhar com indicadores de atividade:

- **Sempre que possível, progresso barras em vez disso, use** – porque um indicador de atividade não dá ao usuário nenhum feedback quanto tempo o processo que está sendo executado levará, sempre use uma barra de progresso se o comprimento é conhece (por exemplo, o número de bytes a ser baixados em um arquivo).
- **Manter o indicador animado** -os usuários relacionar um indicador de atividade estática para um aplicativo paralisado para que você sempre deve ter o indicador animado enquanto ele está sendo exibido.
- **Descrever a tarefa que está sendo processada** -exibir apenas o indicador de atividade por si só não é suficiente, o usuário precisa ser informado sobre o processo que estão aguardando. Inclua um rótulo significativo (geralmente uma frase simples e completa) que define claramente a tarefa.

### <a name="implementing-an-activity-indicator"></a>Implementando um indicador de atividade

Um indicador de atividade é implementado por meio de [ `UIActivityIndictorView` ](https://developer.xamarin.com/api/type/UIKit.UIActivityIndicatorView/) classe para indicar que um `UIActivity` está ocorrendo.

### <a name="activity-indicators-and-storyboards"></a>Storyboards e indicadores de atividade

Se você estiver usando o Designer do iOS para criar sua interface do usuário, o indicador de atividade pode ser adicionado ao seu layout da caixa de ferramentas. As seguintes propriedades podem ser ajustadas de painel de propriedades:

![Painel de propriedades](progress-activity-indicator-images/progress-indicator1.png)

### <a name="managing-activity-indicator-behavior"></a>Gerenciando comportamento de indicador de atividade

Use o `StartAnimating()` e `StopAnimating()` métodos para iniciar e parar a animação do indicador de atividade.

Definir o `HidesWhenStopped` propriedade `true` para fazer com que o indicador de atividade desaparecer depois `StopAnimating()` foi chamado. Isso é definido como `true` por padrão. A qualquer momento, você pode ver se o indicador de atividade está em execução a animação giratório verificando o `IsAnimating` propriedade. 


### <a name="managing-activity-indicator-appearances"></a>Gerenciando a aparência do indicador de atividade

O `UIActivityIndicatorViewStyle` enumeração pode ser passada como um parâmetro ao instanciar o indicador de atividade. Você pode usar isso para definir o estilo visual para `Gray`, `White`, ou `WhiteLarge`, por exemplo:

```csharp
activitySpinner = new UIActivityIndicatorView(UIActivityIndicatorViewStyle.WhiteLarge);
```

Você pode substituir a cor fornecida pelo `UIActivityIndicatorViewStyle` definindo o `Color` propriedade.

## <a name="progress-bar"></a>Barra de Andamento

Apresenta uma barra de progresso como uma linha que preenche com cor para indicar o estado e o comprimento de uma tarefa demorada. Barras de progresso sempre deve ser usadas quando o comprimento das tarefas é saber ou pode ser calculado.

Apple tem as seguintes sugestões para trabalhar com barras de progresso:

- **Relatar com precisão o progresso** -barras de progresso deve sempre ser uma representação precisa do tempo necessário para concluir uma tarefa. Nunca simule o tempo para tornar o aplicativo aparecer ocupado.
- **Use para durações Well-Defined** -barra de progresso não deve apenas exibir que uma tarefa demorada está levando colocar, mas fornece uma estimativa do tempo restante e o usuário e indicação de quanto da tarefa é concluída.

### <a name="implementing-an-progress-bar"></a>Implementando uma barra de progresso

Uma barra de progresso é criada pela instanciação de um [`UIProgressView`](https://developer.xamarin.com/api/type/UIKit.UIProgressView/)

### <a name="progress-bars-and-storyboards"></a>Barras de progresso e Storyboards

Você também pode adicionar uma barra de progresso para a interface do usuário ao usar o Designer do iOS. Procurar **andamento** no **caixa de ferramentas** e arraste-o para o modo de exibição.

As seguintes propriedades podem ser ajustadas no painel de propriedades:

![Painel de propriedades](progress-activity-indicator-images/progress-indicator3.png)


### <a name="managing-progress-bar-behavior"></a>Gerenciando comportamento de barra de progresso

O andamento da barra inicialmente pode ser definido usando o `Progress` propriedade:

```csharp
ProgressBar.Progress = 0f;
```

O progresso pode ser ajustado usando a `SetProgress` método e passando um valor booleano declarando se você quiser que a alteração de animação ou não.

```csharp
ProgressBar.SetProgress(1.0f, true);
```

Para obter mais informações sobre como usar a barra de progresso, consulte o [relatório de andamento](https://developer.xamarin.com/recipes/cross-platform/networking/download_progress/#Reporting_Progress_in_iOS) receita e o [UICatalog tvOS exemplo](https://developer.xamarin.com/samples/monotouch/tvos/UICatalog/).

### <a name="managing-progress-bar-appearance"></a>Gerenciando a aparência da barra de progresso

Semelhante a um indicador de atividade, o `UIProgressViewStyle` enumeração pode ser passada como um parâmetro ao instanciar a barra de progresso.

A imagem de acompanhar e cor do matiz e progresso podem ser ajustado usando as seguintes propriedades:

```csharp
progressBar = new UIProgressView(UIProgressViewStyle.Default)
            {
                ProgressImage = UIImage.FromBundle("TrackImage"),
                ProgressTintColor = UIColor.Cyan,
                TrackImage = UIImage.FromBundle("TrackImage"),
                TrackTintColor = UIColor.Magenta
            }; 
```



