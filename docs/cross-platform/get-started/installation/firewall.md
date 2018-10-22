---
title: Instruções de configuração de firewall do Xamarin
description: Este documento fornece uma lista de hosts que devem ser colocados na lista de permissões do seu firewall para permitir que o Xamarin funcione em um ambiente corporativo.
ms.prod: xamarin
ms.assetid: 658f699b-8cca-48f7-ae54-fa956384b6d6
author: asb3993
ms.author: amburns
ms.date: 12/02/2016
ms.openlocfilehash: dab4aeccd58da22736aea3139f11cbdff259ef7b
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34781085"
---
# <a name="xamarin-firewall-configuration-instructions"></a>Instruções de configuração de firewall do Xamarin

_Uma lista de hosts que precisam ser colocados na lista de permissões no firewall para permitir que a plataforma do Xamarin funcione para a sua empresa._

Para que os produtos Xamarin sejam instalados e funcionem corretamente, determinados pontos de extremidade devem estar acessíveis para baixar as ferramentas necessárias e as atualizações para o seu software. Se você ou sua empresa têm configurações estritas de firewall, é possível que você tenha problemas com a instalação, licenciamento, componentes além de outros problemas. Este documento descreve alguns dos pontos de extremidade conhecidos que precisam ser colocados na lista de permissões em seu firewall para o Xamarin funcionar. Essa lista não inclui os pontos de extremidade necessários para todas as ferramentas de terceiros incluídas no download. Se você ainda estiver com problemas depois de passar por essa lista, consulte os guias de solução de problemas de instalação da Apple ou do Android.

## <a name="endpoints-to-whitelist"></a>Pontos de extremidade a serem adicionados à lista de permissões

### <a name="xamarin-installer"></a>Instalador do Xamarin

Os seguintes endereços conhecidos precisarão ser adicionados para que o software seja instalado corretamente ao usar a versão mais recente do instalador do Xamarin:

-  xamarin.com (manifestos do instalador)
-  dl.xamarin.com (Local de download do pacote)
-  dl.google.com (para baixar o SDK do Android)
-  download.oracle.com (JDK)
-  visualstudio.com (Local de download dos pacotes de instalação)
-  go.microsoft.com (Resolução da URL de instalação)
-  aka.ms (Resolução da URL de instalação)

Se você estiver usando um Mac e encontrar problemas de instalação do Xamarin.Android, verifique se o macOS pode baixar o Java.


### <a name="components-store-and-nuget-including-xamarinforms"></a>Repositório de Componentes e NuGet (incluindo Xamarin.Forms)

Os seguintes endereços precisarão ser adicionados para acessar o Repositório de Componentes do Xamarin ou NuGet (o Xamarin.Forms é empacotado como um NuGet):

-  components.xamarin.com (para usar o Repositório de Componentes do Xamarin)
-  xampubdl.blob.core.windows.net (hospeda downloads do Repositório de Componentes)
-  www\.nuget.org (para acessar o NuGet)
-  az320820.vo.msecnd.net (downloads do NuGet)
-  dl-ssl.google.com (componentes do Google)


### <a name="software-updates"></a>Atualizações de software

Os seguintes endereços precisarão ser adicionados para garantir que as atualizações de software possam ser baixadas corretamente:

-  software.xamarin.com (serviço do atualizador)
-  download.visualstudio.microsoft.com
-  dl.xamarin.com

## <a name="xamarin-mac-agent"></a>Agente do Mac do Xamarin

Para conectar o Visual Studio a um host de build do Mac usando o Agente do Mac do Xamarin é necessário que a porta SSH esteja aberta. Por padrão, essa é a **Porta 22**.

## <a name="summary"></a>Resumo

Este guia abordou os pontos de extremidade a serem adicionados à lista de permissões para permitir que os produtos do Xamarin sejam instalados e atualizados corretamente em seu computador.
