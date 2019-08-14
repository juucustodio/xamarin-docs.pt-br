---
title: Páginas de DataPages do Xamarin. Forms
description: Este artigo apresenta as páginas de dados do Xamarin. Forms, que fornecem uma API para associar rapidamente e facilmente as exibições predefinidas.
ms.prod: xamarin
ms.assetid: DF16EAEE-DB78-42CA-9C59-51D9D6CB6B95
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 9dc34f412549c4be6099b373ddae7cbd2e8d21c8
ms.sourcegitcommit: 41a029c69925e3a9d2de883751ebfd649e8747cd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68980779"
---
# <a name="xamarinforms-datapages"></a>Páginas de DataPages do Xamarin. Forms

![](~/media/shared/preview.png "Essa API está atualmente em versão prévia")

> [!IMPORTANT]
> As páginas de datarequer uma referência de tema do Xamarin. Forms para renderizar. Isso envolve a instalação do pacote NuGet [xamarin. Forms. Theme. base](https://www.nuget.org/packages/Xamarin.Forms.Theme.Base/) em seu projeto, seguido pelos pacotes NuGet [xamarin. Forms. Theme. Light](https://www.nuget.org/packages/Xamarin.Forms.Theme.Light/) ou [Xamarin. Forms. Theme. Dark](https://www.nuget.org/packages/Xamarin.Forms.Theme.Dark/) .

As páginas de DataPages do Xamarin. Forms foram anunciadas em evolua 2016 e estão disponíveis como uma visualização para que os clientes tentem e forneçam comentários.

DataSourceControl fornece uma API para rápida e facilmente vincular uma fonte de dados para exibições predefinidas. Itens de lista e páginas de detalhes irão renderizar automaticamente os dados e podem ser personalizados usando temas.

Para ver como a demonstração da palestra de desenvolvimento funciona, confira o [Guia de introdução](get-started.md).

[![](images/demo-sml.png "Aplicativo de exemplo DataSourceControl")](images/demo.png#lightbox "DataSourceControl exemplo de aplicativo")

## <a name="introduction"></a>Introdução

As fontes de dados e as páginas de dados associadas permitem aos desenvolvedores consumir com rapidez e facilidade uma fonte de dados com suporte e renderizá-la usando a interface do usuário interna scaffolding que pode ser personalizada com temas.

As páginas de DataPage são adicionadas a um aplicativo Xamarin. Forms, incluindo o pacote NuGet **Xamarin. Forms. Pages** .

### <a name="data-sources"></a>Data Sources

A visualização tem algumas fontes de dados predefinidas disponíveis para uso:

* **JsonDataSource**
* **AzureDataSource** (NuGet separado)
* **AzureEasyTableDataSource** (NuGet separado)

Consulte o [Guia de introdução](get-started.md) para obter um exemplo usando `JsonDataSource`um.


### <a name="pages--controls"></a>Controles de & de páginas

As páginas e os controles a seguir estão incluídos para permitir a ligação fácil com as fontes de dados fornecidas:

* **ListDataPage** – consulte o [exemplo de introdução](get-started.md).
* **DirectoryPage** – uma lista com o agrupamento habilitado.
* **PersonDetailPage** – uma exibição de item de dados individual personalizada para um tipo de objeto específico (uma entrada de contato).
* **DataView** – uma exibição para expor dados da origem de maneira genérica.
* **CardView** – uma exibição com estilo que contém uma imagem, texto de título e texto de descrição.
* **HeroImage** – uma exibição de renderização de imagem.
* **ListItem** – uma exibição pré-criada com um layout semelhante aos itens nativos de lista de Ios e Android.

Consulte a [referência de controles](controls.md) de DataPages para obter exemplos.



### <a name="under-the-hood"></a>Nos bastidores

Uma fonte de dados Xamarin. Forms adere à `IDataSource` interface.

A infraestrutura do Xamarin. Forms interage com uma fonte de dados por meio das seguintes propriedades:

* `Data`– uma lista somente leitura de itens de dados que podem ser exibidos.
* `IsLoading`– um booliano que indica se os dados são carregados e estão disponíveis para renderização.
* `[key]`– um indexador para recuperar elementos.

Há dois métodos `MaskKey` e `UnmaskKey` que podem ser usados para ocultar (ou mostrar) Propriedades de item de dados (por ex. impedir que sejam renderizados).
A chave corresponde à propriedade nomeada no objeto de item de dados.
