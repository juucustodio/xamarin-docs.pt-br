---
title: Core NFC no xamarin. IOS
description: Este documento descreve como ler perto de marcas de comunicação de campo no xamarin. IOS usando as APIs apresentadas no iOS 11.
ms.prod: xamarin
ms.technology: xamarin-ios
ms.assetid: 846B59D3-F66A-48F3-A78C-84217697194E
author: lobrien
ms.author: laobri
ms.date: 09/25/2017
ms.openlocfilehash: 6888f7147796d3c00752d10387c19d0d9f269cad
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61170819"
---
# <a name="core-nfc-in-xamarinios"></a>Core NFC no xamarin. IOS

_Marcas de leitura (NFC comunicação) usando o iOS 11_

CoreNFC é uma nova estrutura no iOS 11, o que fornece acesso para o _comunicação a curta distância_ radio (NFC) para ler as marcas de dentro de aplicativos. Ele funciona no iPhone 7, Plus 7, 8, 8, além disso e X.

O leitor de marca NFC em dispositivos iOS dá suporte a todos os tipos de marcas NFC 1 a 5 que contêm _formato de troca de dados de NFC_ informações (NDEF).

Há algumas restrições a serem consideradas:

- CoreNFC dá suporte apenas a marca de leitura (não gravar ou formatação).
- Verificações de marca devem ser iniciada pelo usuário e o tempo limite depois de 60 segundos.
- Aplicativos devem ser visíveis em primeiro plano para a varredura.
- CoreNFC só pode ser testado em dispositivos reais (não no simulador).

Esta página descreve a configuração necessária para usar CoreNFC e mostra como usar a API usando o [código de exemplo "NFCTagReader"](https://developer.xamarin.com/samples/monotouch/ios11/NFCTagReader/).

## <a name="configuration"></a>Configuração

Para habilitar CoreNFC, você deve configurar três itens em seu projeto:

- Uma **Info. plist** chave de privacidade.
- Uma **Entitlements. plist** entrada.
- Um perfil de provisionamento com **leitura de marca NFC** funcionalidade.

### <a name="infoplist"></a>Info.plist

Adicione a **NFCReaderUsageDescription** chave de privacidade e de texto, que é exibido ao usuário durante a verificação. Usar uma mensagem apropriada para seu aplicativo (por exemplo, explique a finalidade da verificação):

```xml
<key>NFCReaderUsageDescription</key>
<string>NFC tag to read NDEF messages into the application</string>
```

### <a name="entitlementsplist"></a>Entitlements.plist

O aplicativo deve solicitar a **perto de leitura de marca de comunicações do campo** funcionalidade usando a seguinte chave/valor par nos seus **Entitlements. plist**:

```xml
<key>com.apple.developer.nfc.readersession.formats</key>
<array>
  <string>NDEF</string>
</array>
```

### <a name="provisioning-profile"></a>Perfil de provisionamento

Criar um novo **ID do aplicativo** e certifique-se de que o **leitura da marca NFC** serviço está marcado:

[![Página nova ID de aplicativo do Portal do desenvolvedor com a leitura de marca NFC selecionado](corenfc-images/app-services-nfc-sml.png)](corenfc-images/app-services-nfc.png#lightbox)

Você deve, em seguida, criar um novo perfil de provisionamento para essa ID do aplicativo, em seguida, baixar e instalá-lo no desenvolvimento do seu Mac.

## <a name="reading-a-tag"></a>Leitura de uma marca

Depois de configurar seu projeto, adicione `using CoreNFC;` na parte superior do arquivo e siga estas três etapas para implementar o NFC funcionalidade de leitura de marca:

### <a name="1-implement-infcndefreadersessiondelegate"></a>1. Implementar `INFCNdefReaderSessionDelegate`

A interface possui dois métodos a serem implementados:

- `DidDetect` – Chamado quando uma marca é lido com êxito.
- `DidInvalidate` – Chamados quando ocorre um erro ou o tempo de limite de 60 segundos for alcançado.

#### <a name="diddetect"></a>DidDetect

No código de exemplo, cada mensagem examinada é adicionada a uma exibição de tabela:

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

Esse método pode ser chamado várias vezes (e uma matriz de mensagens pode ser passada em) se a sessão permite que várias leituras de marca. Isso é definido usando o terceiro parâmetro do `Start` método (explicado [etapa 2](#step2)).

#### <a name="didinvalidate"></a>DidInvalidate

Invalidação pode ocorrer por vários motivos:

- Ocorreu um erro durante a verificação.
- O aplicativo parou de ser em primeiro plano.
- O usuário optou por cancelar o exame.
- A varredura foi cancelada pelo aplicativo.

O código a seguir mostra como manipular um erro:

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

Depois que uma sessão foi invalidada, um novo objeto de sessão deve ser criado para verificar novamente.

<a name="step2" />

### <a name="2-start-an-nfcndefreadersession"></a>2. Iniciar um `NFCNdefReaderSession`

Verificação deve começar com uma solicitação de usuário, como um pressionamento de botão.
O código a seguir cria e inicia uma sessão de verificação:

```csharp
Session = new NFCNdefReaderSession(this, null, true);
Session?.BeginSession();
```

Os parâmetros para o `NFCNdefReaderSession` construtor são da seguinte maneira:

- `delegate` – Uma implementação de `INFCNdefReaderSessionDelegate`. No código de exemplo, o delegado é implementado no controlador de exibição de tabela, portanto `this` é usado como o parâmetro delegado.
- `queue` – A fila retornos de chamada são tratados. Ele pode ser `null`, neste caso, certifique-se de usar o `DispatchQueue.MainQueue` ao atualizar os controles de interface do usuário (conforme mostrado no exemplo).
- `invalidateAfterFirstRead` – Quando `true`, a verificação é interrompida após a primeira verificação bem-sucedida; quando `false` verificação continuará e retornados vários resultados até que a verificação for cancelada ou o tempo de limite de 60 segundos for alcançado.


### <a name="3-cancel-the-scanning-session"></a>3. Cancelar a sessão de verificação

O usuário pode cancelar a sessão de verificação por meio de um botão fornecido pelo sistema na interface do usuário:

![Botão Cancelar ao examinar o](corenfc-images/scan-cancel-sml.png)

O aplicativo por meio de programação pode cancelar a verificação chamando o `InvalidateSession` método:

```csharp
Session.InvalidateSession();
```

Em ambos os casos, o delegado `DidInvalidate` método será chamado.

## <a name="summary"></a>Resumo

CoreNFC permite que seu aplicativo ler dados de marcas NFC. Ele dá suporte à leitura de uma variedade de formatos de marca (tipos NDEF 1 a 5), mas não suporta gravar ou formatação.


## <a name="related-links"></a>Links relacionados

- [NFCTagReader (amostra)](https://developer.xamarin.com/samples/monotouch/ios11/NFCTagReader/)
- [Introdução ao Core NFC (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/718/)
