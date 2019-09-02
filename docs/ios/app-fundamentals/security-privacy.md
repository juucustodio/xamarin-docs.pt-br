---
title: Recursos de privacidade e segurança do iOS
description: Este documento descreve os recursos de segurança e privacidade do iOS e discute como usá-los com o Xamarin. iOS. Ele analisa as atualizações feitas no iOS 10 e como acessar dados privados do usuário.
ms.prod: xamarin
ms.assetid: 718C8721-C359-4650-878A-D68E159A3F53
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 5c818cac3b26e94710a64938a80690b8d4946320
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/31/2019
ms.locfileid: "70200206"
---
# <a name="ios-security-and-privacy-features"></a>Recursos de privacidade e segurança do iOS

_Este artigo aborda como trabalhar com segurança e privacidade no iOS e como eles afetam um aplicativo Xamarin. iOS._

A Apple fez vários aprimoramentos na segurança e na privacidade no iOS 10 (e superior) que ajudarão o desenvolvedor a melhorar a segurança de seus aplicativos e garantir a privacidade do usuário final. Este artigo abordará a implementação desses recursos em um aplicativo Xamarin. iOS.

<a name="General-Enhancements" />

## <a name="general-enhancements"></a>Aprimoramentos gerais

As seguintes alterações gerais foram feitas para segurança e privacidade no iOS 10:

- A API de CDSA (arquitetura de segurança de dados comum) foi preterida e deve ser substituída pela API SecKey para gerar chaves assimétricas.
- A nova `NSAllowsArbitraryLoadsInWebContent` chave pode ser adicionada ao arquivo **info. plist** do aplicativo e permitirá que as páginas da Web sejam carregadas corretamente enquanto a proteção de segurança de transporte da Apple (ATS) ainda estiver habilitada para o restante do aplicativo. Para obter mais informações, consulte nossa documentação de [segurança de transporte de aplicativo](~/ios/app-fundamentals/ats.md) .
- Como a nova área de transferência no iOS 10 e macOS Sierra permite que o usuário Copie e cole entre os dispositivos, a API foi expandida para permitir que uma área de transferência seja limitada a um dispositivo específico e tenha o carimbo de data/hora para ser limpo automaticamente em um determinado ponto. Além disso, os chamados pasteboards não são mais mantidos e devem ser substituídos pelos contêineres de área de ti compartilhados.
- Para todas as conexões SSL/TLS, a cifra simétrica RC4 agora está desabilitada por padrão. Além disso, a API de transporte seguro não dá mais suporte a SSLv3 e é recomendável que o desenvolvedor pare de usar a criptografia SHA-1 e 3DES assim que possível.

<a name="Accessing-Private-User-Data" />

## <a name="accessing-private-user-data"></a>Acessando dados de usuário privado

Aplicativos em execução no iOS 10 (ou posterior) devem declarar estaticamente sua intenção de acessar recursos específicos ou informações do usuário inserindo uma ou mais chaves de privacidade em seus arquivos **info. plist** que explicam ao usuário por que o aplicativo deseja obter acesso.

> [!IMPORTANT]
> Os aplicativos que não fornecerem as chaves necessárias serão encerrados silenciosamente pelo sistema quando tentarem acessar um dos recursos restritos ou informações do usuário, _sem erros_! Se um aplicativo começar a falhar inesperadamente no iOS 10, verifique se todas as **informações necessárias. plist** foram especificadas.

As seguintes chaves relacionadas à privacidade estão disponíveis:

