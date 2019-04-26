---
title: Erros de incorporação do .NET
description: Este documento descreve os erros gerados pela incorporação do .NET. Erros são listados por código e receber uma descrição para ajudar a solucionar problemas.
ms.prod: xamarin
ms.assetid: 932C3F0C-D968-42D1-BB14-D97C73361983
author: lobrien
ms.author: laobri
ms.date: 04/11/2018
ms.openlocfilehash: 5c3dd406f1132f51a86ddf574ab7ad0b279bc9ec
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61215335"
---
# <a name="net-embedding-errors"></a>Erros de incorporação do .NET

## <a name="em0xxx-binding-error-messages"></a>EM0xxx: Mensagens de erro de associação

Por exemplo, parameters, environment

<!-- 0xxx: the generator itself, e.g. parameters, environment -->

<a name="EM0000" />

### <a name="em0000-unexpected-error---please-fill-a-bug-report-at-httpsgithubcommonoembeddinator-4000issues"></a>EM0000: Erro inesperado - preencha um relatório de bugs no https://github.com/mono/Embeddinator-4000/issues

Ocorreu uma condição de erro inesperado. Por favor [registre um problema](https://github.com/mono/Embeddinator-4000/issues) com tantas informações quanto possível, incluindo:

* Build completo logs, com detalhamento máximo
* Um caso de teste mínimo que reproduza o erro
* Todas as informações de versão

A maneira mais fácil para obter informações de versão exata é usar o **Xamarin Studio** menu **sobre o Xamarin Studio** item, **Mostrar detalhes** botão e a versão de copiar/colar informações (você pode usar o **copiar informações** botão).

<a name="EM0001" />

### <a name="em0001-could-not-create-output-directory-x"></a>EM0001: Não foi possível criar o diretório de saída `X`

O nome do diretório especificado por `-o=DIR` não existe e não pôde ser criado. Pode ser um nome inválido para o sistema de arquivos.

<a name="EM0002" />

### <a name="em0002-option-x-is-not-supported"></a>EM0002: Opção `X` não tem suporte

A ferramenta não dá suporte a opção `X`. É possível que outra versão da ferramenta dá suporte a ele ou ela não é aplicada nesse ambiente.

<a name="EM0003" />

### <a name="em0003-the-platform-x-is-not-valid"></a>EM0003: A plataforma `X` não é válido.

A ferramenta não dá suporte para a plataforma `X`. É possível que outra versão da ferramenta dá suporte a ele ou ela não é aplicada nesse ambiente.

<a name="EM0004" />

### <a name="em0004-the-target-x-is-not-valid"></a>EM0004: O destino `X` não é válido.

A ferramenta não dá suporte para o destino `X`. É possível que outra versão da ferramenta dá suporte a ele ou ela não é aplicada nesse ambiente.

<a name="EM0005" />

### <a name="em0005-the-compilation-target-x-is-not-valid"></a>EM0005: O destino de compilação `X` não é válido.

A ferramenta não dá suporte para o destino de compilação `X`. É possível que outra versão da ferramenta dá suporte a ele ou ela não é aplicada nesse ambiente.

<a name="EM0006" />

### <a name="em0006-could-not-find-the-xcode-location"></a>EM0006: Não foi possível encontrar a localização do Xcode.

A ferramenta não foi possível encontrar o local Xcode atualmente selecionado usando o `xcode-select -p` comando. Verifique se que esse comando for bem-sucedido e retorna o local correto do Xcode.

<a name="EM0007" />

### <a name="em0007-could-not-get-the-sdk-version-for-sdk"></a>EM0007: Não foi possível obter a versão do sdk para '{sdk}'.

A ferramenta não foi possível obter a versão do SDK usando o `xcrun --show-sdk-version --sdk {sdk}` comando. Verifique se que esse comando for bem-sucedido e retorna a versão do SDK.

<a name="EM0008" />

### <a name="em0008-the-architecture-arch-is-not-valid-for-platform-valid-architectures-for-platform-are-architectures"></a>EM0008: A arquitetura '{arch}' não é válida para a plataforma de {}. Arquiteturas de válido para {plataforma} são: '{arquiteturas}'.

A arquitetura na mensagem de erro não é válida para a plataforma de destino. Verifique se que a opção – a abi é passada uma arquitetura válida.

<a name="EM0009" />

### <a name="em0009-the-feature-x-is-not-currently-implemented-by-the-generator"></a>EM0009: O recurso `X` não está implementado atualmente pelo gerador de

Isso é um problema conhecido que pretendemos corrigir em uma versão futura do gerador. Contribuições são bem-vindos.

<a name="EM0010" />

### <a name="em0010-cant-merge-the-frameworks-simulatorframework-and-deviceframework-because-the-file-file-exists-in-both"></a>EM0010: Não é possível mesclar as estruturas de '{simulatorFramework}' e '{deviceFramework}' porque o arquivo '{file}' existe em ambos.

A ferramenta não pôde mesclar as estruturas mencionadas na mensagem de erro, porque não há um arquivo comum entre eles.

Isso pode indicar um bug no .NET inserção; Envie um relatório de bug no [ https://github.com/mono/Embeddinator-4000/issues ](https://github.com/mono/Embeddinator-4000/issues) com um caso de teste.

<a name="EM0011" />

### <a name="em0011-the-assembly-x-does-not-exist"></a>EM0011: O assembly `X` não existe.

A ferramenta não foi possível encontrar o assembly `X` especificadas nos argumentos.

<a name="EM0012" />

### <a name="em0012-the-assembly-name-x-is-not-unique"></a>EM0012: O nome do assembly `X` não é exclusivo

Mais de um assembly fornecido tem o mesmo nome interno e não seria possível diferenciá-los em tempo de execução.

A causa mais provável é que um assembly é especificado mais de uma vez nos argumentos de linha de comando. No entanto uma mantém de assembly renomeado ainda é o nome original e várias cópias não pode coexiste.

<a name="EM0013" />

### <a name="em0013-cant-find-the-assembly-x-referenced-by-y"></a>EM0013: Não é possível localizar o assembly 'X', referenciada por 'Y'.

A ferramenta não foi possível encontrar o assembly 'X', referenciado pelo assembly 'Y'. Verifique se todos os assemblies referenciados estão no mesmo diretório que o assembly a ser associado.

<a name="EM0014" />

### <a name="em0014-could-not-find-product-product-minversion-is-required"></a>EM0014: Não foi possível localizar o {product} ({product} {min_version} é necessário).

A dependência mencionada na mensagem de erro não pôde ser encontrada no sistema.

<a name="EM0015" />

### <a name="em0015-could-not-find-a-valid-version-of-product-found-version-but-at-least-minversion-is-required"></a>EM0015: Não foi possível encontrar uma versão válida do {product} (encontrado {version}, mas pelo menos {min_version} é necessário).

A dependência mencionado no erro mensagem foi encontrada no sistema, mas é muito antigo. Atualize para uma versão mais recente.

<a name="EM0016" />

### <a name="em0016-could-not-create-symlink-file---target-error-number"></a>EM0016: Não foi possível criar symlink '{arquivo}' -> '{target}': erro {number}

Não foi possível criar o symlink mencionado na mensagem de erro.

<a name="EM0026" />

### <a name="em0026-could-not-parse-the-command-line-argument-a-"></a>Não EM0026 foi possível analisar o argumento de linha de comando 'A': *

A sintaxe fornecida para a opção de linha de comando `A` não pôde ser analisado pela ferramenta. É provável que incorreto, entre em contato com a documentação ou a Ajuda para obter a sintaxe correta.

<a name="EM0099" />

### <a name="em0099-internal-error--please-file-a-bug-report-with-a-test-case-httpsgithubcommonoembeddinator-4000issues"></a>EM0099: Erro interno *. Envie um relatório de bug com um caso de teste (https://github.com/mono/Embeddinator-4000/issues).

Essa mensagem de erro é relatada quando ocorre falha na verificação de consistência interna na incorporação do .NET.

Isso indica um bug no .NET inserção; Envie um relatório de bug no [ https://github.com/mono/Embeddinator-4000/issues ](https://github.com/mono/Embeddinator-4000/issues) com um caso de teste.

<!-- 1xxx: code processing -->

## <a name="em1xxx-code-processing"></a>EM1xxx: Processamento de código

<a name="EM1010" />

### <a name="em1010-type-t-is-not-generated-because-x-are-not-supported"></a>EM1010: Tipo de `T` não é gerada porque `X` não têm suporte.

Isso é um **aviso** que o tipo `T` será ignorada (ou seja, nada será gerado) porque ele usa `X`, um recurso que não tem suporte.

Observação: Recursos com suporte evoluirá com novas versões da ferramenta.

<a name="EM1011" />

### <a name="em1011-type-t-is-not-generated-because-it-lacks-marshaling-code-with-a-native-counterpart"></a>EM1011: Tipo `T` não é gerada porque ele não tem código de marshaling com um equivalente nativo.

Esse é um **aviso** que o tipo `T` será ignorada (ou seja, nada será gerado) porque ela expõe algo a partir do .NET framework que exige marshaling extra.

Observação: Isso é algo que podem obter suporte, com algumas limitações, em uma versão futura da ferramenta.

<a name="EM1020" />

### <a name="em1020-constructor-c-is-not-generated-because-of-parameter-type-t-is-not-supported"></a>EM1020: Construtor `C` não é gerada devido ao tipo de parâmetro `T` não tem suporte.

Esse é um **aviso** que o construtor `C` será ignorada (ou seja, nada será gerado) porque um parâmetro de tipo `T` não tem suporte.

Deve haver um aviso anterior dando saber por que digite `T` não tem suporte.

Observação: Recursos com suporte evoluirá com novas versões da ferramenta.

<a name="EM1021" />

### <a name="em1021-constructor-c-has-default-values-for-which-no-wrapper-is-generated"></a>EM1021: Construtor `C` tem valores padrão para o qual nenhum wrapper é gerado.

Esse é um **aviso** que os parâmetros padrão do construtor `C` não está gerando nenhum código extra. A causa mais comum é que um método existente já tem a mesma assinatura. Por exemplo, no .net, é possível ter:

```
public class MyType {
    public MyType () { ... }
    public MyType (int i = 0) { ... }
}
```

Nesses casos, apenas dois gerado `init` seletores serão criados, os dois chamando o Mono, mas não continuará a existir nenhum wrapper para o mais tarde.

<a name="EM1030" />

### <a name="em1030-method-m-is-not-generated-because-return-type-t-is-not-supported"></a>EM1030: Método `M` não é gerado porque o tipo de retorno `T` não tem suporte.

Esse é um **aviso** que o método `M` será ignorada (ou seja, nada será gerado) porque ele é o tipo de retorno `T` não tem suporte.

Deve haver um aviso anterior dando saber por que digite `T` não tem suporte.

Observação: Recursos com suporte evoluirá com novas versões da ferramenta.

<a name="EM1031" />

### <a name="em1031-method-m-is-not-generated-because-of-parameter-type-t-is-not-supported"></a>EM1031: Método `M` não é gerada devido ao tipo de parâmetro `T` não tem suporte.

Esse é um **aviso** que o método `M` será ignorada (ou seja, nada será gerado) porque um parâmetro de tipo `T` não tem suporte.

Deve haver um aviso anterior dando saber por que digite `T` não tem suporte.

Observação: Recursos com suporte evoluirá com novas versões da ferramenta.

<a name="EM1032" />

### <a name="em1032-method-m-has-default-values-for-which-no-wrapper-is-generated"></a>EM1032: Método `M` tem valores padrão para o qual nenhum wrapper é gerado.

Esse é um **aviso** que os parâmetros padrão do método `M` não está gerando nenhum código extra. A causa mais comum é que um método existente já tem a mesma assinatura. Por exemplo, no .net, é possível ter:

```
public class MyType {
    public int Increment () { ... }
    public int Increment (int i = 0) { ... }
}
```

Nesses casos, apenas dois gerado `increment` seletores serão criados, os dois chamando o Mono, mas não continuará a existir nenhum wrapper para o mais tarde.

<a name="EM1033" />

### <a name="em1033-method-m-is-not-generated-because-another-method-exposes-the-operator-with-a-friendly-name"></a>EM1033: Método `M` não é gerada porque outro método expõe o operador com um nome amigável.

Esse é um **aviso** que o método `M` não é gerada porque outro método expõe o operador com um nome amigável. (https://msdn.microsoft.com/library/ms229032(v=vs.110).aspx)

<a name="EM1034" />

### <a name="em1034-extension-method-m-is-not-generated-inside-a-category-because-they-cannot-be-created-on-primitive-type-t-a-normal-static-method-was-generated"></a>EM1034: Método de extensão `M` não é gerado dentro de uma categoria, porque eles não podem ser criados no tipo primitivo `T`. Um método estático normal foi gerado.

Esse é um **aviso** que um método de extensão em um primivite de tipo (por exemplo, `System.Int32`) foi encontrado. Em Objective-C não é possível criar categorias de tipo primitivo. Em vez disso, o gerador de produzirá um método estático normal.

<a name="EM1040" />

### <a name="em1040-property-p-is-not-generated-because-of-parameter-type-t-is-not-supported"></a>EM1040: Propriedade `P` não é gerada devido ao tipo de parâmetro `T` não tem suporte.

Esse é um **aviso** que a propriedade `P` será ignorada (ou seja, nada será gerado) porque o tipo exposto `T` não tem suporte.

Deve haver um aviso anterior dando saber por que digite `T` não tem suporte.

Observação: Recursos com suporte evoluirá com novas versões da ferramenta.

<a name="EM1041" />

### <a name="em1041-indexed-properties-on-t-is-not-generated-because-multiple-indexed-properties-are-not-supported"></a>EM1041: Propriedades indexadas em `T` não é gerada porque não há suporte para várias propriedades indexadas.

Esse é um **aviso** que as propriedades indexadas em `T` será ignorada (ou seja, nada será gerado) porque não há suporte para várias propriedades indexadas.

<a name="EM1050" />

### <a name="em1050-field-f-is-not-generated-because-of-field-type-t-is-not-supported"></a>EM1050: Campo `F` não é gerada devido ao tipo de campo `T` não tem suporte.

Esse é um **aviso** que o campo `F` será ignorada (ou seja, nada será gerado) porque o tipo exposto `T` não tem suporte.

Deve haver um aviso anterior dando saber por que digite `T` não tem suporte.

Observação: Recursos com suporte evoluirá com novas versões da ferramenta.

<a name="EM1051" />

### <a name="em1051-element-e-is-generated-instead-as-f-because-its-name-conflicts-with-an-important-objective-c-selector"></a>EM1051: Elemento `E` é gerado em vez disso, como `F` porque seu nome está em conflito com um seletor de objective-c importante.

Esse é um **aviso** que o elemento `E` serão gerados como `F` porque seu nome está em conflito com um seletor de objective-c importante.

Seletores em políticas para o [NSObjectProtocol](https://developer.apple.com/reference/objectivec/1418956-nsobject?language=objc) têm significado importante em objective-c e deve ser substituído com cuidado.

Observação: A lista de seletores reservados evoluirá com novas versões da ferramenta.

<a name="EM1052" />

### <a name="em1052-element-e-is-not-generated-its-name-conflicts-with-other-elements-on-the-same-class"></a>EM1052: Elemento `E` não é gerada seu nome está em conflito com outros elementos na mesma classe.

Esse é um **aviso** desse elemento `E` não é gerado como seu nome está em conflito com outros elementos na mesma classe.

<a name="EM1053" />

### <a name="em1053-target-e-is-not-supported-for-xamarinios-and-xamarinmac-only-the-framework-option-is-considered-supported-and-should-be-used"></a>EM1053: Destino `E` não há suporte para xamarin. IOS e xamarin. Mac. Somente o `framework` opção é considerada com suporte e devem ser usados.

Esse é um **aviso** destinados `E` será considerada sem suporte para casos de uso do xamarin. IOS e xamarin. Mac. 

Consumo de bibliotecas estáticas ou dinâmicas incorporação do .NET pode exigir etapas adicionais de trabalho ou ajustes e deve ser evitado na maioria dos casos de uso.

Considere a remoção de sua `--target` parâmetro ou passar `--target=framework` em vez disso.

<!-- 2xxx: code generation -->

## <a name="em2xxx-code-generation"></a>EM2xxx: Geração de código

<!-- 3xxx: reserved -->
<!-- 4xxx: reserved -->
<!-- 5xxx: reserved -->
<!-- 6xxx: reserved -->
<!-- 7xxx: reserved -->
<!-- 8xxx: reserved -->
<!-- 9xxx: reserved -->
