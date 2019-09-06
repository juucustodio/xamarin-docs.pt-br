---
title: Resolver problemas relacionados a System. Diagnostics. Tracing e a TPL Dataflow
description: 'Estudo de caso de PCL: como posso resolver problemas relacionados a System.Diagnostics.Tracing para o pacote NuGet de Fluxo de Dados de TPL da Microsoft?'
ms.prod: xamarin
ms.assetid: 7986A556-382D-4D00-ACCF-3589B4029DE8
ms.date: 04/17/2018
author: conceptdev
ms.author: crdun
ms.openlocfilehash: e3ced3c989a88c6e759a5bc497147128b0a79868
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70288200"
---
# <a name="pcl-case-study-how-can-i-resolve-problems-related-to-systemdiagnosticstracing-for-the-microsoft-tpl-dataflow-nuget-package"></a>Estudo de caso de PCL: como posso resolver problemas relacionados a System.Diagnostics.Tracing para o pacote NuGet de Fluxo de Dados de TPL da Microsoft?

> [!IMPORTANT]
> Esse exemplo específico de `System.Diagnostic.Tracing` não produz mais erros por padrão nas versões mais recentes do Xamarin. Embora a solução alternativa sugerida ainda funcione, observe que alguns dos bugs mencionados na seção "camadas de erros" foram corrigidos.
> Além disso, você deve observar que .NET Standard agora é a maneira preferida de implementar APIs .NET entre plataformas.

## <a name="summary"></a>Resumo

O xamarin. iOS e o Xamarin. Android não implementam 100% de cada perfil PCL que eles permitem como referências. Para conveniência prática no Visual Studio para Mac, no Visual Studio e no Gerenciador de pacotes NuGet, os projetos do Xamarin permitem o uso de vários perfis que têm apenas implementações _incompletas_ . Por exemplo, nem Xamarin. iOS nem Xamarin. Android atualmente incluem uma implementação completa dos tipos no namespace PCL "System. Diagnostics. Tracing". Essa limitação leva a três camadas de erros ao tentar usar a versão padrão `portable-net45+win8+wpa81` do pacote NuGet do Microsoft TPL Dataflow.

## <a name="workaround-switch-the-app-project-to-reference-the-portable-net45win8wp8wpa81-version-of-the-tpl-dataflow-library"></a>Solução alternativa: Alternar o projeto de aplicativo para referenciar a `portable-net45+win8+wp8+wpa81` versão da biblioteca de fluxo de aplicativos TPL

(Isso evita todas as três camadas de erros e funciona para todas as versões recentes do Xamarin.)

1. Abra o arquivo Project **. csproj** do aplicativo em um editor de texto.

2. Localize a linha que é semelhante a esta:

    ```xml
    <HintPath>..\packages\Microsoft.Tpl.Dataflow.4.5.24\lib\portable-net45+win8+wpa81\System.Threading.Tasks.Dataflow.dll</HintPath>
    ```

3. Alterar `portable-net45+win8+wpa81` para `portable-net45+win8+wp8+wpa81` (`+wp8` é adicionado):

    ```xml
    <HintPath>..\packages\System.Threading.Tasks.Dataflow.4.5.25\lib\portable-net45+win8+wp8+wpa81\System.Threading.Tasks.Dataflow.dll</HintPath>
    ```

### <a name="explanation"></a>Explicação

A `portable-net45+win8+wp8+wpa81` versão da biblioteca não faz referência a **System. Diagnostics. Tracing. dll** _, por_isso evita completamente todas as três camadas de problemas.

### <a name="limitations"></a>Limitações

- A `portable-net45+win8+wp8+wpa81` versão da biblioteca pode não incluir 100% da funcionalidade `portable-net45+win8+wpa81` da versão.

- O Gerenciador de pacotes NuGet instala `portable-net45+win8+wpa81` a versão do pacote do NuGet PCL por padrão, portanto, você deve ajustar a referência manualmente.

## <a name="details-about-the-three-layers-of-errors"></a>Detalhes sobre as três camadas de erros

