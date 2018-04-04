---
title: Erros de inserção de .NET
ms.prod: xamarin
ms.assetid: 932C3F0C-D968-42D1-BB14-D97C73361983
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 64caaf6610d9f9193a686d91b4731cd4d4953fa6
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="em0xxx-binding-error-messages"></a>EM0xxx: mensagens de erro de associação

Por exemplo, parâmetros, ambiente

<!-- 0xxx: the generator itself, e.g. parameters, environment -->
<h3><a name="EM0000"/>EM0000: Erro inesperado - preencha um relatório de erros em https://github.com/mono/Embeddinator-4000/issues</h3>

Ocorreu uma condição de erro inesperado. Por favor, [problema](https://github.com/mono/Embeddinator-4000/issues) com tanta informação quanto possível, incluindo:

* Compilação completa logs, com um máximo de detalhamento
* Um caso de teste mínimo que reproduza o erro
* Todas as informações de versão

A maneira mais fácil de obter informações sobre a versão exata é usar o **Xamarin Studio** menu **Xamarin Studio** item, **Mostrar detalhes** botão e a versão de copiar/colar informações (você pode usar o **copiar informações** botão).

<h3><a name="EM0001"/>EM0001: Não foi possível criar o diretório de saída `X`</h3>

O nome do diretório especificado por `-o=DIR` não existe e não pôde ser criado. Pode ser um nome inválido para o sistema de arquivos.

<h3><a name="EM0002"/>EM0002: Opção `X` não tem suporte</h3>

A ferramenta não dá suporte para a opção `X`. É possível que outra versão da ferramenta oferece suporte a ele ou ela não é aplicada nesse ambiente.

<h3><a name="EM0003"/>EM0003: A plataforma `X` não é válido.</h3>

A ferramenta não dá suporte para a plataforma `X`. É possível que outra versão da ferramenta oferece suporte a ele ou ela não é aplicada nesse ambiente.

<h3><a name="EM0004"/>EM0004: O destino `X` não é válido.</h3>

A ferramenta não dá suporte para o destino `X`. É possível que outra versão da ferramenta oferece suporte a ele ou ela não é aplicada nesse ambiente.

<h3><a name="EM0005"/>EM0005: O destino de compilação `X` não é válido.</h3>

A ferramenta não dá suporte para o destino de compilação `X`. É possível que outra versão da ferramenta oferece suporte a ele ou ela não é aplicada nesse ambiente.

<h3><a name="EM0006"/>EM0006: Não foi possível encontrar o local do Xcode.</h3>

A ferramenta não foi possível encontrar o local Xcode atualmente selecionado usando o `xcode-select -p` comando. Verifique se que esse comando tiver êxito e retorna o local correto do Xcode.

<h3><a name="EM0007"/>EM0007: Não foi possível obter a versão do sdk para '{sdk}'.</h3>

A ferramenta não foi possível obter a versão do SDK usando o `xcrun --show-sdk-version --sdk {sdk}` comando. Verifique se que esse comando tiver êxito e retorna a versão do SDK.

<h3><a name="EM0008"/>EM0008: A arquitetura '{arch}' não é válida para a plataforma de {}. As arquiteturas válidas para {plataforma} são: '{arquiteturas}'.</h3>

A arquitetura na mensagem de erro não é válida para a plataforma de destino. Verifique se a opção – abi é passada a uma arquitetura válida.

<h3><a name="EM0009"/>EM0009: O recurso `X` atualmente não está implementado pelo gerador de</h3>

Este é um problema conhecido que pretendemos corrigir em uma versão futura do gerador. Contribuições são boas-vindas.

<h3><a name="EM0010"/>EM0010: Não é possível mesclar as estruturas '{simulatorFramework}' e '{deviceFramework}' porque o arquivo ' {}' existe em ambos.</h3>

A ferramenta não pôde mesclar as estruturas mencionadas na mensagem de erro, porque não há um arquivo comum entre eles.

Isso pode indicar um bug em 4000 Embeddinator; Envie um relatório de erros em [ https://github.com/mono/Embeddinator-4000/issues ](https://github.com/mono/Embeddinator-4000/issues) com um caso de teste.

<h3><a name="EM0011"/>EM0011: O assembly `X` não existe.</h3>

A ferramenta não foi possível encontrar o assembly `X` especificados nos argumentos.

<h3><a name="EM0012"/>EM0012: O nome do assembly `X` não é exclusivo</h3>

Mais de um assembly fornecido tem o mesmo nome interno e não seria possível para diferenciá-los em tempo de execução.

A causa mais provável é que um assembly é especificado mais de uma vez nos argumentos de linha de comando. No entanto uma mantém ainda renomeado assembly é o nome original e várias cópias não pode coexiste.

<h3><a name="EM0013"/>EM0013: Não é possível localizar o assembly 'X', referenciado por 'Y'.</h3>

A ferramenta não foi possível encontrar o assembly 'X', referenciado pelo assembly 'Y'. Verifique se todos os assemblies referenciados estão no mesmo diretório que o assembly a ser associado.

<h3><a name="EM0014"/>EM0014: Não foi possível localizar {produto} ({produto} {min_version} é necessário).</h3>

A dependência mencionada na mensagem de erro não pôde ser encontrada no sistema.

<h3><a name="EM0015"/>EM0015: Não foi possível localizar uma versão válida do {produto} ({versão} encontrada, mas pelo menos {min_version} é necessário).</h3>

A dependência mencionado no erro mensagem foi encontrada no sistema, mas é muito antigo. Atualize para uma versão mais recente.

<h3><a name="EM0016">EM0016: Não foi possível criar symlink '{arquivo}' -> '{target}': erro {number}</h3>

Não foi possível criar o symlink mencionado na mensagem de erro.

<h3><a name="EM0026"/>Não EM0026 foi possível analisar o argumento de linha de comando 'A': *</h3>

A sintaxe fornecida para a opção de linha de comando `A` não pôde ser analisado pela ferramenta. É provável incorreto, entre em contato com a documentação ou a Ajuda para obter a sintaxe correta.

<h3><a name="EM0099"/>EM0099: Erro interno *. Envie um relatório de erros com um caso de teste (https://github.com/mono/Embeddinator-4000/issues).</h3>

Essa mensagem de erro é relatada quando ocorre falha na verificação de consistência interna em Embeddinator-4000.

Isso indica um bug no Embeddinator-4000; Envie um relatório de erros em [ https://github.com/mono/Embeddinator-4000/issues ](https://github.com/mono/Embeddinator-4000/issues) com um caso de teste.


<!-- 1xxx: code processing -->

# <a name="em1xxx-code-processing"></a>EM1xxx: Processamento de código

<h3><a name="EM1010"/>Tipo `T` não é gerada porque `X` não têm suporte.</h3>

Este é um **aviso** que o tipo `T` serão ignoradas (ou seja, nada será gerado) porque ele usa `X`, um recurso que não tem suporte.

Observação: Recursos com suporte evoluirá com novas versões da ferramenta.


<h3><a name="EM1011"/>Tipo `T` não é gerada porque ele não tem um equivalente nativo.</h3>

Este é um **aviso** que o tipo `T` serão ignoradas (ou seja, nada será gerado) porque ele usa expor algo do .NET framework que não tem contraparte na plataforma nativo.


<h3><a name="EM1011"/>Tipo `T` não é gerada porque ele não tem o código de empacotamento com um representante nativo.</h3>

Este é um **aviso** que o tipo `T` serão ignoradas (ou seja, nada será gerado) porque ele usa expor algo do .NET framework que requer muito marshaling.

Observação: Isso é algo que pode obter suporte, com algumas limitações, em uma versão futura da ferramenta.


<h3><a name="EM1020"/>Construtor `C` não é gerado devido ao tipo de parâmetro `T` não tem suporte.</h3>

Este é um **aviso** que o construtor `C` serão ignoradas (ou seja, nada será gerado) porque um parâmetro de tipo `T` não tem suporte.

Deve haver um aviso anterior fornece mais informações por tipo `T` não tem suporte.

Observação: Recursos com suporte evoluirá com novas versões da ferramenta.


<h3><a name="EM1021"/>Construtor `C` tem valores padrão para o qual nenhuma wrapper é gerado.</h3>

Este é um **aviso** que os parâmetros padrão do construtor `C` não está gerando qualquer código extra. A causa mais comum é que um método existente já tem a mesma assinatura. Por exemplo, no .net, é possível ter:

```
public class MyType {
    public MyType () { ... }
    public MyType (int i = 0) { ... }
}
```

Em tais casos apenas dois gerados `init` seletores serão criados, ambos os chamar mono, mas não continuará a existir nenhum wrapper para o mais tarde.


<h3><a name="EM1030"/>Método `M` não é gerado porque o tipo de retorno `T` não tem suporte.</h3>

Este é um **aviso** que o método `M` serão ignoradas (ou seja, nada será gerado) porque ele é o tipo de retorno `T` não tem suporte.

Deve haver um aviso anterior fornece mais informações por tipo `T` não tem suporte.

Observação: Recursos com suporte evoluirá com novas versões da ferramenta.


<h3><a name="EM1031"/>Método `M` não é gerado devido ao tipo de parâmetro `T` não tem suporte.</h3>

Este é um **aviso** que o método `M` serão ignoradas (ou seja, nada será gerado) porque um parâmetro de tipo `T` não tem suporte.

Deve haver um aviso anterior fornece mais informações por tipo `T` não tem suporte.

Observação: Recursos com suporte evoluirá com novas versões da ferramenta.


<h3><a name="EM1032"/>Método `M` tem valores padrão para o qual nenhuma wrapper é gerado.</h3>

Este é um **aviso** que os parâmetros padrão do método `M` não está gerando qualquer código extra. A causa mais comum é que um método existente já tem a mesma assinatura. Por exemplo, no .net, é possível ter:

```
public class MyType {
    public int Increment () { ... }
    public int Increment (int i = 0) { ... }
}
```

Em tais casos apenas dois gerados `increment` seletores serão criados, ambos os chamar mono, mas não continuará a existir nenhum wrapper para o mais tarde.


<h3><a name="EM1033"/>Método `M` não é gerada porque outro método expõe o operador com um nome amigável.</h3>

Este é um **aviso** que o método `M` não é gerada porque outro método expõe o operador com um nome amigável. (https://msdn.microsoft.com/en-us/library/ms229032(v=vs.110).aspx)


<h3><a name="EM1034"/>Método de extensão `M` não é gerado dentro de uma categoria, porque eles não podem ser criados em tipo primitivo `T`. Um método estático normal foi gerado.</h3>

Este é um **aviso** que tipo de um método de extensão em um primivite (por exemplo, `System.Int32`) foi encontrado. ObjC não é possível criar categorias em tipo primitivo. Em vez disso, o gerador de produzirá um método estático normal.



<h3><a name="EM1040"/>Propriedade `P` não é gerado devido ao tipo de parâmetro `T` não tem suporte.</h3>

Este é um **aviso** que a propriedade `P` serão ignoradas (ou seja, nada será gerado) porque o tipo exposto `T` não tem suporte.

Deve haver um aviso anterior fornece mais informações por tipo `T` não tem suporte.

Observação: Recursos com suporte evoluirá com novas versões da ferramenta.

<h3><a name="EM1041"/>Propriedades indexadas em `T` não é gerada porque não há suporte para várias propriedades indexadas.</h3>

Este é um **aviso** que as propriedades indexadas `T` serão ignoradas (ou seja, nada será gerado) porque não há suporte para várias propriedades indexadas.


<h3><a name="EM1050"/>Campo `F` não é gerado por causa do tipo de campo `T` não tem suporte.</h3>

Este é um **aviso** que o campo `F` serão ignoradas (ou seja, nada será gerado) porque o tipo exposto `T` não tem suporte.

Deve haver um aviso anterior fornece mais informações por tipo `T` não tem suporte.

Observação: Recursos com suporte evoluirá com novas versões da ferramenta.

<h3><a name="EM1051"/>Elemento `E` é gerado em vez disso, como `F` porque seu nome está em conflito com um seletor de objetivo c importante.</h3>

Este é um **aviso** que o elemento `E` será gerado como `F` porque seu nome está em conflito com um seletor de objetivo c importante.

Seletores no [NSObjectProtocol](https://developer.apple.com/reference/objectivec/1418956-nsobject?language=objc) têm significado importante em objective-c e deve ser substituído com cuidado.

Observação: A lista de seletores reservados evoluirá com novas versões da ferramenta.


<!-- 2xxx: code generation -->

# <a name="em2xxx-code-generation"></a>EM2xxx: Geração de código


<!-- 3xxx: reserved -->
<!-- 4xxx: reserved -->
<!-- 5xxx: reserved -->
<!-- 6xxx: reserved -->
<!-- 7xxx: reserved -->
<!-- 8xxx: reserved -->
<!-- 9xxx: reserved -->
