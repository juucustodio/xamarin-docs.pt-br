---
title: Barras de pesquisa no xamarin
description: Este documento descreve como usar barras de pesquisa em xamarin. Ele discute como criar barras de pesquisa e programaticamente em um storyboard.
ms.prod: xamarin
ms.assetid: 22A8249A-19C6-4734-8331-E49FE3170771
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 07/11/2017
ms.openlocfilehash: cd78c58ecb119c437296a0befe1d319d8837edae
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34789919"
---
# <a name="search-bars-in-xamarinios"></a>Barras de pesquisa no xamarin

O UISearchBar é usado para pesquisar por meio de uma lista de valores. 

Ele contém três componentes principais: 

- Um campo usado para inserir texto. Os usuários podem usar essa opção para inserir seu termo de pesquisa.
- Um botão Limpar, remover qualquer texto do campo de pesquisa.
- Um botão Cancelar, para a função de pesquisa de saída.

![Barra de pesquisa](searchbar-images/image1.png)

## <a name="implementing-the-search-bar"></a>Implementando a barra de pesquisa

Para implementar o início da barra de pesquisa, criando um novo:

```csharp
searchBar = new UISearchBar();
```

E, em seguida, colocá-lo. O exemplo a seguir mostra como colocá-lo na barra de navegação ou na HeaderView de uma tabela:

```csharp
NavigationItem.TitleView = searchBar;

\\or

TableView.TableHeaderView = searchBar;
```

Definindo propriedades na barra de pesquisa:

```csharp
 searchBar = new UISearchBar(){
                Placeholder = "Enter your search Item",
                Prompt = "Search Entered here",
                ShowsScopeBar = true,
                ScopeButtonTitles = new string[]{ "Boston", "London", "SF" },
            };
```

![Propriedades da barra de pesquisa](searchbar-images/image6.png)

Gerar o `SearchButtonClicked` evento quando o botão de pesquisa é pressionado. Isso chamará sua lógica de pesquisa:

```csharp
searchBar.SearchButtonClicked += (sender, e) => {
                Search ();
            };
```

Para obter informações sobre como gerenciar a apresentação da barra de pesquisa e os resultados de pesquisa, consulte o [pesquisa controlador ](https://developer.xamarin.com/recipes/ios/content_controls/search-controller/) receita.

## <a name="using-the-search-bar-in-the-designer"></a>Usando a barra de pesquisa no Designer

O Designer oferece duas opções para a implementação de uma barra de pesquisa no designer

- Barra de pesquisa
- Barra de pesquisa com o controlador de exibição de pesquisa (preterido)

![Controles de barra de pesquisa no designer](searchbar-images/image2.png)

Use o painel de propriedade para definir propriedades na barra de pesquisa

![Designer de propriedades de barra de pesquisa](searchbar-images/image3.png)

Essas propriedades são explicadas abaixo:

- **Prompt de texto, o espaço reservado,** – essas propriedades são usadas para sugerir e instruir como os usuários devem usar a barra de pesquisa. Por exemplo, se seu aplicativo exibida uma lista de repositórios você pode usar a propriedade prompt para informar que os usuários podem "inserir uma cidade, nome de texto ou código postal"
- **Pesquisar estilo** – você pode definir a barra de pesquisa para ser **Prominent** ou **mínimo**. Usar o proeminentes será tonalizar tudo na tela, exceto para a pesquisa de barras, fazendo com que o foco seja desenhada para a barra de pesquisa. A barra de pesquisa de estilo mínimo serão mesclados com seu ambiente.
- **Recursos** – habilitar essas propriedades exibe apenas o elemento de interface do usuário. A funcionalidade deve ser implementada para esses, gerando o evento correto, conforme detalhado no [documentos de API de barra de pesquisa](https://developer.xamarin.com/api/type/UIKit.UISearchBar/)
    - Mostra os resultados da pesquisa / botão Marcadores – mostra um ícone de resultados de pesquisa ou indicadores na barra de pesquisa
    - Mostra o botão Cancelar – permite que os usuários para sair a função de pesquisa. É recomendável que essa opção é selecionada.
    - Mostra a barra de escopo – Isso permite que os usuários limitar o escopo da pesquisa. Por exemplo, ao pesquisar no aplicativo de música o usuário pode selecionar se desejam pesquisar Apple música ou sua biblioteca de música específica ou artista. Para exibir várias opções, adicione uma matriz de títulos para o **ScopeBarTitles** propriedade.
    ![Títulos de escopo de barra de pesquisa](searchbar-images/image4.png)

- **O comportamento do texto** – essas opções são usadas para tratar como a entrada do usuário é formatada quando eles são digitando. Capitalização definirá o início de cada palavra ou frase, ou todos os caracteres como letras maiusculas. Correção e verificação ortográfica com solicitam ao usuário grafias sugeridos de palavras como tipo por eles.
- **Teclado** – controla o estilo de teclado exibido para a entrada e, portanto, quais chaves estão disponíveis no teclado. Isso inclui o número preenchimento, preenchimento de telefone, Email, URL junto com outras opções.
- **Aparência** – controla o estilo de aparência do teclado e serão ambos escuro ou claro com tema.
- **Retorna a chave** – alterar o rótulo na chave de retorno para refletir melhor a ação que será executada. Valores com suporte incluem Go, junção, Avançar, rota, concluído e a pesquisa.
- **Proteger** – indica se a entrada é mascarada (como uma entrada de senha).

## <a name="related-links"></a>Links relacionados

- [Controlador de pesquisa](https://developer.xamarin.com/recipes/ios/content_controls/search-controller/)
