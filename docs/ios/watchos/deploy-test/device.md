---
title: Testes em dispositivos do Apple Watch
description: Este documento descreve como implantar um aplicativo do watchOS criado com o Xamarin para teste em um Apple Watch real. Ele discute dispositivos, perfis, teste, o provisionamento e fornece algumas dicas de solução de problemas.
ms.prod: xamarin
ms.assetid: A72A7D38-FAE8-4DD2-843D-54B74C5078D7
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: f4a21e25f418cc81d5f210098ded648b3d70ae14
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116726"
---
# <a name="testing-on-apple-watch-devices"></a>Testes em dispositivos do Apple Watch

Depois de ter seguido as [etapas de implantação](~/ios/watchos/deploy-test/index.md) para criar IDs do aplicativo e grupos de aplicativos (se necessário), use as instruções nesta página para:

- [Configure seus dispositivos](#devices) no Centro de desenvolvimento da Apple, e
- [Criar perfis de provisionamento de desenvolvimento](#profiles), em seguida,
- [Implantar e testar](#testing) em um Apple Watch.

<a name="devices" />

## <a name="devices"></a>Dispositivos

Testando aplicativos iOS em um real iPhone ou iPad sempre exigiu o dispositivo a ser registrado no Centro de desenvolvimento. A lista de dispositivos se parece com isso (clique no sinal de adição **+** para adicionar um novo dispositivo):

![](device-images/devices-sml.png "A lista de dispositivos se parece com isso")

Relógios não são diferentes - agora você precisa adicionar seu dispositivo da Apple Watch antes de implantar aplicativos para ele. Localizar o watch usando o UDID **Xcode** (**Windows > dispositivos** lista). Quando o telefone emparelhado é conectado informações da inspeção também serão exibidas:

[![](device-images/xcode-devices-sml.png "Informações de Watch emparelhado")](device-images/xcode-devices.png#lightbox)

Quando você sabe que a inspeção UDID, adicioná-lo à lista de dispositivos no Centro de desenvolvimento:

![](device-images/devices-watch-sml.png "A inspeção UDID na lista de dispositivos")

Após a adição de dispositivo de inspeção, certifique-se de que ela está selecionada em qualquer desenvolvimento de novo ou existente ou criar os perfis de provisionamento do ad-hoc:

![](device-images/devices-provisioning.png "Lista de dispositivos disponíveis")

Não se esqueça de se você editar um perfil de provisionamento existente para baixar e instalá-la novamente!

<a name="profiles" />

## <a name="development-provisioning-profiles"></a>Perfis de provisionamento de desenvolvimento

Para compilar para testes em seu dispositivo, você precisará criar uma **perfil de provisionamento de desenvolvimento** para cada ID do aplicativo em sua solução.

Se você tiver um ID do aplicativo curinga *somente um perfil de provisionamento será necessário*; mas se você tiver uma ID de aplicativo separada para cada projeto, você precisará de um perfil de provisionamento para cada ID do aplicativo:

![](device-images/provisioningprofile-development.png "O perfil de provisionamento de desenvolvimento")

Depois de criar todos os três perfis, eles aparecerão na lista. Lembre-se baixar e instalar cada um deles:

![](device-images/provisioningprofiles.png "Os perfis de provisionamento de desenvolvimento disponíveis")

Você pode verificar se o perfil de provisionamento a **opções de projeto** selecionando o **compilar > assinatura do pacote iOS** tela e selecionando o **versão** ou **Depurar iPhone** configuração.

O **perfil de provisionamento** lista mostrará todos os perfis de correspondência – você deverá ver os perfis de correspondência que você criou na lista suspensa:

![](device-images/options-selectprofile.png "A lista de perfis de provisionamento")


<a name="testing" />

## <a name="testing-on-a-watch-device"></a>Teste em um dispositivo de inspeção

Quando você tiver configurado seu dispositivo, as IDs do aplicativo e perfis de provisionamento, você está pronto para testar.

1. Verifique se seu iPhone está conectado e a inspeção já está emparelhada com o iPhone.

2. Verifique se a configuração é definida como **Release** ou **depurar**.

3. Certifique-se de que o dispositivo iPhone conectada for selecionado na lista de destino.

4. Clique com botão direito no projeto de aplicativo do iOS (não a inspeção ou extensão) e escolha **definir como projeto de inicialização**.

5. Clique no **executados** botão (ou escolha um **iniciar** opção o **executar** menu).

6. A solução será criado e será implantado o aplicativo do iOS para iPhone.
  Se o aplicativo iOS ou o provisionamento de extensão de inspeção não está definido corretamente a implantação para o iPhone falhará.

7. Se a implantação for concluída com êxito, o iPhone tentará automaticamente enviar o aplicativo de inspeção para o Watch emparelhado. O ícone do aplicativo será exibido na tela de inspeção com circular *instalando* indicador de progresso.

8. Se o aplicativo watch é instalado com êxito, o ícone permanecerá na tela watch - tocá-lo para começar a testar seu aplicativo!


## <a name="troubleshooting"></a>Solução de problemas

Se ocorrer um erro durante o uso de implantação do **Exibir > Pads > Log do dispositivo** para obter mais informações sobre o erro. Alguns erros e suas causas estão listadas abaixo:

### <a name="error-mt3001-could-not-aot-the-assembly"></a>Erro MT3001: Poderia AOT o assembly

Isso pode ocorrer ao compilar no modo de depuração para implantar em um dispositivo Apple Watch.

Para *temporariamente* contornar esse problema, desabilite **compilações incrementais** na extensão de inspeção **opções de projeto > Build > Build do watchOS** janela:

[![](device-images/disable-incremental-sml.png "A caixa de seleção de Builds incrementais")](device-images/disable-incremental.png#lightbox)

Isso será corrigido em uma versão futura, após o qual os builds incrementais podem ser habilitados novamente para tirar proveito dos tempos mais rápidos de build.


### <a name="watch-app-fails-to-start-while-debugging-on-device"></a>Aplicativo Watch não pode ser iniciado durante a depuração no dispositivo

Quando aparecer a tentativa de depurar um aplicativo de inspeção em um dispositivo físico, apenas o ícone & controle giratório de carregamento (e, eventualmente, tempo limite). Isso será corrigido em uma versão futura; uma solução alternativa é executar um build de versão (que não permitirá que a depuração).


### <a name="invalid-application-executable-or-application-verification-failed"></a>Falha na verificação do aplicativo ou executável de aplicativo inválido

```csharp
Failed to install [APPNAME]
Invalid executable/Application Verification Failed
```

![](device-images/invalid-application-executable.png "Alerta do executável de aplicativo inválida")

Se essas mensagens aparecem *na tela de inspeção* depois que o aplicativo tentou instalar, pode haver alguns problemas:

- O dispositivo de inspeção em si não foi adicionado como um dispositivo no Centro de desenvolvimento da Apple. Siga as instruções para [configurar corretamente os dispositivos](#devices).

- Os perfis de provisionamento de desenvolvimento que está sendo usados para teste não tinha o dispositivo de inspeção incluído; ou, depois que o relógio foi adicionado para os perfis de provisionamento, eles não foram baixados novamente e reinstalados. Siga as instruções para [configurar os perfis de provisionamento corretamente](#profiles).

- Se o **Log do dispositivo do iOS** contém `The system version is lower than the minimum OS version specified for bundle...Have 8.2; need 8.3` do, em seguida, o aplicativo de inspeção **Info. plist** tem incorretos **MinimumOSVersion** valor.
  Isso deve ser **8.2** – se você tiver instalado o 6.3 Xcode que talvez você precise editar manualmente a origem para inserção defini-lo como 8.2.

- O aplicativo de inspeção **Entitlements. plist** incorretamente um direito habilitou (como grupos de aplicativos) que ele não deve ter.

- O aplicativo de inspeção **ID do aplicativo** incorretamente tem um direito habilitado (por exemplo, grupos de aplicativos) no Centro de desenvolvimento que ele não deve ter.



### <a name="install-never-finished"></a>Instalação nunca concluída

```csharp
SPErrorGizmoInstallNeverFinishedErrorMessage
```

Esse erro pode indicar chaves desnecessárias (e inválidas) no aplicativo de inspeção **Info. plist** arquivo. Você não deve incluir as chaves para a extensão de inspeção ou aplicativo do iOS no aplicativo de inspeção.

<!--eg. NSLocationAlwaysUsageDescription -->


### <a name="waiting-for-debugger-to-connect"></a>"aguardando o depurador conectar-se"

Se o **saída do aplicativo** janela fica preso mostrando

```csharp
waiting for debugger to connect
```

Verifique se qualquer um dos NuGets que foram incluídos em seu projeto tem uma dependência **Microsoft.Bcl.Build**. Isso é adicionado automaticamente com algumas bibliotecas publicadas pela Microsoft, incluindo o popular [bibliotecas de cliente Http Microsoft](http://www.nuget.org/packages/Microsoft.Net.Http/).

O **Microsoft.Bcl.Build.targets** arquivo que é adicionado para o **. csproj** pode interferir com o empacotamento de extensões do iOS durante a implantação. Você pode acompanhar o [bug](https://bugzilla.xamarin.com/show_bug.cgi?id=29912).
Uma solução alternativa é possível editar o arquivo. csproj e mover manualmente o **Microsoft.Bcl.Build.targets** para ser o último elemento.

