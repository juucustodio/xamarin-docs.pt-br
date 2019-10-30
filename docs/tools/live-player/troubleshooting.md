---
title: Solução de problemas Xamarin Live Player
description: Este documento descreve os problemas conhecidos com o Xamarin Live Player e as possíveis correções. Ele aborda problemas de conexão, problemas de configuração e muito mais.
ms.prod: xamarin
ms.assetid: 29A97ADA-80E0-40A1-8B26-C68FFABE7D26
author: davidortinau
ms.author: daortin
ms.date: 06/13/2019
ms.openlocfilehash: d51241bee5f4ddc06032006071fa8296be37f2fb
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73005939"
---
# <a name="troubleshooting-xamarin-live-player"></a>Solução de problemas Xamarin Live Player

![Versão prévia do recurso](~/media/shared/preview.png)

> [!WARNING]
> A visualização do Xamarin Live Player foi encerrada. O aplicativo não está mais disponível. As instruções a seguir são fornecidas para os clientes que continuam a usar a visualização com o Visual Studio 2017.

> [!TIP]
> Você pode usar o [Visualizar XAML](~/xamarin-forms/xaml/xaml-previewer/index.md) no Visual Studio 2019 ou Visual Studio para Mac para exibir os designs de tela ao editá-los.

Este artigo explica as limitações do Live Player e alguns problemas comuns com as etapas para corrigi-los.

## <a name="limitations-of-xamarin-live-player"></a>Limitações de Xamarin Live Player

### <a name="ide-requirements"></a>Requisitos de IDE

A visualização do Live Player está disponível apenas no Visual Studio 2017.

### <a name="device-requirements"></a>Requisitos do dispositivo

O aplicativo Xamarin Live Player dá suporte aos seguintes dispositivos Android:

- Android 4,2 ou posterior.
- ARM-v7a, ARM-V8A, ARM64-V8A, x86 ou x86_64 Processor.

### <a name="ios-limitations"></a>limitações do iOS

O Live Player não está disponível para iOS.

### <a name="xamarinforms-limitations"></a>Limitações do Xamarin. Forms

- Não há suporte para renderizadores personalizados.
- Não há suporte para efeitos.
- Não há suporte para controles personalizados com propriedades vinculáveis personalizadas.
- Não há suporte para recursos inseridos (ou seja, inserir imagens ou outros recursos em um PCL).
- Não há suporte para estruturas MVVM de terceiros (por ex. Prism, MVVM Cross, MVVM Light, etc.).

### <a name="other-project-type-limitations"></a>Outras limitações de tipo de projeto

- O Live Player não se destina a projetos nativos do Android (que usam o Android XML para a interface do usuário).

### <a name="miscellaneous-limitations"></a>Limitações diversas

- Suporte limitado para reflexão (atualmente afeta alguns NuGets populares, como SQLite e Json.NET). Outros NuGets ainda podem ter suporte.
- Algumas classes do sistema não podem ser substituídas (por exemplo, você não pode implementar uma subclasse).
- Alguns recursos de plataforma que exigem provisionamento não podem funcionar no aplicativo Xamarin Live Player (no entanto, ele foi configurado para operações comuns, como acesso à galeria de fotos).
- Os destinos personalizados e as etapas de compilação são ignorados. Por exemplo, ferramentas como Fody, refit, AutoFac e AutoMapper não podem ser incorporadas.
- F#Não há suporte para projetos
- Cenários avançados com classes e interfaces genéricas personalizadas podem não ter suporte.

## <a name="mobile-device-does-not-connect-after-scanning-barcode-or-entering-code"></a>O dispositivo móvel não se conecta após a verificação do código de barras (ou a inserção de códigos)

Ocorre quando o dispositivo móvel que executa o Xamarin Live Player não está na mesma rede que o computador que está executando o IDE. Confira o seguinte:

- Confirme se o dispositivo e o computador estão na mesma rede Wi-Fi.
  - Se o computador também estiver conectado a uma rede com fio, tente desconectar a conexão com fio.
- A rede pode estar firmemente segura (como algumas redes corporativas), bloqueando as portas necessárias para Xamarin Live Player.
- Feche o aplicativo Xamarin Live Player e reinicie-o.

## <a name="error-while-trying-to-deploy-message-in-ide"></a>Mensagem "erro ao tentar implantar" no IDE

**"IOException: não é possível ler dados da conexão de transporte: a operação em um soquete que não é de bloqueio bloquearia"**

Esse erro costuma ocorrer quando o dispositivo móvel que executa o Xamarin Live Player não está na mesma rede que o computador que executa o Visual Studio; Isso geralmente acontece ao se conectar a um dispositivo que foi emparelhado anteriormente com êxito.

