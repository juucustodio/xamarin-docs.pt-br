---
title: Distribuição do Mobile OpenJDK da Microsoft
description: Um guia passo a passo para configuração e solução de problemas da distribuição do OpenJDK para Desenvolvimento Móvel da Microsoft.
ms.prod: xamarin
ms.assetid: B5F8503D-F4D1-44CB-8B29-187D1E20C979
ms.technology: xamarin-android
author: vyedin
ms.author: vyedin
ms.date: 07/22/2018
ms.openlocfilehash: aad88ad883dea1e0430a047a71a2c191195efffe
ms.sourcegitcommit: 2f6a5c1abf90fbdb0475fd8a3ce6de3cd7c7d575
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52459896"
---
# <a name="microsofts-mobile-openjdk-distribution"></a>Distribuição do Mobile OpenJDK da Microsoft

_Este guia descreve as etapas para mudar para uma distribuição interna do OpenJDK. Essa distribuição se destina ao desenvolvimento móvel._

## <a name="overview"></a>Visão geral

Começando pelo Visual Studio 15.9 e pelo Visual Studio para Mac 7.7, as Ferramentas do Visual Studio para Xamarin mudaram do JDK da Oracle para uma **versão leve do OpenJDK que se destina exclusivamente ao desenvolvimento para Android**. Isso é uma migração necessária, pois a Oracle está encerrando o suporte para distribuição comercial do JDK 8 em 2019 e o JDK 8 é uma dependência necessária para todo o desenvolvimento do Android.

Os benefícios dessa mudança são:

- Você sempre terá uma versão do OpenJDK que funciona para desenvolvimento para Android.

- Baixar o JDK 9 ou superior da Oracle não afetará a experiência de desenvolvimento.

- Redução no volume de memória e tamanho do download.

- Não haverá mais nenhum problema com os instaladores e servidores de terceiros.

Caso queira mudar mais cedo para a experiência aprimorada, há builds de distribuição do Microsoft Mobile OpenJDK disponíveis para teste no Windows e no Mac. O processo de configuração é descrito abaixo, e você poderá reverter para o JDK da Oracle a qualquer momento.

## <a name="download"></a>Baixar

A distribuição do Mobile OpenJDK será instalada automaticamente para você, se você selecionar os pacotes do SDK do Android no instalador do Visual Studio no Windows. Ela será incluída na atualização 15.9 para os usuários que têm o SDK do Android selecionado.

No Mac, o Mobile OpenJDK será instalado como parte da carga de trabalho do Android para novas instalações. Para usuários existentes do Visual Studio para Mac, será solicitado que você o instale como parte de sua atualização para a versão 7.7. O IDE solicitará que você mude para o novo JDK e passará a usá-lo na próxima reinicialização.

## <a name="troubleshooting"></a>Solução de problemas

Se encontrar problemas com a instalação no Mac ou Windows, você poderá executar as seguintes etapas para configuração manual:

Verifique se o OpenJDK está instalado no computador no local correto:

- **Mac** &ndash; **$HOME/Library/Developer/Xamarin/jdk/microsoft_dist_openjdk_1.8.0.9**
- **Windows** &ndash; **C:\\Program Files\\Android\\jdk\\microsoft_dist_openjdk_1.8.0.9**

Se esse caminho está vazio, você pode baixar um dos seguintes pacotes:

- **Mac** &ndash; https://dl.xamarin.com/OpenJDK/mac/microsoft-dist-openjdk-1.8.0.9.zip
- **Windows x86** &ndash; https://dl.xamarin.com/OpenJDK/win32/microsoft-dist-openjdk-1.8.0.9.zip
- **Windows x64** &ndash; https://dl.xamarin.com/OpenJDK/win64/microsoft-dist-openjdk-1.8.0.9.zip

Aponte o IDE para o novo JDK:

- **Mac** &ndash; Clique em **Ferramentas > Gerenciador de SDK > Locais** e altere o **Local do SDK do Java (JDK)** para o caminho completo da instalação do OpenJDK. No exemplo a seguir, este caminho é definido como **$HOME/Library/Developer/Xamarin/jdk/microsoft_dist_openjdk_1.8.0.9**.

![Definir o caminho do JDK para a distribuição do Microsoft Mobile OpenJDK no Mac](openjdk-images/vsm.png)

- **Windows** &ndash; Clique em **Ferramentas > Opções > Xamarin > Configurações do Android** e altere o **Local do Java Development Kit** para o caminho completo da instalação do OpenJDK. No exemplo a seguir, esse caminho é definido como **C:\\Program Files\\Android\\jdk\\microsoft_dist_openjdk_1.8.0.9**:

![Definir o caminho do JDK para a distribuição do Microsoft Mobile OpenJDK no Windows](openjdk-images/vs.png)

## <a name="known-issues"></a>Problemas Conhecidos

### <a name="package-openjdkv1regkeyversion1809chipx64-failed-to-install"></a>Falha na instalação do pacote 'OpenJDKV1.RegKey,version=1.8.0.9,chip=x64'

Isso pode ser um problema em alguns ambientes corporativos. O OpenJDK já está no computador – siga as [etapas de solução de problemas acima](#troubleshooting) para apontar o seu IDE para o local correto. Você pode seguir os status dos problemas [aqui](https://developercommunity.visualstudio.com/content/problem/382549/packageidopenjdkv1regkeypackageactioninstallreturn.html).

### <a name="unknown-update-type-zip-when-upgrading-visual-studio-for-mac-to-77"></a>"tipo de atualização desconhecido: zip" ao atualizar o Visual Studio para Mac para 7.7

Esse problema pode surgir se estiver tentando atualizar de uma versão mais antiga do Visual Studio para Mac para a 7.7. Para contornar esse problema, desmarque OpenJDK no atualizador, instale o restante da atualização e verifique novamente se há atualizações para obter o pacote OpenJDK. Se ainda estiver tendo problemas, você poderá seguir as [etapas de solução de problemas acima](#troubleshooting) para instalar manualmente o OpenJDK. Registre um bug com logs anexados se você encontrar esse problema.

## <a name="summary"></a>Resumo

Neste artigo, você aprendeu a configurar seu IDE para usar a distribuição do Mobile OpenJDK da Microsoft e a solucionar problemas caso venha a encontrá-los.
