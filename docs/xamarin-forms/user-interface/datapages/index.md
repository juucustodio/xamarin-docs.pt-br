---
title: Xamarin. Forms DataSourceControl
description: Este artigo apresenta DataSourceControl xamarin. Forms, que fornecem uma API para rapidamente e facilmente associar uma fonte de dados para exibições predefinidas.
ms.prod: xamarin
ms.assetid: DF16EAEE-DB78-42CA-9C59-51D9D6CB6B95
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 2a74b636a41a72b26776157a774f0a33ef45a075
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38815881"
---
# <a name="xamarinforms-datapages"></a>Xamarin. Forms DataSourceControl

![](~/media/shared/preview.png "Essa API está atualmente em versão prévia")

> [!IMPORTANT]
> DataSourceControl requer uma [xamarin. Forms tema](~/xamarin-forms/user-interface/themes/index.md) referência ao renderizar.

Xamarin. Forms DataSourceControl foram lançados no Evolve 2016 e estão disponível como uma visualização para que os clientes experimentar e fornecer comentários.

DataSourceControl fornece uma API para rápida e facilmente vincular uma fonte de dados para exibições predefinidas. Itens de lista e páginas de detalhes renderização automaticamente os dados e podem ser personalizadas usando temas.

Para ver como funciona a palestra de demonstração evoluem, confira a [guia de Introdução](get-started.md).

[![](images/demo-sml.png "Aplicativo de exemplo DataSourceControl")](images/demo.png#lightbox "DataSourceControl exemplo de aplicativo")

## <a name="introduction"></a>Introdução

Fontes de dados e as páginas de dados associadas permitem que os desenvolvedores rapidamente e facilmente consumir uma fonte de dados com suporte e renderizá-lo usando interno que o scaffolding de interface do usuário pode ser personalizado com temas.

DataSourceControl é adicionados a um aplicativo xamarin. Forms, incluindo o **Xamarin.Forms.Pages** pacote do Nuget.

### <a name="data-sources"></a>Data Sources

A visualização tem algumas fontes de dados predefinidos disponíveis para uso:

* **JsonDataSource**
* **AzureDataSource** (separe Nuget)
* **AzureEasyTableDataSource** (separe Nuget)

Consulte a [guia de Introdução](get-started.md) para obter um exemplo usando um `JsonDataSource`.


### <a name="pages--controls"></a>Páginas e controles

As seguintes páginas e controles estão incluídos para permitir a fácil associação às fontes de dados fornecido:

* **ListDataPage** – consulte a [Introdução ao exemplo](get-started.md).
* **DirectoryPage** – uma lista com o agrupamento habilitado.
* **PersonDetailPage** – um modo de exibição personalizado para um tipo de objeto específico (uma entrada de contato) de item de dados únicos.
* **DataView** – um modo de exibição para expor os dados da origem de uma forma genérica.
* **Widgets CardView** – um com o estilo de exibição que contém uma imagem, o texto do título e o texto de descrição.
* **HeroImage** – um modo de exibição de renderização de imagem.
* **ListItem** – um pré-criados em modo de exibição com um layout semelhante ao iOS nativo e itens de lista para Android.

Consulte a [referência de controles DataSourceControl](controls.md) para obter exemplos.



### <a name="under-the-hood"></a>Nos bastidores

Uma fonte de dados do xamarin. Forms segue o `IDataSource` interface.

A infraestrutura do xamarin. Forms interage com uma fonte de dados por meio das seguintes propriedades:

* `Data` – uma lista somente leitura de itens de dados que podem ser exibidos.
* `IsLoading` – um valor booliano que indica se os dados são carregados e estão disponíveis para renderização.
* `[key]` – um indexador para recuperar elementos.

Há dois métodos `MaskKey` e `UnmaskKey` que pode ser usado para ocultar (ou Mostrar) propriedades do item de dados (ie. impedi que está sendo processado).
A chave corresponde à uma propriedade nomeada no objeto de item de dados.
