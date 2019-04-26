---
title: Atualizando aplicativos existentes para a API unificada
description: Este documento leva a vários guias que descrevem como atualizar os aplicativos para a API unificada do Xamarin. Ele discute aplicativos xamarin. IOS, aplicativos xamarin. Mac. Aplicativos xamarin. Forms, tipos nativos em aplicativos de plataforma cruzada e projetos de associação.
ms.prod: xamarin
ms.assetid: 8A654C95-5DCA-4BB5-A582-F96C2BECC81C
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 2d09be7b85980e5c5a8eb209dc1b4ff3136c34b3
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61195579"
---
# <a name="updating-existing-apps-to-the-unified-api"></a>Atualizando aplicativos existentes para a API unificada

> [!IMPORTANT]
> A API clássica Xamarin, que precedida a API unificada, foi preterida. 
> - A última versão do xamarin. IOS para dar suporte a API clássica (MonoTouch. dll) estava 9.10 de xamarin. IOS.
> - Xamarin. Mac ainda oferece suporte a API clássica, mas ele não é mais atualizado. Uma vez que ela foi preterida, os desenvolvedores devem mover seus aplicativos para a API unificada.

## <a name="how-to-update-your-apps"></a>Como atualizar seus aplicativos

Xamarin University tem um vídeo disponível gratuitamente no **atualização do IOS unificado API**. Visite [Xamarin University Lightning palestras](http://university.xamarin.com/lightninglectures) assistir!

[ ![](updating-apps-images/xamu-video-sml.png "Xamarin University")](http://university.xamarin.com/lightninglectures)

Há três etapas para atualizar seus aplicativos:

1. Corrigi os avisos do compilador no código existente, especialmente as relacionadas a APIs preteridas.

2. Use a ferramenta de migração dentro do Visual Studio para Mac para atualizar seus arquivos de projeto e namespaces.

3. Correção restantes erros de compilador relacionados para o novo [64-types](~/cross-platform/macios/nativetypes.md) e [outras APIs](~/cross-platform/macios/unified/overview.md#deprecated-typos) que foram alterados. Fazer check-out [estas dicas](~/cross-platform/macios/unified/updating-tips.md) para saber mais sobre as atualizações manuais que podem ser necessárias.

Guias específicos estão disponíveis para cada produto para ajudá-lo a atualizar seus aplicativos para a API unificada e o suporte de 64 bits:

### <a name="xamarinios-appscross-platformmaciosunifiedupdating-ios-appsmd"></a>[Aplicativos xamarin. IOS](~/cross-platform/macios/unified/updating-ios-apps.md)

Os aplicativos xamarin. IOS existentes podem ser atualizados para a API unificada, usando a ferramenta de migração automatizada incorporada ao Visual Studio para Mac. Algumas correções adicionais, em seguida, podem ser necessárias, conforme explicado em [estas instruções](~/cross-platform/macios/unified/updating-ios-apps.md) e [dicas](~/cross-platform/macios/unified/updating-tips.md).

###  <a name="xamarinmac-appscross-platformmaciosunifiedupdating-mac-appsmd"></a>[Xamarin.Mac apps](~/cross-platform/macios/unified/updating-mac-apps.md)

Os aplicativos existentes do xamarin. Mac podem ser atualizados para a API unificada, usando a ferramenta de migração automatizada incorporada ao Visual Studio para Mac. Algumas correções adicionais, em seguida, podem ser necessárias, conforme explicado em [estas instruções](~/cross-platform/macios/unified/updating-mac-apps.md) e [dicas](~/cross-platform/macios/unified/updating-tips.md).

###  <a name="xamarinforms-appscross-platformmaciosunifiedupdating-xamarin-forms-appsmd"></a>[Aplicativos xamarin. Forms](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)

Siga estas instruções para atualizar uma solução xamarin. Forms existente com um projeto do iOS para usar a API unificada. Suporte de API unificada só está disponível no xamarin. Forms 1.3 e posterior, portanto [as instruções](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md) também explicam como atualizar seu aplicativo xamarin. Forms para a versão 1.3. Eles [dicas](~/cross-platform/macios/unified/updating-tips.md) pode ajudar a atualizar qualquer código iOS nativo em renderizadores personalizados ou serviços de dependência.

## <a name="working-with-native-types-in-cross-platform-appscross-platformmaciosnativetypesmd"></a>[Trabalhar com tipos nativos em aplicativos de plataforma cruzada](~/cross-platform/macios/nativetypes.md)

Este artigo aborda o uso do iOS novos tipos nativos de API unificada (nint, nuint, nfloat) em um aplicativo de plataforma cruzada em que o código é compartilhado com dispositivos não iOS, como Android ou sistemas operacionais do Windows Phone. Ele fornece informações sobre quando os tipos nativos devem ser usados e fornece várias soluções possíveis para casos em que o novo tipo deve ser usado com o código de plataforma cruzada.

## <a name="update-bindings-to-the-unified-api"></a>Atualizar as associações para a API unificada

Os clientes que criaram as associações para bibliotecas de Objective-C serão necessário atualizar o projeto de associação para refletir as alterações na API subjacente (em que alguns tipos agora será 64 bits).
Siga estas instruções para [atualizar um projeto existente de associação para dar suporte a API unificada do](~/cross-platform/macios/unified/update-binding.md).

## <a name="related-links"></a>Links relacionados

- [Atualizando aplicativos do iOS](~/cross-platform/macios/unified/updating-ios-apps.md)
- [Atualizando aplicativos do Mac](~/cross-platform/macios/unified/updating-mac-apps.md)
- [Atualizando aplicativos xamarin. Forms](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)
- [Atualizando associações](~/cross-platform/macios/unified/update-binding.md)
- [Dicas de atualização](~/cross-platform/macios/unified/updating-tips.md)
- [Diferenças de API unificada do vs clássico](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
