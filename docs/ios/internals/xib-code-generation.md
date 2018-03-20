---
title: "Geração de código .xib"
ms.topic: article
ms.prod: xamarin
ms.assetid: 365991A8-E07A-0420-D28E-BC4D32065E1A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 7d320b3fc40c852c337e5fd1e9bda4e90920cf70
ms.sourcegitcommit: cc38757f56aab53bce200e40f873eb8d0e5393c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2018
---
# <a name="xib-code-generation"></a>Geração de código .xib

> [!IMPORTANT]
>  Este documento explica o Visual Studio para a integração do Mac com o construtor de Interface do Xcode apenas, como ações e saídas não são usadas no Designer de Xamarin para iOS. Para obter mais informações sobre o Designer do iOS, examine o [iOS Designer](~/ios/user-interface/designer/index.md) documento.

A ferramenta construtor de Interface da Apple ("IB") pode ser usada para criar interfaces do usuário visualmente. As definições de interface criadas pelo IB são salvas em **.xib** arquivos. Widgets e outros objetos no **.xib** arquivos podem ser fornecidos uma "classe de identidade", que pode ser um tipo personalizado definido pelo usuário. Isso permite que você para personalizar o comportamento de widgets e gravar widgets personalizados.

Essas classes de usuário normalmente são subclasses de classes do controlador de interface do usuário. Eles têm *tomadas* (como propriedades) e *ações* (como eventos) que pode ser conectado aos objetos de interface. Em tempo de execução, quando o arquivo IB é carregado, os objetos são criados e as saídas e as ações são conectadas a vários objetos de interface do usuário dinamicamente. Ao definir essas classes gerenciadas, você deve definir todas as ações e saídas para corresponder aos que espera IB. Visual Studio para Mac usa um modelo de code-behind para simplificar isso. Isso é semelhante ao que faz Xcode para Objective-C, mas o modelo de geração de código e as convenções tem foi ajustadas para ser mais familiar aos desenvolvedores de .NET.

Trabalhando com **.xib** arquivos não há atualmente suporte no xamarin para Visual Studio.

## <a name="xib-files-and-custom-classes"></a>arquivos de .xib e Classes personalizadas

Assim como os tipos existentes de toque Cocoa, é possível definir tipos personalizados no **.xib** arquivos. Também é possível usar tipos definidos em outros **.xib** arquivos ou definido exclusivamente no código do c#. Atualmente, construtor de Interface não está ciente dos detalhes de tipos definidos fora atual **.xib** arquivo, assim ele não será listá-los ou mostrar suas saídas personalizadas e as ações. Remover esta limitação está planejada para o futuro.

Classes personalizadas podem ser definidas em um **.xib** arquivo usando o comando "Adicionar subclasse" na aba "Classes" do construtor de Interface. Nós nos referimos a eles como classes "Code-behind". Se o **.xib** arquivo tem um ". xib.designer.cs" arquivo correspondente no projeto, em seguida, o Visual Studio para Mac automaticamente o preencherá com definições de classes parciais para todas as classes personalizadas no **.xib**. Chamamos essas classes parciais "classes designer".

## <a name="generating-code"></a>Gerando código

Para qualquer **{0} .xib** arquivo com uma ação de compilação de *página*, se um **{0}.xib.designer.cs** arquivo também existe no projeto, o Visual Studio para Mac irá gerar classes parciais do para todas as classes de usuário, ele pode encontrar no arquivo de designer do **.xib** arquivo, com saídas de propriedades e métodos parciais para todas as ações. Geração de código está habilitada com a presença desse arquivo.

O arquivo de designer é automaticamente atualizado quando o **.xib** arquivo alterações e o Visual Studio para Mac recupera o foco. O arquivo de designer não deve ser modificado manualmente, como alterações serão substituída próxima vez que o Visual Studio para atualizações do Mac o arquivo.

## <a name="registration-and-namespaces"></a>Registro e Namespaces

O Visual Studio para Mac gera classes de designer usando o namespace do projeto padrão para o local do arquivo de designer, para torná-la consistente com namespacing de projeto .NET normal. O namespace do designers arquivos é orientado pelo namespace do projeto"padrão" e suas configurações de ".NET nomear políticas". Lembre-se de que, se namespace padrão seu projeto do for alterado, MD irá regenerar as classes no novo namespace, portanto você pode achar que as classes parciais não correspondem a.

Para tornar a classe detectável pelo tempo de execução Objective-C, o Visual Studio para Mac aplica-se um `[Register (name)]` para a classe de atributo. Embora xamarin registra automaticamente `NSObject`-classes derivadas, ele usa os nomes de .NET totalmente qualificado. O atributo aplicado pelo Visual Studio para Mac substitui isto para garantir que cada classe está registrado com o nome usado no **.xib** arquivo. Se você usar classes personalizadas em IB sem usar o Visual Studio para Mac para gerar arquivos de designer, você pode ter para aplicá-la manualmente para fazer com que as classes gerenciadas correspondem aos nomes de classe esperados Objective-C.

Classes não podem ser definidas em mais de um **.xib**, ou eles entrarão em conflito.

## <a name="non-designer-class-parts"></a>Partes de classe não-Designer

