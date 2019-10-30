---
title: Histórico de lançamento de nitidez do objetivo
description: Este documento descreve o histórico de versões de nitidez objetiva, a ferramenta usada para automatizar a criação de C# associações para o código Objective-C.
ms.prod: xamarin
ms.assetid: 1F4A1BE1-7205-43F4-89D0-6C8672F52598
author: davidortinau
ms.author: daortin
ms.date: 10/11/2017
ms.openlocfilehash: 2f1fb3706012fa86834986064a366071b644b2dc
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73015949"
---
# <a name="objective-sharpie-release-history"></a>Histórico de lançamento de nitidez do objetivo

## <a name="34-october-11-2017"></a>3,4 (11 de outubro de 2017)

[Baixar v 3.4.0](https://dl.xamarin.com/objective-sharpie/ObjectiveSharpie-3.4.0.pkg)

* Suporte para o Xcode 9: iOS 11, macOS 10,13, tvOS 11 e watchOS 4
* Problemas com SIMD e tgmath agora devem ser corrigidos
* A telemetria foi removida completamente

## <a name="33-august-3-2016"></a>3,3 (3 de agosto de 2016)

[Baixar v 3.3.0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.3.0.pkg)

* Suporte para Xcode 8 Beta 4, iOS 10, macOS 10,12, tvOS 10 e watchOS 3.
* Atualizado para a compilação mestra mais recente do Clang (2016-08-02)
* [Persistir as opções de envio de telemetria](https://twitter.com/Symbiatch/status/760373403878559744) de `sharpie pod bind` para `sharpie bind`.

## <a name="32-june-14-2016"></a>3,2 (14 de junho de 2016)

[Baixar v 3.2.3](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.2.3.pkg)

* Suporte para Xcode 8 Beta 1, iOS 10, macOS 10,12, tvOS 10 e watchOS 3.

## <a name="31-may-31-2016"></a>3,1 (31 de maio de 2016)

[Baixar v 3.1.1](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.1.1.pkg)

* Suporte para CocoaPods 1,0
* Confiabilidade de associação de CocoaPods aprimorada primeiro criando um `.framework` nativo e, em seguida, associando isso
* Copie `.framework` CocoaPods e a definição de associação em um diretório `Binding` para tornar a integração com o Xamarin. iOS e os projetos de associação do Xamarin. Mac mais fáceis

## <a name="30-october-5-2015"></a>3,0 (5 de outubro de 2015)

[Baixar v 3.0.8](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.0.8.pkg)

* Suporte para novos recursos de linguagem Objective-C, incluindo genéricos leves e nulidade, conforme introduzido no Xcode 7
* Suporte para os SDKs de iOS e Mac mais recentes.
* Suporte à criação e análise de projetos do Xcode! Agora você pode passar um projeto Xcode completo para a nitidez objetiva e ele fará o melhor para descobrir a coisa certa a fazer (por exemplo, `sharpie bind Project.xcodeproj -sdk ios`).
* Suporte a [CocoaPods](https://cocoapods.org) ! Agora você pode configurar, compilar e associar CocoaPods diretamente da nitidez objetiva (por exemplo, `sharpie pod init ios AFNetworking && sharpie pod bind`).
* Ao associar estruturas, os módulos Clang serão habilitados se a estrutura der suporte a eles, resultando na análise mais correta de uma estrutura, já que a estrutura da estrutura é definida por sua `module.modulemap`.
* Para projetos do Xcode que criam um produto de estrutura, analise esse produto em vez de alvos de produtos intermediários, pois os destinos não estruturados em um projeto do Xcode ainda podem ter ambiguidades que não podem ser resolvidas automaticamente.

## <a name="216-march-17-2015"></a>2.1.6 (17 de março de 2015)

[Baixar v 2.1.6](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.1.6.pkg)

* Associação de expressão de operador binário fixa: o lado esquerdo da expressão foi alternado incorretamente com o lado direito (por exemplo, `1 << 0` foi incorretamente associado como `0 << 1`). Obrigado pelo Adam Kemp para observar isso!
* Corrigido um problema com `NSInteger` e `NSUInteger` estão sendo associados como `int` e `uint` em vez de `nint` e `nuint` em i386; `-DNS_BUILD_32_LIKE_64` agora é passado para Clang para tornar a análise `objc/NSObjCRuntime.h` funcionar conforme o esperado em i386.
* A arquitetura padrão para SDKs de Mac OS X (por exemplo, `-sdk macosx10.10`) agora é x86_64 em vez de i386, para que `-arch` possa ser omitida, a menos que a substituição do padrão seja desejada.

## <a name="210-march-15-2015"></a>2.1.0 (15 de março de 2015)

[Baixar v 2.1.0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.1.0.pkg)

* [BXC # 27849](https://bugzilla.xamarin.com/show_bug.cgi?id=27849): verifique se `using ObjCRuntime;` é produzido quando `ArgumentSemantic` é usado.
* [BXC # 27850](https://bugzilla.xamarin.com/show_bug.cgi?id=27850): verifique se `using System.Runtime.InteropServices;` é produzido quando `DllImport` é usado.
* [BXC # 27852](https://bugzilla.xamarin.com/show_bug.cgi?id=27852): o `DllImport` padrão para carregar símbolos de `__Internal`.
* [BXC # 27848](https://bugzilla.xamarin.com/show_bug.cgi?id=27848): ignorar declarações de contêiner de Objective-C declaradas.
* [BXC # 27846](https://bugzilla.xamarin.com/show_bug.cgi?id=27846): associar tipos de protocolo a uma única qualificação como interfaces concretas (`id<Foo>` como `Foo` em vez de `Foundation.NSObject<Foo>`).
* [BXC # 28037](https://bugzilla.xamarin.com/show_bug.cgi?id=28037): associar `UInt32`, `UInt64`e literais `Int64` como `Int32` para descartar os sufixos `u` e/ou `uL` quando os valores puderem se encaixar com segurança no `Int32`.
* [BXC # 28038](https://bugzilla.xamarin.com/show_bug.cgi?id=28038): corrigir o mapeamento de nome de enumeração quando o nome nativo original começar com um prefixo de `k`.
* `sizeof` expressões C cujo tipo de argumento não é mapeado para C# um tipo primitivo serão avaliadas em Clang e associadas como um literal inteiro para evitar a C#geração de inválida.
* Corrija a sintaxe Objective-C para propriedades cujo tipo é um bloco (o código Objective-C aparece em comentários acima das declarações associadas).
* Associe os tipos Decayed como seu tipo original (`int[]` decays para `int*` durante a análise semântica no Clang, mas associe-o como o `int[]` original gravado em vez disso).

Agradecemos muito a Dave Dunkin por relatar muitos dos bugs corrigidos nesta versão de ponto!

## <a name="200-march-9-2015"></a>2.0.0:9 de março de 2015

[Baixar v 2.0.0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.0.0.pkg)

O objetivo de nitidez 2,0 é uma versão principal que apresenta um driver e analisador aprimorado e baseado em Clang e um novo mecanismo de associação baseado em NRefactory. Esses componentes aprimorados fornecem associações _muito_ melhores, especialmente em relação à associação de tipo. Foram feitas muitas outras melhorias que são internas à nitidez objetiva, o que produzirá muitos recursos visíveis ao usuário em versões futuras.

O objetivo de nitidez 2,0 é baseado no Clang 3.6.1.

### <a name="type-binding-improvements"></a>Aprimoramentos de associação de tipo

* Os blocos Objective-C agora têm suporte. Isso inclui blocos anônimos/embutidos e blocos nomeados por meio de `typedef`. Os blocos anônimos serão associados como `System.Action` ou delegados de `System.Func`, enquanto os blocos nomeados serão vinculados como tipos de `delegate` fortemente nomeados.

* Há uma heurística de nomenclatura aprimorada para enums anônimos que são imediatamente precedidas por um `typedef` resolver para um tipo integral interna, como `long` ou `int`.

* Os ponteiros C agora estão C# associados como ponteiros`unsafe`em vez de`System.IntPtr`. Isso resulta em mais clareza na associação para quando você desejar transformar parâmetros de ponteiro em `out` ou `ref` parâmetros. Não é possível sempre inferir se um parâmetro deve ser `out` ou `ref`, portanto, o ponteiro é retido na associação para permitir a auditoria mais fácil.

* Uma exceção à associação de ponteiros acima é quando um ponteiro de duas classificações para um objeto Objective-C é encontrado como um parâmetro. Nesses casos, a Convenção é predominante e o parâmetro será associado como `out` (por exemplo, `NSError **error` → `out NSError error`).

### <a name="verify-attribute"></a>Verificar atributo

Muitas vezes, você descobrirá que as associações produzidas pela nitidez objetiva agora serão anotadas com o atributo `[Verify]`. Esses atributos indicam que você deve _verificar_ se a nitidez objetiva fez a coisa correta comparando a associação com a declaração c/Objective-c original (que será fornecida em um comentário acima da declaração associada).

A verificação é _recomendada_ para todas as declarações associadas, mas é provavelmente _necessária_ para declarações anotadas com o atributo `[Verify]`. Isso ocorre porque, em muitas situações, não há metadados suficientes no código-fonte nativo original para inferir como produzir melhor uma associação. Talvez seja necessário fazer referência a documentação ou comentários de código dentro dos arquivos de cabeçalho para tomar a melhor decisão de ligação.

Consulte a documentação de [verificar atributos](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md) para obter mais detalhes.

### <a name="other-notable-improvements"></a>Outros aprimoramentos notáveis

* `using` instruções agora são geradas com base nos tipos associados. Por exemplo, se um `NSURL` foi associado, uma instrução `using Foundation;` também será gerada.

* as declarações `struct` e `union` agora serão associadas, usando o truque `[FieldOffset]` para uniões.

* Os valores de enumeração com inicializadores de expressão constantes agora estarão adequadamente associados; a expressão completa é convertida C#em.

* Os métodos e blocos Variadic agora estão associados.

* Agora há suporte para as estruturas por meio da opção `-framework`. Consulte a documentação sobre como [associar estruturas nativas](~/cross-platform/macios/binding/objective-sharpie/index.md) para obter mais detalhes.

* O código-fonte Objective-C será detectado automaticamente agora, o que deve eliminar a necessidade de passar `-ObjC` ou `-xobjective-c` para Clang manualmente.

* O uso do módulo Clang (`@import`) agora é detectado automaticamente, o que deve eliminar a necessidade de passar `-fmodules` para o Clang manualmente para bibliotecas que usam o novo módulo suporte no Clang.

* O API Unificada Xamarin agora é o destino de associação padrão; Use a opção `-classic` para direcionar apenas o API Clássica de 32 bits.

### <a name="notable-bug-fixes"></a>Correções de bugs notáveis

* Corrigir a associação de `instancetype` quando usada em uma categoria Objective-C
* Nome totalmente de Objective categorias-C
* Prefixe os protocolos Objective-C com `I` (por exemplo, `INSCopying` em vez de `NSCopying`)

## <a name="1135-december-21-2014"></a>1.1.35:21 de dezembro de 2014

[Baixar v 1.1.35](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-1.1.35.pkg)

Correções de bugs secundárias.

## <a name="111-december-15-2014"></a>1.1.1:15 de dezembro de 2014

[Baixar o v 1.1.1](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-1.1.1.pkg)

1.1.1 foi a primeira versão principal após 1,5 anos de uso interno e desenvolvimento no Xamarin seguindo a visualização inicial da nitidez do objetivo em abril de 2013. Essa versão é a primeira a ser geralmente considerada estável e utilizável para uma ampla variedade de bibliotecas nativas, apresentando um novo back-end Clang.
