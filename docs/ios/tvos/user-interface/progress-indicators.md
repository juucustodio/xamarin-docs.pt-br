---
title: Trabalhando com indicadores de progresso tvOS no Xamarin
description: Este documento descreve como trabalhar com indicadores de progresso em um aplicativo tvOS criado com o Xamarin. Ele aborda as barras de progresso e os indicadores de atividade.
ms.prod: xamarin
ms.assetid: 582B6D0C-1F16-4299-A9A6-5651E76009FE
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: 202ce8d674a39b06fd1b07460dff4bf573062592
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2019
ms.locfileid: "70291410"
---
# <a name="working-with-tvos-progress-indicators-in-xamarin"></a>Trabalhando com indicadores de progresso tvOS no Xamarin

_Este artigo aborda a criação e o trabalho com indicadores de progresso dentro de um aplicativo Xamarin. tvOS._

Pode haver ocasiões em que o aplicativo Xamarin. tvOS precisa carregar novo conteúdo ou executar uma operação de processamento demorada. Durante esses momentos, você deve apresentar um indicador de atividade ou uma barra de progresso para permitir que o usuário saiba que o aplicativo ainda está em execução e para dar a eles alguma indicação sobre o comprimento da tarefa que está sendo executada.

![Indicadores de progresso de exemplo](progress-indicators-images/intro01.png "Indicadores de progresso de exemplo")

## <a name="about-activity-indicators"></a>Sobre indicadores de atividade

Um indicador de atividade apresenta um engrenagem girando e é usado para representar uma tarefa de um comprimento indeterminado. O indicador é apresentado quando a tarefa é iniciada e desaparece quando a tarefa é concluída.

A Apple tem as seguintes sugestões para trabalhar com indicadores de atividade:

- **Sempre que possível, use as barras de progresso** – como um indicador de atividade fornece ao usuário nenhum comentário sobre quanto tempo o processo será executado, sempre use uma barra de progresso se o comprimento for conhecido (por exemplo, quantos bytes baixar em um arquivo).
- **Mantenha o indicador animado** -os usuários relacionam um indicador de atividade estacionário a um aplicativo interrompido, portanto, você deve sempre animar o indicador enquanto ele está sendo exibido.
- **Descrever a tarefa que está sendo processada** – apenas exibindo o indicador de atividade por si só não é suficiente; o usuário precisa ser informado sobre o processo no qual está aguardando. Inclua um rótulo significativo (geralmente uma frase única, completa) que define claramente a tarefa.

## <a name="about-progress-bars"></a>Sobre as barras de progresso

Uma barra de progresso apresenta uma linha que preenche a cor para indicar o estado e o comprimento de uma tarefa demorada. As barras de progresso sempre devem ser usadas quando o comprimento das tarefas é conhecido ou podem ser computadas.

A Apple tem as seguintes sugestões para trabalhar com barras de progresso:

- **Relatar com precisão o progresso** -as barras de progresso sempre devem apresentar uma representação precisa do tempo necessário para concluir uma tarefa. Nunca simule o tempo para que o aplicativo pareça estar ocupado.
- **Use para durações bem definidas** -as barras de progresso não devem mostrar apenas que uma tarefa demorada está ocorrendo, mas forneça ao usuário e a indicação de quanto da tarefa foi concluída e uma estimativa do tempo restante.

## <a name="progress-indicators-and-storyboards"></a>Indicadores de progresso e storyboards

A maneira mais fácil de trabalhar com um indicador de progresso em um aplicativo Xamarin. tvOS é adicioná-lo à interface do usuário do aplicativo usando o designer do iOS.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Na **painel de soluções**, clique duas vezes no arquivo **Main. Storyboard** e abra-o para edição.

2. Arraste um **indicador de atividade** da **caixa de ferramentas** e solte-o na exibição: 

    ![Um indicador de atividade](progress-indicators-images/activity01.png "Um indicador de atividade")

3. Na guia **widget** da **painel de propriedades**, você pode ajustar várias propriedades do indicador de atividade, como seu **estilo**, **comportamento**e **nome**: 

    ![A guia do widget para um indicador de atividade](progress-indicators-images/activity02.png "A guia do widget para um indicador de atividade")
    
    O **nome** determina o nome da propriedade que representa o indicador de atividade no C# código.

