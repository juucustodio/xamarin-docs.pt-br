---
title: Barras de pesquisa no xamarin. IOS
description: Este documento descreve como usar as barras de pesquisa no xamarin. IOS. Ele aborda como criar barras de pesquisa por meio de programação e em um storyboard.
ms.prod: xamarin
ms.assetid: 22A8249A-19C6-4734-8331-E49FE3170771
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/11/2017
ms.openlocfilehash: 75abb943dbc56d7b4213e0c36c19ff338182ae8a
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67674868"
---
# <a name="search-bars-in-xamarinios"></a>Barras de pesquisa no xamarin. IOS

O UISearchBar é usado para pesquisar por meio de uma lista de valores. 

Ele contém três componentes principais: 

- Um campo usado para inserir texto. Os usuários podem utilizar essa opção para inserir seu termo de pesquisa.
- Um botão Limpar, remover qualquer texto do campo de pesquisa.
- Um botão Cancelar, para a função de pesquisa de sair.

![Barra de pesquisa](searchbar-images/image1.png)

## <a name="implementing-the-search-bar"></a>Implementando a barra de pesquisa

Para implementar o início da barra de pesquisa pela instanciação de um novo:

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

Para obter informações sobre como gerenciar a apresentação dos resultados da pesquisa e barra de pesquisa, consulte o [pesquisa controlador](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/search-controller) receita.

## <a name="using-the-search-bar-in-the-designer"></a>Usando a barra de pesquisa no Designer

O Designer oferece duas opções para a implementação de uma barra de pesquisa no designer

- Barra de pesquisa
- Barra de pesquisa com o controlador de exibição de pesquisa (preterido)

![Controles da barra de pesquisa no designer](searchbar-images/image2.png)

Use o painel de propriedade para definir propriedades na barra de pesquisa

![Designer de propriedades da barra de pesquisa](searchbar-images/image3.png)

Essas propriedades são explicadas abaixo:

- **Prompt de texto, o espaço reservado,** – essas propriedades são usadas para sugerir e instruir como os usuários devem usar a barra de pesquisa. Por exemplo, se seu aplicativo exibida uma lista de repositórios você poderia usar a propriedade prompt para aconselha que os usuários podem "inserir uma cidade, o nome de história ou o código postal"
- **Estilo de pesquisa** – você pode definir a barra de pesquisa para ser **Prominent** ou **mínimo**. Usar o destaque será tonalizar tudo na tela, exceto para a barra de pesquisa, fazendo com que o foco a ser desenhado à barra de pesquisa. A barra de pesquisa de estilo mínimo será mescla com seu ambiente.
- **Recursos** – permitindo que essas propriedades exibe apenas o elemento de interface do usuário. A funcionalidade deve ser implementada para eles, gerando o evento correto conforme detalhado no [documentos de API da barra de pesquisa](xref:UIKit.UISearchBar)
    - Mostra os resultados da pesquisa / indicadores botão – será exibido um ícone de resultados de pesquisa ou indicadores na barra de pesquisa
    - Mostra o botão Cancelar – permite que os usuários para sair a função de pesquisa. É recomendável que essa opção é selecionada.
    - Mostra a barra de escopo – Isso permite que os usuários limitar o escopo da pesquisa. Por exemplo, ao pesquisar no aplicativo de música o usuário pode selecionar se desejarem pesquisar Apple Music ou sua biblioteca para uma determinada música ou artista. Para exibir várias opções, adicione uma matriz de títulos para o **ScopeBarTitles** propriedade.
    ![Títulos de escopo de barra de pesquisa](searchbar-images/image4.png)

- **Comportamento de texto** – essas opções são usadas para tratar como a entrada do usuário é formatada quando eles estão digitando. Capitalização definirá o início de cada palavra ou frase, ou todos os caracteres como letras maiusculas. Correção e verificação ortográfica com solicitar que o usuário ortografias sugeridas de palavras enquanto digitam.
- **Teclado** – o estilo de teclado de controles exibidos para a entrada e, portanto, quais teclas estão disponíveis no teclado. Isso inclui o teclado de número, teclado do telefone, Email, URL junto com outras opções.
- **Aparência** – controla o estilo de aparência do teclado e serão ambos escuro ou tema de luz.
- **Retornar a chave** – alterar o rótulo em que a tecla Return para refletir melhor a ação que será executada. Valores com suporte incluem Go, junção, Avançar, rota, feito e pesquisa.
- **Proteger** – identifica se a entrada é mascarada (por exemplo, para uma entrada de senha).

## <a name="related-links"></a>Links relacionados

- [Controlador de pesquisa](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/search-controller)
