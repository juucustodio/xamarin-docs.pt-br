---
title: Trabalhando com indicadores de progresso
description: Este artigo aborda criando e trabalhando com indicadores de progresso dentro de um aplicativo Xamarin.tvOS.
ms.prod: xamarin
ms.assetid: 582B6D0C-1F16-4299-A9A6-5651E76009FE
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 04/25/2018
ms.openlocfilehash: d512dfddb3a6c81767f937272a4ffb1ab1a35372
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/26/2018
---
# <a name="working-with-progress-indicators"></a>Trabalhando com indicadores de progresso

_Este artigo aborda criando e trabalhando com indicadores de progresso dentro de um aplicativo Xamarin.tvOS._

Pode haver momentos em que seu aplicativo Xamarin.tvOS precisa carregar um novo conteúdo ou executar uma operação de processamento mais extenso. Durante esses horários, você deve apresentar um indicador de atividade ou uma barra de progresso para informar o usuário que o aplicativo ainda está em execução e para fornecer alguma indicação quanto à duração da tarefa que está sendo executada.

![Indicadores de progresso de exemplo](progress-indicators-images/intro01.png "indicadores de progresso de exemplo")

## <a name="about-activity-indicators"></a>Sobre indicadores de atividade

Um indicador de atividade apresenta como uma engrenagem girando e é usado para representar uma tarefa de um período indeterminado. O indicador é apresentado quando a tarefa é iniciado e desaparece quando a tarefa é concluída.

Apple tem as seguintes sugestões para trabalhar com indicadores de atividade:

- **Sempre que possível, use as barras de progresso em vez disso,** – porque um fornece do indicador de atividade levará o usuário sem um feedback como longo o processo está sendo executado, sempre use uma barra de progresso se o comprimento é conhecido (por exemplo, o número de bytes a ser baixados em um arquivo).
- **Manter o indicador de animação** -usuários relacionam para um aplicativo paralisado, um indicador de atividade estática para que você sempre deve animar o indicador enquanto ele está sendo exibido.
- **Descrever a tarefa que está sendo processada** -exibir apenas o indicador de atividade por si só não é suficiente; o usuário precisa ser informado sobre o processo no qual eles estão esperando. Inclua um rótulo significativo (geralmente uma frase simples e completa) que define claramente a tarefa.

## <a name="about-progress-bars"></a>Sobre barras de andamento

Apresenta uma barra de progresso como uma linha que preenche com cor para indicar o estado e o comprimento de uma tarefa demorada. Barras de progresso sempre devem ser usadas quando o comprimento das tarefas é conhecido ou pode ser calculado.

Apple tem as seguintes sugestões para trabalhar com barras de progresso:

- **Relatar com precisão o progresso** -barras de progresso sempre devem apresentar uma representação precisa do tempo necessário para concluir uma tarefa. Nunca simule o tempo para tornar o aplicativo aparecer ocupado.
- **Use para durações bem definidas** -andamento barras não devem apenas exibir que uma tarefa demorada está levando colocar, mas fornecem aos usuários e indicação de quanto da tarefa é concluída e uma estimativa do tempo restante.

## <a name="progress-indicators-and-storyboards"></a>Storyboards e indicadores de progresso

É a maneira mais fácil para trabalhar com um indicador de progresso em um aplicativo de Xamarin.tvOS para adicioná-lo à interface de usuário do aplicativo usando o Designer do iOS.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)
    
1. No **solução preenchimento**, clique duas vezes no **Main.storyboard** de arquivo e abri-lo para edição.

2. Arraste um **indicador de atividade** do **caixa de ferramentas** e solte-o no modo de exibição: 

    ![Um indicador de atividade](progress-indicators-images/activity01.png "um indicador de atividade")

3. No **Widget** guia do **propriedades de preenchimento**, você pode ajustar várias propriedades do indicador de atividade, como seu **estilo**, **comportamento**, e **nome**: 

    ![A guia de Widget para um indicador de atividade](progress-indicators-images/activity02.png "guia o Widget para um indicador de atividade")
    
    O **nome** determina o nome da propriedade que representa o indicador de atividade de código c#.

