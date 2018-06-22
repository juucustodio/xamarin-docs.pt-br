---
title: 'Por que minha compilação iOS falhar com: nenhum código válido iPhone chaves de assinatura encontrada no conjunto de chaves?'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9DF24C46-D521-4112-9B21-52EA4E8D90D0
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 5334e3009906896644caa47c715f912fa379c627
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
ms.locfileid: "30776684"
---
# <a name="why-does-my-ios-build-fail-with-no-valid-iphone-code-signing-keys-found-in-keychain"></a>Por que minha compilação iOS falhar com: nenhum código válido iPhone chaves de assinatura encontrada no conjunto de chaves?

## <a name="cause-of-the-error"></a>Causa do erro
Essa mensagem de erro ocorre quando o projeto em questão está procurando por credenciais de assinatura de código válidas, mas não conseguir encontrá-los. Assinatura de código é necessária para testes e implantações em dispositivos iOS físicos; Assim como Ad-hoc & aplicativo armazenam compilações. 


### <a name="provisioning-devices"></a>Provisionamento de dispositivos
Se você ainda não provisionado um dispositivo iOS antes, o guia a seguir irá levá-lo pelo processo passo a passo completo: [guia de provisionamento de dispositivos](~/ios/get-started/installation/device-provisioning/index.md)


## <a name="bug-when-using-ios-simulator"></a>Erro ao usar o simulador de iOS

> [!NOTE]
> Esse problema foi resolvido em versões recentes do Xamarin para Visual Studio. No entanto, se o problema ocorre na versão mais recente do software, envie um [novo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) com o controle de versão completo completo e informações de log de saída de compilação.


Ocorreu um erro no Xamarin.Visual Studio 3.11 que causou o projeto iOS em um modelo de xamarin. Forms para adicionar que o codesign Entitlements.plist para simulador compilações; bloqueando efetivamente testar o uso do simulador.

### <a name="how-to-fix"></a>Como corrigir
Você pode solucionar o problema removendo o `<CodesignEntitlements>` compilações do sinalizador de depuração no arquivo. csproj. Você pode fazer isso da seguinte maneira:

*Aviso: Erros em arquivos. csproj podem quebrar seu projeto, portanto, é recomendável fazer backup de seus arquivos antes de tentar essa.*

1. Clique com botão direito no projeto do iOS no painel de solução e selecione **descarregar projeto**
2. Clique com botão direito no projeto novamente e selecione **Editar csproj [ProjectName]**
3. Localize o PropertyGroups depurar, eles devem iniciar com sinalizadores que ter esta aparência:
   - Depuração: `<PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Debug|iPhoneSimulator' ">`
   - Versão: `<PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Release|iPhoneSimulator' ">`
4. Em cada uma das compilações do que usam o simulador, exclua ou comente a seguinte propriedade: `<CodesignEntitlements>Entitlements.plist</CodesignEntitlements>`
5. Recarregar o projeto e você deve ser capaz de implantar para o simulador.

### <a name="next-steps"></a>Próximas etapas
Para obter mais assistência, entre em contato conosco ou se esse problema permanece mesmo após utilizando as informações acima, consulte [quais opções de suporte estão disponíveis para Xamarin?](~/cross-platform/troubleshooting/support-options.md) para obter informações sobre opções de contato, sugestões, bem como a registrar um bug de novo, se necessário. 
