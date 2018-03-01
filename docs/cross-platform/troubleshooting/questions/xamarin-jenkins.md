---
title: "Por que o Jenkins não é compatível com o Xamarin?"
ms.topic: article
ms.prod: xamarin
ms.assetid: 9951F980-2C6C-47C0-8A35-A78F06C20BEB
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: 8129229a821edd2ef4f251679ee46bca7b74c8f9
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
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

A causa mais comum desse cenário é o uso de Jenkins ao efetuar logon com uma conta de usuário não está associada com a sua licença do Xamarin. A maneira mais simples de resolver isso, é instalar Jenkins como um aplicativo diretamente por meio da conta de usuário. Processo e algumas considerações adicionais são descritas aqui: [https://forums.xamarin.com/discussion/comment/99397/#Comment_99397](https://forums.xamarin.com/discussion/comment/99397/#Comment_99397)

Outra possibilidade é que as informações de licença do Xamarin estão corrompidas de alguma forma, você pode usar o [Xamarin licença Resync guia](~/cross-platform/troubleshooting/legacy-licenses/resync-licenses.md) para este cenário de solução de problemas.


