---
title: Trabalhando com tvOS indicadores de progresso no Xamarin
description: Este documento descreve como trabalhar com indicadores de progresso em um aplicativo tvOS criados com o Xamarin. Ele aborda as barras de progresso e indicadores de atividade.
ms.prod: xamarin
ms.assetid: 582B6D0C-1F16-4299-A9A6-5651E76009FE
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/25/2018
ms.openlocfilehash: cbd2b2de237a5bb22d1dc0242569b96b12bca070
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61180502"
---
# <a name="working-with-tvos-progress-indicators-in-xamarin"></a>Trabalhando com tvOS indicadores de progresso no Xamarin

_Este artigo aborda a projetar e trabalhar com indicadores de progresso dentro de um aplicativo xamarin. tvos._

Pode haver vezes quando seu aplicativo tvos precisa carregar novo conteúdo ou executar uma operação de processamento mais extenso. Durante esses horários, você deve apresentar um indicador de atividade ou uma barra de progresso para informar o usuário que o aplicativo ainda está em execução e para fornecer alguma indicação quanto à duração da tarefa que está sendo executada.

![Indicadores de progresso de exemplo](progress-indicators-images/intro01.png "indicadores de progresso de exemplo")

## <a name="about-activity-indicators"></a>Sobre indicadores de atividade

Um indicador de atividade apresenta como uma engrenagem de rotação e é usado para representar uma tarefa de um período indeterminado. O indicador é apresentado quando a tarefa é iniciado e desaparece quando a tarefa é concluída.

Apple tem as sugestões a seguir para trabalhar com indicadores de atividade:

- **Sempre que possível, use as barras de progresso em vez disso,** – porque dá um indicador a atividade levará o usuário que nenhum feedback como longo processo que está sendo executado, sempre use uma barra de progresso se o tamanho é conhecido (por exemplo, o número de bytes baixados em um arquivo).
- **Manter o indicador animado** -usuários relacionam um indicador de atividade de estática para um aplicativo paralisado, para que você sempre deve animar o indicador enquanto ele está sendo exibido.
- **Descrever a tarefa que está sendo processada** -exibir apenas o indicador de atividade por si só não é suficiente; o usuário precisa ser informado sobre o processo em que estão aguardando. Inclua um rótulo significativo (geralmente uma frase simples e completa) que defina claramente a tarefa.

## <a name="about-progress-bars"></a>Sobre barras de progresso

Apresenta uma barra de progresso como uma linha que é preenchido com cor para indicar o estado e o comprimento de uma tarefa demorada. Barras de progresso sempre devem ser usadas quando o comprimento das tarefas é conhecido, ou pode ser computado.

Apple tem as sugestões a seguir para trabalhar com barras de progresso:

- **Relatar com precisão o progresso** -barras de progresso sempre devem apresentar uma representação precisa do tempo necessário para concluir uma tarefa. Nunca falsificar o tempo para fazer com que o aplicativo apareça ocupado.
- **Uso de durações bem-definidos** -progresso barras devem não apenas Mostrar que uma tarefa demorada faz colocar, mas fornecem aos usuários e indicação de quanto da tarefa é concluída e uma estimativa do tempo restante.

## <a name="progress-indicators-and-storyboards"></a>Indicadores de progresso e storyboards

A maneira mais fácil trabalhar com um indicador de progresso em um aplicativo xamarin. tvos é adicioná-lo à interface de usuário do aplicativo usando o Designer do iOS.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)
    
1. No **painel de soluções**, clique duas vezes o **Main. Storyboard** de arquivo e abri-lo para edição.

2. Arraste uma **indicador de atividade** da **caixa de ferramentas** e solte-o no modo de exibição: 

    ![Um indicador de atividade](progress-indicators-images/activity01.png "um indicador de atividade")

3. No **Widget** guia da **painel de propriedades**, você pode ajustar várias propriedades do indicador de atividade, como seu **estilo**, **comportamento**, e **nome**: 

    ![Na guia de Widget para um indicador de atividade](progress-indicators-images/activity02.png "guia o Widget para um indicador de atividade")
    
    O **nome** determina o nome da propriedade que representa o indicador de atividade no C# código.

