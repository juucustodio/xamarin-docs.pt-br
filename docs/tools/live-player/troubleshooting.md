---
title: Solução de problemas de Xamarin Live Player
description: Este documento descreve problemas conhecidos com o Xamarin Live Player e possíveis correções. Ele aborda problemas de conexão, problemas de configuração e muito mais.
ms.prod: xamarin
ms.assetid: 29A97ADA-80E0-40A1-8B26-C68FFABE7D26
author: lobrien
ms.author: laobri
ms.date: 06/13/2019
ms.openlocfilehash: bf0186b55b14d9797397b98390f4d825d669d0f4
ms.sourcegitcommit: 93b1e2255d59c8ca6674485938f26bd425740dd1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67157689"
---
# <a name="troubleshooting-xamarin-live-player"></a>Solução de problemas de Xamarin Live Player

![Versão prévia do recurso](~/media/shared/preview.png)

> [!WARNING]
> O Xamarin Live Player visualização foi encerrada. O aplicativo não está mais disponível. As instruções a seguir são fornecidas para clientes que continuam a usar a visualização com o Visual Studio 2017...

> [!TIP]
> Você pode usar o [XAML pré-visualizador](~/xamarin-forms/xaml/xaml-previewer/index.md) no 2019 do Visual Studio ou Visual Studio para Mac para exibir seus designs de tela, conforme você editá-los.

Este artigo explica as limitações do Live Player e alguns problemas comuns com as etapas para corrigi-los.

## <a name="limitations-of-xamarin-live-player"></a>Limitações do Xamarin Live Player

### <a name="ide-requirements"></a>Requisitos de IDE

Visualização Live Player só está disponível no Visual Studio 2017.

### <a name="device-requirements"></a>Requisitos do dispositivo

O aplicativo Xamarin Live Player dá suporte aos seguintes dispositivos Android:

- Android 4.2 ou posterior.
- ARM-v7a, v8a ARM, ARM64-v8a, x86 ou processador x86_64.

### <a name="ios-limitations"></a>limitações do iOS

Live Player não está disponível para iOS.

### <a name="xamarinforms-limitations"></a>Limitações do xamarin. Forms

- Renderizadores personalizados não têm suporte.
- Efeitos não têm suporte.
- Não há suporte para controles personalizados com propriedades vinculáveis personalizadas.
- Não há suporte para recursos inseridos (ie. inserção de imagens ou outros recursos em uma PCL).
- Estruturas MVVM de terceiros não têm suporte (ie. O Prism, Mvvm cruzada, o Mvvm Light, etc.).

### <a name="other-project-type-limitations"></a>Outras limitações de tipo de projeto

- Live Player não se destina a projetos do Android nativos (que usam o XML do Android para a interface do usuário).

### <a name="miscellaneous-limitations"></a>Limitações de diversas

- Suporte limitado para reflexão (atualmente afeta alguns NuGets populares, como o SQLite e Json.NET). Outros NuGets ainda podem ter suporte.
- Algumas classes de sistema não podem ser substituídos (por exemplo, você não pode implementar uma subclasse).
- Alguns recursos de plataforma que exigem o provisionamento não funcionam no aplicativo do Xamarin Live Player (no entanto ele foi configurado para operações comuns, como acesso de galeria de fotos).
- Destinos personalizados e etapas de compilação são ignoradas. Por exemplo, ferramentas como o Fody, Refit, AutoFac e AutoMapper não podem ser incorporadas.
- F#Não há suporte para projetos
- Cenários avançados com interfaces e classes genéricas personalizadas podem não ter suporte.

## <a name="mobile-device-does-not-connect-after-scanning-barcode-or-entering-code"></a>Não se conectar a dispositivos móveis após a verificação de código de barras (ou inserindo código)

Ocorre quando o dispositivo móvel em execução Xamarin Live Player não está na mesma rede que o computador que executa o IDE. Confira o seguinte:

- Confirme se o dispositivo e o computador estão na mesma rede Wi-Fi.
  - Se o computador também é conectado a uma rede com fio, tente a operação de desconectar a conexão com fio.
- A rede pode ser totalmente protegida (por exemplo, algumas redes corporativas) bloqueando as portas necessárias pelo Xamarin Live Player.
- Feche o aplicativo Xamarin Live Player e reiniciá-lo.

