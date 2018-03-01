---
title: DataPages
ms.topic: article
ms.prod: xamarin
ms.assetid: DF16EAEE-DB78-42CA-9C59-51D9D6CB6B95
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 10c473656947dfea8bf832ae5a5704897ef4ce63
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="datapages"></a>DataPages

![](~/media/shared/preview.png "Esta API está atualmente em visualização")

> [!IMPORTANT]
> DataPages requer um [xamarin. Forms tema](~/xamarin-forms/user-interface/themes/index.md) referência para processar.

Xamarin. Forms DataPages foram lançados no Evolve 2016 e está disponível como uma visualização para os clientes tente e fornecer comentários.

DataPages fornecem uma API para rapidamente e facilmente associar uma fonte de dados para exibições predefinidas. Itens de lista e as páginas detalhes automaticamente processará os dados e podem ser personalizadas usando temas.

Para ver como funciona a Evolve palestra de demonstração, confira o [guia de Introdução](get-started.md).

[ ![](images/demo-sml.png "Aplicativo de exemplo DataPages")](images/demo.png "DataPages exemplo de aplicativo")

## <a name="introduction"></a>Introdução

Fontes de dados e as páginas de dados associadas permitem aos desenvolvedores rapidamente e facilmente consumir uma fonte de dados com suporte e renderizá-lo usando internas UI estrutura que pode ser personalizado com temas.

DataPages são adicionadas a um aplicativo xamarin. Forms, incluindo o **Xamarin.Forms.Pages** pacote Nuget.

### <a name="data-sources"></a>Data Sources

A visualização tem algumas fontes de dados predefinidos disponíveis para uso:

* **JsonDataSource**
* **AzureDataSource** (separe Nuget)
* **AzureEasyTableDataSource** (separate Nuget)

Consulte o [guia de Introdução](get-started.md) para um exemplo usando um `JsonDataSource`.


### <a name="pages--controls"></a>Páginas e controles

As seguintes páginas e controles estão incluídos para permitir a fácil associação às fontes de dados fornecido:

* **ListDataPage** – consulte a [Introdução exemplo](get-started.md).
* **DirectoryPage** – uma lista com o agrupamento ativado.
* **PersonDetailPage** – um modo de exibição personalizado para um tipo de objeto específico (uma entrada de contato) do item de dados único.
* **DataView** – um modo de exibição para expor dados de origem de forma genérica.
* **CardView** – um estilo de exibição que contém uma imagem, o texto do título e o texto de descrição.
* **HeroImage** – um modo de renderização de imagem.
* **ListItem** – um predefinidos em modo de exibição com um layout semelhante a itens de lista Android e iOS nativo.

Consulte o [DataPages controla referência](controls.md) para obter exemplos.



### <a name="under-the-hood"></a>Nos bastidores

Uma fonte de dados do xamarin. Forms cumpre a `IDataSource` interface.

A infraestrutura do xamarin. Forms interage com uma fonte de dados usando as seguintes propriedades:

* `Data` – uma lista de itens de dados que podem ser exibidos somente leitura.
* `IsLoading` – um valor booleano que indica se os dados são carregados e estão disponíveis para processamento.
* `[key]` – um indexador para recuperar elementos.

Há dois métodos `MaskKey` e `UnmaskKey` que pode ser usado para ocultar (ou Mostrar) propriedades de item de dados (ie. impedir que eles sejam renderizados).
A chave corresponde de uma propriedade nomeada no objeto de item de dados.

