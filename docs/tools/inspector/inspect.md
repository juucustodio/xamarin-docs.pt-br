---
title: Inspecionando aplicativos ativos
description: Este documento descreve como usar o Xamarin Inspector para inspecionar aplicativos. Ele também aborda as limitações da ferramenta de Xamarin Inspector.
ms.prod: xamarin
ms.assetid: 91B3206E-B2A5-4660-A6E5-B924B8FE69A7
author: davidortinau
ms.author: daortin
ms.date: 06/19/2018
ms.openlocfilehash: bbb1e21139b5f073e2cc7e3d4781e8bc38334449
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73006301"
---
# <a name="inspecting-live-applications"></a>Inspecionando aplicativos ativos

A inspeção do aplicativo ao vivo está disponível para clientes empresariais.

1. Abra qualquer [projeto de aplicativo com suporte](~/tools/inspector/install.md#supported-platforms) no Visual Studio para Mac ou no Visual Studio.
1. Execute seu aplicativo no modo de depuração.
1. Clique no botão **inspecionar** na barra de ferramentas do IDE (no Visual Studio, o item de menu **inspecionar aplicativo atual...** também está disponível no menu **ferramentas** ou **depurar** ).

[![](inspect-images/mac-heres-the-button.png "Click the Inspect button in the IDE toolbar")](inspect-images/mac-heres-the-button.png#lightbox)

Uma nova janela do cliente Xamarin Inspector será aberta, com um novo prompt REPL.

[![](inspect-images/inspector-0.7.0-map-inspect-small.png "A new Xamarin Inspector client window will open, with a fresh REPL prompt")](inspect-images/inspector-0.7.0-map-inspect.png#lightbox)

Depois que essa janela for exibida, você terá C# um prompt interativo que pode ser usado para executar C# e avaliar instruções e expressões. O que torna isso exclusivo é que o código é avaliado no contexto do processo de destino. Nesse caso, estamos mostrando o código em execução no aplicativo iOS exibido.

As alterações feitas no estado do aplicativo estão realmente acontecendo no processo de destino, para que você possa usar C# o para alterar o aplicativo ao vivo ou pode inspecionar o estado do aplicativo em tempo real.

Por exemplo, no iOS, podemos querer localizar nossa classe delegate UIApplication, que é nosso driver principal (onde armazenamos muito do estado do aplicativo):

```csharp
var del = (MyApp.AppDelegate) UIApplication.SharedApplication.Delegate
del.Database.GetAllCustomers ()
...
del.Database.AddCustomer (...)
```

(Observe que cada envio ocorre em um editor de várias linhas. `Shift + Enter` criará uma nova linha e `Cmd + Enter` (`Ctrl + Enter` no Windows) enviará o código para avaliação. `Enter` envia automaticamente quando é seguro.)

Uma maneira mais conveniente de obter os elementos visuais de seu aplicativo é usando o botão "inspecionar". Depois de pressionar isso, você pode selecionar um elemento de interface do usuário clicando em seu aplicativo. A variável `selectedView` será atribuída para apontar para o elemento real na tela. Na captura de tela acima, você pode ver como acessamos e editei `selectedView.BarTintColor` na `UISearchBar` que selecionamos.

A árvore visual ativa também é muito útil. Representa o instantâneo atual da sua hierarquia de exibição. Você pode selecionar linhas para definir `selectedView` no REPL e ver os valores de propriedade da exibição. No Mac, você pode interagir com uma visualização detalhada 3D das exibições em camadas. No Windows, você pode editar visualmente os valores de propriedade de uma exibição.

## <a name="known-limitations"></a>Limitações conhecidas

- Só há suporte para a seleção de exibição na exibição principal.
- A edição da grade de propriedades não está disponível para Mac e no Windows está limitada a alguns tipos de dados. Use o REPL para uma edição mais potente.
- Desde que o suplemento/extensão do Inspetor esteja instalado e habilitado no seu IDE, estamos injetando o código em seu aplicativo sempre que ele for iniciado no modo de depuração. Se você notar algum comportamento estranho em seu aplicativo, tente desabilitar ou desinstalar o suplemento/extensão do Inspetor, reiniciar o IDE e verificar novamente. E registre [bugs](~/tools/inspector/install.md#reporting-bugs) para nos informar!
- Se inspecionar um elemento de interface do usuário faz com que ele seja alterado de qualquer forma, informe- [nos](~/tools/inspector/install.md#reporting-bugs), pois isso pode indicar um bug.
