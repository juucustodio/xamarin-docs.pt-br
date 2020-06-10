---
Título: " Xamarin.Forms DataPages" Descrição: "Este artigo apresenta Xamarin.Forms as páginas de dados, que fornecem uma API para associar de forma rápida e fácil as exibições predefinidas."
MS. Prod: xamarin MS. AssetID: DF16EAEE-DB78-42CA-9C59-51D9D6CB6B95 MS. Technology: xamarin-Forms autor: davidbritch MS. Author: dabritch MS. Date: 12/01/2017 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-datapages"></a>Xamarin.FormsPáginas de

![](~/media/shared/preview.png "This API is currently in preview")

> [!IMPORTANT]
> As páginas de datarequer uma Xamarin.Forms referência de tema para renderizar. Isso envolve a instalação do [ Xamarin.Forms . ](https://www.nuget.org/packages/Xamarin.Forms.Theme.Base/)O pacote NuGet do Theme. base em seu projeto, seguido pelo [ Xamarin.Forms . Theme. Light](https://www.nuget.org/packages/Xamarin.Forms.Theme.Light/) ou [ Xamarin.Forms . ](https://www.nuget.org/packages/Xamarin.Forms.Theme.Dark/)Os pacotes NuGet. Dark do tema.

Xamarin.FormsAs DataPages foram anunciadas em evolua 2016 e estão disponíveis como uma visualização para que os clientes tentem e forneçam comentários.

As páginas de dados fornecem uma API para associar de forma rápida e fácil uma fonte de dado a exibições predefinidas. Itens de lista e páginas de detalhes irão renderizar automaticamente os dados e podem ser personalizados usando temas.

Para ver como a demonstração da palestra de desenvolvimento funciona, confira o [Guia de introdução](get-started.md).

[![](images/demo-sml.png "DataPages Sample Application")](images/demo.png#lightbox "DataPages Sample Application")

## <a name="introduction"></a>Introdução

As fontes de dados e as páginas de dados associadas permitem aos desenvolvedores consumir com rapidez e facilidade uma fonte de dados com suporte e renderizá-la usando a interface do usuário interna scaffolding que pode ser personalizada com temas.

As páginas de DataPage são adicionadas a um Xamarin.Forms aplicativo, incluindo o ** Xamarin.Forms . **Pacote NuGet de páginas.

### <a name="data-sources"></a>Data Sources

A visualização tem algumas fontes de dados predefinidas disponíveis para uso:

* **JsonDataSource**
* **AzureDataSource** (NuGet separado)
* **AzureEasyTableDataSource** (NuGet separado)

Consulte o [Guia de introdução](get-started.md) para obter um exemplo usando um `JsonDataSource` .

### <a name="pages--controls"></a>Controles de & de páginas

As páginas e os controles a seguir estão incluídos para permitir a ligação fácil com as fontes de dados fornecidas:

* **ListDataPage** – consulte o [exemplo de introdução](get-started.md).
* **DirectoryPage** – uma lista com o agrupamento habilitado.
* **PersonDetailPage** – uma exibição de item de dados individual personalizada para um tipo de objeto específico (uma entrada de contato).
* **DataView** – uma exibição para expor dados da origem de maneira genérica.
* **CardView** – uma exibição com estilo que contém uma imagem, texto de título e texto de descrição.
* **HeroImage** – uma exibição de renderização de imagem.
* **ListItem** – uma exibição pré-criada com um layout semelhante aos itens nativos de lista de Ios e Android.

Consulte a [referência de controles de DataPages](controls.md) para obter exemplos.

### <a name="under-the-hood"></a>Nos bastidores

Uma Xamarin.Forms fonte de dados adere à `IDataSource` interface.

A Xamarin.Forms infraestrutura interage com uma fonte de dados por meio das seguintes propriedades:

* `Data`– uma lista somente leitura de itens de dados que podem ser exibidos.
* `IsLoading`– um booliano que indica se os dados são carregados e estão disponíveis para renderização.
* `[key]`– um indexador para recuperar elementos.

Há dois métodos `MaskKey` e `UnmaskKey` que podem ser usados para ocultar (ou mostrar) Propriedades de item de dados (por ex. impedir que sejam renderizados).
A chave corresponde à propriedade nomeada no objeto de item de dados.
