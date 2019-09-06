---
title: Histórico de lançamento de nitidez do objetivo
description: Este documento descreve o histórico de versões de nitidez objetiva, a ferramenta usada para automatizar a criação de C# associações para o código Objective-C.
ms.prod: xamarin
ms.assetid: 1F4A1BE1-7205-43F4-89D0-6C8672F52598
author: conceptdev
ms.author: crdun
ms.date: 10/11/2017
ms.openlocfilehash: b5362c0a809423e2782ee60faa96658cf132d752
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290865"
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
* [Persista as opções de envio de telemetria](https://twitter.com/Symbiatch/status/760373403878559744) de `sharpie pod bind` para `sharpie bind`.

## <a name="32-june-14-2016"></a>3,2 (14 de junho de 2016)

[Baixar v 3.2.3](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.2.3.pkg)

* Suporte para Xcode 8 Beta 1, iOS 10, macOS 10,12, tvOS 10 e watchOS 3.

## <a name="31-may-31-2016"></a>3,1 (31 de maio de 2016)

[Baixar v 3.1.1](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.1.1.pkg)

* Suporte para CocoaPods 1,0
* Confiabilidade de associação de CocoaPods aprimorada primeiro criando `.framework` um nativo e, em seguida, ligando isso
* Copie a `.framework` definição de CocoaPods e de `Binding` associação em um diretório para facilitar a integração com os projetos de associação xamarin. Ios e xamarin. Mac

## <a name="30-october-5-2015"></a>3,0 (5 de outubro de 2015)

[Baixar v 3.0.8](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.0.8.pkg)

* Suporte para novos recursos de linguagem Objective-C, incluindo genéricos leves e nulidade, conforme introduzido no Xcode 7
* Suporte para os SDKs de iOS e Mac mais recentes.
* Suporte à criação e análise de projetos do Xcode! Agora você pode passar um projeto do Xcode completo para a nitidez objetiva e ele fará o melhor para descobrir a coisa certa a fazer (por exemplo, `sharpie bind Project.xcodeproj -sdk ios`).
* Suporte a [CocoaPods](https://cocoapods.org) ! Agora você pode configurar, compilar e associar CocoaPods diretamente da nitidez do objetivo (por exemplo, `sharpie pod init ios AFNetworking && sharpie pod bind`).
* Ao associar estruturas, os módulos Clang serão habilitados se a estrutura der suporte a eles, resultando na análise mais correta de uma estrutura, já que a estrutura da estrutura é definida por `module.modulemap`seu.
* Para projetos do Xcode que criam um produto de estrutura, analise esse produto em vez de alvos de produtos intermediários, pois os destinos não estruturados em um projeto do Xcode ainda podem ter ambiguidades que não podem ser resolvidas automaticamente.

## <a name="216-march-17-2015"></a>2.1.6 (17 de março de 2015)

[Baixar v 2.1.6](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.1.6.pkg)

* Associação de expressão de operador binário fixa: o lado esquerdo da expressão foi alternado incorretamente com o lado direito (por exemplo, `1 << 0` foi associado incorretamente como `0 << 1`). Obrigado pelo Adam Kemp para observar isso!
* Correção de um problema `NSInteger` com `NSUInteger` o e que `int` está sendo ligado `nint` como `nuint` e `uint` em vez de e em i386; Agora é passado para Clang para tornar o `objc/NSObjCRuntime.h` trabalho de análise como esperado em i386. `-DNS_BUILD_32_LIKE_64`
* A arquitetura padrão para SDKs de Mac os X (por `-sdk macosx10.10`exemplo,) é agora x86_64 em vez de `-arch` i386, portanto, pode ser omitida a menos que a substituição do padrão seja desejada.

## <a name="210-march-15-2015"></a>2.1.0 (15 de março de 2015)

[Baixar v 2.1.0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.1.0.pkg)

* [bxC#27849](https://bugzilla.xamarin.com/show_bug.cgi?id=27849): Certifique `using ObjCRuntime;` -se de `ArgumentSemantic` que é produzido quando é usado.
* [bxC#27850](https://bugzilla.xamarin.com/show_bug.cgi?id=27850): Certifique `using System.Runtime.InteropServices;` -se de `DllImport` que é produzido quando é usado.
* [bxC#27852](https://bugzilla.xamarin.com/show_bug.cgi?id=27852): Padrão `DllImport` ao carregar `__Internal`símbolos.
* [bxC#27848](https://bugzilla.xamarin.com/show_bug.cgi?id=27848): Ignorar declarações de contêiner de Objective-C declaradas.
* [bxC#27846](https://bugzilla.xamarin.com/show_bug.cgi?id=27846): Associe os tipos de protocolo a uma única qualificação como interfaces`id<Foo>` concretas ( `Foundation.NSObject<Foo>`como `Foo` em vez de).
* [bxC#28037](https://bugzilla.xamarin.com/show_bug.cgi?id=28037): Associe `UInt32`, `u` `uL` e literais `Int32`como `Int32` para descartar os sufixos e/ou quando os valores podem se ajustar com segurança. `UInt64` `Int64`
* [bxC#28038](https://bugzilla.xamarin.com/show_bug.cgi?id=28038): Corrigir o mapeamento de nome de enumeração quando o nome nativo `k` original começa com um prefixo.
* `sizeof`As expressões C cujo tipo de argumento não é mapeado C# para um tipo primitivo serão avaliadas em Clang e associadas como um literal inteiro para evitar C#a geração de inválida.
* Corrija a sintaxe Objective-C para propriedades cujo tipo é um bloco (o código Objective-C aparece em comentários acima das declarações associadas).
* Associe os tipos de Decayed como seu tipo`int[]` original ( `int*` decays ao durante a análise semântica no Clang, mas associe-o como o `int[]` original como escrito, em vez disso).

Agradecemos muito a Dave Dunkin por relatar muitos dos bugs corrigidos nesta versão de ponto!

## <a name="200-march-9-2015"></a>2.0.0 9 de março de 2015

[Baixar v 2.0.0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.0.0.pkg)

O objetivo de nitidez 2,0 é uma versão principal que apresenta um driver e analisador aprimorado e baseado em Clang e um novo mecanismo de associação baseado em NRefactory. Esses componentes aprimorados fornecem associações _muito_ melhores, especialmente em relação à associação de tipo. Foram feitas muitas outras melhorias que são internas à nitidez objetiva, o que produzirá muitos recursos visíveis ao usuário em versões futuras.

O objetivo de nitidez 2,0 é baseado no Clang 3.6.1.

### <a name="type-binding-improvements"></a>Aprimoramentos de associação de tipo

* Os blocos Objective-C agora têm suporte. Isso inclui blocos anônimos/embutidos e blocos `typedef`chamados por meio de. Os blocos anônimos serão associados `System.Action` como `System.Func` ou delegados, enquanto os blocos nomeados serão vinculados `delegate` como tipos fortemente nomeados.

* Há uma heurística de nomenclatura aprimorada para enums anônimos que são imediatamente precedidas por uma `typedef` resolução para um tipo integral interna `long` , como ou. `int`

* Os ponteiros C agora estão C# `unsafe` associados como ponteiros em vez de. `System.IntPtr` Isso resulta em mais clareza na associação para quando você desejar transformar parâmetros de ponteiro em `out` parâmetros ou. `ref` Não é possível sempre inferir se um parâmetro deve ser `out` ou `ref`, portanto, o ponteiro é retido na associação para permitir a auditoria mais fácil.

* Uma exceção à associação de ponteiros acima é quando um ponteiro de duas classificações para um objeto Objective-C é encontrado como um parâmetro. Nesses casos, a Convenção é predominante e o parâmetro será associado como `out` ( `NSError **error` por exemplo, → `out NSError error`).

### <a name="verify-attribute"></a>Verificar atributo

Muitas vezes, você descobrirá que as associações produzidas pela nitidez objetiva agora serão anotadas com `[Verify]` o atributo. Esses atributos indicam que você deve _verificar_ se a nitidez objetiva fez a coisa correta comparando a associação com a declaração c/Objective-c original (que será fornecida em um comentário acima da declaração associada).

A verificação é _recomendada_ para todas as declarações associadas, mas é provavelmente _necessária_ para declarações anotadas com `[Verify]` o atributo. Isso ocorre porque, em muitas situações, não há metadados suficientes no código-fonte nativo original para inferir como produzir melhor uma associação. Talvez seja necessário fazer referência a documentação ou comentários de código dentro dos arquivos de cabeçalho para tomar a melhor decisão de ligação.

Consulte a documentação de [verificar atributos](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md) para obter mais detalhes.

### <a name="other-notable-improvements"></a>Outros aprimoramentos notáveis

* `using`Agora, as instruções são geradas com base nos tipos associados. Por exemplo, se um `NSURL` foi associado, uma `using Foundation;` instrução também será gerada.

* `struct`as `union` declarações e agora serão associadas, usando o `[FieldOffset]` truque para uniões.

* Os valores de enumeração com inicializadores de expressão constantes agora estarão adequadamente associados; a expressão completa é convertida C#em.

* Os métodos e blocos Variadic agora estão associados.

* As estruturas agora têm suporte por meio `-framework` da opção. Consulte a documentação sobre como [associar estruturas nativas](~/cross-platform/macios/binding/objective-sharpie/index.md) para obter mais detalhes.

* O código-fonte Objective-C será detectado automaticamente agora, o que deve eliminar a necessidade `-ObjC` de `-xobjective-c` passar ou clang manualmente.

* O uso do módulo`@import`Clang () agora é detectado automaticamente, o que deve eliminar a necessidade `-fmodules` de passar para o Clang manualmente para bibliotecas que usam o novo módulo suporte no Clang.

* O API Unificada Xamarin agora é o destino de associação padrão; Use a `-classic` opção para direcionar apenas API clássica de 32 bits.

### <a name="notable-bug-fixes"></a>Correções de bugs notáveis

* Corrigir `instancetype` a associação quando usada em uma categoria Objective-C
* Nome totalmente de Objective categorias-C
* Prefixe os protocolos Objective `I` -C com `INSCopying` (por `NSCopying`exemplo, em vez de)

## <a name="1135-december-21-2014"></a>1.1.35: 21 de dezembro de 2014

[Baixar v 1.1.35](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-1.1.35.pkg)

Correções de bugs secundárias.

## <a name="111-december-15-2014"></a>1.1.1 15 de dezembro de 2014

[Baixar o v 1.1.1](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-1.1.1.pkg)

1.1.1 foi a primeira versão principal após 1,5 anos de uso interno e desenvolvimento no Xamarin seguindo a visualização inicial da nitidez do objetivo em abril de 2013. Essa versão é a primeira a ser geralmente considerada estável e utilizável para uma ampla variedade de bibliotecas nativas, apresentando um novo back-end Clang.

