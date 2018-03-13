---
title: Principais NFC
description: "Marcas de leitura próximo campo NFC (comunicação) usando iOS 11"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-ios
ms.assetid: 846B59D3-F66A-48F3-A78C-84217697194E
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/25/2016
ms.openlocfilehash: 72c19ef09843c137514983b1d7ee7104e3cb32c5
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2018
---
# <a name="core-nfc"></a>Principais NFC

_Marcas de leitura próximo campo NFC (comunicação) usando iOS 11_

CoreNFC é uma nova estrutura no iOS 11 que fornece acesso para o _comunicação a curta distância_ rádio (NFC) para ler as marcas de dentro de aplicativos. Ele funciona no iPhone 7, mais de 7, 8, 8 Plus e X.

O leitor de marca NFC em dispositivos iOS oferece suporte a todos os tipos de marcas NFC 1 a 5 que contêm _formato de troca de dados NFC_ informações (NDEF).

Há algumas restrições a serem consideradas:

- CoreNFC somente dá suporte a marca de leitura (não gravar ou formatação).
- Verificações de marca devem ser iniciado pelo usuário e o tempo limite depois de 60 segundos.
- Aplicativos devem ser visíveis em primeiro plano para a varredura.
- CoreNFC só pode ser testado em dispositivos reais (não no simulador).

Esta página descreve a configuração necessária para usar CoreNFC e mostra como usar a API usando o ["TFCTagReader" código de exemplo](https://developer.xamarin.com/samples/monotouch/ios11/NFCTagReader/).

## <a name="configuration"></a>Configuração

Para habilitar CoreNFC, você deve configurar três itens em seu projeto:

- Um **Info. plist** chave de privacidade.
- Um **Entitlements.plist** entrada.
- Um perfil de provisionamento com **leitura de marcas NFC** recurso.

### <a name="infoplist"></a>Info.plist

Adicionar o **NFCReaderUsageDescription** chave de privacidade e texto, que é exibido ao usuário enquanto a verificação está ocorrendo. Usar uma mensagem apropriada para seu aplicativo (por exemplo, explique a finalidade da varredura):

```xml
<key>NFCReaderUsageDescription</key>
<string>NFC tag to read NDEF messages into the application</string>
```

### <a name="entitlementsplist"></a>Entitlements.plist

Seu aplicativo deve solicitar a **próximo a leitura de marcas de comunicações do campo** recurso usando o seguinte chave/valor par nos seus **Entitlements.plist**:

```xml
<key>com.apple.developer.nfc.readersession.formats</key>
<array>
  <string>NDEF</string>
</array>
```

### <a name="provisioning-profile"></a>Perfil de provisionamento

Criar um novo **ID do aplicativo** e certifique-se de que o **leitura de marcas NFC** serviço está marcado:

[![Página nova ID de aplicativo de Portal do desenvolvedor com leitura de marcas NFC selecionado](corenfc-images/app-services-nfc-sml.png)](corenfc-images/app-services-nfc.png#lightbox)

Você deve, em seguida, criar um novo perfil de provisionamento para essa ID do aplicativo, baixe e instalá-lo em seu desenvolvimento de Mac.

## <a name="reading-a-tag"></a>Ler uma marca

Depois da configuração do seu projeto, adicione `using CoreNFC;` à parte superior do arquivo e siga estas três etapas para implementar NFC marca a funcionalidade de leitura:

### <a name="1-implement-infcndefreadersessiondelegate"></a>1. Implementar `INFCNdefReaderSessionDelegate`

A interface tem dois métodos a serem implementados:

- `DidDetect` – Chamado quando uma marca foi lida com êxito.
- `DidInvalidate` – Chamado quando ocorre um erro ou o tempo de limite de 60 segundos for alcançado.

#### <a name="diddetect"></a>DidDetect

No código de exemplo, cada mensagem digitalizada é adicionada a um modo de exibição de tabela:

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

Esse método pode ser chamado várias vezes (e uma matriz de mensagens pode ser passada) se a sessão permite que várias leituras de marca. Isso é definido usando o terceiro parâmetro do `Start` método (explicado em [etapa 2](#step2)).

#### <a name="didinvalidate"></a>DidInvalidate

Invalidação pode ocorrer por vários motivos:

- Ocorreu um erro durante a verificação.
- O aplicativo parou de ser em primeiro plano.
- O usuário optou por cancelar a verificação.
- A varredura foi cancelada pelo aplicativo.

O código a seguir mostra como tratar o erro:

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

Verificando deve começar com uma solicitação de usuário, como um pressionamento do botão.
O código a seguir cria e inicia uma sessão de verificação:

```csharp
Session = new NFCNdefReaderSession(this, null, true);
Session?.BeginSession();
```

Os parâmetros para o `NFCNdefReaderSession` construtor são da seguinte maneira:

- `delegate` – Uma implementação de `INFCNdefReaderSessionDelegate`. No código de exemplo, o representante é implementado no controlador de exibição de tabela, portanto `this` é usado como o parâmetro de representante.
- `queue` – A fila retornos de chamada são tratados em. Ele pode ser `null`, nesse caso, não deixe de usar o `DispatchQueue.MainQueue` ao atualizar os controles de interface do usuário (conforme mostrado no exemplo).
- `invalidateAfterFirstRead` – Quando `true`, a varredura é interrompida após a primeira verificação bem-sucedida; quando `false` verificação continuará e retornados vários resultados até que a verificação for cancelada ou o tempo de limite de 60 segundos for alcançado.


### <a name="3-cancel-the-scanning-session"></a>3. Cancela a sessão de verificação

O usuário pode cancelar a sessão de verificação por meio de um botão fornecido pelo sistema na interface do usuário:

![Botão Cancelar durante a verificação](corenfc-images/scan-cancel-sml.png)

O aplicativo por meio de programação pode cancelar a verificação chamando o `InvalidateSession` método:

```csharp
Session.InvalidateSession();
```

Em ambos os casos, o representante `DidInvalidate` método será chamado.

## <a name="summary"></a>Resumo

CoreNFC permite que seu aplicativo ler dados de marcas NFC. Ele oferece suporte à leitura de uma variedade de formatos de marca (tipos NDEF 1 a 5), mas não suporta gravar ou formatação.


## <a name="related-links"></a>Links relacionados

- [NFCTagReader (exemplo)](https://developer.xamarin.com/samples/monotouch/ios11/NFCTagReader/)
- [Introdução ao núcleo NFC (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/718/)
