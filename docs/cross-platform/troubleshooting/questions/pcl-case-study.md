---
title: Resolver problemas relacionados à Tracing e fluxo de dados TPL
description: 'Estudo de caso de PCL: como posso resolver problemas relacionados a System.Diagnostics.Tracing para o pacote NuGet de Fluxo de Dados de TPL da Microsoft?'
ms.prod: xamarin
ms.assetid: 7986A556-382D-4D00-ACCF-3589B4029DE8
ms.date: 04/17/2018
author: asb3993
ms.author: amburns
ms.openlocfilehash: d9aa85b946f20addb7d69c559bff68c6b1f75429
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2019
ms.locfileid: "57669837"
---
# <a name="pcl-case-study-how-can-i-resolve-problems-related-to-systemdiagnosticstracing-for-the-microsoft-tpl-dataflow-nuget-package"></a>Estudo de caso de PCL: como posso resolver problemas relacionados a System.Diagnostics.Tracing para o pacote NuGet de Fluxo de Dados de TPL da Microsoft?

> [!IMPORTANT]
> Esse exemplo específico de `System.Diagnostic.Tracing` não produz mais erros por padrão nas versões mais recentes do Xamarin. Enquanto a solução alternativa sugerida ainda funcionará, observe que alguns dos bugs mencionados na seção "camadas de erros" foram corrigidos.
> Além disso, você deve observar que .NET Standard agora é a melhor maneira de implementar as APIs do .NET de plataforma cruzada.

## <a name="summary"></a>Resumo

Xamarin. IOS e xamarin. Android não implementam 100% de cada perfil PCL que permitem a eles como referências. Para conveniência prática no Visual Studio para Mac, o Visual Studio e o Gerenciador de pacotes do NuGet, projetos Xamarin permitem o uso de vários perfis que têm apenas _incompleta_ implementações. Por exemplo, nem o xamarin. IOS nem o xamarin. Android no momento, inclui uma implementação completa dos tipos na PCL "Tracing" namespace. Essa limitação resulta em três camadas de erros ao tentar usar o padrão `portable-net45+win8+wpa81` versão do pacote do NuGet de fluxo de dados TPL da Microsoft.

## <a name="workaround-switch-the-app-project-to-reference-the-portable-net45win8wp8wpa81-version-of-the-tpl-dataflow-library"></a>Solução alternativa: Alternar o projeto de aplicativo para referenciar o `portable-net45+win8+wp8+wpa81` versão da biblioteca de fluxo de dados TPL

(Isso evita a todas as três camadas de erros e funciona para todas as versões recentes do Xamarin).

1. Abra o projeto de aplicativo **. csproj** em um editor de texto.

2. Localize a linha que é semelhante a esta:

    ```xml
    <HintPath>..\packages\Microsoft.Tpl.Dataflow.4.5.24\lib\portable-net45+win8+wpa81\System.Threading.Tasks.Dataflow.dll</HintPath>
    ```

3. Alteração `portable-net45+win8+wpa81` à `portable-net45+win8+wp8+wpa81` (`+wp8` é adicionado):

    ```xml
    <HintPath>..\packages\System.Threading.Tasks.Dataflow.4.5.25\lib\portable-net45+win8+wp8+wpa81\System.Threading.Tasks.Dataflow.dll</HintPath>
    ```

### <a name="explanation"></a>Explicação

O `portable-net45+win8+wp8+wpa81` não faz referência a versão da biblioteca **System.Diagnostics.Tracing.dll** _em todos os_, portanto, ele evita completamente todas as três camadas de problemas.

### <a name="limitations"></a>Limitações

- O `portable-net45+win8+wp8+wpa81` versão da biblioteca não pode incluir a 100% da funcionalidade do `portable-net45+win8+wpa81` versão.

- O Gerenciador de pacotes do NuGet instala o `portable-net45+win8+wpa81` versão do pacote do NuGet PCL por padrão, portanto, você deverá ajustar manualmente a referência.

## <a name="details-about-the-three-layers-of-errors"></a>Detalhes sobre as três camadas de erros

