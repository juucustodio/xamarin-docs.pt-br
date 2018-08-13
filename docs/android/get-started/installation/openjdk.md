---
title: Versão Prévia da Distribuição do Microsoft Mobile OpenJDK
description: Um guia passo a passo para configurar a distribuição do OpenJDK para Desenvolvimento Móvel da Microsoft.
ms.prod: xamarin
ms.assetid: B5F8503D-F4D1-44CB-8B29-187D1E20C979
ms.technology: xamarin-android
author: vyedin
ms.author: vyedin
ms.date: 07/22/2018
ms.openlocfilehash: 2022337ebd65997c7b2492137193586278f2dffd
ms.sourcegitcommit: bf51592be39b2ae3d63d029be1d7745ee63b0ce1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39573588"
---
# <a name="microsofts-mobile-openjdk-distribution-preview"></a>Versão Prévia da Distribuição do Microsoft Mobile OpenJDK

_Este guia descreve as etapas para alternar para a versão prévia de distribuição da Microsoft do OpenJDK. Essa distribuição se destina ao desenvolvimento móvel._

![Recurso de Visualização](~/media/shared/preview.png)

## <a name="overview"></a>Visão geral

A partir do Visual Studio 15.9 e do Visual Studio para Mac 7.7, as Ferramentas do Visual Studio para Xamarin mudarão do JDK da Oracle para uma **versão leve do OpenJDK que se destina exclusivamente ao desenvolvimento para Android**:

![Novo fluxo de trabalho oferecendo uma versão prévia da Web do OpenJDK no VS 15.8 Versão Prévia 5](openjdk-images/openjdk.png)

Os benefícios dessa mudança são:

- Você sempre terá uma versão do OpenJDK que funciona para desenvolvimento para Android.

- Baixar o JDK 9 ou o 10 não afetará a experiência de desenvolvimento.

- Uma redução significativa no volume de memória e tamanho do download.

- Não haverá mais nenhum problema com os instaladores e servidores de terceiros.

Caso queira mudar mais cedo para a experiência aprimorada, há builds de distribuição do Microsoft Mobile OpenJDK disponíveis para teste no Windows e no Mac. O processo de configuração é descrito abaixo, e você poderá reverter para o JDK da Oracle a qualquer momento.

## <a name="download"></a>Baixar

Para começar, baixe o build correto do seu sistema:

- **Mac** &ndash; https://dl.xamarin.com/OpenJDK/mac/microsoft-dist-openjdk-1.8.0.9.zip
- **Windows x86** &ndash; https://dl.xamarin.com/OpenJDK/win32/microsoft-dist-openjdk-1.8.0.9.zip
- **Windows x64** &ndash; https://dl.xamarin.com/OpenJDK/win64/microsoft-dist-openjdk-1.8.0.9.zip

## <a name="configure"></a>Configurar

Descompacte para o local correto:

- **Mac** &ndash; **$HOME/Library/Developer/Xamarin/jdk/microsoft_dist_openjdk_1.8.0.9**
- **Windows** &ndash; **C:\\Program Files\\Android\\jdk\\microsoft_dist_openjdk_1.8.0.9**

> [!IMPORTANT]
> Este exemplo usa o build 1.8.0.9; contudo, a versão que você baixar pode ser mais recente.

Aponte o IDE para o novo JDK:

- **Mac** &ndash; Clique em **Ferramentas > Gerenciador de SDK > Locais** e altere o **Local do SDK do Java (JDK)** para o caminho completo da instalação do OpenJDK. No exemplo a seguir, este caminho é definido como **$HOME/Library/Developer/Xamarin/jdk/microsoft_dist_openjdk_1.8.0.9**.

![Definir o caminho do JDK para a distribuição do Microsoft Mobile OpenJDK no Mac](openjdk-images/vsm.png)

- **Windows** &ndash; Clique em **Ferramentas > Opções > Xamarin > Configurações do Android** e altere o **Local do Java Development Kit** para o caminho completo da instalação do OpenJDK. No exemplo a seguir, esse caminho é definido como **C:\\Program Files\\Android\\jdk\\microsoft_dist_openjdk_1.8.0.9**:

![Definir o caminho do JDK para a distribuição do Microsoft Mobile OpenJDK no Windows](openjdk-images/vs.png)

## <a name="revert"></a>Reverter

Para reverter para o JDK da Oracle, altere o local do SDK do Java para o caminho de JDK da Oracle usado anteriormente e recompile a solução. No Mac, você poderá reverter para o caminho do JDK da Oracle, clicando em **Redefinir Padrões**.

Se você tiver problemas com a distribuição do Microsoft Mobile OpenJDK, relate os problemas usando a ferramenta de comentários no seu IDE, para que eles possam ser rastreados e corrigidos rapidamente.

## <a name="known-issues--planned-fix-dates"></a>Problemas Conhecidos e Datas de Correção Programadas

A variável de ambiente do `JAVA_HOME` pode não ser exportada corretamente para o SDK e para o Gerenciador de Dispositivos. Como alternativa, é possível configurar esta variável de ambiente para o local do OpenJDK no seu computador. Isso foi corrigido na Versão Prévia 15.9.

## <a name="summary"></a>Resumo

Neste artigo, você aprendeu a configurar o seu IDE para usar a versão prévia da distribuição do Microsoft Mobile OpenJDK, cujo lançamento estável está programado para o final de 2018.
