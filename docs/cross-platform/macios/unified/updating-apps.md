---
title: Atualizando aplicativos existentes para o API Unificada
description: Este documento contém links para vários guias que descrevem como atualizar aplicativos Xamarin para o API Unificada. Ele aborda os aplicativos Xamarin. iOS, aplicativos Xamarin. Mac. Aplicativos Xamarin. Forms, tipos nativos em aplicativos de plataforma cruzada e projetos de associação.
ms.prod: xamarin
ms.assetid: 8A654C95-5DCA-4BB5-A582-F96C2BECC81C
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: d4904688302ac7f17c6326249ff08315d929e23c
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73015023"
---
# <a name="updating-existing-apps-to-the-unified-api"></a>Atualizando aplicativos existentes para o API Unificada

> [!IMPORTANT]
> O Xamarin API Clássica, que precede o API Unificada, foi preterido.
>
> - A última versão do Xamarin. iOS para dar suporte ao API Clássica (MonoTouch. dll) era Xamarin. iOS 9,10.
> - O Xamarin. Mac ainda dá suporte ao API Clássica, mas ele não é mais atualizado. Como ele foi preterido, os desenvolvedores devem mover seus aplicativos para a API Unificada.

## <a name="how-to-update-your-apps"></a>Como atualizar seus aplicativos

Há três etapas para atualizar seus aplicativos:

1. Corrija quaisquer avisos do compilador em seu código existente, especialmente aqueles relacionados a APIs preteridas.

2. Use a ferramenta de migração interna para Visual Studio para Mac atualizar os arquivos e namespaces do projeto.

3. Corrija os erros de compilador restantes relacionados aos novos [tipos de 64](~/cross-platform/macios/nativetypes.md) e [outras APIs](~/cross-platform/macios/unified/overview.md#deprecated-typos) que foram alterados. Confira [estas dicas](~/cross-platform/macios/unified/updating-tips.md) para obter informações adicionais sobre atualizações manuais que podem ser necessárias.

Há guias específicos disponíveis para cada produto para ajudá-lo a atualizar seus aplicativos para o API Unificada e suporte de 64 bits:

### <a name="xamarinios-appscross-platformmaciosunifiedupdating-ios-appsmd"></a>[Aplicativos Xamarin. iOS](~/cross-platform/macios/unified/updating-ios-apps.md)

Os aplicativos Xamarin. iOS existentes podem ser atualizados para o API Unificada usando a ferramenta de migração automatizada interna para Visual Studio para Mac. Algumas correções adicionais podem ser necessárias, conforme explicado nestas [instruções](~/cross-platform/macios/unified/updating-ios-apps.md) e [dicas](~/cross-platform/macios/unified/updating-tips.md).

### <a name="xamarinmac-appscross-platformmaciosunifiedupdating-mac-appsmd"></a>[Aplicativos Xamarin. Mac](~/cross-platform/macios/unified/updating-mac-apps.md)

Os aplicativos Xamarin. Mac existentes podem ser atualizados para o API Unificada usando a ferramenta de migração automatizada interna para Visual Studio para Mac. Algumas correções adicionais podem ser necessárias, conforme explicado nestas [instruções](~/cross-platform/macios/unified/updating-mac-apps.md) e [dicas](~/cross-platform/macios/unified/updating-tips.md).

### <a name="xamarinforms-appscross-platformmaciosunifiedupdating-xamarin-forms-appsmd"></a>[Aplicativos do Xamarin.Forms](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)

Siga estas instruções para atualizar uma solução Xamarin. Forms existente com um projeto do iOS para usar o API Unificada. API Unificada suporte só está disponível no Xamarin. Forms 1,3 e posterior, portanto, [as instruções](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md) também explicam como atualizar seu aplicativo Xamarin. Forms para a versão 1,3. Essas [dicas](~/cross-platform/macios/unified/updating-tips.md) podem ajudar a atualizar qualquer código nativo do Ios em renderizadores personalizados ou serviços de dependência.

## <a name="working-with-native-types-in-cross-platform-appscross-platformmaciosnativetypesmd"></a>[Trabalhar com tipos nativos em aplicativos de plataforma cruzada](~/cross-platform/macios/nativetypes.md)

Este artigo aborda o uso dos novos tipos nativos do iOS API Unificada (Nint, nuint, nfloat) em um aplicativo de plataforma cruzada em que o código é compartilhado com dispositivos não iOS, como Android ou Windows Phone sistemas operacionais. Ele fornece informações sobre quando os tipos nativos devem ser usados e fornece várias soluções possíveis para casos em que o novo tipo deve ser usado com código de plataforma cruzada.

## <a name="update-bindings-to-the-unified-api"></a>Atualizar associações para o API Unificada

Os clientes que criaram associações às bibliotecas Objective-C precisarão atualizar o projeto de associação para refletir as alterações na API subjacente (em que alguns tipos agora serão de 64 bits).
Siga estas instruções para [atualizar um projeto de associação existente para dar suporte ao API unificada](~/cross-platform/macios/unified/update-binding.md).

## <a name="related-links"></a>Links relacionados

- [Atualizando aplicativos iOS](~/cross-platform/macios/unified/updating-ios-apps.md)
- [Atualizando aplicativos do Mac](~/cross-platform/macios/unified/updating-mac-apps.md)
- [Atualizando aplicativos do Xamarin. Forms](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)
- [Atualizando associações](~/cross-platform/macios/unified/update-binding.md)
- [Atualizando dicas](~/cross-platform/macios/unified/updating-tips.md)
- [Diferenças clássicas do vs API Unificada](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)
