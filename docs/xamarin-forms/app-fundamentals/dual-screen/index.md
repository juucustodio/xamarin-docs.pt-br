---
title: Xamarin.Forms Tela dupla
description: Este guia explica como criar Xamarin.Forms aplicativos para dispositivos de tela dupla.
ms.prod: xamarin
ms.assetid: f9906e83-f8ae-48f9-997b-e1540b96ee8e
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e1d2a443a6005050c518e21e4e0f2df64c2aab0c
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562620"
---
# <a name="no-locxamarinforms-dual-screen"></a>Xamarin.Forms tela dupla

Dispositivos de tela dupla como o Microsoft Surface Duo facilitam novas possibilidades de experiência do usuário para seus aplicativos. Xamarin.Forms inclui `TwoPaneView` `DualScreenInfo` classes e para que você possa desenvolver aplicativos para dispositivos de tela dupla.

## <a name="get-started"></a>Introdução

Siga estas etapas para adicionar recursos de tela dupla a um Xamarin.Forms aplicativo:

1. Abra a caixa de diálogo **Gerenciador de pacotes NuGet** para sua solução.
2. Na guia **Procurar**, pesquise por `Xamarin.Forms.DualScreen`.
3. Instale o `Xamarin.Forms.DualScreen` pacote em sua solução.
4. Adicione a seguinte chamada de método de inicialização à classe do projeto Android `MainActivity` , no `OnCreate` evento:

    ```csharp
    Xamarin.Forms.DualScreen.DualScreenService.Init(this);
    ```

    Esse método é necessário para que o aplicativo possa detectar alterações no estado do aplicativo, como distribuídas em duas telas.

5. Atualize o `Activity` atributo na classe do projeto do Android `MainActivity` , para que ele inclua _todas_ essas `ConfigurationChanges` opções:

    ```@csharp
    ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation
        | ConfigChanges.ScreenLayout | ConfigChanges.SmallestScreenSize | ConfigChanges.UiMode
    ```

    Esses valores são necessários para que as alterações de configuração e o estado de extensão possam ser relatados de forma confiável. Por padrão, somente dois são adicionados a Xamarin.Forms projetos, portanto, lembre-se de adicionar o restante para obter suporte confiável de tela dupla.

## <a name="troubleshooting"></a>Solução de problemas

Se a `DualScreenInfo` classe ou o `TwoPaneView` layout não estiver funcionando conforme o esperado, verifique as instruções de configuração nesta página. Omitir ou configurar o `Init` método ou os `ConfigurationChanges` valores de atributo são causas comuns de erros.

Examine os [ Xamarin.Forms exemplos de tela dupla](/dual-screen/xamarin/samples) para obter orientações adicionais e implementação de referência.

## <a name="next-steps"></a>Próximas etapas

Depois de adicionar o NuGet, adicione recursos de tela dupla ao seu aplicativo com as seguintes diretrizes:

- [Padrões de design de tela dupla](design-patterns.md) -ao considerar como usar melhor as várias telas em um dispositivo de tela dupla, consulte estas diretrizes de padrão para encontrar a melhor opção para a interface do aplicativo.
- [Layout TwoPaneView](twopaneview.md) – a Xamarin.Forms `TwoPaneView` classe, inspirado pelo controle UWP do mesmo nome, é um layout de plataforma cruzada otimizado para dispositivos de tela dupla.
- [Classe auxiliar DualScreenInfo](dual-screen-info.md) – a `DualScreenInfo` classe permite que você determine em qual painel sua exibição está, qual é o tamanho máximo, em que situação o dispositivo está, o ângulo da dobradiça e muito mais.
- [Gatilhos de tela dupla](triggers.md) : o [`Xamarin.Forms.DualScreen`](xref:Xamarin.Forms.DualScreen) namespace inclui dois gatilhos de estado que disparam uma [`VisualState`](xref:Xamarin.Forms.VisualState) alteração quando o modo de exibição do layout anexado, ou janela, é alterado.

Visite os [documentos do desenvolvedor de tela dupla](/dual-screen/) para obter mais informações.