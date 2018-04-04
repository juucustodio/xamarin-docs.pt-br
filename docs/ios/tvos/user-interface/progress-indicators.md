---
title: Trabalhando com indicadores de progresso
description: Este artigo aborda criando e trabalhando com indicadores de progresso dentro de um aplicativo Xamarin.tvOS.
ms.prod: xamarin
ms.assetid: 582B6D0C-1F16-4299-A9A6-5651E76009FE
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 96fc3ea0aa802f62bd697b34f7bd504eb445a4f6
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="working-with-progress-indicators"></a>Trabalhando com indicadores de progresso

_Este artigo aborda criando e trabalhando com indicadores de progresso dentro de um aplicativo Xamarin.tvOS._


Pode haver momentos em que seu aplicativo Xamarin.tvOS precisa carregar um novo conteúdo ou executar uma operação de processamento mais extenso. Durante esses horários, você deve apresentar um indicador de atividade ou barra de progresso para informar o usuário que o aplicativo ainda está em execução e para fornecer alguma indicação quanto à duração da tarefa que está sendo executada.

[![](progress-indicators-images/intro01.png "Indicadores de progresso de exemplo")](progress-indicators-images/intro01.png#lightbox)

<a name="About-Activity-Indicators" />

## <a name="about-activity-indicators"></a>Sobre indicadores de atividade

Um indicador de atividade apresenta como uma engrenagem giratório visualmente e é usado para representar uma tarefa de um período indeterminado. O indicador é apresentado quando a tarefa é iniciado e desaparece quando a tarefa é concluída.

Apple tem as seguintes sugestões para trabalhar com indicadores de atividade:

- **Sempre que possível, progresso barras em vez disso, use** – porque um indicador de atividade não dá ao usuário nenhum feedback quanto tempo o processo que está sendo executado levará, sempre use uma barra de progresso se o comprimento é conhece (por exemplo, o número de bytes a ser baixados em um arquivo).
- **Manter o indicador animado** -os usuários relacionar um indicador de atividade estática para um aplicativo paralisado para que você sempre deve ter o indicador animado enquanto ele está sendo exibido.
- **Descrever a tarefa que está sendo processada** -exibir apenas o indicador de atividade por si só não é suficiente, o usuário precisa ser informado sobre o processo que estão aguardando. Inclua um rótulo significativo (geralmente uma frase simples e completa) que define claramente a tarefa.

<a name="Summary" />

## <a name="about-progress-bars"></a>Sobre barras de andamento

Apresenta uma barra de progresso como uma linha que preenche com cor para indicar o estado e o comprimento de uma tarefa demorada. Barras de progresso sempre deve ser usadas quando o comprimento das tarefas é saber ou pode ser calculado.

Apple tem as seguintes sugestões para trabalhar com barras de progresso:

- **Relatar com precisão o progresso** -barras de progresso deve sempre ser uma representação precisa do tempo necessário para concluir uma tarefa. Nunca simule o tempo para tornar o aplicativo aparecer ocupado.
- **Use para durações Well-Defined** -barra de progresso não deve apenas exibir que uma tarefa demorada está levando colocar, mas fornece uma estimativa do tempo restante e o usuário e indicação de quanto da tarefa é concluída.

<a name="Progress-Indicators-and-Storyboards" />

## <a name="progress-indicators-and-storyboards"></a>Storyboards e indicadores de progresso

É a maneira mais fácil trabalhar com indicador de progresso em um aplicativo de Xamarin.tvOS para adicioná-los à interface de usuário do aplicativo usando o Designer do iOS.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)
    
