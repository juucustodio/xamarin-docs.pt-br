---
title: 'Por que minha compilação do iOS falha com: nenhum código do iPhone chaves de assinatura encontrada no conjunto de chaves?'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9DF24C46-D521-4112-9B21-52EA4E8D90D0
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/03/2018
ms.openlocfilehash: fe267db1f83695b3d0e8f3d828f91e01b56ba8ee
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50115426"
---
# <a name="why-does-my-ios-build-fail-with-no-valid-iphone-code-signing-keys-found-in-keychain"></a>Por que minha compilação do iOS falha com: nenhum código do iPhone chaves de assinatura encontrada no conjunto de chaves?

## <a name="cause-of-the-error"></a>Causa do erro
Essa mensagem de erro ocorre quando o projeto em questão está procurando por credenciais válidas de assinatura de código, mas não conseguir encontrá-los. Assinatura de código é necessária para testes e implantações em dispositivos iOS físicos; Assim como Ad-hoc & aplicativo armazenam compilações. 


### <a name="provisioning-devices"></a>Provisionamento de dispositivos
Se você ainda não provisionou um dispositivo iOS antes, o guia a seguir o guiará pelo processo passo a passo completo: [guia de provisionamento de dispositivo](~/ios/get-started/installation/device-provisioning/index.md)


## <a name="bug-when-using-ios-simulator"></a>Bug ao usar o simulador de iOS

> [!NOTE]
> Esse problema foi resolvido em versões recentes do Xamarin para Visual Studio. No entanto, se o problema ocorre na versão mais recente do software, envie uma [novo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) com o controle de versão completo completo e informações saída de log de compilação.


Havia um bug no Xamarin.Visual Studio 3.11 que fazia com que o projeto do iOS em um modelo do xamarin. Forms para adicionar que o codesign Entitlements. plist para simulador builds; bloqueando efetivamente testando o uso do simulador.

### <a name="how-to-fix"></a>Como corrigir
Você pode contornar o problema removendo o `<CodesignEntitlements>` sinalizador de depuração se baseia no arquivo. csproj. Você pode fazer isso da seguinte maneira:

*Aviso: Erros em arquivos. csproj podem interromper seu projeto, portanto, é uma boa ideia fazer backup de seus arquivos antes de tentar isso.*

1. Com o botão direito do mouse no projeto do iOS no painel de soluções e selecione **descarregar projeto**
2. Com o botão direito do mouse no projeto novamente e selecione **Editar csproj [ProjectName]**
3. Localize PropertyGroups depurar, eles devem iniciar com sinalizadores que ter esta aparência:
   - Depure: `<PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Debug|iPhoneSimulator' ">`
   - Versão: `<PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Release|iPhoneSimulator' ">`
4. Em cada uma das compilações que usam o simulador, exclua ou comente a seguinte propriedade: `<CodesignEntitlements>Entitlements.plist</CodesignEntitlements>`
5. Recarregue o projeto e você deve ser capaz de implantar para o simulador.

### <a name="next-steps"></a>Próximas etapas
Para obter mais assistência, entre em contato conosco ou se esse problema permanece mesmo após utilizando as informações acima, consulte [quais opções de suporte estão disponíveis para o Xamarin?](~/cross-platform/troubleshooting/support-options.md) para obter informações sobre opções de contato, sugestões, e também como um novo bug de arquivo se necessário. 