Classes parciais designers não se destinam a ser usado como-é. Saídas são particulares e nenhuma classe base for especificada. Espera-se que cada classe terá uma parte de classe "não designer" correspondente no outro arquivo, que define a classe base, usa ou expõe as saídas e define construtores que são necessárias para instanciar a classe do código nativo ao carregar o **.xib**. O padrão **.xib** modelos de fazer isso, mas para as classes personalizadas adicionais que você define em um **.xib**, você deve adicionar a parte do designer não manualmente.

A razão para isso é a necessidade de flexibilidade. Por exemplo, várias classes de code-behind podem subclasse que um comum gerenciados classe abstrata, que herda da classe para ser uma subclasse por IB.

É convencional colocar essas em um **{0}.xib.cs** ao lado do arquivo a **{0}.xib.designer.cs** arquivo de designer.

<a name="generated" />

## <a name="generated-actions-and-outlets"></a>Tomadas e ações geradas

Nas classes parciais designers, o Visual Studio para Mac gera propriedades correspondentes a qualquer tomadas conectadas definidas IB e métodos parciais correspondente a qualquer ação conectada.

### <a name="outlet-properties"></a>Propriedades de saída

Classes de designers contém propriedades correspondentes a todas as saídas definidas na classe personalizada. O fato de que elas são propriedades é um detalhe de implementação do xamarin para ponte Objective C, para habilitar a associação lenta. Você deve considerá-las como equivalente aos campos particulares, deve ser usado apenas na classe code-behind. Se você desejar torná-los pública, adicione propriedades de acessador para a parte de classe não-designer, como faria com qualquer outro campo privado.

Se as propriedades de saída são definidas para ter um tipo de `id` (equivalente a `NSObject`) e o gerador de código de designer atualmente determina o tipo de possíveis mais forte com base nos objetos conectados a essa tomada, por conveniência.
No entanto, isso talvez não tenha suporte em versões futuras, portanto, é recomendável que você explicitamente fortemente tipados saídas de durante a definição de classe personalizada.

### <a name="action-properties"></a>Propriedades da ação

Classes de designers contém métodos parciais correspondente para todas as ações definidas na classe personalizada. Esses são métodos sem uma implementação. A finalidade dos métodos parciais é dupla:

1.  Se você digitar `partial` no corpo da classe da parte de classe não-designer, o Visual Studio para Mac se oferecerá para preenchimento automático as assinaturas de todos os métodos parciais não implementado.
2.  As assinaturas de método parcial tem um atributo aplicado que expõe para o mundo Objective-C, para que eles possam obter tratados como a ação correspondente.


Se desejar, você pode ignorar o método parcial e implementar a ação aplicando o atributo para um método diferente ou deixá-lo a passar para uma classe base.

Se as ações são definidas com um tipo de remetente de `id` (equivalente a `NSObject`), em seguida, o gerador de código de designer atualmente determina o tipo de possíveis mais forte com base nos objetos conectados a essa ação. No entanto, isso talvez não tenha suporte em versões futuras, portanto, é recomendável que você explicitamente fortemente tipados as ações ao definir a classe personalizada.

Observe que esses métodos parciais são criados somente para c#, pois CodeDOM não dá suporte a métodos parciais, para que eles não são gerados para outros idiomas.

## <a name="cross-xib-class-usage"></a>Uso de classe entre XIB

Às vezes, os usuários quiserem referenciar a mesma classe de vários **.xib** arquivos, por exemplo, com os controladores de guia. Isso pode ser feito por explicitamente referenciando a definição de classe de outro **.xib** de arquivos ou por definir o mesmo nome de classe novamente no segundo **.xib**.

O último caso pode ser problemático devido ao Visual Studio para processamento de Mac **.xib** arquivos individualmente. Ele não pode detectar automaticamente e mesclar as definições duplicadas, portanto você pode acabar com conflitos aplicando o atributo de registro várias vezes quando a mesma classe parcial é definida em vários arquivos de designer. As versões recentes do Visual Studio para Mac tentam resolver o problema, mas ele não pode sempre funcionar conforme o esperado. No futuro, isso é adequado para se tornar sem suporte e, em vez disso, o Visual Studio para Mac fará todos os tipos definidos em todos os **.xib** arquivos e código gerenciado no projeto diretamente visível de todos os **.xib** arquivos.

## <a name="type-resolution"></a>Resolução de tipo

Tipos usados em IB são nomes de tipo Objective-C. Estes são mapeados para tipos CLR com o uso de atributos de registro. Ao gerar o código de saída e a ação, o Visual Studio para Mac resolver os tipos CLR correspondentes para todos os tipos de Objective-C encapsulados pelos principais xamarin e qualificar totalmente seus nomes de tipo.

No entanto, o gerador de código no momento não é possível resolver tipos de CLR de nomes de tipo Objective-C no código do usuário ou bibliotecas, portanto em tais casos, ele produz o nome do tipo textual. Isso significa que o tipo CLR correspondente deve ter o mesmo nome como o tipo de Objective-C e estar no mesmo namespace que o código que ele está em uso. Isso é planejado para ser corrigido em algum momento no futuro, levando em consideração todos os tipos de Objective-C no projeto durante a geração de código.
