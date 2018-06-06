---
title: Recursos de segurança e privacidade de iOS
description: Este documento descreve os recursos de segurança e privacidade do iOS e explica como usá-las com xamarin. Ele analisa as atualizações feitas no iOS 10 e como acessar dados particulares do usuário.
ms.prod: xamarin
ms.assetid: 718C8721-C359-4650-878A-D68E159A3F53
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: fd50e42536aabe16de3e44146d28950970dd801d
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34784410"
---
# <a name="ios-security-and-privacy-features"></a>Recursos de segurança e privacidade de iOS

_Este artigo aborda o trabalho com segurança e privacidade no iOS e como eles afetam um aplicativo xamarin._

Apple fez diversos aprimoramentos de segurança e privacidade no iOS 10 (e maior) que podem ajudar o desenvolvedor de melhorar a segurança dos seus aplicativos e garantir a privacidade do usuário final. Este artigo abordará implementar esses recursos em um aplicativo xamarin.

Os tópicos a seguir serão abordados em detalhes:

- [Aprimoramentos gerais](#General-Enhancements)
- [Acessando dados do usuário particular](#Accessing-Private-User-Data)
    - [Configurar as chaves de privacidade](#Setting-Privacy-Keys)
    
<a name="General-Enhancements" />

## <a name="general-enhancements"></a>Aprimoramentos gerais

Foram feitas as seguintes alterações gerais de segurança e privacidade no iOS 10:

- O Common dados segurança arquitetura (CDSA) API foi preterido e deve ser substituído com a API SecKey para gerar chaves assimétricas.
- O novo `NSAllowsArbitraryLoadsInWebContent` chave pode ser adicionada de um aplicativo `Info.plist` de arquivo e permitirá que páginas da web para carregar corretamente enquanto a proteção de segurança de transporte da Apple (ATS) ainda está habilitada para o restante do aplicativo. Para obter mais informações, consulte nosso [segurança de transporte do aplicativo](~/ios/app-fundamentals/ats.md) documentação.
- Como a nova área de transferência no iOS 10 e macOS Serra permite que o usuário copiar e colar entre dispositivos, a API foi expandida para permitir uma área de transferência ser limitada a um dispositivo específico e marca a ser limpo automaticamente em um determinado ponto. Além disso, áreas de trabalho nomeadas não são mais mantidas e devem ser substituídas com os contêineres de área de trabalho compartilhados.
- Para todas as conexões SSL/TLS, a criptografia simétrica RC4 agora é desabilitada por padrão. Além disso, a API de transporte seguro não oferece mais suporte SSLv3 e é recomendável que o desenvolvedor parar usando criptografia SHA-1 e 3DES assim que possível.

<a name="Accessing-Private-User-Data" />

## <a name="accessing-private-user-data"></a>Acessando dados do usuário particular

Aplicativos executados em iOS 10 (ou posterior) estaticamente devem declarar sua intenção para acessar informações do usuário ou recursos específicos, inserindo uma ou mais chaves de privacidade em seus `Info.plist` arquivos explicam para o usuário por que o aplicativo deseja acessar.

> [!IMPORTANT]
> Aplicativos que não fornecem as chaves necessárias serão silenciosamente encerradas pelo sistema quando eles tentam acessar um dos recursos restritos ou informações do usuário, _sem erro_! Se um aplicativo é iniciado com falha inesperada no iOS 10, certifique-se de que todos os `Info.plist` foram especificados.

A seguir privacidade relacionada à chaves estão disponíveis:

- **Privacidade - descrição do uso de música Apple** (`NSAppleMusicUsageDescription`)-permite que o desenvolvedor descrevem por que o aplicativo deseja acessar a biblioteca de mídia do usuário.
- **Privacidade - descrição do uso periféricos Bluetooth** (`NSBluetoothPeripheralUsageDescription`)-permite que o desenvolvedor descrevem por que o aplicativo deseja acessar Bluetooth no dispositivo do usuário.
- **Privacidade - descrição do uso de calendários** (`NSCalendarsUsageDescription`)-permite que o desenvolvedor descrevem por que o aplicativo deseja acessar o calendário do usuário.
- **Privacidade - descrição do uso de câmera** (`NSCameraUsageDescription`)-permite que o desenvolvedor descrevem por que o aplicativo deseja acessar a câmera do dispositivo.
- **Privacidade - descrição do uso de contatos** (`NSContactsUsageDescription`)-permite que o desenvolvedor descrevem por que o aplicativo deseja acessar contatos do usuário.
- **Privacidade - descrição do uso de compartilhamento de integridade** (`NSHealthShareUsageDescription`)-permite que o desenvolvedor descrevem por que o aplicativo deseja acessar dados de integridade do usuário. Para obter mais informações, consulte da Apple [referência de classe HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore).
- **Privacidade - descrição do uso de atualização de integridade** (`NSHealthUpdateUsageDescription`)-permite que o desenvolvedor descrevem por que o aplicativo deseja editar dados de integridade do usuário. Para obter mais informações, consulte da Apple [referência de classe HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore).
- **Privacidade - descrição do uso de HomeKit** (`NSHomeKitUsageDescription`)-permite que o desenvolvedor descrevem por que o aplicativo deseja acessar dados de configuração de HomeKit do usuário.
- **Privacidade - descrição do uso sempre local** (`NSLocationAlwaysUsageDescription`)-permite que o desenvolvedor descrevem por que o aplicativo deseja sempre tenham acesso ao local do usuário.
- [Substituído] **Privacidade - descrição do local de uso** (`NSLocationUsageDescription`)-permite que o desenvolvedor descrevem por que o aplicativo deseja acessar o local do usuário. *Observação: Essa chave foi preterida no iOS 8 (e superior). Use `NSLocationAlwaysUsageDescription` ou `NSLocationWhenInUseUsageDescription` em vez disso.*
- **Privacidade - local quando em uso descrição do uso** (`NSLocationWhenInUseUsageDescription`)-permite que o desenvolvedor descrevem por que o aplicativo deseja acessar o local do usuário enquanto ele está em execução.
- [Substituído] **Privacidade - descrição do uso de biblioteca de mídia** -permite que o desenvolvedor descrevem por que o aplicativo deseja acessar a biblioteca de mídia do usuário. *Observação: Essa chave foi preterida no iOS 8 (e superior). Use `NSAppleMusicUsageDescription` em vez disso.*
- **Privacidade - descrição do uso de microfone** (`NSMicrophoneUsageDescription`)-permite que o desenvolvedor descrevem por que o aplicativo deseja acessar o microfone de dispositivos.
- **Privacidade - descrição do uso de movimento** (`NSMotionUsageDescription`)-permite que o desenvolvedor descrevem por que o aplicativo deseja acessar acelerômetro do dispositivo.
- **Privacidade - descrição do uso de biblioteca de fotos** (`NSPhotoLibraryUsageDescription`)-permite que o desenvolvedor descrevem por que o aplicativo deseja acessar a biblioteca de fotos do usuário.
- **Privacidade - descrição do uso de lembretes** (`NSRemindersUsageDescription`)-permite que o desenvolvedor descrevem por que o aplicativo deseja acessar lembretes do usuário.
- **Privacidade - descrição do uso do Siri** (`NSSiriUsageDescription`)-permite que o desenvolvedor descrevem por que o aplicativo deseja enviar dados de usuário para Siri.
- **Privacidade - descrição do uso de reconhecimento de fala** (`NSSpeechRecognitionUsageDescription`)-permite que o desenvolvedor descrevem por que o aplicativo deseja enviar dados de usuário para servidores de reconhecimento de fala da Apple.
- **Privacidade - descrição do uso de provedor de TV** (`NSVideoSubscriberAccountUsageDescription`)-permite que o desenvolvedor descrevem por que o aplicativo deseja acessar a conta do usuário do provedor de TV.

Para obter mais informações sobre como trabalhar com `Info.plist` chaves, consulte da Apple [informações de referência de chave de lista de propriedade](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Introduction/Introduction.html#//apple_ref/doc/uid/TP40009248-SW1).

<a name="Setting-Privacy-Keys" />

## <a name="setting-privacy-keys"></a>Configurar as chaves de privacidade

Se o exemplo a seguir de acessar HomeKit no iOS 10 (e superior), o desenvolvedor precisa adicionar o `NSHomeKitUsageDescription` chave para o aplicativo `Info.plist` de arquivo e forneça uma cadeia de caracteres declarando por que o aplicativo deseja acessar o banco de dados do usuário HomeKit. Essa cadeia de caracteres será apresentada para o usuário na primeira vez que executar o aplicativo:

[![](security-privacy-images/info01.png "Um exemplo de alerta NSHomeKitUsageDescription")](security-privacy-images/info01.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Xamarin para Visual Studio atual não oferece suporte para editar o aprimoramento de segurança `Info.plist` configurações de dentro do IDE para que seja necessária a seguinte solução alternativa:

1. Abra o `Info.plist` arquivo em um editor de texto externo.
2. Antes que a última `</dict>` nó, adicione o seguinte nó: `<key>NSHomeKitUsageDescription</key>`
3. Adicione o seguinte nó para fornecer a descrição necessária: `<string>Allows the app to control HomeKit enabled devices.</string>`
4. O `Info.plist` arquivo deve ser semelhante ao seguinte: 

    [![](security-privacy-images/info02vs.png "O arquivo Info. plist deve parecer com o seguinte")](security-privacy-images/info02vs.png#lightbox)
4. Salve as alterações no arquivo.
5. Retorne ao Visual Studio e recompilação do aplicativo.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Para definir qualquer uma das chaves de privacidade, faça o seguinte:

1. Clique duas vezes o `Info.plist` arquivo o **Gerenciador de soluções** para abri-lo para edição.
2. Na parte inferior da tela, alterne para o **fonte** exibição.
3. Adicionar um novo **entrada** à lista.
4. Na lista suspensa, selecione uma chave de privacidade (como **privacidade - descrição do uso de HomeKit**): 

    [![](security-privacy-images/info02.png "Selecione uma chave de privacidade")](security-privacy-images/info02.png#lightbox)
5. Insira uma descrição de por que o aplicativo deseja acessar as informações de recurso ou usuário específica: 

    [![](security-privacy-images/info03.png "Insira uma descrição")](security-privacy-images/info03.png#lightbox)
6. Salve as alterações no arquivo.

-----

> [!IMPORTANT]
> No exemplo fornecido acima, Falha ao definir o `NSHomeKitUsageDescription` chave no `Info.plist` arquivo resultaria no aplicativo _silenciosamente falhando_ (que está sendo fechado pelo sistema em tempo de execução) sem erro quando executado no iOS 10 (ou superior).

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo abordou a segurança e alterações de privacidade que Apple fez em 10 de iOS e como eles afetam um aplicativo xamarin.



## <a name="related-links"></a>Links relacionados

- [Exemplos de iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