1. O **System.Diagnostics.Tracing.dll** assembly fachada está ausente de todas as versões do Mac do xamarin. Android (não público Bug 34888) e estavam ausentes de todas as versões de xamarin. IOS menores do que 9.0 (ou menores do que XamarinVS 3.11.1443 no Windows) (corrigidos [Bug 32388](https://bugzilla.xamarin.com/show_bug.cgi?id=32388)). Esse problema fará com que um dos erros a seguir, dependendo do destino de implantação e o vinculador configurações:

    - Xamarin.Android.Common.targets: Erro: Exceção ao carregar assemblies: System.IO.FileNotFoundException: Não foi possível carregar o assembly ' Tracing, versão version=4.0.0.0, Culture = neutral, PublicKeyToken = b03f5f7f11d50a3a'. Talvez ele não existe no Mono para o perfil do Android?

    - Não foi possível carregar arquivo ou assembly 'Tracing' ou uma de suas dependências. O sistema não pode encontrar o arquivo especificado. (System.IO.FileNotFoundException)

    - MTOUCH: erro MT3001: Poderia AOT o assembly ' / Users/macuser/Projects/TPLDataflow/UnifiedSingleViewIphone1/obj/iPhone/Debug/mtouch-cache/64/Build/System.Threading.Tasks.Dataflow.dll '

    - MTOUCH: erro MT2002: Falha ao resolver o assembly: 'System.Diagnostics.Tracing, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a'

2. O atual [Mono implementação dos tipos em "Tracing"](https://github.com/mono/mono/blob/master/mcs/class/corlib/System.Diagnostics.Tracing/EventSource.cs) Faltam algumas sobrecargas de método ([Bug 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337)). Esse problema fará com que um dos seguintes erros de vinculador ao compilar um aplicativo Xamarin:

    - /Library/Frameworks/Mono.framework/External/xbuild/Xamarin/Android/Xamarin.Android.Common.targets: error : Erro ao executar tarefa LinkAssemblies: erro XA2006: Referência aos metadados do item 'System. void System.Diagnostics.Tracing.EventSource::WriteEvent(System.Int32,System.Object[])' (definido em ' Dataflow, versão = 4.5.24.0, Culture = neutral, PublicKeyToken = b03f5f7f11d50a3a') de ' Dataflow, versão = 4.5.24.0, Culture = neutral, PublicKeyToken = b03f5f7f11d50a3a' não pôde ser resolvido.

    - MTOUCH: erro MT2002: Falha ao resolver a referência de "System. void System.Diagnostics.Tracing.EventSource::WriteEvent(System.Int32,System.Object[])" de "Tracing, versão version=4.0.0.0, Culture = neutral, PublicKeyToken = b03f5f7f11d50a3a"

3. Atual [Mono implementação dos tipos em "Tracing"](https://github.com/mono/mono/blob/master/mcs/class/corlib/System.Diagnostics.Tracing/EventSource.cs) também é atualmente um _vazia_ implementação "fictícia" ([Bug 34890](https://bugzilla.xamarin.com/show_bug.cgi?id=34890)). Qualquer tentativa de usar esses métodos em tempo de execução, portanto, pode produzir resultados inesperados. Para o _particular_ caso da biblioteca de fluxo de dados do Microsoft TPL, parece que as chamadas para `WriteEvent(System.Int32,System.Object[])` não são essenciais para a maior parte do comportamento da biblioteca, portanto, a correção para "camada 2" ([Bug 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337), adicionando vazio implementações) provavelmente será suficientes para a maioria dos casos de uso do Microsoft TPL Dataflow.

## <a name="questions--answers"></a>Perguntas e respostas

### <a name="i-was-able-to-leave-linking-enabled-with-the-codeportable-net45win8wpa81code-version-of-the-library-on-older-versions-of-xamarinios-or-on-xamarinandroid-how-did-that-work"></a>Conseguiu deixar vinculação habilitada com o <code>portable-net45+win8+wpa81</code> versão da biblioteca em versões mais antigas do xamarin. IOS ou no xamarin. Android. Como esse trabalho?

#### <a name="answer"></a>Resposta

É _possíveis_ para obter a compilação concluída "com êxito" (com a vinculação habilitado) em versões mais antigas do xamarin. IOS ou no xamarin. Android no Mac se você incluir uma referência para o `System.Diagnostics.Tracing.dll` _doassemblydereferência_ \[1\] em vez de _assembly fachada_ \[2], mas infelizmente isso não é uma solução alternativa "correta". Assemblies de referência só devem ser usadas ao criar _bibliotecas portáteis_, código de não específico da plataforma, como aplicativos. Tentando _executar_ provavelmente o código contido em assemblies de referência (em vez de apenas crie em relação a ela) produzir resultados inesperados. A correção certa será para a equipe Mono adicionar o ausente `WriteEvent(System.Int32,System.Object[])` sobrecarga para o [ `EventSource` ](https://github.com/mono/mono/blob/master/mcs/class/corlib/System.Diagnostics.Tracing/EventSource.cs) tipo ([Bug 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337)). Agora a melhor opção é alternar para o `portable-net45+win8+wp8+wpa81` versão da biblioteca do Microsoft TPL Dataflow, conforme discutido na seção solução acima.

(Para qualquer pessoa que pode ler este artigo após ver uma resposta relacionada mais antiga e mais resumida do StackOverflow (<https://stackoverflow.com/a/23591322/2561894>), observe que foi a distinção entre os assemblies de referência e o assembly de fachada _não_ mencionado lá.)

**\[1\] locais "Referência de assembly"**

Windows: `C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETPortable\v4.5\System.Diagnostics.Tracing.dll`

Mac (Mono): `/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/xbuild-frameworks/.NETPortable/v4.5/System.Diagnostics.Tracing.dll`

**\[2\] locais "assembly Facade"**

Windows: `C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\v4.5\Facades\System.Diagnostics.Tracing.dll`

Mac (Mono): `/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/4.5/Facades/System.Diagnostics.Tracing.dll`


### <a name="will-it-help-if-i-manually-add-a-reference-to-the-systemdiagnosticstracing-facade-assembly"></a>Ele ajudará a se eu adicionar manualmente uma referência ao assembly de fachada "Tracing"?

_Em particular para resolver o problema usando estas 2 etapas?_

1. _Copie o `System.Diagnostics.Tracing.dll` assembly Facade que aponta para a pasta de projeto de aplicativo de um dos seguintes locais:_

    Windows: `C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\v4.5\Facades\System.Diagnostics.Tracing.dll`

    Mac (Mono): `/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/4.5/Facades/System.Diagnostics.Tracing.dll`

2. _Adicione uma referência ao assembly fachada no projeto de aplicativo do xamarin. IOS ou xamarin. Android._

#### <a name="answer"></a>Resposta

Não, isso não ajudará.

- Para xamarin. IOS 9.0 ou qualquer versão recente do xamarin. Android no Windows, essa solução alternativa é estritamente redundante e pode causar erros de compilação semelhante a "um assembly 'Tracing' com a mesma identidade já foi importado.".

- Para xamarin. IOS 8.10 ou inferior ou para xamarin. Android no Mac, essa solução alternativa ajudará, mas _apenas_ para o problema de assembly ausente "camada 1". Ele irá _não_ resolver os erros de vinculador "camada 2", portanto, não é uma solução completa.

### <a name="can-i-use-the-systemdiagnosticstracing-nuget-packagehttpswwwnugetorgpackagessystemdiagnosticstracing-to-solve-the-problem"></a>Posso usar o [pacote do NuGet Tracing](https://www.nuget.org/packages/System.Diagnostics.Tracing/) para resolver o problema?

#### <a name="answer"></a>Resposta

Não, o pacote do NuGet 3.0 "Tracing" só inclui implementações específicas à plataforma para "DNXCore50" e "netcore50". Ele explicitamente _omite_ implementações para xamarin. Android ("MonoAndroid") e xamarin. IOS ("MonoTouch" e "xamarinios"). Isso significa que a instalação do pacote tenha _nenhum efeito_ para projetos xamarin. Android e xamarin. IOS. O pacote do NuGet pressupõe que essas plataformas fornecem suas _próprio_ implementação dos tipos. Esse pressuposto é "correto" no sentido de que tenha Mono _uma_ implementação do namespace, mas, como discutido em pontos \#2 e \#3 de "Detalhes sobre as três camadas de erros" acima, o implementação está incompleta no momento. Portanto, a correção apropriada será para a equipe Mono resolver [Bug 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337) e [Bug 34890](https://bugzilla.xamarin.com/show_bug.cgi?id=34890).

## <a name="next-steps"></a>Próximas etapas

Para obter mais assistência, entre em contato conosco ou se esse problema permanece mesmo após utilizando as informações acima, consulte [quais opções de suporte estão disponíveis para o Xamarin?](~/cross-platform/troubleshooting/support-options.md) para obter informações sobre opções de contato, sugestões, e também como um novo bug de arquivo se necessário.
