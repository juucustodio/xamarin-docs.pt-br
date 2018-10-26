---
title: Controles de tabela no Xamarin do watchOS
description: Este documento descreve como usar controles de tabela do watchOS no Xamarin. Ele aborda a adição de uma tabela, adicionando um controlador de linha, criando e populando linhas, respondendo a toques e muito mais.
ms.prod: xamarin
ms.assetid: 7C14126D-9591-4387-A588-3C4521F11C55
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: cd5e7299874bbfb1b652315a549b9d067d58e9a0
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50109387"
---
# <a name="watchos-table-controls-in-xamarin"></a>Controles de tabela no Xamarin do watchOS

WatchOS `WKInterfaceTable` controle é muito mais simples do que seu equivalente do iOS, mas realiza uma função semelhante. Ele cria uma lista de rolagem de linhas que pode ter layouts personalizados, e que respondem a eventos de toque.

![](table-images/table-list-sml.png "Lista de observação da tabela") ![](table-images/table-detail-sml.png)
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

## <a name="adding-a-table"></a>Adicionando uma tabela

Arraste o **tabela** controle em uma cena. Por padrão, ele será semelhante (mostrando um layout de única linha não especificado):

[![](table-images/add-table-sml.png "Adicionando uma tabela")](table-images/add-table.png#lightbox)

Dê um nome de tabela **propriedades** do painel **nome** caixa, para que ele pode ser chamado no código.

## <a name="add-a-row-controller"></a>Adicionar um controlador de linha

A tabela inclui automaticamente uma única linha, representada por um controlador de linha que contém um **grupo** controle por padrão.

Para definir a **classe** para o controlador de linha, selecione a linha na **Document Outline** e digite um nome de classe no **propriedades** painel:

[![](table-images/add-row-controller-sml.png "Inserir um nome de classe no painel de propriedades")](table-images/add-row-controller.png#lightbox)

Depois que a classe de controlador da linha é definida, o IDE criará um correspondente C# arquivo no projeto. Arraste os controles (como rótulos) para a linha e dar-lhes nomes para que eles podem ser referenciados no código.




## <a name="create-and-populate-rows"></a>Criar e preencher linhas

`SetNumberOfRows` cria classes do controlador de linha para cada linha, usando o `Identifier` para selecionar o correto. Se você atribuiu ao seu controlador de linha personalizado `Identifier`, altere **padrão** no trecho de código abaixo para o identificador usado. O `RowController` *para cada linha* é criado quando `SetNumberOfRows` é chamado e a tabela exibida.

```csharp
myTable.SetNumberOfRows ((nint)rows.Count, "default");
        // loads row controller by identifier
```

> [!IMPORTANT]
> Linhas da tabela não são virtualizadas, como eles estão no iOS. Tente limitar o número de linhas (a Apple recomenda menor que 20).

Depois que as linhas tiverem sido criadas, você precisará popular cada célula (como `GetCell` faria no iOS). Este trecho de código a partir de [WatchTables exemplo](https://developer.xamarin.com/samples/monotouch/watchOS/WatchTables/) atualiza o rótulo em cada linha

```csharp
for (var i = 0; i < rows.Count; i++) {
    var elementRow = (RowController)myTable.GetRowController (i);
    elementRow.myRowLabel.SetText (rows [i]);
}
```

> [!IMPORTANT]
> Usando o `SetNumberOfRows` e, em seguida, um loop por meio do usando `GetRowController` faz com que toda a tabela a ser enviado para o relógio. Em modos de exibição subsequentes da tabela, se você precisar adicionar ou remover linhas específicas usam `InsertRowsAt` e `RemoveRowsAt` para melhorar o desempenho.


## <a name="respond-to-taps"></a>Responder aos toques

Você pode responder a seleção de linhas de duas maneiras diferentes:

- implementar o `DidSelectRow` método no controlador de interface, ou
- criar um segue no storyboard e implementar `GetContextForSegue` se você deseja que a seleção de linha para abrir outro cena.

### <a name="didselectrow"></a>DidSelectRow

Para manipular programaticamente a seleção de linha, implementar o `DidSelectRow` método. Para abrir uma nova cena, use `PushController` e passar o identificador da cena e o contexto de dados a ser usado:

```csharp
public override void DidSelectRow (WKInterfaceTable table, nint rowIndex)
{
    var rowData = rows [(int)rowIndex];
    Console.WriteLine ("Row selected:" + rowData);
    // if selection should open a new scene
    PushController ("secondInterface", rows[(int)rowIndex]);
}
```

### <a name="getcontextforsegue"></a>GetContextForSegue

Arraste um segue no storyboard da sua linha de tabela para outra cena (mantenha pressionada a **controle** chave enquanto arrasta).
Certifique-se de selecionar o segue e dê a ele um identificador **propriedades** painel (tais como `secondLevel` no exemplo a seguir).

No controlador de interface, implemente o `GetContextForSegue` método e retornar o contexto de dados que deve ser fornecido para a cena que é apresentada pelo segue.

```csharp
public override NSObject GetContextForSegue (string segueIdentifier, WKInterfaceTable table, nint rowIndex)
{
    if (segueIdentifier == "secondLevel") {
        return new NSString (rows[(int)rowIndex]);
    }
    return null;
}
```

Estes dados são passados para a cena do storyboard de destino em seu `Awake` método.

## <a name="multiple-row-types"></a>Vários tipos de linha

Por padrão, o controle da tabela tem um tipo de linha única que pode ser criado. Para adicionar mais linhas 'modelos' usam o **linhas** caixa de **propriedades** pad para criar mais controladores de linha:

![](table-images/prototype-rows1.png "Definir o número de linhas de protótipo")

Definindo o **linhas** propriedade **3** criará os espaços reservados de linha adicional para que você arraste os controles em. Para cada linha, defina a **classe** nome na **propriedades** pad para garantir que a classe de controlador de linha é criada.

![](table-images/prototype-rows2.png "As linhas de protótipo no designer")

Para popular uma tabela com tipos diferentes de linha, use o `SetRowTypes` método para especificar o tipo de controlador de linha a ser usado para cada linha na tabela. Use identificadores da linha para especificar qual controlador de linha deve ser usado para cada linha.

O número de elementos nesta matriz deve corresponder ao número de linhas que você espera ser na tabela:

```csharp
myTable.SetRowTypes (new [] {"type1", "default", "default", "type2", "default"});
```

Ao popular uma tabela com vários controladores de linha, você precisará manter o controle de qual tipo de espera conforme você preencher a interface do usuário:

```csharp
for (var i = 0; i < rows.Count; i++) {
    if (i == 0) {
        var elementRow = (Type1RowController)myTable.GetRowController (i);
        // populate UI controls
    } else if (i == 3) {
        var elementRow = (Type2RowController)myTable.GetRowController (i);
        // populate UI controls
    } else {
        var elementRow = (DefaultRowController)myTable.GetRowController (i);
        // populate UI controls
    }
}
```


## <a name="vertical-detail-paging"></a>Paginação de detalhe vertical

watchOS 3 introduziu um novo recurso para tabelas: a capacidade de percorrer as páginas de detalhes relacionados a cada linha, sem precisar voltar para a tabela e escolha outra linha. As telas de detalhes podem ser roladas dedo para cima e para baixo, ou usando o Crown Digital.

![](table-images/table-scroll-sml.png "Exemplo de detalhes de paginação vertical") ![](table-images/table-detail-sml.png)

> [!IMPORTANT]
> Esse recurso está apenas disponível atualmente editando o storyboard no Xcode Interface Builder.

Para habilitar esse recurso, selecione o `WKInterfaceTable` na superfície de design e escala de **paginação Vertical de detalhes** opção:

![](table-images/vertical-detail-paging-sml.png "Selecionando a opção de paginação Vertical de detalhes")

Como [explicado pela Apple](https://developer.apple.com/reference/watchkit/wkinterfacetable#1682023) a navegação de tabela deve usar segues para o recurso de paginação trabalhar. Gravar novamente qualquer código existente que usa `PushController` usar segues em vez disso.

<a name="add_row_controller" />

## <a name="appendix-row-controller-code-example"></a>Apêndice: Exemplo de código de controlador de linha

O IDE criará dois arquivos de código automaticamente quando um controlador de linha é criado no designer. O código nesses arquivos gerado é mostrado abaixo para referência.

O primeiro será nomeado para a classe, por exemplo **RowController.cs**, semelhante a esta:

```csharp
using System;
using Foundation;

namespace WatchTablesExtension
{
    public partial class RowController : NSObject
    {
        public RowController ()
        {
        }
    }
}
```

A outra **. designer.cs** arquivo é uma definição de classe parcial que contém as saídas e ações que são criadas na superfície do designer, como no exemplo com um `WKInterfaceLabel` controle:

```csharp
using Foundation;
using System;
using System.CodeDom.Compiler;
using UIKit;

namespace WatchTables.OnWatchExtension
{
    [Register ("RowController")]
    partial class RowController
    {
        [Outlet]
        [GeneratedCode ("iOS Designer", "1.0")]
        public WatchKit.WKInterfaceLabel MyLabel { get; set; }

        void ReleaseDesignerOutlets ()
        {
            if (MyLabel != null) {
                MyLabel.Dispose ();
                MyLabel = null;
            }
        }
    }
}
```

As saídas e ações declaradas aqui, em seguida, podem ser referenciadas em código – no entanto, o **. designer.cs** arquivo não deve ser editado diretamente.



## <a name="related-links"></a>Links relacionados

- [WatchTables (amostra)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchTables/)
- [WatchKitCatalog (amostra)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Documento de tabela da Apple](https://developer.apple.com/reference/watchkit/wkinterfacetable)
