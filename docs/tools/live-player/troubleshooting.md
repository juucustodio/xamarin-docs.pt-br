---
title: "Solução de problemas"
description: Problemas conhecidos com o Xamarin Live Player e como corrigi-los.
ms.topic: article
ms.prod: xamarin
ms.assetid: 29A97ADA-80E0-40A1-8B26-C68FFABE7D26
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 05/17/2017
ms.openlocfilehash: d7c5bedb03d7c869be65e3c704bac58a9cdfcbbd
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="troubleshooting"></a>Solução de problemas

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

Esse erro geralmente é ocorreu quando o dispositivo móvel que executa Xamarin Live Player não está na mesma rede do computador que executa o IDE; Isso geralmente ocorre ao se conectar a um dispositivo que foi anteriormente emparelhado com êxito.

* Verifique se o dispositivo e o computador estão na mesma rede Wi-Fi.
* A rede pode ser bem protegida (por exemplo, algumas redes corporativas), bloqueando as portas necessárias pelo Xamarin Live Player. As portas a seguir são necessárias para o Player de Live Xamarin:
  * 37847 – acesso à rede interna 
  * 8090 – acesso à rede externo

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