- **Privacidade – descrição do uso de Apple Music** (`NSAppleMusicUsageDescription`) – Permite que o desenvolvedor descreva por que o aplicativo deseja acessar a biblioteca de mídia do usuário.
- **Privacidade – descrição do uso de periféricos Bluetooth** (`NSBluetoothPeripheralUsageDescription`) – Permite que o desenvolvedor descreva por que o aplicativo deseja acessar o Bluetooth no dispositivo do usuário.
- **Privacidade – descrição do uso de calendários** (`NSCalendarsUsageDescription`) – Permite que o desenvolvedor descreva por que o aplicativo deseja acessar o calendário do usuário.
- **Privacidade – descrição do uso da câmera** (`NSCameraUsageDescription`) – Permite que o desenvolvedor descreva por que o aplicativo deseja acessar a câmera do dispositivo.
- **Privacidade – descrição do uso de contatos** (`NSContactsUsageDescription`) – Permite que o desenvolvedor descreva por que o aplicativo deseja acessar os contatos do usuário.
- **Privacidade – descrição do uso do compartilhamento de integridade** (`NSHealthShareUsageDescription`) – Permite que o desenvolvedor descreva por que o aplicativo deseja acessar os dados de integridade do usuário. Para obter mais informações, consulte a [referência de classe HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore)da Apple.
- **Privacidade – descrição do uso da atualização de integridade** (`NSHealthUpdateUsageDescription`) – Permite que o desenvolvedor descreva por que o aplicativo deseja editar os dados de integridade do usuário. Para obter mais informações, consulte a [referência de classe HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore)da Apple.
- **Privacidade – descrição do uso do homekit** (`NSHomeKitUsageDescription`) – Permite que o desenvolvedor descreva por que o aplicativo deseja acessar os dados de configuração do homekit do usuário.
- **Privacidade – descrição de uso de sempre local** (`NSLocationAlwaysUsageDescription`) – Permite que o desenvolvedor descreva por que o aplicativo deseja sempre ter acesso ao local do usuário.
- Preterido **Privacidade – descrição do uso do local** (`NSLocationUsageDescription`) – Permite que o desenvolvedor descreva por que o aplicativo deseja acessar o local do usuário. *OBSERVAÇÃO: Essa chave foi preterida no iOS 8 (e superior). Use `NSLocationAlwaysUsageDescription` ou`NSLocationWhenInUseUsageDescription` em vez disso.*
- **Privacidade – localização na descrição de uso em uso** (`NSLocationWhenInUseUsageDescription`) – Permite que o desenvolvedor descreva por que o aplicativo deseja acessar o local do usuário enquanto ele está em execução.
- Preterido **Privacidade – descrição do uso da biblioteca de mídia** – permite que o desenvolvedor descreva por que o aplicativo deseja acessar a biblioteca de mídia do usuário. *OBSERVAÇÃO: Essa chave foi preterida no iOS 8 (e superior). Use `NSAppleMusicUsageDescription` em vez disso.*
- **Privacidade – descrição do uso do microfone** (`NSMicrophoneUsageDescription`) – Permite que o desenvolvedor descreva por que o aplicativo deseja acessar o microfone dos dispositivos.
- **Privacidade – descrição do uso de movimento** (`NSMotionUsageDescription`) – Permite que o desenvolvedor descreva por que o aplicativo deseja acessar o acelerômetro do dispositivo.
- **Privacidade – descrição de uso da biblioteca de fotos** (`NSPhotoLibraryUsageDescription`) – Permite que o desenvolvedor descreva por que o aplicativo deseja acessar a biblioteca de fotos do usuário.
- **Privacidade – descrição do uso de lembretes** (`NSRemindersUsageDescription`) – Permite que o desenvolvedor descreva por que o aplicativo deseja acessar os lembretes do usuário.
- **Privacidade – descrição do uso do Siri** (`NSSiriUsageDescription`) – Permite que o desenvolvedor descreva por que o aplicativo deseja enviar dados de usuário para Siri.
- **Privacidade – descrição do uso do reconhecimento de fala** (`NSSpeechRecognitionUsageDescription`) – Permite que o desenvolvedor descreva por que o aplicativo deseja enviar dados de usuário para os servidores de reconhecimento de fala da Apple.
- **Privacidade – descrição de uso do provedor de TV** (`NSVideoSubscriberAccountUsageDescription`) – Permite que o desenvolvedor descreva por que o aplicativo deseja acessar a conta do provedor de TV do usuário.

