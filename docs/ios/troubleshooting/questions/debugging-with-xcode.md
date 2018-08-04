---
title: Depuração de aplicativos do xamarin. IOS com Xcode
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5FDDEDB3-AEB9-4D9C-9F7B-FEFAA9AF0031
ms.technology: xamarin-ios
author: migueldeicaza
ms.author: miguel
ms.date: 02/22/2018
ms.openlocfilehash: e0127d4b24236d350e5fa967110316544c320d0f
ms.sourcegitcommit: bf05041cc74fb05fd906746b8ca4d1403fc5cc7a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/04/2018
ms.locfileid: "39514703"
---
# <a name="debugging-xamarinios-apps-with-xcode"></a>Depuração de aplicativos do xamarin. IOS com Xcode

Pode haver situações em que você deseja usar o Xcode para depurar algumas partes do seu aplicativo xamarin. IOS. Enquanto você não poderá depurar o código do .NET nela, você ainda poderá depurar código nativo e usar alguns dos visualizadores nativos no Xcode.

## <a name="walkthrough"></a>Passo a passo

Embora não haja nenhum suporte interno para Xcode depuração no Visual Studio para Mac, você pode usar as seguintes etapas para fazer isso:

1. Crie um aplicativo iOS de Xcode com a mesma ID de pacote que aquele do seu aplicativo Xamarin.
   
    - Você pode encontrar o identificador de pacote do seu projeto xamarin. IOS, abrindo o **Info. plist** arquivo:

        ![Edição de info. plist](debugging-with-xcode-images/vsmac-infoplist.png "edição Info.list")

    - No Xcode, você deve definir um identificador de pacote ao criar seu projeto ou selecionando o destino do projeto:

        ![Definir o identificador de pacote no Xcode](debugging-with-xcode-images/xcode-bundle.png "definindo o identificador de pacote no Xcode")

2. Altere o projeto do Xcode para aguardar o lançamento em vez de iniciar o aplicativo automaticamente:

    - Abra o **Editar painel de esquema** selecionando **produto > esquema > Editar esquema** ou usando o **cmd⌘ + <** atalho de teclado.

    - Selecione o **executados** esquema e, na parte direita do painel você verá **iniciar** opções. Selecione **Aguarde até que o executável a ser iniciado** e clique em **fechar**.

        ![Aguarde até que o executável a ser iniciado](debugging-with-xcode-images/xcode-schemes.png "Aguarde o executável a ser iniciado")

3. Execute o projeto do Xcode.

    Isso instalará o aplicativo Xcode fictício no seu dispositivo, mas não serão iniciados.

4. Execute o aplicativo Xamarin.

    Xcode deve se conectar ao aplicativo Xamarin quando ele for iniciado.

### <a name="caveats"></a>Advertências

Talvez você precise fazer uma pequena alteração no aplicativo xamarin. IOS toda vez que você iniciar. Caso contrário, o Visual Studio para Mac detectará que o aplicativo não precisa ser compilado *e* já estiver instalado, e ele não reinstalá-lo sobre o aplicativo fictício do Xcode.

## <a name="alternative---using-lldb"></a>Alternativa – usando lldb

Se você estiver familiarizado com o uso **lldb** da linha de comando, há uma solução muito mais simples.

No shell, digite o seguinte comando:

```bash
touch ~/.mtouch-launch-with-lldb
```

Você receberá instruções na **saída do aplicativo** janela sobre o que fazer, mas basicamente, quando você executa seu aplicativo, você poderá usar **lldb** da linha de comando para depurar seu aplicativo.
