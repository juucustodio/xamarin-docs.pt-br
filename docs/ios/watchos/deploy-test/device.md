---
title: Testando em dispositivos Apple Watch
description: Este documento descreve como implantar um aplicativo watchOS criado com o Xamarin para teste em um Apple Watch real. Ele aborda os dispositivos, Provisionando perfis, testando e fornece algumas dicas de solução de problemas.
ms.prod: xamarin
ms.assetid: A72A7D38-FAE8-4DD2-843D-54B74C5078D7
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/17/2017
ms.openlocfilehash: 6d3756f4215174e17ec45518f430dc38270e3289
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70768695"
---
# <a name="testing-on-apple-watch-devices"></a>Testando em dispositivos Apple Watch

Depois de seguir as [etapas de implantação](~/ios/watchos/deploy-test/index.md) para criar IDs de aplicativo e grupos de aplicativos (se necessário), use as instruções nesta página para:

- [Configure seus dispositivos](#devices) no centro de desenvolvimento da Apple e
- [Criar perfis de provisionamento de desenvolvimento](#profiles)e, em seguida,
- [Implantar e testar](#testing) em um Apple Watch.

<a name="devices" />

## <a name="devices"></a>Dispositivos

Testar aplicativos iOS em um iPhone ou iPad real sempre exigiu que o dispositivo fosse registrado no centro de desenvolvimento. A lista de dispositivos tem esta aparência (clique no sinal **+** de adição para adicionar um novo dispositivo):

![](device-images/devices-sml.png "A lista de dispositivos é parecida com esta")

Os Watches não são diferentes-agora você precisa adicionar seu dispositivo de Apple Watch antes de implantar aplicativos nele. Localize o UDID do Watch usando a lista de dispositivos do **Xcode** (**Windows >** ). Quando o telefone emparelhado estiver conectado, as informações do observador também serão exibidas:

[![](device-images/xcode-devices-sml.png "Informações de inspeção emparelhadas")](device-images/xcode-devices.png#lightbox)

Quando você souber o UDID da inspeção, adicione-o à lista de dispositivos no centro de desenvolvimento:

![](device-images/devices-watch-sml.png "O UDID da inspeção na lista de dispositivos")

Depois que o dispositivo observado tiver sido adicionado, certifique-se de que ele esteja selecionado em qualquer desenvolvimento novo ou existente ou perfis de provisionamento ad-hoc que você criar:

![](device-images/devices-provisioning.png "Lista de dispositivos disponíveis")

Não se esqueça de editar um perfil de provisionamento existente para baixar e reinstalar o!

<a name="profiles" />

## <a name="development-provisioning-profiles"></a>Perfis de provisionamento de desenvolvimento

Para criar testes em seu dispositivo, você precisa criar um **perfil de provisionamento de desenvolvimento** para cada ID de aplicativo em sua solução.

Se você tiver uma ID de aplicativo curinga, *somente um perfil de provisionamento será necessário*; Mas se você tiver uma ID de aplicativo separada para cada projeto, precisará de um perfil de provisionamento para cada ID de aplicativo:

![](device-images/provisioningprofile-development.png "O perfil de provisionamento de desenvolvimento")

Depois de criar todos os três perfis, eles aparecerão na lista. Lembre-se de baixar e instalar cada uma delas:

![](device-images/provisioningprofiles.png "Os perfis de provisionamento de desenvolvimento disponíveis")

Você pode verificar o perfil de provisionamento nas opções do **projeto** selecionando a tela **Build > assinatura de pacote do IOS** e selecionando a configuração **versão** ou **depurar iPhone** .

A lista de **perfis de provisionamento** mostrará todos os perfis de correspondência – você deverá ver os perfis de correspondência criados nesta lista suspensa:

![](device-images/options-selectprofile.png "A lista de perfis de provisionamento")

<a name="testing" />

## <a name="testing-on-a-watch-device"></a>Testando em um dispositivo de inspeção

Depois de configurar seu dispositivo, as IDs de aplicativo e os perfis de provisionamento, você estará pronto para testar.

1. Verifique se o iPhone está conectado e se o relógio já está emparelhado com o iPhone.

2. Verifique se a configuração está definida como **liberar** ou **depurar**.

3. Verifique se o dispositivo iPhone conectado está selecionado na lista destino.

4. Clique com o botão direito do mouse no projeto do aplicativo iOS (não na inspeção ou extensão) e escolha **definir como projeto de inicialização**.

5. Clique no botão **executar** (ou escolha uma opção **Iniciar** no menu **executar** ).

6. A solução será criada e o aplicativo iOS será implantado no iPhone.
  Se o aplicativo iOS ou o provisionamento de extensão de inspeção não estiver definido corretamente, a implantação para o iPhone falhará.

7. Se a implantação for concluída com êxito, o iPhone tentará enviar automaticamente o aplicativo Watch para o relógio emparelhado. O ícone do aplicativo será exibido na tela de inspeção com um indicador de progresso de *instalação* circular.

8. Se o aplicativo Watch for instalado com êxito, o ícone permanecerá na tela Watch – toque-o para começar a testar seu aplicativo!

## <a name="troubleshooting"></a>Solução de problemas

Se ocorrer um erro durante a implantação, use a **exibição > Pads > log do dispositivo** para ver mais informações sobre o erro. Alguns erros e suas causas são listados abaixo:

### <a name="error-mt3001-could-not-aot-the-assembly"></a>Erro MT3001: Não foi possível AOT o assembly

Isso pode ocorrer ao criar no modo de depuração para implantar em um dispositivo Apple Watch.

Para contornar *temporariamente* esse problema, desabilite as **compilações incrementais** nas opções de projeto de extensão de inspeção **> compilar > janela de compilação watchOS** :

[![](device-images/disable-incremental-sml.png "A caixa de seleção compilações incrementais")](device-images/disable-incremental.png#lightbox)

Isso será corrigido em uma versão futura, após a qual as compilações incrementais podem ser habilitadas novamente para aproveitar os tempos de compilação mais rápidos.

### <a name="watch-app-fails-to-start-while-debugging-on-device"></a>Falha ao iniciar o aplicativo de inspeção durante a depuração no dispositivo

Ao tentar depurar um aplicativo Watch em um dispositivo físico, somente o ícone & carregamento de controle giratório aparece (e eventualmente o tempo limite). Isso será abordado em uma versão futura; uma solução alternativa é executar uma compilação de versão (que não permitirá a depuração).

### <a name="invalid-application-executable-or-application-verification-failed"></a>Falha de verificação de aplicativo ou executável de aplicativo inválido

```csharp
Failed to install [APPNAME]
Invalid executable/Application Verification Failed
```

![](device-images/invalid-application-executable.png "Alerta de executável de aplicativo inválido")

Se essas mensagens aparecerem *na tela Watch* depois que o aplicativo tentar instalar, pode haver alguns problemas:

- O próprio dispositivo de observação não foi adicionado como um dispositivo no centro de desenvolvimento da Apple. Siga as instruções para [configurar os dispositivos corretamente](#devices).

- Os perfis de provisionamento de desenvolvimento que estão sendo usados para teste não tinham o dispositivo de observação incluído; ou depois que o relógio foi adicionado aos perfis de provisionamento, eles não foram rebaixados e reinstalados. Siga as instruções para [configurar os perfis de provisionamento corretamente](#profiles).

- Se o **log do dispositivo IOS** contiver `The system version is lower than the minimum OS version specified for bundle...Have 8.2; need 8.3` , o **info. plist** do aplicativo de inspeção terá o valor de **MinimumOSVersion** errado.
  Isso deve ser **8,2** -se você tiver instalado o Xcode 6,3, talvez seja necessário editar manualmente a origem para inserir defini-la como 8,2.

- Os direitos do aplicativo Watch **. o plist** tem um direito habilitado (como grupos de aplicativos) que ele não deveria ter.

- A **ID do aplicativo** do aplicativo de inspeção incorretamente tem um direito habilitado (como grupos de aplicativos) no centro de desenvolvimento que não deveria ter.

### <a name="install-never-finished"></a>Instalação nunca concluída

```csharp
SPErrorGizmoInstallNeverFinishedErrorMessage
```

Esse erro pode indicar chaves desnecessárias (e inválidas) no arquivo **info. plist** do aplicativo Watch. Você não deve incluir chaves destinadas ao aplicativo iOS ou à extensão Watch no aplicativo Watch.

<!--eg. NSLocationAlwaysUsageDescription -->

### <a name="waiting-for-debugger-to-connect"></a>"aguardando o depurador se conectar"

Se a janela **saída do aplicativo** ficar presa, mostrando

```csharp
waiting for debugger to connect
```

Verifique se qualquer um dos NuGets que foram incluídos no seu projeto tem uma dependência em **Microsoft. BCL. Build**. Isso é adicionado automaticamente com algumas bibliotecas publicadas pela Microsoft, incluindo as populares [bibliotecas de clientes http da Microsoft](https://www.nuget.org/packages/Microsoft.Net.Http/).

O arquivo **Microsoft. BCL. Build. targets** que é adicionado ao **. csproj** pode interferir no empacotamento de extensões do IOS durante a implantação. Você pode acompanhar o [bug](https://bugzilla.xamarin.com/show_bug.cgi?id=29912).
Uma possível solução alternativa é editar o arquivo. csproj e mover manualmente o **Microsoft. BCL. Build. targets** para ser o último elemento.
