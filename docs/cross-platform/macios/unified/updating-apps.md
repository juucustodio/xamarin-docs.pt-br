---
title: Atualizando aplicativos existentes para a API unificada
ms.prod: xamarin
ms.assetid: 8A654C95-5DCA-4BB5-A582-F96C2BECC81C
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: ca14d35bcc167bd35cf1ec9e822e86421579b7b8
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="updating-existing-apps-to-the-unified-api"></a>Atualizando aplicativos existentes para a API unificada

> [!IMPORTANT]
> **Substituição de perfil clássica:** conforme são adicionadas novas plataformas xamarin começamos gradualmente substituir recursos do perfil clássico (monotouch.dll). Por exemplo, a opção de não-NRC (novo-ref count) foi removida. NRC sempre foi habilitada para unificada de todos os aplicativos (ou seja, não-NRC nunca foi uma opção) e não tem nenhum problema conhecido. Versões futuras removerá a opção de usar Boehm como o coletor de lixo. Isso também era uma opção nunca disponível para aplicativos unificados. A remoção completa de suporte clássico é agendada para Avançar estão com a versão do xamarin 10.0.




## <a name="how-to-update-your-apps"></a>Como atualizar seus aplicativos

Universidade de Xamarin tem um vídeo disponível gratuitamente no **atualização para o iOS API unificada**. Visite [Xamarin University raios palestras](http://university.xamarin.com/lightninglectures) assistir!

[ ![](updating-apps-images/xamu-video-sml.png "Xamarin University")](http://university.xamarin.com/lightninglectures)

Há três etapas para atualizar seus aplicativos:

1. Corrija os avisos do compilador no seu código existente, especialmente aquelas relacionadas a APIs obsoletas.

2. Use a ferramenta de migração interna do Visual Studio para Mac para atualizar seus arquivos de projeto e os namespaces.

3. Correção restantes erros de compilador relacionados ao novo [64 tipos](~/cross-platform/macios/nativetypes.md) e [outras APIs](~/cross-platform/macios/unified/index.md#deprecated-typos) que foram alterados. Check-out [essas dicas](~/cross-platform/macios/unified/updating-tips.md) para obter informações adicionais sobre atualizações manuais que podem ser necessárias.

Guias específicas estão disponíveis para cada produto para ajudá-lo a atualizar seus aplicativos para a API unificada e o suporte de 64 bits:

### <a name="xamarinios-appscross-platformmaciosunifiedupdating-ios-appsmd"></a>[Aplicativos xamarin](~/cross-platform/macios/unified/updating-ios-apps.md)

Aplicativos xamarin existentes podem ser atualizados para a API de Unificação de mensagens usando a ferramenta de migração automatizada criada para o Visual Studio para Mac. Algumas correções adicionais poderão ser necessárias, conforme explicado em [estas instruções](~/cross-platform/macios/unified/updating-ios-apps.md) e [dicas](~/cross-platform/macios/unified/updating-tips.md).

###  <a name="xamarinmac-appscross-platformmaciosunifiedupdating-mac-appsmd"></a>[Xamarin.Mac apps](~/cross-platform/macios/unified/updating-mac-apps.md)

Aplicativos Xamarin.Mac existentes podem ser atualizados para a API de Unificação de mensagens usando a ferramenta de migração automatizada criada para o Visual Studio para Mac. Algumas correções adicionais poderão ser necessárias, conforme explicado em [estas instruções](~/cross-platform/macios/unified/updating-mac-apps.md) e [dicas](~/cross-platform/macios/unified/updating-tips.md).

###  <a name="xamarinforms-appscross-platformmaciosunifiedupdating-xamarin-forms-appsmd"></a>[Aplicativos xamarin. Forms](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)

Siga estas instruções para atualizar uma solução xamarin. Forms existente com um projeto do iOS para usar a API unificada. Suporte de API unificada só está disponível no xamarin. Forms 1.3 e posterior, portanto [as instruções](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md) também explicam como atualizar seu aplicativo xamarin. Forms para versão 1.3. Essas [dicas](~/cross-platform/macios/unified/updating-tips.md) pode ajudar a atualizar qualquer código de iOS nativo renderizadores personalizados ou serviços de dependência.

## <a name="working-with-native-types-in-cross-platform-appscross-platformmaciosnativetypesmd"></a>[Trabalhar com tipos nativos em aplicativos de plataforma cruzada](~/cross-platform/macios/nativetypes.md)

Este artigo aborda o uso do iOS novos tipos unificado API nativa (nint, nuint, nfloat) em um aplicativo de plataforma cruzada onde o código é compartilhado com dispositivos iOS não, como Android ou sistemas operacionais do Windows Phone. Ele fornece informações sobre quando os tipos nativos devem ser usados e fornece várias soluções possíveis para casos em que o novo tipo deve ser usado com o código de plataforma cruzada.

## <a name="update-bindings-to-the-unified-api"></a>Associações de atualização para a API unificada

Os clientes que criaram associações para Objective-C bibliotecas precisarão atualizar o projeto de vinculação para refletir as alterações na API subjacente (onde alguns tipos agora será 64 bits).
Siga estas instruções para [atualizar um projeto existente de associação para dar suporte a API unificada](~/cross-platform/macios/unified/update-binding.md).




## <a name="related-links"></a>Links relacionados

- [Atualizando aplicativos do iOS](~/cross-platform/macios/unified/updating-ios-apps.md)
- [Atualizando aplicativos do Mac](~/cross-platform/macios/unified/updating-mac-apps.md)
- [Atualizar aplicativos xamarin. Forms](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)
- [Atualizando associações](~/cross-platform/macios/unified/update-binding.md)
- [Atualizar dicas](~/cross-platform/macios/unified/updating-tips.md)
- [Diferenças de API unificada do vs clássico](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
