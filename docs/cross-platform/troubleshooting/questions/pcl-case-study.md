---
title: 'Estudo de caso PCL: como resolver problemas relacionados a Tracing para o pacote do NuGet do fluxo de dados do Microsoft TPL?'
ms.topic: article
ms.prod: xamarin
ms.assetid: 7986A556-382D-4D00-ACCF-3589B4029DE8
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: c1d8bab1af8082d74f447cd51422a7eedb7c18c4
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="pcl-case-study-how-can-i-resolve-problems-related-to-systemdiagnosticstracing-for-the-microsoft-tpl-dataflow-nuget-package"></a>Estudo de caso PCL: como resolver problemas relacionados a Tracing para o pacote do NuGet do fluxo de dados do Microsoft TPL?

## <a name="summary"></a>Resumo

Xamarin e xamarin não implementam 100% de cada perfil PCL permitem como referências. Para conveniência prática no Visual Studio para Mac, o Visual Studio e o NuGet package manager, Xamarin projetos permitem o uso de vários perfis que têm apenas _incompleta_ implementações. Por exemplo, nem xamarin nem xamarin atualmente inclui uma implementação completa dos tipos "Tracing" PCL namespace. Essa limitação resulta em 3 camadas de erros ao tentar usar o padrão `portable-net45+win8+wpa81` versão do pacote do NuGet do fluxo de dados TPL do Microsoft.


## <a name="workaround-switch-the-app-project-to-reference-the-portable-net45win8wp8wpa81-version-of-the-tpl-dataflow-library"></a>Solução alternativa: Alternar o projeto do aplicativo para referenciar o `portable-net45+win8+wp8+wpa81` versão da biblioteca de fluxo de dados TPL

(Isso evita todas as 3 camadas de erros e funciona para todas as versões recentes do Xamarin.)

1. Abra o projeto de aplicativo `.csproj` em um editor de texto.

2. Localize a linha que é semelhante a este:

    ```xml
    <HintPath>..\packages\Microsoft.Tpl.Dataflow.4.5.24\lib\portable-net45+win8+wpa81\System.Threading.Tasks.Dataflow.dll</HintPath>
    ```

3. Altere `portable-net45+win8+wpa81` para `portable-net45+win8+**wp8**+wpa81`:

    ```xml
    <HintPath>..\packages\System.Threading.Tasks.Dataflow.4.5.25\lib\portable-net45+win8+wp8+wpa81\System.Threading.Tasks.Dataflow.dll</HintPath>
    ```

### <a name="explanation"></a>Explicação

O `portable-net45+win8+wp8+wpa81` versão da biblioteca não faz referência a "System.Diagnostics.Tracing.dll" _em todos os_, por isso evita completamente todas as 3 camadas de problemas.

### <a name="limitations"></a>Limitações

- O `portable-net45+win8+wp8+wpa81` versão da biblioteca não pode incluir a 100% da funcionalidade do `portable-net45+win8+wpa81` versão.

- O NuGet package manager instala o `portable-net45+win8+wpa81` versão do pacote do NuGet PCL por padrão, portanto você deve ajustar a referência manualmente.




## <a name="details-about-the-3-layers-of-errors"></a>Detalhes sobre as 3 camadas de erros

