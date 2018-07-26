---
title: Progresso e indicadores de atividade no xamarin. IOS
description: Este documento discute como usar indicadores de progresso e a atividade no xamarin. IOS. Ele descreve como usá-los programaticamente e com um storyboard.
ms.prod: xamarin
ms.assetid: 7AA887E4-51F7-4867-82C5-A8D2EA48AE07
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 07/11/2017
ms.openlocfilehash: 24ad47bd37693eccf38033159acef72a9d4942be
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242173"
---
# <a name="progress-and-activity-indicators-in-xamarinios"></a>Progresso e indicadores de atividade no xamarin. IOS

É provável que seu aplicativo terá para a realização de longa execução de tarefas como carregar ou processamento de dados e que esse atraso pode causar um atraso na atualização de sua interface do usuário. Durante esse tempo, você sempre deve usar um indicador de progresso para reforçar o usuário que o sistema está ocupado fazendo o trabalho. Isso dá o controle de usuário que o aplicativo estiver funcionando em sua solicitação, o que ele não está aguardando sua entrada, e pode fornecer um meio de detalhando exatamente quanto tempo ele precisam esperar.

iOS fornece duas maneiras principais para fornecer essa indicação de progresso em seu aplicativo: indicadores de atividade (incluindo um determinado _rede_ indicador de atividade) e barras de progresso.

## <a name="activity-indicator"></a>Indicador de atividade

Indicadores de atividade devem ser mostrados quando o aplicativo é executado em um processo longo, mas você não souber o comprimento exato de tempo que a tarefa exigirá.

Apple tem as sugestões a seguir para trabalhar com indicadores de atividade:

- **Sempre que possível, progresso barras em vez disso, use** – porque um indicador de atividade fornece ao usuário feedback quanto tempo vai levar o processo em execução, sempre use uma barra de progresso, se o comprimento é conhecido (por exemplo, o número de bytes baixados em um arquivo).
- **Manter o indicador animado** -usuários relacionam um indicador de atividade estática para um aplicativo paralisado para que você deve ter sempre o indicador animado enquanto ele está sendo exibido.
- **Descrever a tarefa que está sendo processada** -exibir apenas o indicador de atividade por si só não é suficiente, o usuário precisa ser informado sobre o processo que estão aguardando. Inclua um rótulo significativo (geralmente uma frase simples e completa) que defina claramente a tarefa.

### <a name="implementing-an-activity-indicator"></a>Implementando um indicador de atividade

Um indicador de atividade é implementado por meio de [ `UIActivityIndictorView` ](https://developer.xamarin.com/api/type/UIKit.UIActivityIndicatorView/) classe para indicar que um `UIActivity` está ocorrendo.

### <a name="activity-indicators-and-storyboards"></a>Indicadores de atividade e storyboards

Se você estiver usando o Designer do iOS para criar sua interface do usuário, o indicador de atividade podem ser adicionado ao seu layout da caixa de ferramentas. As propriedades a seguir podem ser ajustadas do painel de propriedades:

![Painel de propriedades](progress-activity-indicator-images/progress-indicator1.png)

### <a name="managing-activity-indicator-behavior"></a>Gerenciando comportamento de indicador de atividade

Use o `StartAnimating()` e `StopAnimating()` métodos para iniciar e parar a animação do indicador de atividade.

Defina a `HidesWhenStopped` propriedade para `true` para fazer com que o indicador de atividade desaparecer depois `StopAnimating()` foi chamado. Isso é definido como `true` por padrão. A qualquer momento você pode ver se o indicador de atividade está executando sua animação de rotação, verificando o `IsAnimating` propriedade. 


### <a name="managing-activity-indicator-appearances"></a>Gerenciando aparências de indicador de atividade

O `UIActivityIndicatorViewStyle` enumeração pode ser passada como um parâmetro ao instanciar o indicador de atividade. Você pode usar isso para definir o estilo visual para `Gray`, `White`, ou `WhiteLarge`, por exemplo:

```csharp
activitySpinner = new UIActivityIndicatorView(UIActivityIndicatorViewStyle.WhiteLarge);
```

Você pode substituir a cor fornecida pela `UIActivityIndicatorViewStyle` definindo o `Color` propriedade.

## <a name="progress-bar"></a>Barra de Andamento

Apresenta uma barra de progresso como uma linha que é preenchido com cor para indicar o estado e o comprimento de uma tarefa demorada. Barras de progresso sempre deve ser usadas quando o comprimento das tarefas é saber ou pode ser computado.

Apple tem as sugestões a seguir para trabalhar com barras de progresso:

- **Relatar com precisão o progresso** -barras de progresso deve ser sempre uma representação precisa do tempo necessário para concluir uma tarefa. Nunca falsificar o tempo para fazer com que o aplicativo apareça ocupado.
- **Uso de durações Well-Defined** -barra de progresso não deve apenas Mostrar que uma tarefa demorada faz colocar, mas fornecem aos usuários e indicação de quanto da tarefa é concluída e uma estimativa do tempo restante.

### <a name="implementing-an-progress-bar"></a>Implementando uma barra de progresso

Uma barra de progresso é criar uma instância de um [`UIProgressView`](https://developer.xamarin.com/api/type/UIKit.UIProgressView/)

### <a name="progress-bars-and-storyboards"></a>Barras de progresso e Storyboards

Você também pode adicionar uma barra de progresso para a interface do usuário ao usar o Designer do iOS. Pesquise **modo de exibição do progresso** na **caixa de ferramentas** e arraste-o para o modo de exibição.

As propriedades a seguir podem ser ajustadas no painel de propriedades:

![Painel de propriedades](progress-activity-indicator-images/progress-indicator3.png)


### <a name="managing-progress-bar-behavior"></a>Gerenciando comportamento de barra de progresso

O andamento da barra de inicialmente pode ser definido usando o `Progress` propriedade:

```csharp
ProgressBar.Progress = 0f;
```

O progresso pode ser ajustado usando o `SetProgress` método e passar um valor booliano declarando se quiser que a alteração animada ou não.

```csharp
ProgressBar.SetProgress(1.0f, true);
```

Para obter mais informações sobre como usar a barra de progresso, consulte o [relatório de progresso](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/networking/download_progress) receita e o [UICatalog tvOS exemplo](https://developer.xamarin.com/samples/monotouch/tvos/UICatalog/).

### <a name="managing-progress-bar-appearance"></a>Gerenciando a aparência da barra de progresso

Semelhante a um indicador de atividade, o `UIProgressViewStyle` enumeração pode ser passada como um parâmetro ao instanciar a barra de progresso.

O progresso e a faixa de imagem e a cor da tonalidade podem ser ajustado usando as seguintes propriedades:

```csharp
progressBar = new UIProgressView(UIProgressViewStyle.Default)
            {
                ProgressImage = UIImage.FromBundle("TrackImage"),
                ProgressTintColor = UIColor.Cyan,
                TrackImage = UIImage.FromBundle("TrackImage"),
                TrackTintColor = UIColor.Magenta
            }; 
```