1. No **solução preenchimento**, clique duas vezes no `Main.storyboard` de arquivo e abri-lo para edição.
1. Arraste um **indicador de atividade** do **caixa de ferramentas** e solte-o no modo de exibição: 

    [![](progress-indicators-images/activity01.png "Um indicador de atividade")](progress-indicators-images/activity01.png#lightbox)
1. No **Widget guia** do **propriedades de preenchimento**, você pode ajustar várias propriedades do indicador de atividade, como seu **estilo** e **comportamento**: 

    [![](progress-indicators-images/activity02.png "A guia de Widget ")](progress-indicators-images/activity02.png#lightbox)
1. Arraste um **andamento** do **caixa de ferramentas** e solte-o no modo de exibição: 

    [![](progress-indicators-images/activity03.png "Um modo de exibição do andamento")](progress-indicators-images/activity03.png#lightbox)
1. No **Widget guia** do **propriedade Explorer**, você pode ajustar várias propriedades da exibição de progresso, como seu **estilo** e **andamento**(porcentagem concluída): 

    [![](progress-indicators-images/activity04.png "A guia de Widget")](progress-indicators-images/activity04.png#lightbox)
1. Por fim, atribuir **nomes** para os controles para que você pode responder a eles no código c#. Por exemplo: 

    [![](progress-indicators-images/activity05.png "Atribua um nome")](progress-indicators-images/activity05.png#lightbox)
1. Salve as alterações.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)
    
1. No **Solution Explorer**, clique duas vezes o `Main.storyboard` de arquivo e abri-lo para edição.
1. Arraste um **indicador de atividade** do **caixa de ferramentas** e solte-o no modo de exibição: 

    [![](progress-indicators-images/activity01-vs.png "Um indicador de atividade")](progress-indicators-images/activity01-vs.png#lightbox)
1. No **Widget guia** do **propriedades Explorer**, você pode ajustar várias propriedades do indicador de atividade, como seu **estilo** e **comportamento**: 

    [![](progress-indicators-images/activity02-vs.png "A guia de Widget")](progress-indicators-images/activity02-vs.png#lightbox)
1. Arraste um **andamento** do **caixa de ferramentas** e solte-o no modo de exibição: 

    [![](progress-indicators-images/activity03-vs.png "Um modo de exibição do andamento")](progress-indicators-images/activity03-vs.png#lightbox)
1. No **Widget guia** do **propriedade Explorer**, você pode ajustar várias propriedades da exibição de progresso, como seu **estilo** e **andamento**(porcentagem concluída): 

    [![](progress-indicators-images/activity04-vs.png "A guia de Widget")](progress-indicators-images/activity04-vs.png#lightbox)
1. Por fim, atribuir **nomes** para os controles para que você pode responder a eles no código c#. Por exemplo: 

    [![](progress-indicators-images/activity05-vs.png "Atribua um nome")](progress-indicators-images/activity05-vs.png#lightbox)
1. Salve as alterações.

-----

Para obter mais informações sobre como trabalhar com Storyboards, consulte nosso [Hello, tvOS Quick Start Guide](~/ios/tvos/get-started/hello-tvos.md). 

<a name="Working-with-Activity-Indicators" />

## <a name="working-with-activity-indicators"></a>Trabalhando com indicadores de atividade

Como mencionado acima, os indicadores de atividade deve ser mostrados quando seu aplicativo estiver executando um processo longo, mas você não souber o comprimento exato de tempo que exigirá que a tarefa.

A qualquer momento, você pode ver se o indicador de atividade está em execução a animação giratório verificando o `IsAnimating` propriedade. Se o `HidesWhenStopped` é de propriedade `true`, o indicador de atividade será ocultado automaticamente quando a animação é interrompida.

Você pode usar o código a seguir para iniciar a animação: 

```csharp
ActivityIndicator.StartAnimating();
```

E o seguinte irá parar a animação:

```csharp
ActivityIndicator.StopAnimating();
```

<a name="Working-with-Progress-Bars" />

## <a name="working-with-progress-bars"></a>Trabalhando com barras de andamento

Novamente, uma barra de progresso deve ser usada sempre que seu aplicativo está executando uma tarefa de longa execução de uma duração conhecidos. 

O `Progress` propriedade é usada para definir o valor da tarefa que foi concluída de 0% a 100% (0,0 a 1,0). Use o `ProgressTintColor` propriedade para definir a cor da barra de quantidade concluída e o `TrackTintColor` propriedade para definir a cor de plano de fundo (quantidade incompleta).

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo abordou criando e trabalhando com indicadores de progresso dentro de um aplicativo Xamarin.tvOS.



## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS guias de Interface Humana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