Para obter mais informações sobre como trabalhar com chaves **info. plist** , consulte [referência de chave de lista de propriedades de informações](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Introduction/Introduction.html#//apple_ref/doc/uid/TP40009248-SW1)da Apple.

<a name="Setting-Privacy-Keys" />

## <a name="setting-privacy-keys"></a>Definindo chaves de privacidade

Veja o exemplo a seguir de como acessar homekit no Ios 10 (e superior), o desenvolvedor precisará adicionar `NSHomeKitUsageDescription` a chave ao arquivo **info. plist** do aplicativo e fornecer uma cadeia de caracteres declarando por que o aplicativo deseja acessar o banco de dados homekit do usuário. Essa cadeia de caracteres será apresentada ao usuário na primeira vez em que executar o aplicativo:

![Um alerta de exemplo do NSHomeKitUsageDescription] (security-privacy-images/info01.png "Um alerta de exemplo do NSHomeKitUsageDescription")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

O Xamarin. iOS para Visual Studio atualmente não oferece suporte à edição das chaves de privacidade **info. plist** de dentro do editor de manifesto do IOS padrão. Em vez disso, você precisará usar o editor do PList genérico, portanto, faça o seguinte:

1. Clique com o botão direito do mouse no arquivo **info. plist** na **Gerenciador de soluções** e selecione **abrir com...**.
2. Selecione o **Editor do PList genérico** na lista de programas para abrir o arquivo e clique em **OK**.

    ![Selecione o editor do PList genérico] (security-privacy-images/InfoEditorSelectionVs.png "Selecione o editor do PList genérico")
3. Clique no **+** botão na última linha no editor para adicionar uma nova entrada à lista. Isso será chamado de "propriedade personalizada", com o tipo definido como `String` e um valor vazio.
4. Clique no nome da propriedade e uma lista suspensa será exibida.
5. Na lista suspensa, selecione uma chave de privacidade (como **privacidade – descrição do uso do homekit**): 

    ![Selecione uma chave de privacidade] (security-privacy-images/InfoPListEditorSelectKey.png "Selecione uma chave de privacidade")
6. Insira uma descrição na coluna valor para o motivo pelo qual o aplicativo deseja acessar o recurso ou as informações de usuário determinadas: 

    ![Insira uma descrição] (security-privacy-images/InfoPListSetValue.png "Insira uma descrição")
7. Salve as alterações no arquivo.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Para definir qualquer uma das chaves de privacidade, faça o seguinte:

1. Clique duas vezes no arquivo **Info.plist** no **Gerenciador de Soluções** para abri-lo para edição.
2. Na parte inferior da tela, alterne para o modo de exibição de **código-fonte** .
3. Adicione uma nova **entrada** à lista.
4. Na lista suspensa, selecione uma chave de privacidade (como **privacidade – descrição do uso do homekit**): 

    ![Selecione uma chave de privacidade] (security-privacy-images/info02.png "Selecione uma chave de privacidade")
5. Insira uma descrição para o motivo pelo qual o aplicativo deseja acessar o recurso ou as informações do usuário fornecidas: 

    ![Insira uma descrição] (security-privacy-images/info03.png "Insira uma descrição")
6. Salve as alterações no arquivo.

-----

> [!IMPORTANT]
> No exemplo fornecido acima, falha ao definir a `NSHomeKitUsageDescription` chave no arquivo **info. plist** resultaria no aplicativo _falhar silenciosamente_ (sendo fechado pelo sistema em tempo de execução) sem erro quando executado no Ios 10 (ou superior).

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo abordou as alterações de segurança e privacidade feitas pela Apple no iOS 10 e como elas afetam um aplicativo Xamarin. iOS.

## <a name="related-links"></a>Links relacionados

- [Amostras do iOS 10](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