4. Arraste um **modo de exibição de progresso** da **caixa de ferramentas** e solte-o na exibição: 

    ![Um modo de exibição de progresso](progress-indicators-images/activity03.png "Um modo de exibição de progresso")

5. Na guia **widget** do Gerenciador de **Propriedades**, você pode ajustar várias propriedades da exibição de progresso, como seu **estilo**, **progresso** (porcentagem concluída) e **nome**: 

    ![A guia do widget para um modo de exibição de progresso](progress-indicators-images/activity04.png "A guia do widget para um modo de exibição de progresso")
    
    O **nome** determina o nome da propriedade que representa o modo de exibição de C# andamento no código.

6. Salve as alterações.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Na **Gerenciador de soluções**, clique duas vezes no arquivo **Main. Storyboard** e abra-o para edição.

2. Arraste um **indicador de atividade** da **caixa de ferramentas** e solte-o na exibição: 

    ![Um](progress-indicators-images/activity01-vs.png
    "indicador de atividade de um indicador de atividade")

3. Na guia **widget** do Gerenciador de **Propriedades**, você pode ajustar várias propriedades do indicador de atividade, como seu **estilo**, **comportamento**e **nome**: 

    ![A guia do widget para um indicador de atividade](progress-indicators-images/activity02-vs.png "A guia do widget para um indicador de atividade")

    O **nome** determina o nome da propriedade que representa o indicador de atividade no C# código.

4. Arraste um **modo de exibição de progresso** da **caixa de ferramentas** e solte-o na exibição: 

   ![Um modo de exibição de progresso](progress-indicators-images/activity03-vs.png "Um modo de exibição de progresso")

5. Na guia **widget** do Gerenciador de **Propriedades**, você pode ajustar várias propriedades da exibição de progresso, como seu **estilo**, **progresso** (porcentagem concluída) e **nome**: 

    ![A guia do widget para um modo de exibição de progresso](progress-indicators-images/activity04-vs.png "A guia do widget para um modo de exibição de progresso")
    
    O **nome** determina o nome da propriedade que representa o modo de exibição de C# andamento no código.

6. Salve as alterações.

-----

Para obter mais informações sobre como trabalhar com storyboards, consulte nossa [Guia de início rápido Olá, tvOS](~/ios/tvos/get-started/hello-tvos.md). 

## <a name="working-with-activity-indicators"></a>Trabalhando com indicadores de atividade

Conforme mencionado acima, os indicadores de atividade devem ser mostrados quando seu aplicativo estiver executando um longo processo de comprimento indeterminado.

A qualquer momento, você pode ver se um indicador de atividade é animado verificando `IsAnimating` sua propriedade. Se a `HidesWhenStopped` propriedade for `true`, o indicador de atividade será ocultado automaticamente quando sua animação for interrompida.

Você pode usar o seguinte código para iniciar a animação: 

```csharp
ActivityIndicator.StartAnimating();
```

E o seguinte irá parar a animação:

```csharp
ActivityIndicator.StopAnimating();
```

> [!NOTE]
> Esses trechos de código pressupõem que o **nome** do indicador de atividade foi definido como **ActivityIndicator** na guia **widget** do designer do Ios.

## <a name="working-with-progress-bars"></a>Trabalhando com barras de progresso

Novamente, uma barra de progresso deve ser usada sempre que seu aplicativo estiver executando uma tarefa de longa execução de uma duração conhecida. 

A `Progress` propriedade é usada para definir a quantidade da tarefa que foi concluída de 0% a 100% (0,0 a 1,0). Use a `ProgressTintColor` propriedade para definir a cor da barra quantidade concluída e da `TrackTintColor` propriedade para definir a cor do plano de fundo (valor não concluído).

## <a name="summary"></a>Resumo

Este artigo abordou a criação e o trabalho com indicadores de progresso dentro de um aplicativo Xamarin. tvOS.

## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guias de interface humana do tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
