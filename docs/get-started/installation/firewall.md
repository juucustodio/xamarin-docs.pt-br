---
title: Instruções de configuração de firewall do Xamarin
description: Este documento fornece uma lista de hosts que devem ser permitidos em seu firewall para permitir que o Xamarin funcione em um ambiente corporativo.
ms.prod: xamarin
ms.assetid: 658f699b-8cca-48f7-ae54-fa956384b6d6
author: conceptdev
ms.author: crdun
ms.date: 07/17/2019
ms.openlocfilehash: 2b52dfd55194ec076f28f8c33e758a39d14f5943
ms.sourcegitcommit: 3f0e4f10e5def19122588bb05f26ab2baa9df6eb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2020
ms.locfileid: "70291325"
---
# <a name="xamarin-firewall-configuration-instructions"></a>Instruções de configuração de firewall do Xamarin

_Uma lista de hosts que precisam ser permitidos no firewall para permitir que a plataforma do Xamarin funcione para a sua empresa._

Para que os produtos Xamarin sejam instalados e funcionem corretamente, determinados pontos de extremidade devem estar acessíveis para baixar as ferramentas necessárias e as atualizações para o seu software. Se você ou sua empresa têm configurações estritas de firewall, é possível que você tenha problemas com a instalação, licenciamento, componentes além de outros problemas. Este documento descreve alguns dos pontos de extremidade conhecidos que precisam ser permitidos em seu firewall para o Xamarin funcionar. Essa lista não inclui os pontos de extremidade necessários para todas as ferramentas de terceiros incluídas no download. Se você ainda estiver com problemas depois de passar por essa lista, consulte os guias de solução de problemas de instalação da Apple ou do Android.

## <a name="endpoints-to-allow"></a>Pontos de extremidade a permitir

### <a name="xamarin-installer"></a>Instalador do Xamarin

Os seguintes endereços conhecidos precisarão ser adicionados para que o software seja instalado corretamente ao usar a versão mais recente do instalador do Xamarin:

- xamarin.com (manifestos do instalador)
- dl.xamarin.com (Local de download do pacote)
- dl.google.com (para baixar o SDK do Android)
- download.oracle.com (JDK)
- visualstudio.com (Local de download dos pacotes de instalação)
- go.microsoft.com (Resolução da URL de instalação)
- aka.ms (Resolução da URL de instalação)

Se você estiver usando um Mac e encontrar problemas de instalação do Xamarin.Android, verifique se o macOS pode baixar o Java.

### <a name="nuget-including-xamarinforms"></a>NuGet (incluindo o Xamarin.Forms)

Os seguintes endereços precisarão ser adicionados para acessar o NuGet (o Xamarin.Forms é empacotado como um NuGet):

- www.nuget.org (para acessar o NuGet)
- globalcdn.nuget.org (downloads do NuGet)
- dl-ssl.google.com (componentes do Google para Android e Xamarin.Forms)

### <a name="software-updates"></a>Atualizações de software

Os seguintes endereços precisarão ser adicionados para garantir que as atualizações de software possam ser baixadas corretamente:

- software.xamarin.com (serviço do atualizador)
- download.visualstudio.microsoft.com
- dl.xamarin.com

## <a name="xamarin-mac-agent"></a>Agente do Mac do Xamarin

Para conectar o Visual Studio a um host de build do Mac usando o Agente do Mac do Xamarin é necessário que a porta SSH esteja aberta. Por padrão, essa é a **Porta 22**.
