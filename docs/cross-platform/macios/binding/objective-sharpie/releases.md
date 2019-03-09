---
title: Histórico de versão do objetivo Sharpie
description: Este documento descreve o histórico de lançamento de Sharpie objetivo, a ferramenta usada para automatizar a criação de C# associações a código Objective-C.
ms.prod: xamarin
ms.assetid: 1F4A1BE1-7205-43F4-89D0-6C8672F52598
author: asb3993
ms.author: amburns
ms.date: 10/11/2017
ms.openlocfilehash: 03e4a5ac8906d2593cbdf3c15f6b2d1f4a2c6d19
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2019
ms.locfileid: "57667250"
---
# <a name="objective-sharpie-release-history"></a>Histórico de versão do objetivo Sharpie

## <a name="34-october-11-2017"></a>3.4 (11 de outubro de 2017)

[Baixar v3.4.0](https://dl.xamarin.com/objective-sharpie/ObjectiveSharpie-3.4.0.pkg)

* Suporte para Xcode 9: iOS 11, macOS 10.13, 11, do tvOS e watchOS 4
* Problemas com SIMD e tgmath agora devem ser corrigidos
* Telemetria foi removida completamente

## <a name="33-august-3-2016"></a>3.3 (3 de agosto de 2016)

[Baixar v3.3.0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.3.0.pkg)

* Suporte para Xcode 8 Beta 4, o iOS 10, o macOS 10.12, tvOS 10 e watchOS 3.
* Atualizado para Clang mais recente compilação mestre (2016-08-02)
* [Manter as opções de envio de telemetria](https://twitter.com/Symbiatch/status/760373403878559744) partir `sharpie pod bind` para `sharpie bind`.

## <a name="32-june-14-2016"></a>3.2 (14 de junho de 2016)

[Baixar v 3.2.3](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.2.3.pkg)

* Suporte para Xcode 8 Beta 1, o iOS 10, o macOS 10.12, tvOS 10 e watchOS 3.

## <a name="31-may-31-2016"></a>3.1 (31 de maio de 2016)

[Baixar v3.1.1](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.1.1.pkg)

* Suporte para o CocoaPods 1.0
* Confiabilidade de associação de CocoaPods aprimorada criando primeiro um nativo `.framework` e, em seguida, a associação que
* Copie o CocoaPods `.framework` e associando a definição em um `Binding` diretório para facilitar a integração com projetos de associação do xamarin. IOS e xamarin. Mac

## <a name="30-october-5-2015"></a>3.0 (5 de outubro de 2015)

[Baixar v3.0.8](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.0.8.pkg)

* Suporte para novos recursos de linguagem de Objective-C, incluindo genéricos leves e a nulidade, conforme apresentada no Xcode 7
* Suporte para o iOS e Mac SDKs mais recentes.
* Criação e análise de suporte de projeto do Xcode! Agora você pode transmitir um projeto completo do Xcode para o objetivo Sharpie e fará o melhor para descobrir a coisa certa a fazer (por exemplo, `sharpie bind Project.xcodeproj -sdk ios`).
* [O CocoaPods](https://cocoapods.org) suporte! Você agora pode configurar, criar e associar CocoaPods diretamente do objetivo Sharpie (por exemplo, `sharpie pod init ios AFNetworking && sharpie pod bind`).
* Ao associar estruturas, módulos de Clang serão habilitados se o framework oferece suporte a eles, resultando na análise correta de mais de uma estrutura, desde que a estrutura da estrutura é definida por seu `module.modulemap`.
* Para projetos do Xcode que criar um produto de estrutura, analise esse produto em vez de destinos de produto intermediário como destinos de não-framework em um projeto do Xcode ainda poderão ter a ambiguidades que não podem ser resolvidas automaticamente.

## <a name="216-march-17-2015"></a>2.1.6 (17 de março de 2015)

[Baixar v2.1.6](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.1.6.pkg)

* Associação de expressão de operador binário fixo: o lado esquerdo da expressão incorretamente foi trocado por à direita (por exemplo, `1 << 0` incorretamente foram associados como `0 << 1`). Graças ao Adam Kemp para perceber isso!
* Corrigido um problema com o `NSInteger` e `NSUInteger` que está sendo associado como `int` e `uint` em vez de `nint` e `nuint` em i386; `-DNS_BUILD_32_LIKE_64` agora é passado para Clang para fazer a análise `objc/NSObjCRuntime.h` funcionar conforme esperado no i386.
* A arquitetura padrão para SDKs do Mac OS X (por exemplo, `-sdk macosx10.10`) é agora x86_64 em vez de i386, portanto, `-arch` pode ser omitido, a menos que substituindo o padrão é desejado.

## <a name="210-march-15-2015"></a>2.1.0 (15 de março de 2015)

[Baixar v2.1.0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.1.0.pkg)

* [bxc#27849](https://bugzilla.xamarin.com/show_bug.cgi?id=27849): Certifique-se `using ObjCRuntime;` é produzido quando `ArgumentSemantic` é usado.
* [bxc#27850](https://bugzilla.xamarin.com/show_bug.cgi?id=27850): Certifique-se `using System.Runtime.InteropServices;` é produzido quando `DllImport` é usado.
* [bxc#27852](https://bugzilla.xamarin.com/show_bug.cgi?id=27852): Padrão `DllImport` para carregar símbolos de `__Internal`.
* [bxc#27848](https://bugzilla.xamarin.com/show_bug.cgi?id=27848): Ignorar declarações de contêiner de Objective-C declarados de encaminhamento.
* [bxc#27846](https://bugzilla.xamarin.com/show_bug.cgi?id=27846): Associar tipos de protocolo com um único qualificação como interfaces concretos (`id<Foo>` como `Foo` em vez de `Foundation.NSObject<Foo>`).
* [bxc#28037](https://bugzilla.xamarin.com/show_bug.cgi?id=28037): Associar `UInt32`, `UInt64`, e `Int64` literais como `Int32` para descartar o `u` e/ou `uL` sufixos quando os valores com segurança podem se encaixar em `Int32`.
* [bxc#28038](https://bugzilla.xamarin.com/show_bug.cgi?id=28038): Corrija o mapeamento de nome de enumeração quando o nome nativo original começa com um `k` prefixo.
* `sizeof` Expressões de C cujo tipo de argumento não é mapeado para um C# tipo primitivo será avaliado em Clang e vinculado como um literal inteiro para evitar a geração inválido C#.
* Corrija a sintaxe de Objective-C para propriedades cujo tipo é um bloco (código Objective-C que aparece nos comentários acima declarações associadas).
* Associar tipos diminuísse como seu tipo original (`int[]` decai para `int*` durante a análise semântica em Clang, mas associá-lo do original como escrito `int[]` em vez disso).

Muito obrigado a Dave Dunkin para muitos dos bugs corrigidos nesta versão do ponto de relatório!

## <a name="200-march-9-2015"></a>2.0.0: 9 de março de 2015

[Baixar v2.0.0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.0.0.pkg)

2.0 de Sharpie objetivo é uma versão principal que possui um driver de aprimorado com base em Clang e o analisador e um novo mecanismo de associação com base em NRefactory. Esses componentes aprimoradas fornecem para _muito_ melhor associações, principalmente em torno de associação de tipo. Foram feitas muitas outras melhorias que são internas ao Sharpie objetivo qual produzirá vários recursos visíveis ao usuário em versões futuras.

Objetivo 2.0 de Sharpie é baseado em Clang 3.6.1.

### <a name="type-binding-improvements"></a>Melhorias no tipo de associação

* Agora há suporte para blocos de Objective-C. Isso inclui blocos/embutida anônima e blocos nomeados por meio de `typedef`. Blocos anônimos serão associados como `System.Action` ou `System.Func` delega, enquanto os blocos nomeados serão associados como forte `delegate` tipos.

* Há uma heurística de nomenclatura aprimorada para enums anônimo que precedida imediatamente por um `typedef` Resolvendo como para um tipo integral de builtin `long` ou `int`.

* Ponteiros de C agora são associados como C# `unsafe` ponteiros em vez de `System.IntPtr`. Isso resulta em mais clareza na associação para quando talvez você queira ativar os parâmetros de ponteiro para `out` ou `ref` parâmetros. Não é possível inferir sempre se um parâmetro deve ser `out` ou `ref`, portanto, o ponteiro é mantido na associação para permitir a auditoria mais fácil.

* Uma exceção para a associação de ponteiro acima é quando um ponteiro de classificação 2 para um objeto Objective-C é encontrado como um parâmetro. Nesses casos, a convenção é predominante e o parâmetro será associado como `out` (por exemplo, `NSError **error` → `out NSError error`).

### <a name="verify-attribute"></a>Verifique se o atributo

Você descobrirá com frequência que associações produzidas por objetivo Sharpie agora serão ser anotadas com o `[Verify]` atributo. Esses atributos indicam que você deva _verificar_ que o objetivo Sharpie fez a coisa correta, comparando a ligação com a declaração do C/Objective-C original (que será fornecida em um comentário acima da declaração associada).

A verificação é _recomendado_ para todas as declarações associadas, mas é mais provável _necessária_ para declarações anotados com o `[Verify]` atributo. Isso ocorre porque, em muitas situações, não há metadados suficientes no código nativo de origem original para inferir como melhor produzir uma associação. Você talvez precise fazer referência a documentação ou comentários de código dentro dos arquivos de cabeçalho para tomar a melhor decisão de associação.

Consulte a [verificar atributos](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md) documentação para obter mais detalhes.

### <a name="other-notable-improvements"></a>Outros aperfeiçoamentos notáveis

* `using` instruções agora são geradas com base nos tipos associados. Por exemplo, se um `NSURL` foi associado, um `using Foundation;` instrução será gerada também.

* `struct` e `union` declarações serão vinculadas, usando o `[FieldOffset]` truque para uniões.

* Valores de enumeração com os inicializadores de expressão de constante agora serão corretamente associados; a expressão completa é convertida em C#.

* Blocos e os métodos de Variadic agora estão associados.

* Agora há suporte para estruturas por meio de `-framework` opção. Consulte a documentação sobre [associação estruturas nativas](https://developer.xamarin.com/guides/ios/advanced_topics/binding_objective-c/objective_sharpie/#frameworks) para obter mais detalhes.

* Código-fonte do Objective-C será detectado automaticamente agora, que deve eliminar a necessidade de passar `-ObjC` ou `-xobjective-c` para Clang manualmente.

* Clang o uso do módulo (`@import`) agora é detectado automaticamente, que deve eliminar a necessidade de passar `-fmodules` para Clang manualmente para bibliotecas que usar o novo suporte de módulo em Clang.

* A API unificada do Xamarin agora é o destino da associação padrão; Use o `-classic` opção para a API clássica somente de 32 bits de destino.

### <a name="notable-bug-fixes"></a>Correções de bugs notáveis

* Corrigir `instancetype` associação quando usado em uma categoria de Objective-C
* Nome totalmente categorias de Objective-C
* Protocolos de Objective-C com do prefixo `I` (por exemplo, `INSCopying` em vez de `NSCopying`)

## <a name="1135-december-21-2014"></a>1.1.35: 21 de dezembro de 2014

[Baixar v1.1.35](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-1.1.35.pkg)

Correções de bugs secundários.

## <a name="111-december-15-2014"></a>1.1.1: 15 de dezembro de 2014

[Baixar v 1.1.1](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-1.1.1.pkg)

1.1.1 foi a primeira versão principal após 1,5 anos de uso interno e desenvolvimento em Xamarin, seguindo a visualização inicial do objetivo Sharpie em abril de 2013. Esta versão é o primeiro a ser considerado estável e pode ser usado para uma ampla variedade de bibliotecas nativas, apresentando um novo back-end do Clang.

