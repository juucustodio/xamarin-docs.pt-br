---
title: . xib geração de código no Xamarin. iOS
description: Este documento descreve como o Xamarin. iOS gera código para mapear arquivos. C#XIB para, tornando os controles visuais acessíveis programaticamente.
ms.prod: xamarin
ms.assetid: 365991A8-E07A-0420-D28E-BC4D32065E1A
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: f6218977e9ad0d4c396ef127c3c3ca53dc56d7d3
ms.sourcegitcommit: 52fb214c0e0243587d4e9ad9306b75e92a8cc8b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2020
ms.locfileid: "78292523"
---
# <a name="xib-code-generation-in-xamarinios"></a>. xib geração de código no Xamarin. iOS

> [!IMPORTANT]
> Este documento explica a integração de Visual Studio para Mac com o Interface Builder do Xcode apenas, pois as ações e as saídas não são usadas no Xamarin Designer para iOS. Para obter mais informações sobre o designer do iOS, consulte o documento do [designer do IOS](~/ios/user-interface/designer/index.md) .

A Apple Interface Builder Tool ("IB") pode ser usada para criar interfaces de usuário visualmente. As definições de interface criadas pelo IB são salvas em arquivos **. xib** . Widgets e outros objetos em arquivos **. xib** podem receber uma "identidade de classe", que pode ser um tipo personalizado definido pelo usuário. Isso permite que você personalize o comportamento dos widgets e escreva os widgets personalizados.

Essas classes de usuário normalmente são subclasses de classes de controlador de interface do usuário. Elas têm *saídas* (análogas a Propriedades) e *ações* (análogas a eventos) que podem ser conectadas a objetos de interface. Em tempo de execução, quando o arquivo IB é carregado, os objetos são criados, e as saídas e ações são conectadas a vários objetos de interface do usuário dinamicamente. Ao definir essas classes gerenciadas, você deve definir todas as ações e saídas para corresponder àquelas que a IB espera. Visual Studio para Mac usa um modelo semelhante a codebehind para simplificar isso. Isso é semelhante ao que o Xcode faz para o Objective-C, mas o modelo e as convenções de geração de código foram ajustados para serem mais familiarizados com os desenvolvedores do .NET.

No momento, não há suporte para o trabalho com arquivos **. xib** no Xamarin. Ios para Visual Studio.

## <a name="xib-files-and-custom-classes"></a>Arquivos. xib e classes personalizadas

Além de usar tipos existentes do Cocoa Touch, é possível definir tipos personalizados em arquivos **. xib** . Também é possível usar tipos que são definidos em outros arquivos **. xib** ou definidos puramente no C# código. Atualmente, o Interface Builder não está ciente dos detalhes dos tipos definidos fora do arquivo **. xib** atual, portanto, ele não os listará ou mostrará as ações e saídas personalizadas. A remoção dessa limitação está planejada para algum momento no futuro.

As classes personalizadas podem ser definidas em um arquivo **. xib** usando o comando "Adicionar subclasse" na guia "classes" de interface Builder. Nós nos referimos a isso como classes "codebehind". Se o arquivo **. xib** tiver um arquivo de contraparte ". xib.designer.cs" no projeto, Visual Studio para Mac irá preenchê-lo automaticamente com definições de classes parciais para todas as classes personalizadas em **. xib**. Nós nos referimos a essas classes parciais como "classes de designer".

## <a name="generating-code"></a>Gerando código

Para qualquer arquivo **{0}. xib** com uma ação de compilação de *página*, se um arquivo **{0}. xib.designer.cs** também existir no projeto, Visual Studio para Mac gerará classes parciais no arquivo de designer para todas as classes de usuário que ele pode encontrar no arquivo **. xib** , com propriedades para as saídas e métodos parciais para todas as ações. A geração de código é habilitada simplesmente pela presença desse arquivo.

O arquivo de designer é atualizado automaticamente quando o arquivo **. xib** é alterado e Visual Studio para Mac recupera o foco. O arquivo de designer não deve ser modificado manualmente, pois as alterações serão substituídas na próxima vez Visual Studio para Mac atualizar o arquivo.

## <a name="registration-and-namespaces"></a>Registro e namespaces

Visual Studio para Mac gera as classes de designer usando o namespace padrão do projeto para o local do arquivo de designer, para torná-lo consistente com o projeto normal do .NET namespacing. O namespace de arquivos de designer é orientado pelo "namespace padrão" do projeto e suas configurações de "políticas de nomenclatura do .NET". Saiba que, se o namespace padrão do seu projeto for alterado, MD irá regenerar as classes no novo namespace, de modo que você pode descobrir que suas classes parciais não correspondem mais.

Para tornar a classe detectável pelo tempo de execução Objective-C, Visual Studio para Mac aplica um atributo `[Register (name)]` à classe. Embora o Xamarin. iOS registre automaticamente classes derivadas de `NSObject`, ele usa os nomes .NET totalmente qualificados. O atributo aplicado por Visual Studio para Mac substitui isso para garantir que cada classe seja registrada com o nome usado no arquivo **. xib** . Se você usar classes personalizadas no IB sem usar Visual Studio para Mac para gerar arquivos de designer, talvez seja necessário aplicar isso manualmente para fazer com que as classes gerenciadas correspondam aos nomes de classe Objective-C esperados.

As classes não podem ser definidas em mais de um **. xib**ou entrarão em conflito.

