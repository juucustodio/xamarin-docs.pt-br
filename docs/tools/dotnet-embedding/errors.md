---
title: Erros de inserção do .NET
description: Este documento descreve os erros gerados pela inserção do .NET. Os erros são listados por código e recebem uma descrição para ajudar na solução de problemas.
ms.prod: xamarin
ms.assetid: 932C3F0C-D968-42D1-BB14-D97C73361983
author: davidortinau
ms.author: daortin
ms.date: 04/11/2018
ms.openlocfilehash: b7cdf56ac4d917c8692f33d388adc003fabd9cac
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73007238"
---
# <a name="net-embedding-errors"></a>Erros de inserção do .NET

## <a name="em0xxx-binding-error-messages"></a>EM0xxx: mensagens de erro de associação

Por exemplo, parâmetros, ambiente

<!-- 0xxx: the generator itself, e.g. parameters, environment -->

<a name="EM0000" />

### <a name="em0000-unexpected-error---please-fill-a-bug-report-at-httpsgithubcommonoembeddinator-4000issues"></a>EM0000: erro inesperado-preencha um relatório de bug em https://github.com/mono/Embeddinator-4000/issues

Ocorreu uma condição de erro inesperada. Registre [um problema](https://github.com/mono/Embeddinator-4000/issues) com o máximo de informações possível, incluindo:

* Logs de compilação completos, com detalhes máximos
* Um caso de teste mínimo que reproduza o erro
* Todas as informações de versão

A maneira mais fácil de obter informações exatas sobre a versão é usar o menu **Xamarin Studio** , **sobre Xamarin Studio** item, mostrar o botão **detalhes** e copiar/colar a versão informações (você pode usar o botão **copiar informações** ).

<a name="EM0001" />

### <a name="em0001-could-not-create-output-directory-x"></a>EM0001: não foi possível criar o diretório de saída `X`

O nome de diretório especificado pelo `-o=DIR` não existe e não pôde ser criado. Pode ser um nome inválido para o sistema de arquivos.

<a name="EM0002" />

### <a name="em0002-option-x-is-not-supported"></a>EM0002: não há suporte para a opção `X`

A ferramenta não oferece suporte à opção `X`. É possível que outra versão da ferramenta dê suporte a ela ou que não se aplique a esse ambiente.

<a name="EM0003" />

### <a name="em0003-the-platform-x-is-not-valid"></a>EM0003: a plataforma `X` não é válida.

A ferramenta não oferece suporte à plataforma `X`. É possível que outra versão da ferramenta dê suporte a ela ou que não se aplique a esse ambiente.

<a name="EM0004" />

### <a name="em0004-the-target-x-is-not-valid"></a>EM0004: o `X` de destino não é válido.

A ferramenta não oferece suporte à `X`de destino. É possível que outra versão da ferramenta dê suporte a ela ou que não se aplique a esse ambiente.

<a name="EM0005" />

### <a name="em0005-the-compilation-target-x-is-not-valid"></a>EM0005: a `X` de destino de compilação não é válida.

A ferramenta não dá suporte à `X`de destino de compilação. É possível que outra versão da ferramenta dê suporte a ela ou que não se aplique a esse ambiente.

<a name="EM0006" />

### <a name="em0006-could-not-find-the-xcode-location"></a>EM0006: não foi possível encontrar o local do Xcode.

A ferramenta não pôde localizar o local do Xcode selecionado no momento usando o comando `xcode-select -p`. Verifique se esse comando é bem sucedido e retorna o local correto do Xcode.

<a name="EM0007" />

### <a name="em0007-could-not-get-the-sdk-version-for-sdk"></a>EM0007: não foi possível obter a versão do SDK para ' {SDK} '.

A ferramenta não pôde obter a versão do SDK usando o comando `xcrun --show-sdk-version --sdk {sdk}`. Verifique se esse comando é bem sucedido e retorna a versão do SDK.

<a name="EM0008" />

### <a name="em0008-the-architecture-arch-is-not-valid-for-platform-valid-architectures-for-platform-are-architectures"></a>EM0008: a arquitetura ' {Arch} ' não é válida para {Platform}. As arquiteturas válidas para {Platform} são: ' {Architectures} '.

A arquitetura na mensagem de erro não é válida para a plataforma de destino. Verifique se a opção--Abi é passada para uma arquitetura válida.

<a name="EM0009" />

### <a name="em0009-the-feature-x-is-not-currently-implemented-by-the-generator"></a>EM0009: o recurso `X` não está implementado atualmente pelo gerador

Esse é um problema conhecido que pretendemos corrigir em uma versão futura do gerador. As contribuições são boas-vindas.

<a name="EM0010" />

### <a name="em0010-cant-merge-the-frameworks-simulatorframework-and-deviceframework-because-the-file-file-exists-in-both"></a>EM0010: não é possível mesclar as estruturas ' {simulatorFramework} ' e ' {deviceFramework} ' porque o arquivo ' {file} ' existe em ambos.

A ferramenta não pôde mesclar as estruturas mencionadas na mensagem de erro, pois há um arquivo comum entre elas.

Isso pode indicar um bug na inserção do .NET; Registre um relatório de bugs em [https://github.com/mono/Embeddinator-4000/issues](https://github.com/mono/Embeddinator-4000/issues) com um caso de teste.

<a name="EM0011" />

### <a name="em0011-the-assembly-x-does-not-exist"></a>EM0011: o assembly `X` não existe.

A ferramenta não pôde localizar o assembly `X` especificado nos argumentos.

<a name="EM0012" />

### <a name="em0012-the-assembly-name-x-is-not-unique"></a>EM0012: o nome do assembly `X` não é exclusivo

Mais de um assembly fornecido têm o mesmo nome interno e não seria possível fazer distinção entre eles em tempo de execução.

A causa mais provável é que um assembly seja especificado mais de uma vez nos argumentos da linha de comando. No entanto, um assembly renomeado ainda mantém seu nome original e várias cópias não podem coexistir.

<a name="EM0013" />

### <a name="em0013-cant-find-the-assembly-x-referenced-by-y"></a>EM0013: não é possível localizar o assembly ' X ', referenciado por ' Y '.

A ferramenta não pôde localizar o assembly ' X ', referenciado pelo assembly ' Y '. Verifique se todos os assemblies referenciados estão no mesmo diretório que o assembly a ser associado.

<a name="EM0014" />

### <a name="em0014-could-not-find-product-product-min_version-is-required"></a>EM0014: não foi possível localizar {Product} ({Product} {min_version} é necessário).

A dependência mencionada na mensagem de erro não foi encontrada no sistema.

<a name="EM0015" />

### <a name="em0015-could-not-find-a-valid-version-of-product-found-version-but-at-least-min_version-is-required"></a>EM0015: não foi possível encontrar uma versão válida de {Product} (encontrada {Version}, mas pelo menos {min_version} é necessário).

A dependência mencionada na mensagem de erro foi encontrada no sistema, mas é muito antiga. Atualize para uma versão mais recente.

<a name="EM0016" />

### <a name="em0016-could-not-create-symlink-file---target-error-number"></a>EM0016: não foi possível criar symlink ' {file} '-> ' {target} ': erro {Number}

Não foi possível criar o symlink mencionado na mensagem de erro.

<a name="EM0026" />

### <a name="em0026-could-not-parse-the-command-line-argument-a-"></a>EM0026 não pôde analisar o argumento de linha de comando ' A ': *

A sintaxe fornecida para a opção de linha de comando `A` não pôde ser analisada pela ferramenta. Provavelmente está incorreto, consulte a documentação ou a ajuda para obter a sintaxe correta.

<a name="EM0099" />

### <a name="em0099-internal-error--please-file-a-bug-report-with-a-test-case-httpsgithubcommonoembeddinator-4000issues"></a>EM0099: erro interno *. Registre um relatório de bug com um caso de teste (https://github.com/mono/Embeddinator-4000/issues).

Essa mensagem de erro é relatada quando há falha em uma verificação de consistência interna na inserção do .NET.

Isso indica um bug na inserção do .NET; Registre um relatório de bugs em [https://github.com/mono/Embeddinator-4000/issues](https://github.com/mono/Embeddinator-4000/issues) com um caso de teste.

<!-- 1xxx: code processing -->

## <a name="em1xxx-code-processing"></a>EM1xxx: processamento de código

<a name="EM1010" />

### <a name="em1010-type-t-is-not-generated-because-x-are-not-supported"></a>EM1010: o tipo `T` não é gerado porque não há suporte para `X`.

Este é um **aviso** de que o tipo `T` será ignorado (ou seja, nada será gerado) porque ele usa `X`, um recurso que não tem suporte.

Observação: os recursos com suporte irão evoluir com as novas versões da ferramenta.

<a name="EM1011" />

### <a name="em1011-type-t-is-not-generated-because-it-lacks-marshaling-code-with-a-native-counterpart"></a>EM1011: o tipo `T` não é gerado porque falta o empacotamento de código com uma contraparte nativa.

Este é um **aviso** de que o tipo `T` será ignorado (ou seja, nada será gerado) porque ele expõe algo do .NET Framework que requer marshaling extra.

Observação: isso é algo que pode ter suporte, com algumas limitações, em uma versão futura da ferramenta.

<a name="EM1020" />

### <a name="em1020-constructor-c-is-not-generated-because-of-parameter-type-t-is-not-supported"></a>EM1020: o Construtor `C` não é gerado porque não há suporte para o tipo de parâmetro `T`.

Este é um **aviso** de que o Construtor `C` será ignorado (ou seja, nada será gerado) porque não há suporte para um parâmetro do tipo `T`.

Deve haver um aviso anterior, fornecendo mais informações por que não há suporte para o tipo `T`.

Observação: os recursos com suporte irão evoluir com as novas versões da ferramenta.

<a name="EM1021" />

### <a name="em1021-constructor-c-has-default-values-for-which-no-wrapper-is-generated"></a>EM1021: o Construtor `C` tem valores padrão para os quais nenhum wrapper é gerado.

Este é um **aviso** de que os parâmetros padrão do Construtor `C` não estão gerando nenhum código extra. A causa mais comum é que um método existente já tem a mesma assinatura. Por exemplo, no .net, é possível ter:

```csharp
public class MyType {
    public MyType () { ... }
    public MyType (int i = 0) { ... }
}
```

Nesses casos, apenas dois seletores de `init` gerados serão criados, ambos chamando em mono, mas nenhum wrapper para o mais tarde existirá.

<a name="EM1030" />

### <a name="em1030-method-m-is-not-generated-because-return-type-t-is-not-supported"></a>EM1030: o método `M` não é gerado porque não há suporte para o tipo de retorno `T`.

Este é um **aviso** de que o método `M` será ignorado (ou seja, nada será gerado) porque o tipo de retorno `T` não tem suporte.

Deve haver um aviso anterior, fornecendo mais informações por que não há suporte para o tipo `T`.

Observação: os recursos com suporte irão evoluir com as novas versões da ferramenta.

<a name="EM1031" />

### <a name="em1031-method-m-is-not-generated-because-of-parameter-type-t-is-not-supported"></a>EM1031: o método `M` não é gerado porque não há suporte para o tipo de parâmetro `T`.

Este é um **aviso** de que o método `M` será ignorado (ou seja, nada será gerado) porque não há suporte para um parâmetro do tipo `T`.

Deve haver um aviso anterior, fornecendo mais informações por que não há suporte para o tipo `T`.

Observação: os recursos com suporte irão evoluir com as novas versões da ferramenta.

<a name="EM1032" />

### <a name="em1032-method-m-has-default-values-for-which-no-wrapper-is-generated"></a>EM1032: o método `M` tem valores padrão para os quais nenhum wrapper é gerado.

Este é um **aviso** de que os parâmetros padrão do método `M` não estão gerando nenhum código extra. A causa mais comum é que um método existente já tem a mesma assinatura. Por exemplo, no .net, é possível ter:

```csharp
public class MyType {
    public int Increment () { ... }
    public int Increment (int i = 0) { ... }
}
```

Nesses casos, apenas dois seletores de `increment` gerados serão criados, ambos chamando em mono, mas nenhum wrapper para o mais tarde existirá.

<a name="EM1033" />

### <a name="em1033-method-m-is-not-generated-because-another-method-exposes-the-operator-with-a-friendly-name"></a>EM1033: o método `M` não é gerado porque outro método expõe o operador com um nome amigável.

Este é um **aviso** de que o método `M` não é gerado porque outro método expõe o operador com um nome amigável. (https://msdn.microsoft.com/library/ms229032(v=vs.110).aspx)

<a name="EM1034" />

### <a name="em1034-extension-method-m-is-not-generated-inside-a-category-because-they-cannot-be-created-on-primitive-type-t-a-normal-static-method-was-generated"></a>EM1034: o método de extensão `M` não é gerado dentro de uma categoria porque eles não podem ser criados no tipo primitivo `T`. Um método normal, estático, foi gerado.

Este é um **aviso** de que um método de extensão em um tipo primivite (por exemplo, `System.Int32`) foi encontrado. Em Objective-C, não é possível criar categorias no tipo primitivo. Em vez disso, o gerador produzirá um método normal e estático.

<a name="EM1040" />

### <a name="em1040-property-p-is-not-generated-because-of-parameter-type-t-is-not-supported"></a>EM1040: a propriedade `P` não é gerada porque não há suporte para o tipo de parâmetro `T`.

Este é um **aviso** de que a propriedade `P` será ignorada (ou seja, nada será gerado) porque não há suporte para o tipo exposto `T`.

Deve haver um aviso anterior, fornecendo mais informações por que não há suporte para o tipo `T`.

Observação: os recursos com suporte irão evoluir com as novas versões da ferramenta.

<a name="EM1041" />

### <a name="em1041-indexed-properties-on-t-is-not-generated-because-multiple-indexed-properties-are-not-supported"></a>EM1041: as propriedades indexadas em `T` não são geradas porque não há suporte para várias propriedades indexadas.

Este é um **aviso** de que as propriedades indexadas em `T` serão ignoradas (ou seja, nada será gerado) porque não há suporte para várias propriedades indexadas.

<a name="EM1050" />

### <a name="em1050-field-f-is-not-generated-because-of-field-type-t-is-not-supported"></a>EM1050: o campo `F` não é gerado porque não há suporte para o tipo de campo `T`.

Este é um **aviso** de que o campo `F` será ignorado (ou seja, nada será gerado) porque não há suporte para o tipo exposto `T`.

Deve haver um aviso anterior, fornecendo mais informações por que não há suporte para o tipo `T`.

Observação: os recursos com suporte irão evoluir com as novas versões da ferramenta.

<a name="EM1051" />

### <a name="em1051-element-e-is-generated-instead-as-f-because-its-name-conflicts-with-an-important-objective-c-selector"></a>EM1051: o elemento `E` é gerado como `F` porque seu nome está em conflito com um seletor Objective-c importante.

Este é um **aviso** de que o elemento `E` será gerado como `F` porque seu nome entra em conflito com um seletor Objective-c importante.

Os seletores no [NSObjectProtocol](https://developer.apple.com/reference/objectivec/1418956-nsobject?language=objc) têm significado importante em Objective-c e devem ser substituídos com cuidado.

Observação: a lista de seletores reservados se desenvolverá com novas versões da ferramenta.

<a name="EM1052" />

### <a name="em1052-element-e-is-not-generated-its-name-conflicts-with-other-elements-on-the-same-class"></a>EM1052: o elemento `E` não é gerado quando seu nome está em conflito com outros elementos na mesma classe.

Esse é um **aviso** de que o elemento `E` não é gerado, pois seu nome entra em conflito com outros elementos na mesma classe.

<a name="EM1053" />

### <a name="em1053-target-e-is-not-supported-for-xamarinios-and-xamarinmac-only-the-framework-option-is-considered-supported-and-should-be-used"></a>EM1053: não há suporte para o `E` de destino para Xamarin. iOS e Xamarin. Mac. Somente a opção `framework` é considerada com suporte e deve ser usada.

Este é um **aviso** de que a `E` de destino é considerada sem suporte para casos de uso do Xamarin. Ios e Xamarin. Mac. 

O consumo de bibliotecas de incorporação .NET estáticas ou dinâmicas pode exigir etapas ou ajustes de trabalho adicionais e deve ser evitado na maioria dos casos de uso.

Considere remover o parâmetro `--target` ou passar `--target=framework` em vez disso.

<!-- 2xxx: code generation -->

## <a name="em2xxx-code-generation"></a>EM2xxx: geração de código

<!-- 3xxx: reserved -->
<!-- 4xxx: reserved -->
<!-- 5xxx: reserved -->
<!-- 6xxx: reserved -->
<!-- 7xxx: reserved -->
<!-- 8xxx: reserved -->
<!-- 9xxx: reserved -->
