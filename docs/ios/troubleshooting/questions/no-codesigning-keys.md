---
title: 'Por que minha compilação do iOS falha com: Nenhuma chave de assinatura de código do iPhone foi encontrada no conjunto de chaves?'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9DF24C46-D521-4112-9B21-52EA4E8D90D0
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/03/2018
ms.openlocfilehash: 0c777b8d5326963e959d8bb13d81d7058caa6bde
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030941"
---
# <a name="why-does-my-ios-build-fail-with-no-valid-iphone-code-signing-keys-found-in-keychain"></a>Por que minha compilação do iOS falha com: Nenhuma chave de assinatura de código do iPhone foi encontrada no conjunto de chaves?

## <a name="cause-of-the-error"></a>Causa do erro

Essa mensagem de erro ocorre quando o projeto em questão está procurando credenciais de assinatura de código válidas, mas não consegue encontrá-las. A assinatura de código é necessária para testes e implantações em dispositivos iOS físicos; assim como as compilações ad hoc & App Store.

### <a name="provisioning-devices"></a>Dispositivos de provisionamento

Se você ainda não tiver provisionado um dispositivo iOS antes, o guia a seguir o conduzirá pelo processo completo passo a passo: guia de [provisionamento de dispositivos](~/ios/get-started/installation/device-provisioning/index.md)

## <a name="bug-when-using-ios-simulator"></a>Bug ao usar o simulador do iOS

> [!NOTE]
> Esse problema foi resolvido em versões recentes do Xamarin para Visual Studio. No entanto, se o problema ocorrer na versão mais recente do software, registre um [novo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) com suas informações completas de controle de versão e saída de log de compilação completa.

Houve um bug no Xamarin. Visual Studio 3,11 que causou o projeto iOS em um modelo Xamarin. Forms para adicionar os direitos do codesign. o plist para compilações do simulador; bloqueando efetivamente os testes usando o simulador.

### <a name="how-to-fix"></a>Como corrigir

Você pode solucionar o problema removendo o sinalizador `<CodesignEntitlements>` das compilações de depuração no arquivo. csproj. Você pode fazer isso da seguinte maneira:

> [!WARNING]
> Erros em arquivos. csproj podem dividir seu projeto, portanto, é uma boa ideia fazer backup de seus arquivos antes de tentar isso.

1. Clique com o botão direito do mouse no projeto do iOS no painel de solução e selecione **descarregar projeto**
2. Clique com o botão direito do mouse no projeto novamente e selecione **Editar [ProjectName]. csproj**
3. Localize o PropertyGroups de depuração, eles devem começar com sinalizadores que têm esta aparência:
   - Depurar: `<PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Debug|iPhoneSimulator' ">`
   - Versão: `<PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Release|iPhoneSimulator' ">`
4. Em cada uma das compilações que usam o simulador, exclua ou comente a seguinte propriedade: `<CodesignEntitlements>Entitlements.plist</CodesignEntitlements>`
5. Recarregue o projeto e você deve ser capaz de implantar no simulador.

### <a name="next-steps"></a>Próximas etapas
Para obter mais assistência, entrar em contato conosco ou, se esse problema permanecer mesmo depois de utilizar as informações acima, consulte [quais opções de suporte estão disponíveis para o Xamarin?](~/cross-platform/troubleshooting/support-options.md) para obter informações sobre opções de contato, sugestões, bem como como arquivar um novo bug, se necessário .
