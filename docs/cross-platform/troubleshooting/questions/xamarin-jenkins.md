---
title: Por que o Jenkins não é compatível com o Xamarin?
description: Em um nível alto, este documento descreve a interação do Xamarin com o sistema de CI Jenkins. Ele também aborda alguns problemas comuns que surgem ao trabalhar com Jenkins.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9951F980-2C6C-47C0-8A35-A78F06C20BEB
author: asb3993
ms.author: amburns
ms.openlocfilehash: cf1a59d3084f178187209fdf3999af10efe6203a
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34782444"
---
# <a name="why-isnt-jenkins-supported-by-xamarin"></a>Por que o Jenkins não é compatível com o Xamarin?

## <a name="jenkins-support-explanation"></a>Explicação de suporte Jenkins

Jenkins é um conjunto de CI do código-fonte aberto; devido a problemas de tantos diretamente causados pelo Jenkins *próprio* precisará ser arquivado como problemas em relação a onde você obteve o código; por exemplo, o [repositório principal de Jenkins](https://github.com/jenkinsci/jenkins), ou o repositório para [ Jenkins.App](https://github.com/stisti/jenkins-app).

A exceção a isso é para problemas que podem ser isolados para determinado bugs nas ferramentas do Xamarin; Se você suspeitar que esse seja o caso você pode verificar seu [opções de suporte](~/cross-platform/troubleshooting/support-options.md), embora novamente, o problema pode ser algo fora que o suporte de Xamarin equipe pode *diretamente* ajudar.

## <a name="setup-jenkins-with-xamarin"></a>Instalação Jenkins com Xamarin

Enquanto conforme observado acima Jenkins problemas não são suportados diretamente pela nossa equipe; o [Jenkins usando com o Xamarin](~/tools/ci/jenkins-walkthrough.md) guia pode ser usado para configurar um servidor de CI Jenkins é integrado com o Xamarin. 

## <a name="fixes-for-common-issues"></a>Correções para problemas comuns

### <a name="jenkins-is-unable-to-find-the-android-sdk"></a>Jenkins é não é possível localizar o SDK do Android

A mensagem de erro para esse problema é semelhante a esta:

> Erro XA5205: não foi possível encontrar o diretório do SDK do Android. Defina via /p:AndroidSdkDirectory

As opções para definir o local do SDK podem variar dependendo exata Jenkins Android plug-in que estiver usando; é uma boa fonte para definir isso no guia de plug-in. Por exemplo, o [plug-in do emulador Android](https://wiki.jenkins-ci.org/display/JENKINS/Android+Emulator+Plugin#AndroidEmulatorPlugin-Systemconfiguration) procura automaticamente o SDK, mas se ele não é possível encontrá-lo; também é possível definir o local por meio da página de configuração do sistema Jenkins para plug-in. 


## <a name="deprecated-errors"></a>Erros preteridos

> [!IMPORTANT]
> Esse problema foi resolvido em versões recentes do Xamarin. No entanto, se o problema ocorre na versão mais recente do software, envie um [novo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) com o controle de versão completo completo e informações de log de saída de compilação.



### <a name="jenkins-reports-an-invalid-xamarin-license"></a>Jenkins relata uma licença inválida do Xamarin
As mensagens de erro para esse problema geralmente são algo como

> Erro XA9008: criação de linha de comando requer uma licença de negócios

ou

> Erro: A edição do xamarin Starter não dá suporte à construção fora do Xamarin Studio 

A causa mais comum desse cenário é o uso de Jenkins ao efetuar logon com uma conta de usuário não está associada com a sua licença do Xamarin. A maneira mais simples de resolver isso, é instalar Jenkins como um aplicativo diretamente por meio da conta de usuário. Esse processo e algumas considerações adicionais são descritas aqui: [https://forums.xamarin.com/discussion/comment/99397/#Comment_99397](https://forums.xamarin.com/discussion/comment/99397/#Comment_99397)