1. O `System.Diagnostics.Tracing.dll` assembly de fachada está ausente de todas as versões do Mac do xamarin (erro de não-públicos 34888) e ausente de todas as versões do xamarin menor do que 9.0 (ou menor do que XamarinVS 3.11.1443 no Windows) (corrigidos [Bug 32388](https://bugzilla.xamarin.com/show_bug.cgi?id=32388)). Esse problema fará com que um dos seguintes erros dependendo de destino de implantação e vinculador configurações:

    - > Xamarin.Android.Common.targets: Erro: exceção ao carregar assemblies: System.IO. FileNotFoundException: não foi possível carregar o assembly ' Tracing, Version = 4.0.0.0, Culture = neutral, PublicKeyToken = b03f5f7f11d50a3a'. Talvez ele não existe na Mono para Android perfil?

    - > Não foi possível carregar arquivo ou assembly 'Tracing' ou uma de suas dependências. O sistema não pode encontrar o arquivo especificado. (System.IO.FileNotFoundException)

    - > MTOUCH: erro MT3001: pôde AOT o assembly ' / Users/macuser/Projects/TPLDataflow/UnifiedSingleViewIphone1/obj/iPhone/Debug/mtouch-cache/64/Build/System.Threading.Tasks.Dataflow.dll '

    - > MTOUCH: erro MT2002: Falha ao resolver o assembly: ' Tracing, Version = 4.0.0.0, Culture = neutral, PublicKeyToken = b03f5f7f11d50a3a'



2. Atual [Mono implementação dos tipos "Tracing"](https://github.com/mono/mono/blob/master/mcs/class/corlib/System.Diagnostics.Tracing/EventSource.cs) está faltando algumas sobrecargas de método ([Bug 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337)). Esse problema fará com que um dos seguintes erros de vinculador ao compilar um aplicativo Xamarin:

    - > / Library/Frameworks/Mono.framework/External/xbuild/Xamarin/Android/Xamarin.Android.Common.targets: erro: erro ao executar tarefa LinkAssemblies: erro XA2006: referência ao item de metadados de ' System. void System:: WriteEvent(System.Int32,System.Object[])' (definido em ' Dataflow, Version = 4.5.24.0, Culture = neutral, PublicKeyToken = b03f5f7f11d50a3a') do ' Dataflow, versão = 4.5.24.0, Culture = neutral, PublicKeyToken = b03f5f7f11d50a3a' não pôde ser resolvido.

    - > MTOUCH: erro MT2002: Falha ao resolver a referência "System. void System.Diagnostics.Tracing.EventSource::WriteEvent(System.Int32,System.Object[])" de "Tracing, Version = 4.0.0.0, Culture = neutral, PublicKeyToken = b03f5f7f11d50a3a"


3. Atual [Mono implementação dos tipos "Tracing"](https://github.com/mono/mono/blob/master/mcs/class/corlib/System.Diagnostics.Tracing/EventSource.cs) também é atualmente um _vazio_ "fictícia" implementação ([Bug 34890](https://bugzilla.xamarin.com/show_bug.cgi?id=34890)). Qualquer tentativa de usar esses métodos em tempo de execução, portanto, pode produzir resultados inesperados. Para o _específico_ caso da biblioteca de fluxo de dados do Microsoft TPL, parece que as chamadas para `WriteEvent(System.Int32,System.Object[])` não são essenciais para a maioria do comportamento da biblioteca, portanto, a correção para "camada 2" ([Bug 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337), adicionando vazio implementações) serão likey ser suficiente para a maioria dos casos de uso de fluxo de dados do Microsoft TPL.




## <a name="questions--answers"></a>Perguntas e respostas


###<a name="i-was-able-to-leave-linking-enabled-with-the-codeportable-net45win8wpa81code-version-of-the-library-on-older-versions-of-xamarinios-or-on-xamarinandroid-how-did-that-work"></a>"Conseguiu deixar a vinculação habilitado com o <code>portable-net45+win8+wpa81</code> versão da biblioteca em versões anteriores do xamarin ou no xamarin. Como funcionou?"

#### <a name="answer"></a>Resposta

É _possíveis_ para obter a compilação a ser concluída "com êxito" (com a vinculação habilitado) em versões mais antigas do xamarin ou no xamarin no Mac se você incluir uma referência para o `System.Diagnostics.Tracing.dll` _referência assembly_ \[1\] em vez de _assembly de fachada_ \[2], mas infelizmente não é uma solução alternativa "correta". Assemblies de referência só devem ser usadas ao criar _bibliotecas portáteis_, código de não específico da plataforma, como aplicativos. Tentando _executar_ o código contido em assemblies de referência (em vez de compilação apenas em relação a ela) é provável produzir resultados inesperados. A correção correta será usado para a equipe Mono para adicionar o ausentes `WriteEvent(System.Int32,System.Object[])` de sobrecarga para o [ `EventSource` ](https://github.com/mono/mono/blob/master/mcs/class/corlib/System.Diagnostics.Tracing/EventSource.cs) tipo ([Bug 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337)). Agora a melhor opção é alternar para o `portable-net45+win8+wp8+wpa81` versão da biblioteca de fluxo de dados do Microsoft TPL, conforme discutido na seção de solução alternativa acima.

(Para qualquer pessoa que pode ler este artigo após ver uma resposta mais antiga e mais resumida relacionada de StackOverflow (<http://stackoverflow.com/a/23591322/2561894>), observe que a diferença entre os assemblies de referência e o assembly de fachada foi _não_ mencionado existe.)


**\[1\] locais "Referência de assembly"**

Windows: `C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETPortable\v4.5\System.Diagnostics.Tracing.dll`

Mac (Mono): `/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/xbuild-frameworks/.NETPortable/v4.5/System.Diagnostics.Tracing.dll`

**\[2\] locais "assembly Facade"**

Windows: <code>C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\v4.5\<strong>Facades</strong>\System.Diagnostics.Tracing.dll</code>

Mac (Mono): <code>/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/4.5/<strong>Facades</strong>/System.Diagnostics.Tracing.dll</code>


###<a name="will-it-help-if-i-manually-add-a-reference-to-the-systemdiagnosticstracing-facade-assembly"></a>"Será útil se adicionar manualmente uma referência ao assembly de fachada"Tracing"?"

Em particular é pode resolver o problema usando estas 2 etapas?

1. Copie o `System.Diagnostics.Tracing.dll` assembly Facade que aponta para a pasta de projeto de aplicativo de um dos seguintes locais:

    Windows: `C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\v4.5\Facades\System.Diagnostics.Tracing.dll`

    Mac (Mono): `/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/4.5/Facades/System.Diagnostics.Tracing.dll`

2. Adicione uma referência ao assembly fachada no projeto de aplicativo xamarin ou xamarin.

#### <a name="answer"></a>Resposta

Não, isso não ajudará.

- Para xamarin 9.0 ou qualquer versão recente do xamarin no Windows, essa solução alternativa é estritamente redundante e pode causar erros de compilação semelhante a "um assembly 'Tracing' com a mesma identidade já foi importado.".

- Para xamarin 8.10 ou inferior ou xamarin no Mac, essa solução alternativa ajudará mas _somente_ para o problema de assembly ausente "camada 1". Ele irá _não_ resolver os erros de vinculador "da camada 2", portanto, não é uma solução completa.


###"Posso usar o <a href="https://www.nuget.org/packages/System.Diagnostics.Tracing/">pacote NuGet Tracing</a> para resolver o problema?"

#### <a name="answer"></a>Resposta

Não, o pacote do NuGet 3.0 "Tracing" só inclui implementações específico da plataforma para "DNXCore50" e "netcore50". Ele explicitamente _omite_ implementações para xamarin ("MonoAndroid") e xamarin ("MonoTouch" e "xamarinios"). Isso significa que terá uma instalação do pacote _nenhum efeito_ para projetos xamarin e xamarin. O pacote NuGet pressupõe que ambas as plataformas fornecem seus _próprio_ implementação dos tipos. Esse pressuposto é "correto" no sentido de que tem Mono _um_ implementação do namespace, mas como discutido em pontos \#2 e \#3 de "Detalhes sobre as 3 camadas de erros" acima, a implementação está atualmente incompleta. Para que a correção apropriada para a equipe Mono resolver [Bug 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337) e [Bug 34890](https://bugzilla.xamarin.com/show_bug.cgi?id=34890).


## <a name="next-steps"></a>Próximas etapas

Para obter mais assistência, entre em contato conosco ou se esse problema permanece mesmo após utilizando as informações acima, consulte [quais opções de suporte estão disponíveis para Xamarin?](~/cross-platform/troubleshooting/support-options.md) para obter informações sobre opções de contato, sugestões, bem como a registrar um bug de novo, se necessário.
