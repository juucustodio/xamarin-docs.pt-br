---
title: Por que o Jenkins não é suportado pela Microsoft?
description: Em um alto nível, este documento descreve a interação do Xamarin com o sistema de CI Jenkins. Ele também aborda alguns problemas comuns que surgem ao trabalhar com o Jenkins.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9951F980-2C6C-47C0-8A35-A78F06C20BEB
author: asb3993
ms.author: amburns
ms.date: 06/05/2018
ms.openlocfilehash: c2e409b796d5ef2525079e02aafdd0c6e8db5d81
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113437"
---
# <a name="why-isnt-jenkins-supported-by-microsoft"></a>Por que o Jenkins não é suportado pela Microsoft?

## <a name="jenkins-support-explanation"></a>Explicação de suporte do Jenkins

Jenkins é um conjunto de CI do código-fonte aberto; devido a problemas de tantos que são causados diretamente pelo Jenkins *em si* precisará ser arquivados como problemas em relação a onde você obteve o código; por exemplo, o [repositório principal do Jenkins](https://github.com/jenkinsci/jenkins), ou o repositório para [ Jenkins.App](https://github.com/stisti/jenkins-app).

A exceção a isso é para os problemas que podem ser isolados para bugs específicos nas ferramentas do Xamarin; Se você suspeitar que esse seja o caso você pode verificar seu [opções de suporte](~/cross-platform/troubleshooting/support-options.md), embora, novamente, o problema pode ser algo fora do qual o suporte de Xamarin equipe pode *diretamente* ajudá-lo.

## <a name="setup-jenkins-with-xamarin"></a>Configurar o Jenkins com Xamarin

Embora conforme observado acima Jenkins problemas não são suportados diretamente pela nossa equipe; o [usando o Jenkins com Xamarin](~/tools/ci/jenkins-walkthrough.md) guia pode ser usado para configurar um servidor de CI Jenkins que esteja integrado com o Xamarin. 

## <a name="fixes-for-common-issues"></a>Correções para problemas comuns

### <a name="jenkins-is-unable-to-find-the-android-sdk"></a>Jenkins é não é possível encontrar o SDK do Android

A mensagem de erro para esse problema tem esta aparência:

> Erro XA5205: não foi possível encontrar o diretório do SDK do Android. Defina via /p:AndroidSdkDirectory

Suas opções para definir o local do SDK podem variar, dependendo do plugin Jenkins Android exatamente que você está usando; é um bom lugar para procurar como configurar isso no guia de plug-in. Por exemplo: o [plug-in do emulador do Android](https://wiki.jenkins-ci.org/display/JENKINS/Android+Emulator+Plugin#AndroidEmulatorPlugin-Systemconfiguration) procura automaticamente o SDK, mas se ele não é possível encontrá-lo; o local também pode ser definido por meio da página de configuração do sistema Jenkins para esse plug-in. 


## <a name="deprecated-errors"></a>Erros preteridos

> [!IMPORTANT]
> Esse problema foi resolvido em versões recentes do Xamarin. No entanto, se o problema ocorre na versão mais recente do software, envie uma [novo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) com o controle de versão completo completo e informações saída de log de compilação.



### <a name="jenkins-reports-an-invalid-xamarin-license"></a>Jenkins relata uma licença Xamarin inválida
As mensagens de erro para esse problema normalmente são algo como

> Erro XA9008: construção de linha de comando requer uma licença comercial

ou

> Erro: A Starter Edition do xamarin. IOS não dá suporte à construção fora do Xamarin Studio 

A causa mais comum desse cenário é o uso do Jenkins, fazendo logon com uma conta de usuário não está associada com a licença do Xamarin. É a maneira mais simples de resolver isso instalar o Jenkins como um aplicativo diretamente por meio da conta de usuário. Esse processo e algumas considerações adicionais são descritas aqui: [https://forums.xamarin.com/discussion/comment/99397/#Comment_99397](https://forums.xamarin.com/discussion/comment/99397/#Comment_99397)
