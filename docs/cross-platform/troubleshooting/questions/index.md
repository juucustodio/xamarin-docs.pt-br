---
title: Perguntas frequentes sobre o geral
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: C7E6E54D-3957-407D-BB87-22B095148C6B
author: asb3993
ms.author: amburns
ms.date: 05/08/2018
ms.openlocfilehash: 06aa6569301d1bfdbf9f6fd1e7397a38a9beb6f6
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61157187"
---
# <a name="general-frequently-asked-questions"></a>Perguntas frequentes sobre o geral

## <a name="portable-class-libraries"></a>Bibliotecas de Classes Portáteis

### <a name="how-can-i-view-what-libraries-are-supported-in-a-pclpcl-support-librariesmd"></a>[Como posso exibir as bibliotecas com suporte em um PCL?](pcl-support-libraries.md)
Este guia lista os recursos e métodos para determinar se sua biblioteca existente é compatível com várias plataformas de destino PCL ou pode ser convertida em um perfil PCL.

### <a name="pcl-reflection-apipcl-reflectionmd"></a>[API de reflexão de PCL](pcl-reflection.md)
A Microsoft desenvolveu uma nova API de reflexão para uso em bibliotecas de classes portáteis. Se você tiver um código de reflexão existente que você deseja mover para uma PCL, podem não funcionar.

### <a name="pcl-case-study-how-can-i-resolve-problems-related-to-systemdiagnosticstracing-for-the-microsoft-tpl-dataflow-nuget-packagepcl-case-studymd"></a>[Estudo de caso PCL: Como resolver problemas relacionados à Tracing para o pacote NuGet de fluxo de dados TPL da Microsoft?](pcl-case-study.md)
Xamarin. IOS e xamarin. Android não implementam 100% de cada perfil PCL que permitem a eles como referências. Para conveniência prática no Visual Studio para Mac, o Visual Studio e o Gerenciador de pacotes do NuGet, projetos Xamarin permitem o uso de vários perfis que só têm implementações incompletas. Por exemplo, nem o xamarin. IOS nem o xamarin. Android no momento, inclui uma implementação completa dos tipos no `System.Diagnostics.Tracing` namespace PCL. Você pode contornar isso, alternando o projeto de aplicativo para referenciar o portátil TargetFramework="net45 win8 + wp8 + wpa81 versão da biblioteca de fluxo de dados TPL.

## <a name="nuget-packages--xamarin-components"></a>Pacotes do NuGet e componentes do Xamarin
### <a name="how-can-i-update-nugetnuget-updatemd"></a>[Como posso atualizar o NuGet?](nuget-update.md)
As atualizações do NuGet, extensões e suplementos podem ser encontrados na **atualizações** guia o **Gerenciador de pacotes NuGet**. A navegação detalhada para encontrar as atualizações no Visual Studio para Mac e Visual Studio é neste guia.

### <a name="how-do-i-downgrade-a-nuget-packagenuget-package-downgrademd"></a>[Como fazer downgrade de um pacote NuGet?](nuget-package-downgrade.md)
O Visual Studio para Mac e o Visual Studio têm recursos para selecionar as versões mais antigas de pacotes e instalá-los automaticamente. semelhante a como atualizar pacotes funciona.

### <a name="missing-packages-error-after-updating-nuget-packagesnuget-packages-missingmd"></a>[Erro de pacotes ausentes após atualizar os pacotes do Nuget](nuget-packages-missing.md)
Esse problema foi relatado principalmente em soluções de aplicativo de exemplo do xamarin. Forms, mas o potencial para esse problema pode ocorrer em qualquer projeto que usa pacotes do NuGet.

### <a name="unifying-google-play-services-components-and-nugetgps-components-nugetmd"></a>[Unificar os componentes do Google Play Services e o NuGet](gps-components-nuget.md)
Existe usado vários componentes do Google Play Services e pacotes do NuGet, mas para facilitar as coisas para os desenvolvedores, estamos já agora unified nossos componentes e NuGet pacotes em duas. Em quase todos os casos, o Google Play Services devem ser usado. O único motivo para usar o pacote (Froyo) é se você estiver direcionando ativamente Froyo.

### <a name="where-are-the-components-stored-on-my-machinecomponent-storagemd"></a>[Onde meus componentes são armazenados no meu computador?](component-storage.md)
Sempre que você instala um componente do Xamarin em um projeto de aplicativo, ele obtém colocado nos dois locais listados neste guia.


## <a name="troubleshooting"></a>Solução de problemas
### <a name="where-can-i-find-my-version-information-and-logsversion-logsmd"></a>[Onde posso encontrar informações e logs da minha versão?](version-logs.md)
Este guia fornece detalhes sobre onde encontrar a maior parte das informações de diagnóstico que pode ser usado para solucionar problemas do Xamarin.

### <a name="when-and-how-should-i-file-a-bug-reporthowto-file-bugmd"></a>[Quando e como eu devo criar um relatório de bugs?](howto-file-bug.md)
Este guia fornece dicas para arquivar relatórios de bugs de alta qualidade, para que nossos engenheiros possam determinar a causa (e quaisquer possíveis correções) para um problema com mais eficiência.

### <a name="why-isnt-jenkins-supported-by-xamarinxamarin-jenkinsmd"></a>[Por que não há suporte para Jenkins no Xamarin?](xamarin-jenkins.md)
Jenkins é um conjunto de CI do código-fonte aberto; devido a problemas de tantos que são causados diretamente pelo Jenkins *em si* precisará ser arquivados como problemas em relação a onde você obteve o código; por exemplo, o [repositório principal do Jenkins](https://github.com/jenkinsci/jenkins), ou o repositório para [ Jenkins.App](https://github.com/stisti/jenkins-app).

### <a name="what-project-settings-are-required-for-the-debuggerdebugger-settingsmd"></a>[Quais configurações do projeto são necessárias para o depurador?](debugger-settings.md)
Em ordem para o depurador funcionar conforme o esperado (ocorrências de pontos de interrupção, exibir logs de depuração, etc.), exibição de informações de instrumentação e depuração de desenvolvedor deverão estar habilitada. Este guia fornece detalhes sobre como localizar e ativar essas configurações.

