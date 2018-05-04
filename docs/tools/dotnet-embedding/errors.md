---
title: Erros de inserção de .NET
ms.prod: xamarin
ms.assetid: 932C3F0C-D968-42D1-BB14-D97C73361983
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 04/11/2018
ms.openlocfilehash: 677242ea12f8fd87d82f337eafd96a1743ad806a
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/03/2018
---
# <a name="net-embedding-errors"></a>Erros de inserção de .NET

## <a name="em0xxx-binding-error-messages"></a>EM0xxx: As mensagens de erro de associação

Por exemplo, parâmetros, ambiente

<!-- 0xxx: the generator itself, e.g. parameters, environment -->

<a name="EM0000" />

### <a name="em0000-unexpected-error---please-fill-a-bug-report-at-httpsgithubcommonoembeddinator-4000issues"></a>EM0000: Erro inesperado - preencha um relatório de erros em https://github.com/mono/Embeddinator-4000/issues

Ocorreu uma condição de erro inesperado. Por favor, [problema](https://github.com/mono/Embeddinator-4000/issues) com tanta informação quanto possível, incluindo:

* Compilação completa logs, com um máximo de detalhamento
* Um caso de teste mínimo que reproduza o erro
* Todas as informações de versão

A maneira mais fácil de obter informações sobre a versão exata é usar o **Xamarin Studio** menu **Xamarin Studio** item, **Mostrar detalhes** botão e a versão de copiar/colar informações (você pode usar o **copiar informações** botão).

<a name="EM0001" />

### <a name="em0001-could-not-create-output-directory-x"></a>EM0001: Não foi possível criar o diretório de saída `X`

O nome do diretório especificado por `-o=DIR` não existe e não pôde ser criado. Pode ser um nome inválido para o sistema de arquivos.

<a name="EM0002" />

### <a name="em0002-option-x-is-not-supported"></a>EM0002: Opção `X` não tem suporte

A ferramenta não dá suporte para a opção `X`. É possível que outra versão da ferramenta oferece suporte a ele ou ela não é aplicada nesse ambiente.

<a name="EM0003" />

### <a name="em0003-the-platform-x-is-not-valid"></a>EM0003: A plataforma `X` não é válido.

A ferramenta não dá suporte para a plataforma `X`. É possível que outra versão da ferramenta oferece suporte a ele ou ela não é aplicada nesse ambiente.

<a name="EM0004" />

### <a name="em0004-the-target-x-is-not-valid"></a>EM0004: O destino `X` não é válido.

A ferramenta não dá suporte para o destino `X`. É possível que outra versão da ferramenta oferece suporte a ele ou ela não é aplicada nesse ambiente.

<a name="EM0005" />

### <a name="em0005-the-compilation-target-x-is-not-valid"></a>EM0005: O destino de compilação `X` não é válido.

A ferramenta não dá suporte para o destino de compilação `X`. É possível que outra versão da ferramenta oferece suporte a ele ou ela não é aplicada nesse ambiente.

<a name="EM0006" />

### <a name="em0006-could-not-find-the-xcode-location"></a>EM0006: Não foi possível encontrar o local do Xcode.

A ferramenta não foi possível encontrar o local Xcode atualmente selecionado usando o `xcode-select -p` comando. Verifique se que esse comando tiver êxito e retorna o local correto do Xcode.

<a name="EM0007" />

### <a name="em0007-could-not-get-the-sdk-version-for-sdk"></a>EM0007: Não foi possível obter a versão do sdk para '{sdk}'.

A ferramenta não foi possível obter a versão do SDK usando o `xcrun --show-sdk-version --sdk {sdk}` comando. Verifique se que esse comando tiver êxito e retorna a versão do SDK.

<a name="EM0008" />

### <a name="em0008-the-architecture-arch-is-not-valid-for-platform-valid-architectures-for-platform-are-architectures"></a>EM0008: A arquitetura '{arch}' não é válida para a plataforma de {}. As arquiteturas válidas para {plataforma} são: '{arquiteturas}'.

A arquitetura na mensagem de erro não é válida para a plataforma de destino. Verifique se a opção – abi é passada a uma arquitetura válida.

<a name="EM0009" />

### <a name="em0009-the-feature-x-is-not-currently-implemented-by-the-generator"></a>EM0009: O recurso `X` atualmente não está implementado pelo gerador de

Este é um problema conhecido que pretendemos corrigir em uma versão futura do gerador. Contribuições são boas-vindas.

<a name="EM0010" />

### <a name="em0010-cant-merge-the-frameworks-simulatorframework-and-deviceframework-because-the-file-file-exists-in-both"></a>EM0010: Não é possível mesclar as estruturas '{simulatorFramework}' e '{deviceFramework}' porque o arquivo ' {}' existe em ambos.

A ferramenta não pôde mesclar as estruturas mencionadas na mensagem de erro, porque não há um arquivo comum entre eles.

Isso pode indicar um erro na inserção de .NET; Envie um relatório de erros em [ https://github.com/mono/Embeddinator-4000/issues ](https://github.com/mono/Embeddinator-4000/issues) com um caso de teste.

<a name="EM0011" />

### <a name="em0011-the-assembly-x-does-not-exist"></a>EM0011: O assembly `X` não existe.

A ferramenta não foi possível encontrar o assembly `X` especificados nos argumentos.

<a name="EM0012" />

### <a name="em0012-the-assembly-name-x-is-not-unique"></a>EM0012: O nome do assembly `X` não é exclusivo

Mais de um assembly fornecido tem o mesmo nome interno e não seria possível para diferenciá-los em tempo de execução.

A causa mais provável é que um assembly é especificado mais de uma vez nos argumentos de linha de comando. No entanto uma mantém ainda renomeado assembly é o nome original e várias cópias não pode coexiste.

<a name="EM0013" />

### <a name="em0013-cant-find-the-assembly-x-referenced-by-y"></a>EM0013: Não é possível localizar o assembly 'X', referenciado por 'Y'.

A ferramenta não foi possível encontrar o assembly 'X', referenciado pelo assembly 'Y'. Verifique se todos os assemblies referenciados estão no mesmo diretório que o assembly a ser associado.

<a name="EM0014" />

### <a name="em0014-could-not-find-product-product-minversion-is-required"></a>EM0014: Não foi possível localizar {produto} ({produto} {min_version} é necessário).

A dependência mencionada na mensagem de erro não pôde ser encontrada no sistema.

<a name="EM0015" />

### <a name="em0015-could-not-find-a-valid-version-of-product-found-version-but-at-least-minversion-is-required"></a>EM0015: Não foi possível localizar uma versão válida do {produto} ({versão} encontrada, mas pelo menos {min_version} é necessário).

A dependência mencionado no erro mensagem foi encontrada no sistema, mas é muito antigo. Atualize para uma versão mais recente.

<a name="EM0016" />

### <a name="em0016-could-not-create-symlink-file---target-error-number"></a>EM0016: Não foi possível criar symlink '{arquivo}' -> '{target}': erro {number}

Não foi possível criar o symlink mencionado na mensagem de erro.

<a name="EM0026" />

### <a name="em0026-could-not-parse-the-command-line-argument-a-"></a>Não EM0026 foi possível analisar o argumento de linha de comando 'A': *

A sintaxe fornecida para a opção de linha de comando `A` não pôde ser analisado pela ferramenta. É provável incorreto, entre em contato com a documentação ou a Ajuda para obter a sintaxe correta.

<a name="EM0099" />

### <a name="em0099-internal-error--please-file-a-bug-report-with-a-test-case-httpsgithubcommonoembeddinator-4000issues"></a>EM0099: Erro interno *. Envie um relatório de erros com um caso de teste (https://github.com/mono/Embeddinator-4000/issues).

Essa mensagem de erro é relatada quando ocorre falha na verificação de consistência interna na inserção do .NET.

Isso indica um erro na inserção de .NET; Envie um relatório de erros em [ https://github.com/mono/Embeddinator-4000/issues ](https://github.com/mono/Embeddinator-4000/issues) com um caso de teste.

<!-- 1xxx: code processing -->

## <a name="em1xxx-code-processing"></a>EM1xxx: Processamento de código

<a name="EM1010" />

### <a name="em1010-type-t-is-not-generated-because-x-are-not-supported"></a>EM1010: Digite `T` não é gerada porque `X` não têm suporte.

Este é um **aviso** que o tipo `T` serão ignoradas (ou seja, nada será gerado) porque ele usa `X`, um recurso que não tem suporte.

Observação: Recursos com suporte evoluirá com novas versões da ferramenta.

<a name="EM1011" />

### <a name="em1011-type-t-is-not-generated-because-it-lacks-marshaling-code-with-a-native-counterpart"></a>EM1011: Digite `T` não é gerada porque ele não tem o código de empacotamento com um representante nativo.

Este é um **aviso** que o tipo `T` serão ignoradas (ou seja, nada será gerado) porque expõe algo do .NET framework que requer muito marshaling.

Observação: Isso é algo que podem obter suporte, com algumas limitações, em uma versão futura da ferramenta.

<a name="EM1020" />

### <a name="em1020-constructor-c-is-not-generated-because-of-parameter-type-t-is-not-supported"></a>EM1020: Construtor `C` não é gerado devido ao tipo de parâmetro `T` não tem suporte.

Este é um **aviso** que o construtor `C` serão ignoradas (ou seja, nada será gerado) porque um parâmetro de tipo `T` não tem suporte.

Deve haver um aviso anterior fornece mais informações por tipo `T` não tem suporte.

Observação: Recursos com suporte evoluirá com novas versões da ferramenta.

<a name="EM1021" />

### <a name="em1021-constructor-c-has-default-values-for-which-no-wrapper-is-generated"></a>EM1021: Construtor `C` tem valores padrão para o qual nenhuma wrapper é gerado.

Este é um **aviso** que os parâmetros padrão do construtor `C` não está gerando qualquer código extra. A causa mais comum é que um método existente já tem a mesma assinatura. Por exemplo, no .net, é possível ter:

```
public class MyType {
    public MyType () { ... }
    public MyType (int i = 0) { ... }
}
```

Em tais casos apenas dois gerados `init` seletores serão criados, ambos os chamar Mono, mas não continuará a existir nenhum wrapper para o mais tarde.

<a name="EM1030" />

### <a name="em1030-method-m-is-not-generated-because-return-type-t-is-not-supported"></a>EM1030: Método `M` não é gerado porque o tipo de retorno `T` não tem suporte.

Este é um **aviso** que o método `M` serão ignoradas (ou seja, nada será gerado) porque ele é o tipo de retorno `T` não tem suporte.

Deve haver um aviso anterior fornece mais informações por tipo `T` não tem suporte.

Observação: Recursos com suporte evoluirá com novas versões da ferramenta.

<a name="EM1031" />

### <a name="em1031-method-m-is-not-generated-because-of-parameter-type-t-is-not-supported"></a>EM1031: Método `M` não é gerado devido ao tipo de parâmetro `T` não tem suporte.

Este é um **aviso** que o método `M` serão ignoradas (ou seja, nada será gerado) porque um parâmetro de tipo `T` não tem suporte.

Deve haver um aviso anterior fornece mais informações por tipo `T` não tem suporte.

Observação: Recursos com suporte evoluirá com novas versões da ferramenta.

<a name="EM1032" />

### <a name="em1032-method-m-has-default-values-for-which-no-wrapper-is-generated"></a>EM1032: Método `M` tem valores padrão para o qual nenhuma wrapper é gerado.

Este é um **aviso** que os parâmetros padrão do método `M` não está gerando qualquer código extra. A causa mais comum é que um método existente já tem a mesma assinatura. Por exemplo, no .net, é possível ter:

```
public class MyType {
    public int Increment () { ... }
    public int Increment (int i = 0) { ... }
}
```

Em tais casos apenas dois gerados `increment` seletores serão criados, ambos os chamar Mono, mas não continuará a existir nenhum wrapper para o mais tarde.

<a name="EM1033" />

### <a name="em1033-method-m-is-not-generated-because-another-method-exposes-the-operator-with-a-friendly-name"></a>EM1033: Método `M` não é gerada porque outro método expõe o operador com um nome amigável.

Este é um **aviso** que o método `M` não é gerada porque outro método expõe o operador com um nome amigável. (https://msdn.microsoft.com/library/ms229032(v=vs.110).aspx)

<a name="EM1034" />

### <a name="em1034-extension-method-m-is-not-generated-inside-a-category-because-they-cannot-be-created-on-primitive-type-t-a-normal-static-method-was-generated"></a>EM1034: Método de extensão `M` não é gerado dentro de uma categoria, porque eles não podem ser criados em tipo primitivo `T`. Um método estático normal foi gerado.

Este é um **aviso** que tipo de um método de extensão em um primivite (por exemplo, `System.Int32`) foi encontrado. Em Objective-C não é possível criar categorias em tipo primitivo. Em vez disso, o gerador de produzirá um método estático normal.

<a name="EM1040" />

### <a name="em1040-property-p-is-not-generated-because-of-parameter-type-t-is-not-supported"></a>EM1040: Propriedade `P` não é gerado devido ao tipo de parâmetro `T` não tem suporte.

Este é um **aviso** que a propriedade `P` serão ignoradas (ou seja, nada será gerado) porque o tipo exposto `T` não tem suporte.

Deve haver um aviso anterior fornece mais informações por tipo `T` não tem suporte.

Observação: Recursos com suporte evoluirá com novas versões da ferramenta.

<a name="EM1041" />

### <a name="em1041-indexed-properties-on-t-is-not-generated-because-multiple-indexed-properties-are-not-supported"></a>EM1041: Propriedades indexadas em `T` não é gerada porque não há suporte para várias propriedades indexadas.

Este é um **aviso** que as propriedades indexadas `T` serão ignoradas (ou seja, nada será gerado) porque não há suporte para várias propriedades indexadas.

<a name="EM1050" />

### <a name="em1050-field-f-is-not-generated-because-of-field-type-t-is-not-supported"></a>EM1050: Campo `F` não é gerado por causa do tipo de campo `T` não tem suporte.

Este é um **aviso** que o campo `F` serão ignoradas (ou seja, nada será gerado) porque o tipo exposto `T` não tem suporte.

Deve haver um aviso anterior fornece mais informações por tipo `T` não tem suporte.

Observação: Recursos com suporte evoluirá com novas versões da ferramenta.

<a name="EM1051" />

### <a name="em1051-element-e-is-generated-instead-as-f-because-its-name-conflicts-with-an-important-objective-c-selector"></a>EM1051: Elemento `E` é gerado em vez disso, como `F` porque seu nome está em conflito com um seletor de objetivo c importante.

Este é um **aviso** que o elemento `E` será gerado como `F` porque seu nome está em conflito com um seletor de objetivo c importante.

Seletores no [NSObjectProtocol](https://developer.apple.com/reference/objectivec/1418956-nsobject?language=objc) têm significado importante em objective-c e deve ser substituído com cuidado.

Observação: A lista de seletores reservados evoluirá com novas versões da ferramenta.

<a name="EM1052" />

### <a name="em1052-element-e-is-not-generated-its-name-conflicts-with-other-elements-on-the-same-class"></a>EM1052: Elemento `E` não é gerada seu nome está em conflito com outros elementos na mesma classe.

Este é um **aviso** elemento `E` não é gerada como seu nome está em conflito com outros elementos na mesma classe.

<a name="EM1053" />

### <a name="em1053-target-e-is-not-supported-for-xamarinios-and-xamarinmac-only-the-framework-option-is-considered-supported-and-should-be-used"></a>EM1053: Destino `E` não tem suporte para xamarin e Xamarin.Mac. Somente o `framework` opção é considerada com suporte e deve ser usado.

Este é um **aviso** destino `E` é considerado sem suporte de xamarin e Xamarin.Mac casos de uso. 

Consumo de bibliotecas .NET inserindo estáticos ou dinâmicos pode exigir etapas adicionais de trabalho ou ajustes e deve ser evitado na maioria dos casos de uso.

Considere remover o `--target` parâmetro ou passe `--target=framework` em vez disso.

<!-- 2xxx: code generation -->

## <a name="em2xxx-code-generation"></a>EM2xxx: Geração de código

<!-- 3xxx: reserved -->
<!-- 4xxx: reserved -->
<!-- 5xxx: reserved -->
<!-- 6xxx: reserved -->
<!-- 7xxx: reserved -->
<!-- 8xxx: reserved -->
<!-- 9xxx: reserved -->
