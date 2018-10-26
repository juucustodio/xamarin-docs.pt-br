---
title: Geração de código. XIB no xamarin. IOS
description: Este documento descreve como o xamarin. IOS gera código para mapear arquivos. XIB para C#, tornando controles visuais acessíveis por meio de programação.
ms.prod: xamarin
ms.assetid: 365991A8-E07A-0420-D28E-BC4D32065E1A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 57988374b4383f5659e29edff3834958b8f99f1b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103914"
---
# <a name="xib-code-generation-in-xamarinios"></a>Geração de código. XIB no xamarin. IOS

> [!IMPORTANT]
>  Este documento explica o Visual Studio para a integração do Mac com o Interface Builder do Xcode, como ações e as saídas não são usadas no Designer do Xamarin para iOS. Para obter mais informações sobre o Designer do iOS, consulte a [Designer do iOS](~/ios/user-interface/designer/index.md) documento.

A ferramenta construtor de Interface da Apple ("IB") pode ser usada para criar interfaces do usuário visualmente. As definições de interface criadas pelo IB são salvos no **. XIB** arquivos. Widgets e outros objetos no **. XIB** arquivos podem ser fornecidos um "classe de identidade", que pode ser um tipo personalizado definido pelo usuário. Isso permite que você personalize o comportamento de widgets e gravar widgets personalizados.

Essas classes de usuário normalmente são subclasses de classes do controlador de interface do usuário. Eles têm *tomadas* (análogas às propriedades) e *ações* (análogo a eventos) que podem ser conectadas a objetos de interface. No tempo de execução quando o arquivo IB é carregado, os objetos são criados e as saídas e ações estão conectadas a vários objetos de interface do usuário dinamicamente. Ao definir essas classes gerenciadas, você deve definir todas as ações e saídas para coincidir com aqueles que espera IB. O Visual Studio para Mac usa um modelo de code-behind para simplificar isso. Isso é semelhante ao que Xcode faz para Objective-C, mas o modelo de geração de código e convenções de tem sido ajustadas para ser mais familiar para os desenvolvedores do .NET.

Trabalhando com **. XIB** arquivos não é suportada no xamarin. IOS para Visual Studio.

## <a name="xib-files-and-custom-classes"></a>arquivos. XIB e Classes personalizadas

Além de usar os tipos existentes do Cocoa Touch, é possível definir tipos personalizados em **. XIB** arquivos. Também é possível usar tipos definidos em outros **. XIB** arquivos ou definida puramente no C# código. Atualmente, o construtor de Interface não está ciente dos detalhes dos tipos definidos fora do atual **. XIB** arquivo, portanto, não irá listá-los ou mostrar suas saídas personalizadas e as ações. Remover esta limitação está planejado para o futuro.

Classes personalizadas podem ser definidas em um **. XIB** usando o comando "Adicionar subclasse" na guia "Classes" do Interface Builder do arquivo. Nos referimos a eles como "Code-behind" classes. Se o **. XIB** arquivo tem um ". xib.designer.cs" arquivo equivalente no projeto, em seguida, o Visual Studio para Mac automaticamente irá preenchê-lo com as definições de classes parciais para todas as classes personalizadas na **. XIB**. Nós nos referimos a essas classes parciais como "classes de designer".

## <a name="generating-code"></a>Gerando código

Para qualquer  **{0}. XIB** arquivo com uma ação de build *página*, se um  **{0}. xib.designer.cs** arquivo também existe no projeto, o Visual Studio para Mac irá gerar classes parciais no arquivo do designer para todas as classes de usuário que encontra a **. XIB** arquivo, com propriedades para as saídas e métodos parciais para todas as ações. Geração de código é habilitada apenas pela presença desse arquivo.

O arquivo de designer é automaticamente atualizado quando o **. XIB** arquivo alterações e o Visual Studio para Mac recupera o foco. O arquivo de designer não deve ser modificado manualmente, conforme as alterações serão sobrescrita próxima vez que o Visual Studio para atualizações do Mac o arquivo.

## <a name="registration-and-namespaces"></a>Registro e Namespaces

O Visual Studio para Mac gera as classes de designer usando o namespace do projeto padrão para o local do arquivo de designer, para torná-lo consistente com o namespace de projeto do .NET normal. O namespace de arquivos de designer é controlado pelas suas configurações de "Políticas de nomenclatura do .NET" e o namespace do projeto"padrão". Lembre-se de que, se namespace padrão seu projeto do for alterado, MD irá regenerar as classes no novo namespace, você talvez ache que suas classes parciais não correspondem mais para cima.

Para tornar a classe detectável pelo tempo de execução Objective-C, o Visual Studio para Mac aplica-se um `[Register (name)]` à classe de atributo. Embora o xamarin. IOS é registrado automaticamente no `NSObject`-classes derivadas, ele usa os nomes de .NET totalmente qualificado. O atributo aplicado pelo Visual Studio para Mac substitui isto para garantir que cada classe é registrado com o nome usado na **. XIB** arquivo. Se você usar as classes personalizadas em IB sem usar o Visual Studio para Mac para gerar arquivos de designer, talvez você precise aplicá-la manualmente para fazer com que suas classes gerenciadas correspondem aos nomes de classe esperados do Objective-C.

Classes não podem ser definidas em mais de um **. XIB**, ou eles entrarão em conflito.

