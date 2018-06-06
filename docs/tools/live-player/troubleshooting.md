---
title: Solucionando problemas de Player ao vivo do Xamarin
description: Este documento descreve problemas conhecidos com o Xamarin Live Player e possíveis correções. Ele discute problemas de conexão, problemas de configuração e muito mais.
ms.prod: xamarin
ms.assetid: 29A97ADA-80E0-40A1-8B26-C68FFABE7D26
author: topgenorth
ms.author: toopge
ms.date: 05/17/2017
ms.openlocfilehash: 3db14db2c64e024ef1c04275661f610f9407dfb7
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34793738"
---
# <a name="troubleshooting-xamarin-live-player"></a>Solucionando problemas de Player ao vivo do Xamarin

![Recurso de visualização](~/media/shared/preview.png)

Este artigo explica alguns problemas comuns e fornece etapas para corrigi-los.

## <a name="mobile-device-does-not-connect-after-scanning-barcode-or-entering-code"></a>Dispositivo móvel não se conectar após a verificação de código de barras (ou código de inserção)

Ocorre quando o dispositivo móvel que executa Xamarin Live Player não está na mesma rede do computador que executa o IDE. Verifique o seguinte:

- Confirme se o dispositivo e o computador estão na mesma rede Wi-Fi.
  - Se o computador também é conectado a uma rede com fio, tente desconectar a conexão com fio.
- A rede pode ser bem protegida (por exemplo, algumas redes corporativas), bloqueando as portas necessárias pelo Xamarin Live Player.
- Feche o aplicativo Xamarin ao vivo Player e reiniciá-lo.

## <a name="error-while-trying-to-deploy-message-in-ide"></a>Mensagem "Erro ao tentar implantar" no IDE

**"IOException: não é possível ler dados de conexão de transporte: operação de soquete sem bloqueio bloquearia"**

Esse erro geralmente é ocorreu quando o dispositivo móvel que executa Xamarin Live Player não está na mesma rede que o computador executando o Visual Studio; Isso geralmente ocorre ao se conectar a um dispositivo que foi anteriormente emparelhado com êxito.

* Verifique se o dispositivo e o computador estão na mesma rede Wi-Fi.
* A rede pode ser bem protegida (por exemplo, algumas redes corporativas), bloqueando as portas necessárias pelo Xamarin Live Player. As portas a seguir são necessárias para o Player de Live Xamarin:
  * 37847 – acesso à rede interna 
  * 8090 – acesso à rede externo

## <a name="manually-configure-device"></a>Configurar o dispositivo manualmente

Se você não pode conectar a seu dispositivo por Wi-Fi pode tentar configurar manualmente seu dispositivo por meio do arquivo de configuração, com as seguintes etapas:

**Etapa 1: Abrir o arquivo de configuração**

Vá até a pasta de dados do aplicativo:

* Windows: **%userprofile%\AppData\Roaming**
* macOS: **~/Users/$USER/.config**

Essa pasta, você encontrará **PlayerDeviceList.xml** se não existir, você precisará criar um.

**Etapa 2: Obter o endereço IP**

No aplicativo Xamarin ao vivo Player, vá para **sobre > teste de Conexão > Iniciar o teste de Conexão**.

Tome nota do endereço IP, será necessário o endereço IP listado quando você configurar seu dispositivo.

**Etapa 3: Obter o código de emparelhamento**

Dentro do toque de Player de Live Xamarin **par** ou **par novamente**, em seguida, pressione **insira manualmente**. Um código numérico será exibido, que você precisará atualizar o arquivo de configuração.

**Etapa 4: Gerar um GUID**

Vá para: https://www.guidgenerator.com/online-guid-generator.aspx e gerar um novo guid e certifique-se de letras maiusculas é no.

**Etapa 5: Configurar o dispositivo**

Abra o **PlayerDeviceList.xml** backup em um editor, como o Visual Studio ou o código do Visual Studio. Você precisa configurar seu dispositivo manualmente neste arquivo. Por padrão, o arquivo deve conter o seguinte vazio `Devices` elemento XML:

```xml
<DeviceList xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
<Devices>

</Devices>
</DeviceList>
```

**Adicione um dispositivo iOS:**

```xml
<PlayerDevice>
<SecretCode>ENTER-PAIR-CODE-HERE</SecretCode>
<UniqueIdentifier>ENTER-GUID-HERE</UniqueIdentifier>
<Name>iPhone Player</Name>
<Platform>iOS</Platform>
<AndroidApiLevel>0</AndroidApiLevel>
<DebuggerEndPoint>ENTER-IP-HERE:37847</DebuggerEndPoint>
<HostEndPoint />
<NeedsAppInstall>false</NeedsAppInstall>
<IsSimulator>false</IsSimulator>
<SimulatorIdentifier />
<LastConnectTimeUtc>2018-01-08T20:36:03.9492291Z</LastConnectTimeUtc>
</PlayerDevice>
```

**Adicione dispositivo Android:**

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

**Feche e reabra o Visual Studio.** Seu dispositivo deve aparecem na lista.

## <a name="type-or-namespace-cannot-be-found-message-in-ide"></a>Mensagem "não é possível localizar tipo ou namespace" no IDE

Verifique se você tiver selecionado um **projeto de inicialização** que corresponde ao tipo de dispositivo (iOS ou Android) e a configuração corresponder (por exemplo, esse tipo de dispositivo **Depurar | iPhone simulador** para iOS).

## <a name="constructor-on-type-interpretedxamarinformsbutton-not-found-message-in-player"></a>Mensagem "Construtor no tipo 'InterpretedXamarin.Forms.Button' não encontrada" no Player

Por exemplo, algumas classes de sistema não podem ser substituídos:

```csharp
public class SomeCustomButton : Xamarin.Forms.Button { ... }
```

## <a name="mainactivitycs-resourcelayout-does-not-contain-a-definition-for-main"></a>"MainActivity.cs: 'Resource.Layout' não contém uma definição para 'Main'"

Esse erro ocorre em projetos Android com interfaces de usuário definidas em arquivos AXML.
Arquivos AXML não têm suporte no momento no Player de Live Xamarin.

### <a name="android-toolbar-and-tabs-render-incorrectly-using-xamarinforms"></a>Android barra de ferramentas e guias renderizam incorretamente usando xamarin. Forms

Projetos Android xamarin. Forms devem usar "Toolbar.axml" e "Tabbar.axml" para os nomes dos arquivos de layout relevantes. O modelo padrão usa esses nomes; Renomear-los causará problemas de renderização.

Relate problemas adicionais em [bugzilla](https://aka.ms/live-player-report-issue).

## <a name="related-links"></a>Links relacionados

- [Limitações](~/tools/live-player/limitations.md)
- [Instalação](~/tools/live-player/install.md)
