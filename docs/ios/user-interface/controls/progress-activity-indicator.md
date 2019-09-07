---
title: Indicadores de progresso e atividade no Xamarin. iOS
description: Este documento discute como usar indicadores de progresso e atividade no Xamarin. iOS. Ele descreve como usá-los de forma programática e com um Storyboard.
ms.prod: xamarin
ms.assetid: 7AA887E4-51F7-4867-82C5-A8D2EA48AE07
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 07/11/2017
ms.openlocfilehash: a2197a1ff9c37546fd97eb5a2459764ec05d4412
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70768922"
---
# <a name="progress-and-activity-indicators-in-xamarinios"></a>Indicadores de progresso e atividade no Xamarin. iOS

É provável que seu aplicativo precise realizar tarefas de longa execução, como carregar ou processar dados e que esse atraso possa causar um atraso na atualização da interface do usuário. Durante esse tempo, você sempre deve usar um indicador de progresso para regarantir o usuário de que o sistema está ocupado funcionando. Isso fornece ao controle de usuário que o aplicativo está trabalhando em sua solicitação, que não está aguardando sua entrada e pode fornecer um meio de detalhar exatamente quanto tempo eles devem esperar.

o iOS fornece duas maneiras principais de fornecer essa indicação de progresso em seu aplicativo: Indicadores de atividade (incluindo um indicador de atividade de _rede_ específico) e barras de progresso.

## <a name="activity-indicator"></a>Indicador de atividade

Os indicadores de atividade devem ser mostrados quando seu aplicativo estiver executando um longo processo, mas você não souber o tempo exato que a tarefa exigirá.

A Apple tem as seguintes sugestões para trabalhar com indicadores de atividade:

- **Sempre que possível, use as barras de progresso** – como um indicador de atividade fornece ao usuário nenhum comentário sobre quanto tempo o processo será executado, sempre use uma barra de progresso se o comprimento for conhecido (por exemplo, quantos bytes serão baixados em um arquivo).
- **Mantenha o indicador animado** -os usuários relacionam um indicador de atividade de imóvel a um aplicativo interrompido para que você sempre tenha o indicador animado enquanto ele está sendo exibido.
- **Descrever a tarefa que está sendo processada** – apenas exibindo o indicador de atividade por si só não é suficiente, o usuário precisa ser informado sobre o processo em que estão aguardando. Inclua um rótulo significativo (geralmente uma frase única, completa) que define claramente a tarefa.

### <a name="implementing-an-activity-indicator"></a>Implementando um indicador de atividade

Um indicador de atividade é implementado por [`UIActivityIndictorView`](xref:UIKit.UIActivityIndicatorView) meio da classe para indicar `UIActivity` que está ocorrendo.

### <a name="activity-indicators-and-storyboards"></a>Indicadores de atividade e storyboards

Se você estiver usando o designer do iOS para criar sua interface do usuário, o indicador de atividade poderá ser adicionado ao layout na caixa de ferramentas. As propriedades a seguir podem ser ajustadas da Painel de Propriedades:

![Painel de Propriedades](progress-activity-indicator-images/progress-indicator1.png)

### <a name="managing-activity-indicator-behavior"></a>Gerenciamento do comportamento do indicador de atividade

Use os `StartAnimating()` métodos `StopAnimating()` e para iniciar e parar a animação do indicador de atividade.

Defina a `HidesWhenStopped` Propriedade como `true` para fazer com que o indicador de `StopAnimating()` atividade desapareça após ter sido chamado. Isso é definido como `true` por padrão. A qualquer momento, você pode ver se o indicador de atividade está executando sua animação de rotação `IsAnimating` verificando a propriedade. 

### <a name="managing-activity-indicator-appearances"></a>Gerenciando aparências do indicador de atividade

A `UIActivityIndicatorViewStyle` enumeração pode ser passada como um parâmetro ao instanciar o indicador de atividade. Você pode usar isso para definir o estilo visual como `Gray`, `White`ou `WhiteLarge`, por exemplo:

```csharp
activitySpinner = new UIActivityIndicatorView(UIActivityIndicatorViewStyle.WhiteLarge);
```

Você pode substituir a cor fornecida pelo `UIActivityIndicatorViewStyle` definindo a `Color` propriedade.

## <a name="progress-bar"></a>Barra de Andamento

Uma barra de progresso apresenta uma linha que preenche a cor para indicar o estado e o comprimento de uma tarefa demorada. As barras de progresso sempre devem ser usadas quando o comprimento das tarefas é conhecido ou podem ser computadas.

A Apple tem as seguintes sugestões para trabalhar com barras de progresso:

- **Relatar com precisão o progresso** -as barras de progresso sempre devem ser uma representação precisa do tempo necessário para concluir uma tarefa. Nunca simule o tempo para que o aplicativo pareça estar ocupado.
- **Use para durações bem definidas** -a barra de progresso não deve mostrar apenas que uma tarefa demorada está ocorrendo, mas forneça ao usuário e a indicação de quanto da tarefa foi concluída e uma estimativa do tempo restante.

### <a name="implementing-an-progress-bar"></a>Implementando uma barra de progresso

Uma barra de progresso é criada instanciando-se um[`UIProgressView`](xref:UIKit.UIProgressView)

### <a name="progress-bars-and-storyboards"></a>Barras de progresso e storyboards

Você também pode adicionar uma barra de progresso à sua interface do usuário ao usar o designer do iOS. Pesquise o **modo de exibição de progresso** na **caixa de ferramentas** e arraste-o para sua exibição.

As propriedades a seguir podem ser ajustadas no painel de propriedades:

![Painel de Propriedades](progress-activity-indicator-images/progress-indicator3.png)

### <a name="managing-progress-bar-behavior"></a>Gerenciamento do comportamento da barra de progresso

O progresso da barra pode ser inicialmente definido usando a `Progress` Propriedade:

```csharp
ProgressBar.Progress = 0f;
```

O progresso pode ser ajustado usando o `SetProgress` método e passando um booliano declarando se você deseja que a alteração seja animada ou não.

```csharp
ProgressBar.SetProgress(1.0f, true);
```

Para obter mais informações sobre como usar a barra de progresso, consulte a receita de [progresso de relatórios](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/networking/download_progress) e o exemplo de [tvOS de UICatalog](https://docs.microsoft.com/samples/xamarin/ios-samples/tvos-uicatalog).

### <a name="managing-progress-bar-appearance"></a>Gerenciando a aparência da barra de progresso

Semelhante a um indicador de atividade, `UIProgressViewStyle` a enumeração pode ser passada como um parâmetro ao instanciar a barra de progresso.

A imagem de progresso e de rastreamento e a cor de tonalidade podem ser ajustadas usando as seguintes propriedades:

```csharp
progressBar = new UIProgressView(UIProgressViewStyle.Default)
            {
                ProgressImage = UIImage.FromBundle("TrackImage"),
                ProgressTintColor = UIColor.Cyan,
                TrackImage = UIImage.FromBundle("TrackImage"),
                TrackTintColor = UIColor.Magenta
            }; 
```
