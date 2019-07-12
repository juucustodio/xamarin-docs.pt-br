---
title: Inspecionando aplicativos ativos
description: Este documento descreve como usar o Xamarin Inspector para inspecionar os aplicativos. Ele também discute as limitações da ferramenta Xamarin Inspector.
ms.prod: xamarin
ms.assetid: 91B3206E-B2A5-4660-A6E5-B924B8FE69A7
author: lobrien
ms.author: laobri
ms.date: 06/19/2018
ms.openlocfilehash: 2def0a01bdd28af5eefb76afc19a0e49fd1df355
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67831560"
---
# <a name="inspecting-live-applications"></a>Inspecionando aplicativos ativos

Inspeção de aplicativo em tempo real está disponível para clientes empresariais.

1. Abrir qualquer [suporte para o projeto de aplicativo](~/tools/inspector/install.md#supported-platforms) no Visual Studio para Mac ou Visual Studio.
1. Execute seu aplicativo no modo de depuração.
1. Clique o **inspecionar** botão na barra de ferramentas do IDE (no Visual Studio, o **inspecionar de aplicativo atual...**  item de menu também está disponível na **ferramentas** ou **depurar** menu).

[![](inspect-images/mac-heres-the-button.png "Clique no botão inspecionar na barra de ferramentas IDE")](inspect-images/mac-heres-the-button.png#lightbox)

Uma nova janela do cliente Xamarin Inspector será aberta, com um novo prompt REPL.

[![](inspect-images/inspector-0.7.0-map-inspect-small.png "Uma nova janela do cliente Xamarin Inspector será aberta, com um novo prompt do REPL")](inspect-images/inspector-0.7.0-map-inspect.png#lightbox)

Depois que essa janela for exibida, você tem um interativo C# prompt de que você pode usar para executar e avaliar C# instruções e expressões. O que torna isso exclusivo é que o código é avaliado no contexto do processo de destino. Nesse caso, estamos mostrando o código em execução no aplicativo iOS exibido.

As alterações que você fizer para o estado do aplicativo, na verdade, estão ocorrendo no processo de destino, então você pode usar C# para alterar o aplicativo ao vivo, ou você pode inspecionar o estado do aplicativo ao vivo.

Por exemplo, no iOS, queremos localizar nossa classe de representante de UIApplication, que é a nossa meta principal (onde armazenamos muito do estado do aplicativo):

    var del = (MyApp.AppDelegate) UIApplication.SharedApplication.Delegate
    del.Database.GetAllCustomers ()
    ...
    del.Database.AddCustomer (...)

(Observe que cada envio ocorre em um editor de várias linhas. `Shift + Enter` criará uma nova linha, e `Cmd + Enter` (`Ctrl + Enter` no Windows) enviará o código para avaliação. `Enter` envia automaticamente quando é seguro.)

É uma maneira mais conveniente para acessar os elementos visuais de seu aplicativo usando o botão "Inspecionar". Depois de pressionar isso, você pode selecionar um elemento de interface do usuário clicando em seu aplicativo. A variável `selectedView` será atribuído para apontar para o elemento real na tela. Na captura de tela acima, você pode ver como podemos acessados e, em seguida, editado `selectedView.BarTintColor` sobre o `UISearchBar` tivéssemos selecionado.

A árvore visual também é muito útil. Representa o instantâneo atual da sua hierarquia de exibição. Você pode selecionar linhas para definir `selectedView` no REPL e para ver os valores de propriedade da exibição. No Mac, você pode interagir com uma visualização destacada 3D dos modos de exibição em camadas. No Windows, você pode editar valores de propriedade do modo de exibição visualmente.

## <a name="known-limitations"></a>Limitações conhecidas

- Somente há suporte para seleção de exibição no vídeo principal.
- Edição de grade de propriedade não está disponível para Mac e no Windows é limitada a alguns tipos de dados. Use o REPL para uma edição mais poderosa.
- Desde que a extensão da addin Inspector é instalado e ativado no seu IDE, estamos injetando código no seu aplicativo sempre que for iniciado no modo de depuração. Se você observar qualquer comportamento estranho em seu aplicativo, por favor tente desabilitar ou desinstalar a extensão da addin Inspector, reiniciar o IDE e verificando novamente. E, por favor [arquivar bugs](~/tools/inspector/install.md#reporting-bugs) informe!
- Se inspecionar um elemento de interface do usuário faz com que a mudança no mesmo assim, faça [Fale conosco](~/tools/inspector/install.md#reporting-bugs), pois isso pode indicar um bug.

