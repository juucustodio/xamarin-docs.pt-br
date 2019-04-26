---
title: Recursos de segurança e privacidade de iOS
description: Este documento descreve os recursos de segurança e privacidade do iOS e discute como usá-los com o xamarin. IOS. Ele examina as atualizações feitas no iOS 10 e como acessar dados particulares do usuário.
ms.prod: xamarin
ms.assetid: 718C8721-C359-4650-878A-D68E159A3F53
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 119fd478727a002622861c94462b93b75720a992
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61400541"
---
# <a name="ios-security-and-privacy-features"></a>Recursos de segurança e privacidade de iOS

_Este artigo aborda o trabalho com segurança e privacidade no iOS e como eles afetam um aplicativo xamarin. IOS._

Apple fez várias melhorias de segurança e privacidade no iOS 10 (e superior) que ajudarão o desenvolvedor melhorar a segurança dos seus aplicativos e garantir a privacidade do usuário final. Este artigo abordará a implementação desses recursos em um aplicativo xamarin. IOS.
    
<a name="General-Enhancements" />

## <a name="general-enhancements"></a>Aprimoramentos gerais

Foram feitas as seguintes alterações gerais para segurança e privacidade no iOS 10:

- O Common Data Security arquitetura (CDSA) API foi preterida e deve ser substituído com a API SecKey para gerar as chaves assimétricas.
- O novo `NSAllowsArbitraryLoadsInWebContent` chave pode ser adicionada à um aplicativo **Info. plist** de arquivo e permitirá que as páginas da web para carregar corretamente enquanto a proteção de segurança de transporte da Apple (ATS) ainda está habilitada para o restante do aplicativo. Para obter mais informações, consulte nosso [segurança de transporte de aplicativo](~/ios/app-fundamentals/ats.md) documentação.
- Como a nova área de transferência no iOS 10 e no macOS Sierra permite que o usuário copiar e colar entre dispositivos, a API foi expandida para permitir uma área de transferência a ser limitado a um dispositivo específico e data/hora a ser limpa automaticamente em um determinado ponto. Além disso, áreas de trabalho nomeadas não são persistentes e devem ser substituídas com os contêineres de área de trabalho compartilhados.
- Para todas as conexões SSL/TLS, a criptografia simétrica de RC4 agora é desabilitada por padrão. Além disso, a API de transporte seguro não oferece suporte a SSLv3 e é recomendável que o desenvolvedor parar usando criptografia SHA-1 e 3DES assim que possível.

<a name="Accessing-Private-User-Data" />

## <a name="accessing-private-user-data"></a>Acessando dados do usuário privada

Aplicativos que executam o iOS 10 (ou posterior) estaticamente devem declarar sua intenção para acessar informações do usuário ou recursos específicos, inserindo uma ou mais chaves de privacidade em seus **Info. plist** arquivos explicam ao usuário por que o aplicativo deseja obter acesso.

> [!IMPORTANT]
> Aplicativos que não fornecer as chaves necessárias serão silenciosamente encerradas pelo sistema quando eles tentam acessar uma das informações do usuário, ou a recursos restritos _sem erro_! Se um aplicativo começa a falhar inesperadamente no iOS 10, verifique se todos os necessárias **Info. plist** foram especificados.

A seguir privacidade relacionadas as chaves estão disponíveis:

- **Privacidade – descrição de uso de Apple Music** (`NSAppleMusicUsageDescription`)-permite que o desenvolvedor descrever por que o aplicativo deseja acessar a biblioteca de mídia do usuário.
- **Privacidade – descrição de uso periférico do Bluetooth** (`NSBluetoothPeripheralUsageDescription`)-permite que o desenvolvedor descrever por que o aplicativo deseja acessar o Bluetooth no dispositivo do usuário.
- **Privacidade – descrição do uso de calendários** (`NSCalendarsUsageDescription`)-permite que o desenvolvedor descrever por que o aplicativo deseja acessar o calendário do usuário.
- **Privacidade – descrição do uso de câmera** (`NSCameraUsageDescription`)-permite que o desenvolvedor descrever por que o aplicativo deseja acessar a câmera do dispositivo.
- **Privacidade – descrição do uso de contatos** (`NSContactsUsageDescription`)-permite que o desenvolvedor descrever por que o aplicativo deseja acessar os contatos do usuário.
- **Privacidade – descrição de uso do compartilhamento de integridade** (`NSHealthShareUsageDescription`)-permite que o desenvolvedor descrever por que o aplicativo deseja acessar os dados de integridade do usuário. Para obter mais informações, consulte da Apple [referência de classe HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore).
- **Privacidade – descrição de uso de atualização de integridade** (`NSHealthUpdateUsageDescription`)-permite que o desenvolvedor descrever por que o aplicativo deseja editar dados de integridade do usuário. Para obter mais informações, consulte da Apple [referência de classe HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore).
- **Privacidade – descrição de uso do HomeKit** (`NSHomeKitUsageDescription`)-permite que o desenvolvedor descrever por que o aplicativo deseja acessar dados de configuração do usuário HomeKit.
- **Privacidade – descrição de uso sempre local** (`NSLocationAlwaysUsageDescription`)-permite que o desenvolvedor descrever por que o aplicativo deseja sempre têm acesso ao local do usuário.
- [Preterido] **Privacidade – descrição do local de uso** (`NSLocationUsageDescription`)-permite que o desenvolvedor descrever por que o aplicativo deseja acessar o local do usuário. *OBSERVAÇÃO: Essa chave foi preterida no iOS 8 (e superior). Use `NSLocationAlwaysUsageDescription` ou `NSLocationWhenInUseUsageDescription` em vez disso.*
- **Privacidade – local quando em uso descrição de uso** (`NSLocationWhenInUseUsageDescription`)-permite que o desenvolvedor descrever por que o aplicativo deseja acessar o local do usuário enquanto ele está em execução.
- [Preterido] **Privacidade – descrição de uso da biblioteca de mídia** -permite que o desenvolvedor descrever por que o aplicativo deseja acessar a biblioteca de mídia do usuário. *OBSERVAÇÃO: Essa chave foi preterida no iOS 8 (e superior). Use `NSAppleMusicUsageDescription` em vez disso.*
- **Privacidade – descrição de uso do microfone** (`NSMicrophoneUsageDescription`)-permite que o desenvolvedor descrever por que o aplicativo deseja acessar o microfone de dispositivos.
- **Privacidade – descrição do uso de movimento** (`NSMotionUsageDescription`)-permite que o desenvolvedor descrever por que o aplicativo deseja acessar o acelerômetro do dispositivo.
- **Privacidade – descrição de uso da biblioteca de fotos** (`NSPhotoLibraryUsageDescription`)-permite que o desenvolvedor descrever por que o aplicativo deseja acessar a biblioteca de fotos do usuário.
- **Privacidade – descrição do uso de lembretes** (`NSRemindersUsageDescription`)-permite que o desenvolvedor descrever por que o aplicativo deseja acessar lembretes do usuário.
- **Privacidade – descrição de uso do Siri** (`NSSiriUsageDescription`)-permite que o desenvolvedor descrever por que o aplicativo deseja enviar dados de usuário para o Siri.
- **Privacidade – descrição de uso do reconhecimento de fala** (`NSSpeechRecognitionUsageDescription`)-permite que o desenvolvedor descrever por que o aplicativo deseja enviar dados de usuário para servidores de reconhecimento de fala da Apple.
- **Privacidade – descrição de uso do provedor de TV** (`NSVideoSubscriberAccountUsageDescription`)-permite que o desenvolvedor descrever por que o aplicativo deseja acessar a conta do usuário do provedor de TV.

