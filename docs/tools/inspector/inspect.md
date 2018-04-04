---
title: Inspecionando os aplicativos ativos
description: Visualizar e depurar seu aplicativo em tempo real
ms.prod: xamarin
ms.assetid: 91B3206E-B2A5-4660-A6E5-B924B8FE69A7
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 03/29/2017
ms.openlocfilehash: 841013fd6b836a7c9e065dc86e7d34c71146ca96
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="inspecting-live-applications"></a>Inspecionando os aplicativos ativos

Inspeção de aplicativo em tempo real está disponível para os clientes corporativos.


1. [Instale o Inspetor de & Xamarin pastas de trabalho.](~/tools/inspector/install.md)

1. Abra qualquer [suporte para o projeto de aplicativo](~/tools/inspector/install.md#supported-platforms) no Visual Studio para Mac ou o Visual Studio.
1. Execute seu aplicativo no modo de depuração.
1. Clique o **inspecionar** botão na barra de ferramentas IDE (no Visual Studio, o **aplicativo atual Inspecione....**  item de menu também está disponível na **ferramentas** ou **depurar** menu).



[![](inspect-images/mac-heres-the-button.png "Clique no botão inspecionar na barra de ferramentas do IDE")](inspect-images/mac-heres-the-button.png#lightbox)

Abrirá uma nova janela de cliente do Inspetor de Xamarin, com um novo prompt REPL.

[![](inspect-images/inspector-0.7.0-map-inspect-small.png "Uma nova janela de cliente Xamarin Inspetor será aberto, com um novo prompt REPL")](inspect-images/inspector-0.7.0-map-inspect.png#lightbox)

Depois que essa janela aparece, você terá um prompt c# interativo que você pode usar para executar e avaliar expressões e instruções do c#. O que torna isso exclusivo é que o código é avaliado no contexto do processo de destino. Nesse caso, estamos mostrando o código em execução em relação ao aplicativo do iOS exibido.

As alterações que você fizer para o estado do aplicativo estão acontecendo no processo de destino, para que você possa usar c# para alterar o aplicativo ao vivo, ou você pode inspecionar o estado do aplicativo em tempo real.

Por exemplo, no iOS, talvez queremos localizar nossa classe de delegado UIApplication, que é o nosso principal driver (onde armazenamos muito o estado do aplicativo):

    var del = (MyApp.AppDelegate) UIApplication.SharedApplication.Delegate
    del.Database.GetAllCustomers ()
    ...
    del.Database.AddCustomer (...)

(Observe que cada envio ocorre em um editor de várias linhas. `Shift + Enter` cria uma nova linha, e `Cmd + Enter` (`Ctrl + Enter` no Windows) enviar o código para avaliação. `Enter` envia automaticamente quando for seguro.)

É uma maneira mais conveniente para obter os elementos visuais de seu aplicativo usando o botão "Inspecionar". Quando você pressiona a isso, você pode selecionar um elemento de interface do usuário clicando em seu aplicativo. A variável `selectedView` será atribuído para apontar para o elemento atual na tela. Na captura de tela acima, você pode ver como podemos acessados e, em seguida, editado `selectedView.BarTintColor` no `UISearchBar` é selecionado.

A árvore visual dinâmica também é muito útil. Representa o instantâneo atual da sua hierarquia do modo de exibição. Você pode selecionar linhas para definir `selectedView` no REPL e para ver os valores de propriedade do modo de exibição. No Mac, você pode interagir com uma visualização 3D detalhada dos modos de exibição em camadas. No Windows, você pode editar os valores de propriedade do modo de exibição visualmente.

## <a name="known-limitations"></a>Limitações conhecidas

 - Somente há suporte para a seleção de exibição no vídeo principal.
 - Edição de grade de propriedade não está disponível para Mac e no Windows é limitada a alguns tipos de dados. Use o REPL para edição mais eficiente.
 - Desde que o suplemento/extensão de inspetor está instalado e habilitado em seu IDE, estamos injetando código no seu aplicativo sempre que for iniciado no modo de depuração. Se você notar qualquer comportamento estranho em seu aplicativo, por favor tente desabilitar ou desinstalar o suplemento/extensão do Inspetor, reiniciar o IDE e verificá-la novamente. E. [arquivo bugs](~/tools/inspector/install.md#reporting-bugs) para nos contar!
 - Se a inspeção de um elemento de interface do usuário faz com que a mudança no mesmo assim, [Fale conosco](~/tools/inspector/install.md#reporting-bugs), pois isso pode indicar um erro.

