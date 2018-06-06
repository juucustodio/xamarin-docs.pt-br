---
title: Histórico de lançamento do objetivo Sharpie
description: Este documento descreve o histórico de versão de Sharpie objetivo, a ferramenta usada para automatizar a criação de associações do c# para código Objective-C.
ms.prod: xamarin
ms.assetid: 1F4A1BE1-7205-43F4-89D0-6C8672F52598
author: asb3993
ms.author: amburns
ms.date: 10/11/2017
ms.openlocfilehash: a173df769068a3834dfdc314675254af1f3594ed
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34781725"
---
# <a name="objective-sharpie-release-history"></a>Histórico de lançamento do objetivo Sharpie

## <a name="34-october-11-2017"></a>3.4 (11 de outubro de 2017)

[Baixar v3.4.0](https://dl.xamarin.com/objective-sharpie/ObjectiveSharpie-3.4.0.pkg)

* Suporte para o Xcode 9: iOS 11, macOS 10.13, tvOS 11 e watchOS 4
* Problemas com SIMD e tgmath agora devem ser corrigidos
* Telemetria foi removida completamente

## <a name="33-august-3-2016"></a>3.3 (3 de agosto de 2016)

[Baixar v3.3.0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.3.0.pkg)

* Suporte para Xcode 8 Beta 4, iOS 10, macOS 10.12, tvOS 10 e watchOS 3.
* Atualizado para Clang mais recente compilação mestre (2016-08-02)
* [Manter as opções de envio de telemetria](https://twitter.com/Symbiatch/status/760373403878559744) de `sharpie pod bind` para `sharpie bind`.

## <a name="32-june-14-2016"></a>3.2 (14 de junho de 2016)

[Baixar v 3.2.3](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.2.3.pkg)

* Suporte para Xcode 8 Beta 1, 10 do iOS, macOS 10.12, tvOS 10 e watchOS 3.

## <a name="31-may-31-2016"></a>3.1 (31 de maio de 2016)

[Baixar v 3.1.1](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.1.1.pkg)

* Suporte para CocoaPods 1.0
* Melhor confiabilidade de associação CocoaPods criando primeiro um nativo `.framework` e, em seguida, a associação que
* Copiar CocoaPods `.framework` e associação de definição em um `Binding` diretório para facilitar a integração com o xamarin e Xamarin.Mac projetos de associação

## <a name="30-october-5-2015"></a>3.0 (5 de outubro de 2015)

[Baixar v3.0.8](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.0.8.pkg)

* Suporte para novos recursos de idioma Objective-C incluindo genéricos leves e a nulidade, conforme apresentada no Xcode 7
* Suporte para o iOS e Mac SDKs mais recentes.
* Projeto Xcode criação e análise de suporte! Agora você pode passar um projeto completo do Xcode para Sharpie objetivo e fará o melhor para descobrir o certo (por exemplo, `sharpie bind Project.xcodeproj -sdk ios`).
* [CocoaPods](https://cocoapods.org) suporte! Agora configurar, compilar e vincular CocoaPods diretamente do objetivo Sharpie (por exemplo, `sharpie pod init ios AFNetworking && sharpie pod bind`).
* Ao associar estruturas, módulos Clang serão habilitados se o framework oferece suporte a eles, resultando na análise correta de mais de uma estrutura, desde que a estrutura da estrutura é definida por seu `module.modulemap`.
* Para projetos de Xcode que cria um produto do framework, analise esse produto em vez de destinos de produto intermediário como destinos de framework não em um projeto no Xcode ainda poderão ter ambiguidades que não podem ser resolvidas automaticamente.

## <a name="216-march-17-2015"></a>2.1.6 (17 de março de 2015)

[Baixar v2.1.6](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.1.6.pkg)

* Associação de expressão de operador binário fixo: o lado esquerdo da expressão incorretamente foi trocado à direita (por exemplo, `1 << 0` incorretamente foram associados como `0 << 1`). Graças ao Adam Kemp para observar isso!
* Corrigido um problema com `NSInteger` e `NSUInteger` que está sendo associada como `int` e `uint` em vez de `nint` e `nuint` em i386; `-DNS_BUILD_32_LIKE_64` agora é passado para Clang para fazer a análise `objc/NSObjCRuntime.h` funcionem como esperado em i386.
* A arquitetura de padrão de SDKs do Mac OS X (por exemplo, `-sdk macosx10.10`) é agora x86_64 em vez de i386, portanto `-arch` pode ser omitido, a menos que substituindo o padrão é desejado.

## <a name="210-march-15-2015"></a>2.1.0 (15 de março de 2015)

[Baixar v 2.1.0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.1.0.pkg)

* [bxc #27849](https://bugzilla.xamarin.com/show_bug.cgi?id=27849): Certifique-se de `using ObjCRuntime;` é produzido quando `ArgumentSemantic` é usado.
* [bxc #27850](https://bugzilla.xamarin.com/show_bug.cgi?id=27850): Certifique-se de `using System.Runtime.InteropServices;` é produzido quando `DllImport` é usado.
* [bxc #27852](https://bugzilla.xamarin.com/show_bug.cgi?id=27852): padrão `DllImport` para carregar símbolos de `__Internal`.
* [bxc 27848 #](https://bugzilla.xamarin.com/show_bug.cgi?id=27848): ignorar declarações de contêiner Objective-C declarado de avanço.
* [bxc #27846](https://bugzilla.xamarin.com/show_bug.cgi?id=27846): associar tipos de protocolo com um único qualificação como interfaces concretas (`id<Foo>` como `Foo` em vez de `Foundation.NSObject<Foo>`).
* [bxc #28037](https://bugzilla.xamarin.com/show_bug.cgi?id=28037): associar `UInt32`, `UInt64`, e `Int64` literais como `Int32` para descartar o `u` e/ou `uL` sufixos quando os valores com segurança se adaptam ao `Int32`.
* [bxc #28038](https://bugzilla.xamarin.com/show_bug.cgi?id=28038): corrija o mapeamento de nome de enum quando o nome original do native começa com um `k` prefixo.
* `sizeof` Expressões de C cujo tipo de argumento não é mapeado para um tipo primitivo c# serão avaliadas no Clang e vinculadas como um literal inteiro para evitar a geração inválido c#.
* Corrija a sintaxe de Objective-C para propriedades cujo tipo é um bloco (código Objective-C aparece nos comentários acima declarações associadas).
* Associar tipos diminuísse como seu tipo original (`int[]` se deteriorar para `int*` durante a análise semântica em Clang, mas associá-lo do original como gravada `int[]` em vez disso).

Muito obrigado a Dave Dunkin para muitos os bugs corrigidos nesta versão do ponto de relatório!

## <a name="200-march-9-2015"></a>2.0.0: 9 de março de 2015

[Baixar v 2.0.0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.0.0.pkg)

Objetivo 2.0 de Sharpie é uma versão principal que possui um driver de aprimorado com base em Clang e analisador e um novo mecanismo de associação com base em NRefactory. Esses componentes aprimorados fornecem para _muito_ melhor associações, principalmente em torno de associação de tipo. Foram feitas muitas outras melhorias que são internas ao Sharpie objetivo que produzirá muitos recursos visíveis ao usuário em versões futuras.

Objetivo 2.0 de Sharpie baseia-se em Clang 3.6.1.

### <a name="type-binding-improvements"></a>Aprimoramentos de associação de tipo

* Agora há suporte para blocos Objective-C. Isso inclui blocos anônimo/embutido e blocos nomeados `typedef`. Blocos anônimos serão associados como `System.Action` ou `System.Func` delega, enquanto blocos nomeados serão associados como nomeada `delegate` tipos.

* Há uma heurística de nomenclatura aprimorada para enums anônimo que são imediatamente precedido por um `typedef` resolver para um tipo integral builtin como `long` ou `int`.

* Ponteiros de C agora são associados como c# `unsafe` ponteiros em vez de `System.IntPtr`. Isso resulta em mais clareza na associação para quando talvez você queira ativar os parâmetros de ponteiro em `out` ou `ref` parâmetros. Não é possível inferir sempre se um parâmetro deve ser `out` ou `ref`, portanto, o ponteiro é mantido na associação para permitir que mais fácil a auditoria.

* Uma exceção à associação de ponteiro acima é quando um ponteiro de classificação 2 para um objeto Objective-C é encontrado como um parâmetro. Nesses casos, a convenção é predominante e o parâmetro será associado como `out` (por exemplo, `NSError **error` → `out NSError error`).

### <a name="verify-attribute"></a>Verifique se o atributo

Você descobrirá com frequência que associações produzidas por objetivo Sharpie agora serão ser anotadas com a `[Verify]` atributo. Esses atributos indicam que você deve _verificar_ que Sharpie objetivo foi a ação correta, comparando a associação com a declaração original do C/Objective-C (que será fornecida em um comentário acima da declaração associada).

A verificação é _recomendado_ para todas as declarações associadas, mas é mais provável _necessária_ para declarações anotados com o `[Verify]` atributo. Isso ocorre porque, em muitas situações, não há metadados suficientes no código-fonte nativo original para inferir como melhor produzir uma associação. Talvez seja necessário fazer referência a documentação ou comentários de código em arquivos de cabeçalho para tomar a melhor decisão de associação.

Consulte o [verificar atributos](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md) documentação para obter mais detalhes.

### <a name="other-notable-improvements"></a>Outros aprimoramentos importantes

* `using` instruções agora são geradas com base nos tipos associados. Por exemplo, se um `NSURL` foi associado, um `using Foundation;` instrução será gerada também.

* `struct` e `union` declarações serão vinculadas, usando o `[FieldOffset]` truque para uniões.

* Valores de enumeração com inicializadores de expressão de constante serão corretamente vinculados; a expressão completa é convertida em c#.

* Blocos e métodos Variadic agora estão associados.

* Agora há suporte para estruturas por meio de `-framework` opção. Consulte a documentação sobre [estruturas nativas associação](http://developer.xamarin.com/guides/ios/advanced_topics/binding_objective-c/objective_sharpie/#frameworks) para obter mais detalhes.

* Código-fonte Objective-C será detectado automaticamente agora, que deve eliminar a necessidade de passar `-ObjC` ou `-xobjective-c` para Clang manualmente.

* Clang uso do módulo (`@import`) agora é detectado automaticamente, que deve eliminar a necessidade de passar `-fmodules` para Clang manualmente para bibliotecas do que usar o novo suporte de módulo no Clang.

* A API do Xamarin Unified agora é o destino de associação padrão; Use o `-classic` opção para a API clássica somente de 32 bits de destino.

### <a name="notable-bug-fixes"></a>Correções de bugs notáveis

* Corrigir `instancetype` associação quando usada em uma categoria Objective-C
* Nome totalmente categorias Objective-C
* Prefixo protocolos Objective-C com `I` (por exemplo, `INSCopying` em vez de `NSCopying`)

## <a name="1135-december-21-2014"></a>1.1.35: 21 de dezembro de 2014

[Baixar v1.1.35](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-1.1.35.pkg)

Correções de bugs secundários.

## <a name="111-december-15-2014"></a>1.1.1: 15 de dezembro de 2014

[Baixar 1.1.1](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-1.1.1.pkg)

1.1.1 foi a primeira versão principal após 1,5 anos de uso interno e desenvolvimento no Xamarin após a visualização inicial do objetivo Sharpie em abril de 2013. Esta versão é a primeira geralmente considerado estável e pode ser usado para uma ampla variedade de bibliotecas nativas, apresentando um novo back-end Clang.