## <a name="error-while-trying-to-deploy-message-in-ide"></a>Mensagem "Erro ao tentar implantar" no IDE

**"IOException: não é possível ler dados de conexão de transporte: Operação em um soquete sem bloqueio bloquearia"**

Esse erro geralmente está ocorrendo quando o dispositivo móvel em execução Xamarin Live Player não está na mesma rede que o computador executando o Visual Studio; Isso geralmente ocorre ao se conectar a um dispositivo que anteriormente foi emparelhado com êxito.

* Verifique se o dispositivo e o computador estão na mesma rede Wi-Fi.
* A rede pode ser totalmente protegida (por exemplo, algumas redes corporativas) bloqueando as portas necessárias pelo Xamarin Live Player. As seguintes portas são necessárias para o Xamarin Live Player:
  * 37847 – acesso à rede interno 
  * 8090 – acesso à rede externo

## <a name="manually-configure-device"></a>Configurar manualmente o dispositivo

Se você não pode conectar seu dispositivo por Wi-Fi que você pode tentar configurar manualmente seu dispositivo por meio do arquivo de configuração, com as seguintes etapas:

**Etapa 1: Abrir arquivo de configuração**

Vá para a pasta de dados do aplicativo:

* Windows: **%userprofile%\AppData\Roaming**
* macOS: **~/Users/$USER/.config**

Essa pasta, você encontrará **PlayerDeviceList.xml** se não existir, você precisará criá-lo.

**Etapa 2: Obter o endereço IP**

No aplicativo do Xamarin Live Player, vá para **sobre > teste de Conexão > Iniciar o teste de Conexão**.

Tome nota do endereço IP, será necessário o endereço IP listado quando você configura seu dispositivo.

**Etapa 3: Obtenha o código de emparelhamento**

Dentro do programa tap Xamarin Live Player **par** ou **par novamente**, em seguida, pressione **inserir manualmente**. Um código numérico será exibido, que você precisará atualizar o arquivo de configuração.

**Etapa 4: Gerar GUID**

Vá para: https://www.guidgenerator.com/online-guid-generator.aspx e gerar um novo guid e certifique-se letras maiusculas em.

**Etapa 5: Configurar dispositivo**

Abra o **PlayerDeviceList.xml** backup em um editor como o Visual Studio ou Visual Studio Code. Você precisa configurar seu dispositivo manualmente neste arquivo. Por padrão, o arquivo deve conter a seguinte esvaziar `Devices` elemento XML:

```xml
<DeviceList xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
<Devices>

</Devices>
</DeviceList>
```

**Adicione um dispositivo Android:**

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

**Feche e reabra o Visual Studio.** Seu dispositivo deve aparecer na lista.

## <a name="type-or-namespace-cannot-be-found-message-in-ide"></a>Mensagem "namespace ou tipo não encontrada" no IDE

Verifique se você tiver selecionado um **projeto de inicialização** que corresponde ao seu tipo de dispositivo (por exemplo. Android) e a configuração corresponde a esse tipo de dispositivo (por exemplo. **Depurar** para Android).

## <a name="constructor-on-type-interpretedxamarinformsbutton-not-found-message-in-player"></a>Mensagem "Construtor no tipo 'InterpretedXamarin.Forms.Button' não encontrada" no Player

Por exemplo, algumas classes de sistema não podem ser substituídas:

```csharp
public class SomeCustomButton : Xamarin.Forms.Button { ... }
```

## <a name="mainactivitycs-resourcelayout-does-not-contain-a-definition-for-main"></a>"MainActivity.cs: "Resource.Layout' não contém uma definição para 'Main'"

Esse erro ocorre para projetos do Android com interfaces de usuário definidas nos arquivos AXML.
Arquivos AXML atualmente não têm suporte no Xamarin Live Player.

### <a name="android-toolbar-and-tabs-render-incorrectly-using-xamarinforms"></a>Android barra de ferramentas e guias renderizam incorretamente usando xamarin. Forms

Projetos do Android do xamarin. Forms devem usar "Toolbar.axml" e "Tabbar.axml" para os nomes dos arquivos de layout relevantes. O modelo padrão usa esses nomes; renomeá-los causará problemas de renderização.

## <a name="related-links"></a>Links relacionados

- [Instalação](~/tools/live-player/install.md)