- Verifique se o dispositivo e o computador estão na mesma rede Wi-Fi.
- A rede pode estar firmemente segura (como algumas redes corporativas), bloqueando as portas necessárias para Xamarin Live Player. As seguintes portas são necessárias para o Xamarin Live Player:
  - 37847 – acesso à rede interna 
  - 8090 – acesso externo à rede

## <a name="manually-configure-device"></a>Configurar o dispositivo manualmente

Se você não puder se conectar ao seu dispositivo por Wi-Fi, poderá tentar configurar manualmente seu dispositivo por meio do arquivo de configuração, com as seguintes etapas:

**Etapa 1: abrir o arquivo de configuração**

Vá para a pasta de dados do aplicativo:

- Windows: **%USERPROFILE%\AppData\Roaming**
- macOS: **~/Users/$User/.config**

Nesta pasta, você encontrará **PlayerDeviceList. xml** se ele não existir, será necessário criar um.

**Etapa 2: obter endereço IP**

No aplicativo Xamarin Live Player, acesse **sobre > teste de conexão > iniciar o teste de conexão**.

Anote o endereço IP, você precisará do endereço IP listado ao configurar o dispositivo.

**Etapa 3: obter o código de emparelhamento**

Dentro do Xamarin Live Player toque em **emparelhar** ou **emparelhar novamente**e pressione **Enter manualmente**. Um código numérico será exibido, que será necessário para atualizar o arquivo de configuração.

**Etapa 4: gerar GUID**

Vá para: https://www.guidgenerator.com/online-guid-generator.aspx e gere um novo GUID e verifique se a letra maiúscula está ativada.

**Etapa 5: configurar o dispositivo**

Abra o **PlayerDeviceList. xml** em um editor como o Visual Studio ou Visual Studio Code. Você precisa configurar seu dispositivo manualmente neste arquivo. Por padrão, o arquivo deve conter o seguinte elemento XML de `Devices` vazio:

```xml
<DeviceList xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
<Devices>

</Devices>
</DeviceList>
```

**Adicionar dispositivo Android:**

```xml
<PlayerDevice>
<SecretCode>ENTER-PAIR-CODE-HERE</SecretCode>
<UniqueIdentifier>ENTER-GUID-HERE</UniqueIdentifier>
<Name>Android Player</Name>
<Platform>Android</Platform>
<AndroidApiLevel>24</AndroidApiLevel>
<DebuggerEndPoint>ENTER-IP-HERE:37847</DebuggerEndPoint>
<HostEndPoint />
<NeedsAppInstall>false</NeedsAppInstall>
<IsSimulator>false</IsSimulator>
<SimulatorIdentifier />
<LastConnectTimeUtc>2018-01-08T20:34:42.2332328Z</LastConnectTimeUtc>
</PlayerDevice>
```

**Feche e abra novamente o Visual Studio.** Seu dispositivo deve aparecer na lista.

## <a name="type-or-namespace-cannot-be-found-message-in-ide"></a>Mensagem "tipo ou namespace não encontrado" no IDE

Verifique se você selecionou um **projeto de inicialização** que corresponde ao seu tipo de dispositivo (por exemplo, Android) e que a configuração corresponde a esse tipo de dispositivo (por exemplo, **Depurar** para Android).

## <a name="constructor-on-type-interpretedxamarinformsbutton-not-found-message-in-player"></a>"Construtor no tipo ' InterpretedXamarin. Forms. Button ' não encontrado" no Player

Algumas classes do sistema não podem ser substituídas, por exemplo:

```csharp
public class SomeCustomButton : Xamarin.Forms.Button { ... }
```

## <a name="mainactivitycs-resourcelayout-does-not-contain-a-definition-for-main"></a>"MainActivity.cs: ' Resource. layout ' não contém uma definição para ' Main '"

Esse erro ocorre para projetos Android com interfaces de usuário definidas em arquivos AXML.
Atualmente, não há suporte para arquivos AXML no Xamarin Live Player.

### <a name="android-toolbar-and-tabs-render-incorrectly-using-xamarinforms"></a>As guias e a barra de ferramentas do Android são renderizadas incorretamente usando o Xamarin. Forms

Os projetos Android do Xamarin. Forms devem usar "Toolbar. axml" e "Tabbar. axml" para os nomes dos arquivos de layout relevantes. O modelo padrão usa esses nomes; renomeá-los causará problemas de renderização.

## <a name="related-links"></a>Links relacionados

- [Instalação](~/tools/live-player/install.md)
