---
title: Por que o Jenkins não tem suporte da Microsoft?
description: Este documento descreve, em um alto nível, a interação do Xamarin com o sistema de CI Jenkins. Ele também aborda alguns problemas comuns que surgem ao trabalhar com Jenkins.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9951F980-2C6C-47C0-8A35-A78F06C20BEB
author: davidortinau
ms.author: daortin
ms.date: 06/05/2018
ms.openlocfilehash: 7be60eaa1135284522ef1e6ce81c911a68ff9915
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73012223"
---
# <a name="why-isnt-jenkins-supported-by-microsoft"></a>Por que o Jenkins não tem suporte da Microsoft?

## <a name="jenkins-support-explanation"></a>Explicação de suporte do Jenkins

Jenkins é um pacote de CI de código-fonte aberto; por causa dessa quantidade de problemas que são provocados diretamente pelo Jenkins em *si* , precisarão ser arquivados como problemas em relação ao local em que você obteve o código; como o [repositório Jenkins principal](https://github.com/jenkinsci/jenkins)ou o repositório para [Jenkins. app](https://github.com/stisti/jenkins-app).

A exceção a isso é para problemas que podem ser isolados para bugs específicos nas ferramentas do Xamarin; Se você suspeitar disso para ser o caso, poderá verificar suas [Opções de suporte](~/cross-platform/troubleshooting/support-options.md), porém, o problema pode ser algo fora do que a equipe de suporte do Xamarin pode ajudar *diretamente* .

## <a name="setup-jenkins-with-xamarin"></a>Configurar o Jenkins com o Xamarin

Embora conforme observado acima, os problemas de Jenkins não têm suporte diretamente pela nossa equipe; o guia [usando o Jenkins com o xamarin](~/tools/ci/jenkins-walkthrough.md) pode ser usado para configurar um servidor de CI do Jenkins integrado ao Xamarin. 

## <a name="fixes-for-common-issues"></a>Correções para problemas comuns

### <a name="jenkins-is-unable-to-find-the-android-sdk"></a>Jenkins não consegue localizar o SDK do Android

A mensagem de erro para esse problema é algo assim:

> erro XA5205: não foi possível encontrar o diretório SDK do Android. Defina por meio de/p: AndroidSdkDirectory

As opções para definir o local do SDK podem variar dependendo do plug-in exato do Jenkins do Android que você está usando; um bom local para procurar como definir isso está no guia do plug-in. Por exemplo; o [plug-in Android Emulator](https://wiki.jenkins-ci.org/display/JENKINS/Android+Emulator+Plugin#AndroidEmulatorPlugin-Systemconfiguration) procura automaticamente o SDK, mas se ele não conseguir encontrá-lo; o local também pode ser definido por meio da página de configuração do sistema Jenkins para esse plug-in. 

## <a name="deprecated-errors"></a>Erros preteridos

> [!IMPORTANT]
> Esse problema foi resolvido nas versões recentes do Xamarin. No entanto, se o problema ocorrer na versão mais recente do software, registre um [novo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) com suas informações completas de controle de versão e saída de log de compilação completa.

### <a name="jenkins-reports-an-invalid-xamarin-license"></a>Jenkins relata uma licença do Xamarin inválida
As mensagens de erro para esse problema normalmente são algo como

> Erro de XA9008: a criação da linha de comando requer uma licença de negócios

ou

> Erro: a edição inicial do Xamarin. iOS não oferece suporte à compilação fora do Xamarin Studio 

A causa mais comum desse cenário é o uso do Jenkins fazendo logon com uma conta de usuário não associada à sua licença do Xamarin. A maneira mais simples de resolver isso é instalar o Jenkins como um aplicativo diretamente por meio da conta de usuário. Esse processo e algumas considerações adicionais são descritos aqui: [https://forums.xamarin.com/discussion/comment/99397/#Comment_99397](https://forums.xamarin.com/discussion/comment/99397/#Comment_99397)