1. O assembly de fachada **System. Diagnostics. Tracing. dll** está ausente atualmente de todas as versões Mac do Xamarin. Android (bug não público 34888) e está ausente de todas as versões do Xamarin. Ios inferiores a 9,0 (ou inferior a XamarinVS 3.11.1443 no Windows) (corrigido em [Bug 32388](https://bugzilla.xamarin.com/show_bug.cgi?id=32388)). Esse problema causará um dos seguintes erros dependendo do destino da implantação e das configurações do vinculador:

    - Xamarin.Android.Common.targets: Erro: Exceção ao carregar assemblies: System.IO.FileNotFoundException: Não foi possível carregar o assembly ' System. Diagnostics. Tracing ', versão = 4.0.0.0, Culture = neutral, PublicKeyToken = b03f5f7f11d50a3a '. Talvez ele não exista no perfil mono para Android?

    - Não foi possível carregar o arquivo ou o assembly ' System. Diagnostics. Tracing ' ou uma de suas dependências. O sistema não pode encontrar o arquivo especificado. (System.IO.FileNotFoundException)

    - MTOUCH: erro MT3001: Não foi possível AOT o assembly '/Users/macuser/Projects/TPLDataflow/UnifiedSingleViewIphone1/obj/iPhone/Debug/mtouch-cache/64/Build/System.Threading.Tasks.Dataflow.dll '

    - MTOUCH: erro MT2002: Falha ao resolver o assembly: 'System.Diagnostics.Tracing, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a'

2. A [implementação mono atual dos tipos em "System. Diagnostics. Tracing"](https://github.com/mono/mono/blob/master/mcs/class/corlib/System.Diagnostics.Tracing/EventSource.cs) não tem algumas sobrecargas de método ([bug 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337)). Esse problema causará um dos seguintes erros de vinculador ao criar um aplicativo Xamarin:

    - /Library/Frameworks/Mono.framework/External/xbuild/Xamarin/Android/Xamarin.Android.Common.targets: erro: Erro ao executar a tarefa LinkAssemblies: erro XA2006: Referência ao item de metadados ' System. void System. Diagnostics. Tracing. EventSource:: WriteEvent (System. Int32, System. Object []) ' (definido em ' System. Threading. Tasks. Dataflow, Version = 4.5.24.0, Culture = neutral, PublicKeyToken = b03f5f7f11d50a3a ') de ' System. Threading. Tasks. Dataflow, Version = 4.5.24.0, Culture = neutral, PublicKeyToken = b03f5f7f11d50a3a ' não pôde ser resolvido.

    - MTOUCH: erro MT2002: Falha ao resolver "System. void System. Diagnostics. Tracing. EventSource:: WriteEvent (System. Int32, System. Object [])" referência de "System. Diagnostics. Tracing, versão = 4.0.0.0, Culture = neutral, PublicKeyToken = b03f5f7f11d50a3a"

3. A [implementação mono atual dos tipos em "System. Diagnostics. Tracing"](https://github.com/mono/mono/blob/master/mcs/class/corlib/System.Diagnostics.Tracing/EventSource.cs) também é uma implementação "fictícia" _vazia_ ([bug 34890](https://bugzilla.xamarin.com/show_bug.cgi?id=34890)). Qualquer tentativa de usar esses métodos em tempo de execução pode, portanto, gerar resultados inesperados. Para o caso _específico_ da biblioteca de Dataflow do Microsoft tpl, parece que as `WriteEvent(System.Int32,System.Object[])` chamadas não são essenciais para a maior parte do comportamento da biblioteca, portanto, a correção para "camada 2" ([bug 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337), adição de implementações vazias) provavelmente será suficiente para a maioria dos casos de uso do Microsoft TPL Dataflow.

## <a name="questions--answers"></a>Perguntas & respostas

### <a name="i-was-able-to-leave-linking-enabled-with-the-portable-net45win8wpa81-version-of-the-library-on-older-versions-of-xamarinios-or-on-xamarinandroid-how-did-that-work"></a>Consegui deixar a vinculação habilitada com a `portable-net45+win8+wpa81` versão da biblioteca em versões anteriores do xamarin. Ios ou no xamarin. Android. Como isso funcionou?

#### <a name="answer"></a>Atenda

É _possível_ fazer com que a compilação seja concluída "com êxito" (com vinculação habilitada) em versões mais antigas do xamarin. Ios ou no xamarin. Android no Mac se você incluir uma referência `System.Diagnostics.Tracing.dll` ao _assembly_ \[de referência 1\]em vez do _assembly_ \[de fachada 2], mas infelizmente essa não é uma solução alternativa "correta". Os assemblies de referência só devem ser usados durante a criação de _bibliotecas portáteis_, não o código específico da plataforma, como aplicativos. A tentativa de _executar_ o código contido em assemblies de referência (em vez de apenas criar a partir dele) provavelmente produzirá resultados inesperados. A correção correta será para que a equipe mono adicione a sobrecarga ausente `WriteEvent(System.Int32,System.Object[])` [`EventSource`](https://github.com/mono/mono/blob/master/mcs/class/corlib/System.Diagnostics.Tracing/EventSource.cs) ao tipo ([bug 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337)). Por enquanto, a melhor opção é alternar para a `portable-net45+win8+wp8+wpa81` versão da biblioteca de Dataflow do Microsoft tpl, conforme discutido na seção de solução alternativa acima.

(Para qualquer pessoa que possa estar lendo este artigo depois de ver uma resposta mais antiga e mais curta do<https://stackoverflow.com/a/23591322/2561894>StackOverflow (), observe que a distinção entre assemblies de referência e o assembly de fachada _não_ foi mencionada lá.)

**\[1\] locais de "assembly de referência"**

Windows: `C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETPortable\v4.5\System.Diagnostics.Tracing.dll`

Mac (mono):`/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/xbuild-frameworks/.NETPortable/v4.5/System.Diagnostics.Tracing.dll`

**\[2\] locais de "assembly fachada"**

Windows: `C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\v4.5\Facades\System.Diagnostics.Tracing.dll`

Mac (mono):`/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/4.5/Facades/System.Diagnostics.Tracing.dll`


### <a name="will-it-help-if-i-manually-add-a-reference-to-the-systemdiagnosticstracing-facade-assembly"></a>Ajudará se eu adicionar manualmente uma referência ao assembly de fachada "System. Diagnostics. Tracing"?

_Em particular, é possível resolver o problema usando essas duas etapas?_

1. _Copie o `System.Diagnostics.Tracing.dll` assembly de fachada na pasta do projeto de aplicativo de um dos seguintes locais:_

    Windows: `C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\v4.5\Facades\System.Diagnostics.Tracing.dll`

    Mac (mono):`/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/4.5/Facades/System.Diagnostics.Tracing.dll`

2. _Adicione uma referência ao assembly de fachada no projeto de aplicativo Xamarin. iOS ou Xamarin. Android._

#### <a name="answer"></a>Atenda

Não, isso não ajudará.

- Para o Xamarin. iOS 9,0 ou qualquer versão recente do Xamarin. Android no Windows, essa solução alternativa é estritamente redundante e pode causar erros de compilação semelhantes a "um assembly ' System. Diagnostics. Tracing ' com a mesma identidade já foi importado.".

- Para o Xamarin. iOS 8,10 ou inferior ou para o Xamarin. Android no Mac, essa solução alternativa ajudará _apenas_ para o problema de assembly "camada 1" ausente. Ele _não_ resolverá os erros de vinculador de "camada 2", portanto, não é uma solução completa.

### <a name="can-i-use-the-systemdiagnosticstracing-nuget-packagehttpswwwnugetorgpackagessystemdiagnosticstracing-to-solve-the-problem"></a>Posso usar o [pacote NuGet System. Diagnostics. Tracing](https://www.nuget.org/packages/System.Diagnostics.Tracing/) para resolver o problema?

#### <a name="answer"></a>Atenda

Não, o pacote NuGet 3,0 "System. Diagnostics. Tracing" inclui apenas implementações específicas de plataforma para "DNXCore50" e "netcore50". Ele _omite_ explicitamente as implementações para Xamarin. Android ("monoandroid") e Xamarin. Ios ("MonoTouch" e "xamarinios"). Isso significa que a instalação do pacote _não terá nenhum efeito_ para os projetos Xamarin. Android e Xamarin. Ios. O pacote NuGet pressupõe que essas duas plataformas fornecem sua _própria_ implementação dos tipos. Essa suposição é "correta" no sentido de que o mono tem _uma_ implementação do namespace, mas, como discutido nos \#pontos 2 \#e 3 de "detalhes sobre as três camadas de erros" acima, a implementação está atualmente completa. Portanto, a correção correta será para que a equipe mono resolva o [bug 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337) e o [bug 34890](https://bugzilla.xamarin.com/show_bug.cgi?id=34890).

## <a name="next-steps"></a>Próximas etapas

Para obter mais assistência, entrar em contato conosco ou, se esse problema permanecer mesmo depois de utilizar as informações acima, consulte [quais opções de suporte estão disponíveis para o Xamarin?](~/cross-platform/troubleshooting/support-options.md) para obter informações sobre opções de contato, sugestões, bem como como arquivar um novo bug, se necessário .