## <a name="non-designer-class-parts"></a>Partes de classe que não são do designer

As classes parciais do designer não devem ser usadas no estado em que se encontram. As saídas são privadas e nenhuma classe base é especificada. Espera-se que cada classe tenha uma parte de classe "não-designer" correspondente em outro arquivo, que define a classe base, usa ou expõe as saídas e define construtores que são necessários para instanciar a classe a partir do código nativo ao carregar o **. xib**. Os modelos default **. xib** fazem isso, mas para quaisquer classes personalizadas adicionais que você definir em um **. xib**, você deve adicionar manualmente a parte que não é de designer.

A razão para isso é a necessidade de flexibilidade. Por exemplo, várias classes codebehind podiam fazer uma subclasse de uma classe abstrata gerenciada comum, que subclasse a classe a ser subclasse por IB.

É convencional colocá-los em um arquivo **{0}. xib.cs** ao lado do arquivo do designer **{0}. xib.designer.cs** .

<a name="generated" />

## <a name="generated-actions-and-outlets"></a>Ações geradas e saídas

Nas classes parciais do designer, Visual Studio para Mac gera propriedades correspondentes a quaisquer saídas conectadas definidas em IB, e métodos parciais correspondentes a quaisquer ações conectadas.

### <a name="outlet-properties"></a>Propriedades da tomada

As classes de designer contêm propriedades correspondentes a todas as saídas definidas na classe personalizada. O fato de que essas são propriedades são um detalhe de implementação do Xamarin. iOS para o objetivo da ponte C, para habilitar a vinculação lenta. Você deve considerá-los equivalentes a campos privados, destinados a serem usados somente da classe codebehind. Se você quiser torná-los públicos, adicione Propriedades de acessador à parte de classe que não seja do designer, como faria em qualquer outro campo privado.

Se as propriedades da tomada forem definidas para ter um tipo de `id` (equivalente a `NSObject`), o gerador de código do designer determinará atualmente o tipo mais forte possível com base em objetos conectados a essa tomada, por conveniência.
No entanto, isso pode não ter suporte em versões futuras, portanto, é recomendável que você explicitamente digite as saídas ao definir a classe personalizada.

### <a name="action-properties"></a>Propriedades da ação

As classes de designer contêm métodos parciais correspondentes a todas as ações definidas na classe personalizada. Esses são métodos sem implementação. A finalidade dos métodos parciais é dupla:

1. Se você digitar `partial` no corpo da classe da parte de classe que não é do designer, o Visual Studio para Mac oferecerá a conclusão automático das assinaturas de todos os métodos parciais não implementados.
2. As assinaturas de método parcial têm um atributo aplicado que as expõe ao mundo Objective-C, para que possam ser manipuladas como a ação correspondente.

Se desejar, você pode ignorar o método parcial e implementar a ação aplicando o atributo a um método diferente ou deixá-lo passar para uma classe base.

Se as ações forem definidas para ter um tipo de remetente de `id` (equivalente a `NSObject`), o gerador de código do designer determinará atualmente o tipo mais forte possível com base em objetos conectados a essa ação. No entanto, isso pode não ter suporte em versões futuras, portanto, é recomendável que você explicitamente digite as ações ao definir a classe personalizada.

Observe que esses métodos parciais são criados apenas C#para o, porque o CodeDOM não dá suporte a métodos parciais, portanto, eles não são gerados para outras linguagens.

## <a name="cross-xib-class-usage"></a>Uso de classe entre XIB

Às vezes, os usuários desejam referenciar a mesma classe de vários arquivos **. xib** , por exemplo, com os controladores de tabulação. Isso pode ser feito referenciando explicitamente a definição de classe de outro arquivo **. xib** ou definindo o mesmo nome de classe novamente no segundo **. xib**.

O último caso pode ser problemático devido à Visual Studio para Mac processamento de arquivos **. xib** individualmente. Ele não pode detectar e mesclar automaticamente definições duplicadas, portanto, você pode acabar com conflitos aplicando o atributo de registro várias vezes quando a mesma classe parcial é definida em vários arquivos de designer. As versões recentes do Visual Studio para Mac tentam resolver isso, mas nem sempre podem funcionar conforme o esperado. No futuro, isso provavelmente não terá suporte e, em vez disso Visual Studio para Mac tornará todos os tipos definidos em todos os arquivos **. xib** e código gerenciado no projeto visível diretamente de todos os arquivos **. xib** .

## <a name="type-resolution"></a>Resolução de tipo

Os tipos usados no IB são nomes de tipo Objective-C. Eles são mapeados para tipos CLR por meio do uso de atributos de registro. Ao gerar o código de tomada e ação, Visual Studio para Mac resolverá os tipos CLR correspondentes para todos os tipos Objective-C encapsulados pelo Xamarin. iOS Core e qualificará totalmente seus nomes de tipo.

No entanto, o gerador de código não pode resolver atualmente tipos CLR de nomes de tipo Objective-C em código de usuário ou bibliotecas, portanto, nesses casos, ele gera o nome de tipo textualmente. Isso significa que o tipo CLR correspondente deve ter o mesmo nome que o tipo Objective-C e estar no mesmo namespace que o código que o está usando. Isso está planejado para ser corrigido em algum momento no futuro Considerando todos os tipos Objective-C no projeto durante a geração de código.
