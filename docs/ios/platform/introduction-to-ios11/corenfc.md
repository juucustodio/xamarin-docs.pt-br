---
title: Básico NFC no Xamarin. iOS
description: Este documento descreve como ler marcas de comunicação de campo Near no Xamarin. iOS usando as APIs introduzidas no iOS 11.
ms.prod: xamarin
ms.technology: xamarin-ios
ms.assetid: 846B59D3-F66A-48F3-A78C-84217697194E
author: davidortinau
ms.author: daortin
ms.date: 09/25/2017
ms.openlocfilehash: 17f5caa7849b076fd8ac2b7a22459fbf8fed1c9d
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91432803"
---
# <a name="core-nfc-in-xamarinios"></a>Básico NFC no Xamarin. iOS

_Lendo marcas de NFC (comunicação a curta distância) usando o iOS 11_

CoreNFC é uma nova estrutura no iOS 11 que fornece acesso ao rádio NFC ( _comunicação a curta distância_ ) para ler marcas de dentro de aplicativos. O CoreNFC funciona nos modelos iPhone 7, iPhone 7 Plus, iPhone 8, iPhone 8 mais, iPhone X, iPhone XS e iPhone 11 (enquanto os modelos iPhone 6 e iPhone 6 Plus têm a funcionalidade de pagamento NFC, eles não dão suporte a CoreNFC).

O leitor de marca NFC em dispositivos iOS dá suporte a todos os tipos de marca NFC de 1 a 5 que contêm informações de _formato de troca de dados_ (NDEF).

Há algumas restrições a serem consideradas:

- CoreNFC dá suporte apenas à leitura de marca (não gravação ou formatação).
- Verificações de marca devem ser iniciadas pelo usuário e expirar após 60 segundos.
- Os aplicativos devem estar visíveis no primeiro plano para verificação.
- O CoreNFC só pode ser testado em dispositivos reais (não no simulador).

Esta página descreve a configuração necessária para usar o CoreNFC e mostra como usar a API usando o [código de exemplo "NFCTagReader"](/samples/xamarin/ios-samples/ios11-nfctagreader).

## <a name="configuration"></a>Configuração

Para habilitar o CoreNFC, você deve configurar três itens em seu projeto:

- Uma chave de privacidade do **info. plist** .
- Uma entrada **. plist de direitos** .
- Um perfil de provisionamento com a funcionalidade de **leitura de marca NFC** .

### <a name="infoplist"></a>Info.plist

Adicione a chave de privacidade e o texto do **NFCReaderUsageDescription** , que é exibido para o usuário enquanto a verificação está ocorrendo. Use uma mensagem apropriada para seu aplicativo (por exemplo, explique a finalidade da verificação):

```xml
<key>NFCReaderUsageDescription</key>
<string>NFC tag to read NDEF messages into the application</string>
```

### <a name="entitlementsplist"></a>Entitlements.plist

Seu aplicativo deve solicitar a capacidade de **leitura de marca de comunicação a curta distância** usando o seguinte par de chave/valor em seus **direitos. plist**:

```xml
<key>com.apple.developer.nfc.readersession.formats</key>
<array>
  <string>NDEF</string>
</array>
```

### <a name="provisioning-profile"></a>Perfil de provisionamento

Crie uma nova **ID de aplicativo** e verifique se o serviço de **leitura de marca NFC** está marcado:

