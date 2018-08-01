---
title: Apple Watch dispositivos de teste
description: Este documento descreve como implantar um aplicativo watchOS criado com o Xamarin para teste em um Apple Watch real. Ele discute dispositivos, o provisionamento de perfis, teste e fornece algumas dicas de solução de problemas.
ms.prod: xamarin
ms.assetid: A72A7D38-FAE8-4DD2-843D-54B74C5078D7
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: a960d81d41ff127fa3316e6190dfbf4881305c02
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790855"
---
# <a name="testing-on-apple-watch-devices"></a>Apple Watch dispositivos de teste

Depois que você seguiu o [etapas de implantação](~/ios/watchos/deploy-test/index.md) para criar grupos de aplicativos e de IDs de aplicativo (se necessário), use as instruções nesta página para:

- [Configuração de seus dispositivos](#devices) no Centro de desenvolvimento da Apple, e
- [Criar perfis de provisionamento de desenvolvimento](#profiles), em seguida,
- [Implantar e testar](#testing) em um Apple Watch.

<a name="devices" />

## <a name="devices"></a>Dispositivos

Testando aplicativos do iOS em um iPhone real ou iPad sempre exigiu o dispositivo a ser registrada no Centro de desenvolvimento. A lista de dispositivos tem esta aparência (clique no sinal de adição **+** para adicionar um novo dispositivo):

![](device-images/devices-sml.png "A lista de dispositivos tem esta aparência")

Observa não é diferentes - agora, você precisa adicionar seu dispositivo Apple Watch antes de implantar aplicativos para ele. Localizar o relógio UDID usando **Xcode** (**Windows > dispositivos** lista). Quando estiver conectado ao telefone emparelhado informações da observação também serão exibidas:

[![](device-images/xcode-devices-sml.png "Informações de inspecionar emparelhados")](device-images/xcode-devices.png#lightbox)

Quando você sabe que o relógio UDID, adicioná-lo à lista de dispositivos no Centro de desenvolvimento:

![](device-images/devices-watch-sml.png "O relógio UDID na lista de dispositivos")

Após a adição de dispositivo Watch, certifique-se de que estiver em qualquer desenvolvimento de novo ou existente ou perfis de provisionamento do ad-hoc criados:

![](device-images/devices-provisioning.png "Lista de dispositivos disponíveis")

Não se esqueça de se você editar um perfil de provisionamento existente para baixar e instalá-lo novamente!

<a name="profiles" />

## <a name="development-provisioning-profiles"></a>Perfis de provisionamento de desenvolvimento

Para criar para teste em seu dispositivo, você precisa criar um **perfil de provisionamento de desenvolvimento** para cada ID de aplicativo em sua solução.

Se você tiver um curinga ID do aplicativo, *somente um perfil de provisionamento será necessário*; mas se você tem uma ID de aplicativo separado para cada projeto, você precisará de um perfil de provisionamento para cada ID do aplicativo:

![](device-images/provisioningprofile-development.png "O perfil de provisionamento de desenvolvimento")

Depois que você tenha criado todos os três perfis, elas aparecerão na lista. Lembre-se de baixar e instalar cada um:

![](device-images/provisioningprofiles.png "Os perfis de provisionamento de desenvolvimento disponíveis")

Você pode verificar o perfil de provisionamento no **opções de projeto** selecionando o **Build > iOS de assinatura de pacote** tela e selecionando o **versão** ou **Depurar iPhone** configuração.

O **perfil de provisionamento de** lista mostrará todos os perfis de correspondência – você deve ver os perfis de correspondência que você criou na lista suspensa:

![](device-images/options-selectprofile.png "A lista de perfis de provisionamento")


<a name="testing" />

## <a name="testing-on-a-watch-device"></a>Teste em um dispositivo de inspeção

Depois de configurar seu dispositivo, IDs de aplicativo e perfis de provisionamento, você está pronto para testar.

1. Verifique se seu iPhone está conectado e o relógio já está emparelhado com o iPhone.

2. Verifique a configuração está definida como **versão** ou **depurar**.

3. Certifique-se de que o dispositivo iPhone conectado é selecionado na lista de destino.

4. Clique com botão direito no projeto de aplicativo iOS (não o relógio ou extensão) e escolha **definir como projeto de inicialização**.

5. Clique o **executar** botão (ou escolha um **iniciar** opção o **executar** menu).

6. A solução será criado e será implantado o aplicativo do iOS para iPhone.
  Se o aplicativo iOS ou inspecionar o provisionamento de extensão não está definido corretamente, em seguida, ocorrerá falha na implantação para iPhone.

7. Se a implantação for concluída com êxito, iPhone automaticamente tentará enviar o aplicativo de inspeção para inspecionar o par. O ícone do aplicativo aparecerá na tela de observação com circular *instalando* indicador de progresso.

8. Se o aplicativo watch é instalado com êxito, o ícone permanecerá na tela Observação - toque para começar a testar seu aplicativo!


## <a name="troubleshooting"></a>Solução de problemas

Se ocorrer um erro durante o uso de implantação de **exibição > preenche > Log dispositivo** para obter mais informações sobre o erro. Alguns erros e suas causas estão listadas abaixo:

### <a name="error-mt3001-could-not-aot-the-assembly"></a>Erro MT3001: Pôde AOT o assembly

Isso pode ocorrer ao compilar no modo de depuração para implantar em um dispositivo da Apple Watch.

Para *temporariamente* contornar esse problema, desabilite **compilações incrementais** na extensão de inspeção **opções de projeto > compilar > watchOS Build** janela:

[![](device-images/disable-incremental-sml.png "A caixa de seleção de compilações incrementais")](device-images/disable-incremental.png#lightbox)

Isso será corrigido em uma versão futura, após o qual compilações incrementais podem ser habilitadas novamente para tirar proveito dos tempos de compilação.


### <a name="watch-app-fails-to-start-while-debugging-on-device"></a>Observar o que aplicativo não for iniciado durante a depuração no dispositivo

Quando aparecer a tentar depurar um aplicativo de inspeção em um dispositivo físico, apenas o ícone de & controle giratório de carregamento (e, eventualmente, tempo limite). Isso será abordado em uma versão futura; uma solução alternativa é executar uma compilação de versão (que não permitirá que a depuração).


### <a name="invalid-application-executable-or-application-verification-failed"></a>Executável de aplicativo inválido ou a falha na verificação do aplicativo

```csharp
Failed to install [APPNAME]
Invalid executable/Application Verification Failed
```

![](device-images/invalid-application-executable.png "Alerta do executável de aplicativo inválida")

Se essas mensagens aparecem *na tela de inspeção* depois que o aplicativo tentou instalar, pode haver alguns problemas:

- O próprio dispositivo inspeção não foi adicionado como um dispositivo no Centro de desenvolvimento da Apple. Siga as instruções para [configurar corretamente os dispositivos](#devices).

- Os perfis de provisionamento de desenvolvimento que está sendo usados para teste não tinha o dispositivo inspecionar incluído; ou, depois que o relógio foi adicionado aos perfis de provisionamento, eles não foram baixados novamente e reinstalados. Siga as instruções para [configurar os perfis de provisionamento corretamente](#profiles).

- Se o **iOS dispositivo Log** contém `The system version is lower than the minimum OS version specified for bundle...Have 8.2; need 8.3` , em seguida, inspeção do aplicativo **Info. plist** tem errado **MinimumOSVersion** valor.
  Isso deve ser **8.2** - se você tiver instalado o Xcode 6.3 que talvez você precise editar manualmente a origem para inserção defini-lo como 8.2.

- O aplicativo de inspeção **Entitlements.plist** incorretamente uma autorização habilitou (como grupos de aplicativos) que ele não deve ter.

- O aplicativo de inspeção **ID do aplicativo** incorretamente tem autorização habilitada (como grupos de aplicativos) no Centro de desenvolvimento que não deve ter.



### <a name="install-never-finished"></a>Nunca instalar concluído

```csharp
SPErrorGizmoInstallNeverFinishedErrorMessage
```

Esse erro pode indicar chaves desnecessárias (e inválidas) no aplicativo de inspeção **Info. plist** arquivo. Você não deve incluir destinadas-se a extensão de inspeção ou aplicativo do iOS no aplicativo de inspeção de chaves.

<!--eg. NSLocationAlwaysUsageDescription -->


### <a name="waiting-for-debugger-to-connect"></a>"aguardando o depurador conectar-se"

Se o **saída do aplicativo** janela travou mostrando

```csharp
waiting for debugger to connect
```

Verifique se qualquer o NuGets que foram incluídos em seu projeto possui uma dependência **Microsoft.Bcl.Build**. Isso é adicionado automaticamente com algumas bibliotecas Microsoft publicou, incluindo os populares [bibliotecas de cliente Http Microsoft](http://www.nuget.org/packages/Microsoft.Net.Http/).

O **Microsoft.Bcl.Build.targets** arquivo que é adicionado para o **. csproj** pode interferir com o pacote de extensões do iOS durante a implantação. Você pode acompanhar o [bug](https://bugzilla.xamarin.com/show_bug.cgi?id=29912).
Uma solução alternativa é editar o arquivo. csproj e mover manualmente o **Microsoft.Bcl.Build.targets** para ser o último elemento.