4. Arraste uma **modo de exibição do progresso** da **caixa de ferramentas** e solte-o no modo de exibição: 

    ![Um modo de exibição do progresso](progress-indicators-images/activity03.png "um modo de exibição do progresso")

5. No **Widget** guia da **Property Explorer**, você pode ajustar várias propriedades da exibição de progresso, como seu **estilo**, **progresso**(porcentagem concluída), e **nome**: 

    ![Na guia de Widget para um modo de exibição do progresso](progress-indicators-images/activity04.png "guia o Widget para um modo de exibição do progresso")
    
    O **nome** determina o nome da propriedade que representa o modo de exibição do progresso em C# código.

6. Salve as alterações.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)
    
1. No **Gerenciador de soluções**, clique duas vezes o **Main. Storyboard** de arquivo e abri-lo para edição.

2. Arraste uma **indicador de atividade** da **caixa de ferramentas** e solte-o no modo de exibição: 

    ![Um indicador de atividade](progress-indicators-images/activity01-vs.png
    "um indicador de atividade")

3. No **Widget** guia da **Gerenciador de propriedades**, você pode ajustar várias propriedades do indicador de atividade, como seu **estilo**, **comportamento**, e **nome**: 

    ![Na guia de Widget para um indicador de atividade](progress-indicators-images/activity02-vs.png "guia o Widget para um indicador de atividade")

    O **nome** determina o nome da propriedade que representa o indicador de atividade no C# código.

4. Arraste uma **modo de exibição do progresso** da **caixa de ferramentas** e solte-o no modo de exibição: 

   ![Um modo de exibição do progresso](progress-indicators-images/activity03-vs.png "um modo de exibição do progresso")

5. No **Widget** guia da **Property Explorer**, você pode ajustar várias propriedades da exibição de progresso, como seu **estilo**, **progresso**(porcentagem concluída), e **nome**: 

    ![Na guia de Widget para um modo de exibição do progresso](progress-indicators-images/activity04-vs.png "guia o Widget para um modo de exibição do progresso")
    
    O **nome** determina o nome da propriedade que representa o modo de exibição do progresso em C# código.

6. Salve as alterações.

-----

Para obter mais informações sobre como trabalhar com storyboards, consulte nosso [Olá, guia de início rápido do tvOS](~/ios/tvos/get-started/hello-tvos.md). 

## <a name="working-with-activity-indicators"></a>Trabalhar com indicadores de atividade

Como mencionado acima, os indicadores de atividade devem ser mostrados quando seu aplicativo está em execução em um processo longo de comprimento indeterminado.

Em qualquer ponto, você pode ver se um indicador de atividade é animada, verificando seu `IsAnimating` propriedade. Se o `HidesWhenStopped` é de propriedade `true`, o indicador de atividade será ocultado automaticamente quando a animação é interrompida.

Você pode usar o código a seguir para iniciar a animação: 

```csharp
ActivityIndicator.StartAnimating();
```

E o código a seguir interromperá a animação:

```csharp
ActivityIndicator.StopAnimating();
```

> [!NOTE]
> Esses trechos de código supõem que o indicador de atividade **nome** foi definida como **ActivityIndicator** no **Widget** guia do Designer do iOS.

## <a name="working-with-progress-bars"></a>Trabalhando com barras de progresso

Novamente, uma barra de progresso deve ser usada sempre que seu aplicativo está executando uma tarefa demorada de uma duração conhecida. 

O `Progress` propriedade é usada para definir o valor da tarefa que foi concluída de 0% a 100% (0,0 a 1,0). Use o `ProgressTintColor` propriedade para definir a cor da barra de quantidade concluída e o `TrackTintColor` propriedade para definir a cor do plano de fundo (quantidade não concluída).

## <a name="summary"></a>Resumo

Este artigo abordou a projetar e trabalhar com indicadores de progresso dentro de um aplicativo xamarin. tvos.

## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [Guias de Interface humana do tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