[![Portal do desenvolvedor página nova ID do aplicativo com a marca NFC lendo selecionada](corenfc-images/app-services-nfc-sml.png)](corenfc-images/app-services-nfc.png#lightbox)

Em seguida, você deve criar um novo perfil de provisionamento para essa ID de aplicativo e, em seguida, baixar e instalá-lo em seu Mac de desenvolvimento.

## <a name="reading-a-tag"></a>Lendo uma marca

Quando o projeto estiver configurado, adicione- `using CoreNFC;` o à parte superior do arquivo e siga estas três etapas para implementar a funcionalidade de leitura de marca NFC:

### <a name="1-implement-infcndefreadersessiondelegate"></a>1. implementar `INFCNdefReaderSessionDelegate`

A interface tem dois métodos a serem implementados:

- `DidDetect` – Chamado quando uma marca é lida com êxito.
- `DidInvalidate` – Chamado quando ocorre um erro ou o tempo limite de 60 segundos é atingido.

#### <a name="diddetect"></a>DidDetect

No código de exemplo, cada mensagem verificada é adicionada a uma exibição de tabela:

```csharp
public void DidDetect(NFCNdefReaderSession session, NFCNdefMessage[] messages)
{
    foreach (NFCNdefMessage msg in messages)
    {  // adds the messages to a list view
        DetectedMessages.Add(msg);
    }
    DispatchQueue.MainQueue.DispatchAsync(() =>
    {
        this.TableView.ReloadData();
    });
}
```

Esse método pode ser chamado várias vezes (e uma matriz de mensagens pode ser passada) se a sessão permitir várias leituras de marca. Isso é definido usando o terceiro parâmetro do `Start` método (explicado na [etapa 2](#step2)).

#### <a name="didinvalidate"></a>DidInvalidate

A invalidação pode ocorrer por vários motivos:

- Ocorreu um erro durante a verificação.
- O aplicativo parou de estar em primeiro plano.
- O usuário optou por cancelar a verificação.
- A verificação foi cancelada pelo aplicativo.

O código a seguir mostra como tratar um erro:

```csharp
public void DidInvalidate(NFCNdefReaderSession session, NSError error)
{
    var readerError = (NFCReaderError)(long)error.Code;
    if (readerError != NFCReaderError.ReaderSessionInvalidationErrorFirstNDEFTagRead &&
        readerError != NFCReaderError.ReaderSessionInvalidationErrorUserCanceled)
    {
      // some error handling
    }
}
```

Depois que uma sessão for invalidada, um novo objeto de sessão deverá ser criado para verificar novamente.

<a name="step2"></a>

### <a name="2-start-an-nfcndefreadersession"></a>2. iniciar um `NFCNdefReaderSession`

A verificação deve começar com uma solicitação de usuário, como um pressionamento de botão.
O código a seguir cria e inicia uma sessão de verificação:

```csharp
Session = new NFCNdefReaderSession(this, null, true);
Session?.BeginSession();
```

Os parâmetros para o `NFCNdefReaderSession` Construtor são os seguintes:

- `delegate` – Uma implementação de `INFCNdefReaderSessionDelegate` . No código de exemplo, o delegado é implementado no controlador de exibição de tabela, portanto, `this` é usado como o parâmetro delegado.
- `queue` – A fila em que os retornos de chamada são tratados. Pode ser `null` , nesse caso, não se esqueça de usar o `DispatchQueue.MainQueue` ao atualizar os controles da interface do usuário (conforme mostrado no exemplo).
- `invalidateAfterFirstRead` – Quando `true` , a verificação para após a primeira verificação bem-sucedida; quando a `false` verificação continuará e vários resultados forem retornados até que a verificação seja cancelada ou o tempo limite de 60 segundos seja atingido.

### <a name="3-cancel-the-scanning-session"></a>3. cancelar a sessão de verificação

O usuário pode cancelar a sessão de verificação por meio de um botão fornecido pelo sistema na interface do usuário:

![Botão Cancelar durante a verificação](corenfc-images/scan-cancel-sml.png)

O aplicativo pode cancelar programaticamente a verificação chamando o `InvalidateSession` método:

```csharp
Session.InvalidateSession();
```

Em ambos os casos, o método do delegado `DidInvalidate` será chamado.

## <a name="summary"></a>Resumo

O CoreNFC permite que seu aplicativo leia dados de marcas NFC. Ele dá suporte à leitura de uma variedade de formatos de marca (NDEF tipos de 1 a 5), mas não oferece suporte a gravação ou formatação.

## <a name="related-links"></a>Links Relacionados

- [NFCTagReader (exemplo)](/samples/xamarin/ios-samples/ios11-nfctagreader)
- [Introdução ao Core NFC (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/718/)