## <a name="non-designer-class-parts"></a>Partes de classe não-Designer

Classes parciais Designer não se destinam a ser usado como-está. Saídas são particulares e nenhuma classe base for especificada. Espera-se que cada classe terá uma parte da classe "não designer" correspondente no outro arquivo, que define a classe base, usa ou expõe as saídas e define construtores que são necessários para instanciar a classe do código nativo ao carregar o **. XIB**. O padrão **. XIB** modelos de fazer isso, mas para as classes personalizadas adicionais que você define em um **. XIB**, você deve adicionar a parte não designer manualmente.

A razão para isso é a necessidade de flexibilidade. Por exemplo, várias classes code-behind pode subclasse que um comum gerenciada classe abstrata, que herda a classe a ser criada como subclasse por IB.

É convencional para colocá-las um  **{0}. xib.cs** do arquivo ao lado de  **{0}. xib.designer.cs** arquivo de designer.

<a name="generated" />

## <a name="generated-actions-and-outlets"></a>Saídas e ações geradas

Em classes de designer parciais, o Visual Studio para Mac gera propriedades correspondente a qualquer conectadas saídas definidas no IB e métodos parciais correspondente a qualquer ação conectadas.

### <a name="outlet-properties"></a>Propriedades de saída

Classes de designer contêm propriedades correspondentes a todas as saídas definidas na classe personalizada. O fato de que elas são propriedades é um detalhe de implementação do xamarin. IOS para ponte do Objective C, para habilitar a associação lenta. Você deve considerá-las como equivalente aos campos privados, deve ser usado apenas da classe code-behind. Se você quiser torná-los públicos, adicione propriedades de acessador para a parte de classe não-designer, como você faria para qualquer outro campo privado.

Se as propriedades de saída são definidas para ter um tipo de `id` (equivalente a `NSObject`) e em seguida, o gerador de código do designer atualmente determina o tipo de possíveis mais forte com base nos objetos conectados a essa saída, para sua conveniência.
No entanto, isso pode não ter suporte em versões futuras, portanto, é recomendável que você explicitamente tipar fortemente os meios de comunicação durante a definição de classe personalizada.

### <a name="action-properties"></a>Propriedades da ação

Classes de designer contêm métodos parciais que corresponde a todas as ações definidas na classe personalizada. Esses são métodos sem implementação. A finalidade dos métodos parciais é dupla:

1.  Se você digitar `partial` no corpo da classe da parte de classe não-designer, o Visual Studio para Mac se oferecerá para preenchimento automático as assinaturas de todos os métodos parciais não implementado.
2.  As assinaturas de método parcial tem um atributo aplicado que expõe-los para o mundo de Objective-C, para que eles possam obter tratados como a ação correspondente.


Se desejar, você pode ignorar o método parcial e implementar a ação por meio da aplicação do atributo a um método diferente ou deixá-lo a passar para uma classe base.

Se as ações são definidas para ter um tipo de remetente de `id` (equivalente a `NSObject`), em seguida, o gerador de código do designer atualmente determina o tipo de possíveis mais forte com base em objetos conectados a essa ação. No entanto, isso pode não ter suporte em versões futuras, portanto, é recomendável que você explicitamente tipar fortemente as ações durante a definição de classe personalizada.

Observe que esses métodos parciais são criados somente para C#, pois o CodeDOM não dá suporte a métodos parciais, portanto, eles não são gerados para outros idiomas.

## <a name="cross-xib-class-usage"></a>Uso de classe entre XIB

Às vezes, os usuários queiram fazer referência a mesma classe de vários **. XIB** arquivos, por exemplo, com controladores de guia. Isso pode ser feito por travado referenciando a definição de classe de outro **. XIB** do arquivo ou por definir o mesmo nome de classe novamente no segundo **. XIB**.

O último caso pode ser problemático devido ao Visual Studio para o processamento do Mac **. XIB** arquivos individualmente. Automaticamente, ele não pode detectar e mesclar definições duplicadas, portanto, você pode acabar com conflitos de aplicação do atributo de registro várias vezes quando a mesma classe parcial é definida em vários arquivos de designer. As versões recentes do Visual Studio para Mac tentará resolver esse problema, mas ele não pode sempre funcionar conforme o esperado. No futuro, isso é provável ficar sem suporte e, em vez disso, o Visual Studio para Mac tornará todos os tipos definidos em todos os **. XIB** arquivos e código gerenciado no projeto diretamente visível de todos os **. XIB** arquivos.

## <a name="type-resolution"></a>Resolução de tipo

Tipos usados em IB são nomes de tipo Objective-C. Estes são mapeados para tipos CLR com o uso de atributos do registro. Ao gerar o código de saída e a ação, o Visual Studio para Mac resolver os tipos CLR correspondentes para todos os tipos de Objective-C encapsulados pelo núcleo do xamarin. IOS e qualificar totalmente seus nomes de tipo.

No entanto, o gerador de código no momento, não é possível resolver tipos de CLR de nomes de tipo de Objective-C no código do usuário ou bibliotecas, portanto, nesses casos, ele gera o nome do tipo textual. Isso significa que o tipo CLR correspondente deve ter o mesmo nome que o tipo de Objective-C e estar no mesmo namespace que o código que está sendo usado. Isso é planejado para ser corrigido em algum momento no futuro, levando em conta todos os tipos de Objective-C no projeto durante a geração de código.