Para obter mais informações sobre como trabalhar com **Info. plist** chaves, consulte da Apple [referência de chave de lista de propriedades de informações](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Introduction/Introduction.html#//apple_ref/doc/uid/TP40009248-SW1).

<a name="Setting-Privacy-Keys" />

## <a name="setting-privacy-keys"></a>Definindo as chaves de privacidade

Faça o seguinte exemplo de como acessar o HomeKit no iOS 10 (e superior), o desenvolvedor precisará adicionar o `NSHomeKitUsageDescription` chave do aplicativo **Info. plist** de arquivo e forneça uma cadeia de caracteres declarando por que o aplicativo deseja acessar HomeKit do usuário banco de dados. Essa cadeia de caracteres será apresentada para o usuário na primeira vez que executar o aplicativo:

![Um exemplo de alerta NSHomeKitUsageDescription](security-privacy-images/info01.png "um exemplo de alerta NSHomeKitUsageDescription")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Xamarin. IOS para Visual Studio atualmente não dá suporte a edição do **Info. plist** editor de manifesto de chaves de privacidade de dentro do iOS padrão. Em vez disso, você precisará usar o editor de PList genérico, portanto, faça o seguinte:

1. Com o botão direito no **Info. plist** arquivo na **Gerenciador de soluções** e selecione **abrir com...** .
2. Selecione o **Editor de PList genérico** na lista de programas para abrir o arquivo, em seguida, clique em **Okey**.

    ![Selecione o Editor de PList genérico](security-privacy-images/InfoEditorSelectionVs.png "selecione o Editor de PList genérico")
3. Clique o **+** botão na última linha no editor para adicionar uma nova entrada à lista. Isso será chamado "Propriedade personalizada", com o tipo definido como `String` e um valor vazio.
4. Clique no nome de propriedade e uma lista suspensa será exibida.
5. Na lista suspensa, selecione uma chave de privacidade (como **privacidade – descrição de uso do HomeKit**): 

    ![Selecione uma chave de privacidade](security-privacy-images/InfoPListEditorSelectKey.png "selecionar uma chave de privacidade")
6. Insira uma descrição para a coluna de valor para por que o aplicativo deseja acessar as informações de determinado recurso ou de usuário: 

    ![Insira uma descrição](security-privacy-images/InfoPListSetValue.png "insira uma descrição")
7. Salve as alterações no arquivo.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Para definir qualquer uma das chaves de privacidade, faça o seguinte:

1. Clique duas vezes no arquivo **Info.plist** no **Gerenciador de Soluções** para abri-lo para edição.
2. Na parte inferior da tela, alterne para o **origem** modo de exibição.
3. Adicione um novo **entrada** à lista.
4. Na lista suspensa, selecione uma chave de privacidade (como **privacidade – descrição de uso do HomeKit**): 

    ![Selecione uma chave de privacidade](security-privacy-images/info02.png "selecionar uma chave de privacidade")
5. Insira uma descrição para por que o aplicativo deseja acessar as informações de determinado recurso ou de usuário: 

    ![Insira uma descrição](security-privacy-images/info03.png "insira uma descrição")
6. Salve as alterações no arquivo.

-----

> [!IMPORTANT]
> No exemplo fornecido acima, Falha ao definir a `NSHomeKitUsageDescription` principais na **Info. plist** arquivo resultaria no aplicativo _falhando silenciosamente_ (que está sendo fechado pelo sistema em tempo de execução) sem erros quando executado no iOS 10 ( ou superior).

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo abordou a segurança e privacidade as alterações que Apple tem no iOS 10 e como eles afetam um aplicativo xamarin. IOS.

## <a name="related-links"></a>Links relacionados

- [Amostras do iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