4. Arraste um **andamento** do **caixa de ferramentas** e solte-o no modo de exibição: 

    ![Um modo de exibição do andamento](progress-indicators-images/activity03.png "um modo de exibição do andamento")

5. No **Widget** guia do **propriedade Explorer**, você pode ajustar várias propriedades da exibição de progresso, como seu **estilo**, **andamento**(porcentagem concluída), e **nome**: 

    ![A guia de Widget para uma exibição do andamento](progress-indicators-images/activity04.png "guia o Widget para uma exibição de andamento")
    
    O **nome** determina o nome da propriedade que representa o modo de exibição do andamento em código c#.

6. Salve as alterações.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)
    
1. No **Solution Explorer**, clique duas vezes o **Main.storyboard** de arquivo e abri-lo para edição.

2. Arraste um **indicador de atividade** do **caixa de ferramentas** e solte-o no modo de exibição: 

    ![Um indicador de atividade](progress-indicators-images/activity01-vs.png
    "um indicador de atividade")

3. No **Widget** guia do **propriedades Explorer**, você pode ajustar várias propriedades do indicador de atividade, como seu **estilo**, **comportamento**, e **nome**: 

    ![A guia de Widget para um indicador de atividade](progress-indicators-images/activity02-vs.png "guia o Widget para um indicador de atividade")

    O **nome** determina o nome da propriedade que representa o indicador de atividade de código c#.

4. Arraste um **andamento** do **caixa de ferramentas** e solte-o no modo de exibição: 

   ![Um modo de exibição do andamento](progress-indicators-images/activity03-vs.png "um modo de exibição do andamento")

5. No **Widget** guia do **propriedade Explorer**, você pode ajustar várias propriedades da exibição de progresso, como seu **estilo**, **andamento**(porcentagem concluída), e **nome**: 

    ![A guia de Widget para uma exibição do andamento](progress-indicators-images/activity04-vs.png "guia o Widget para uma exibição de andamento")
    
    O **nome** determina o nome da propriedade que representa o modo de exibição do andamento em código c#.

6. Salve as alterações.

-----

Para obter mais informações sobre como trabalhar com storyboards, consulte nosso [Hello, tvOS Quick Start Guide](~/ios/tvos/get-started/hello-tvos.md). 

## <a name="working-with-activity-indicators"></a>Trabalhando com indicadores de atividade

Como mencionado acima, os indicadores de atividade devem ser mostrados quando seu aplicativo estiver executando um processo longo de comprimento indeterminado.

A qualquer momento, você pode ver se um indicador de atividade é animada verificando seu `IsAnimating` propriedade. Se o `HidesWhenStopped` é de propriedade `true`, o indicador de atividade será ocultado automaticamente quando a animação é interrompida.

Você pode usar o código a seguir para iniciar a animação: 

```csharp
ActivityIndicator.StartAnimating();
```

E o seguinte irá parar a animação:

```csharp
ActivityIndicator.StopAnimating();
```

> [!NOTE]
> Estes trechos de código supõem que o indicador de atividade **nome** foi definida como **ActivityIndicator** no **Widget** guia do Designer do iOS.

## <a name="working-with-progress-bars"></a>Trabalhando com barras de andamento

Novamente, uma barra de progresso deve ser usada sempre que seu aplicativo está executando uma tarefa de longa execução de uma duração conhecida. 

O `Progress` propriedade é usada para definir o valor da tarefa que foi concluída de 0% a 100% (0,0 a 1,0). Use o `ProgressTintColor` propriedade para definir a cor da barra de quantidade concluída e o `TrackTintColor` propriedade para definir a cor de plano de fundo (quantidade incompleta).

## <a name="summary"></a>Resumo

Este artigo abordou criando e trabalhando com indicadores de progresso dentro de um aplicativo Xamarin.tvOS.

## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS guias de Interface Humana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
