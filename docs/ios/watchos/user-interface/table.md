---
title: Controle de tabela
ms.topic: article
ms.prod: xamarin
ms.assetid: 7C14126D-9591-4387-A588-3C4521F11C55
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: c55ba4fb90181aaa1aa8ec52e2fcb3e2b2cc76d0
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2018
---
# <a name="table-control"></a>Controle de tabela

O watchOS `WKInterfaceTable` controle é muito mais simples do que a contraparte de iOS, mas executa uma função semelhante. Ele cria uma lista de rolagem de linhas que pode ter layouts personalizados e que respondem a eventos de toque.

![](table-images/table-list-sml.png "Lista de observação da tabela") ![](table-images/table-detail-sml.png)
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

## <a name="adding-a-table"></a>Adicionando uma tabela

Arraste o **tabela** controle em uma cena. Por padrão, ele será semelhante (mostrando um layout de única linha não especificado):

[![](table-images/add-table-sml.png "Adicionando uma tabela")](table-images/add-table.png#lightbox)

Dar um nome de tabela no **propriedades** do preenchimento **nome** caixa, para que ele pode ser chamado no código.

## <a name="add-a-row-controller"></a>Adicionar um controlador de linha

A tabela inclui automaticamente uma única linha, representada por um controlador de linha que contém um **grupo** controle por padrão.

Para definir o **classe** para o controlador de linha, selecione a linha no **esboço de documento** e digite um nome de classe no **propriedades** teclado:

[![](table-images/add-row-controller-sml.png "Inserir um nome de classe no painel de propriedades")](table-images/add-row-controller.png#lightbox)

Depois que a classe de controlador da linha é definida, o IDE criará um arquivo c# correspondente no projeto. Arraste os controles (como rótulos) para a linha e dar-lhes nomes para que eles podem ser referenciados em código.




## <a name="create-and-populate-rows"></a>Criar e preencher linhas

`SetNumberOfRows` cria classes do controlador de linha para cada linha, usando o `Identifier` para selecionar o correto. Se você deu seu controlador de linha um personalizado `Identifier`, alterar **padrão** no trecho de código abaixo para o identificador é usado. O `RowController` *para cada linha* é criado quando `SetNumberOfRows` é chamado e a tabela exibida.

```csharp
myTable.SetNumberOfRows ((nint)rows.Count, "default");
        // loads row controller by identifier
```

> [!IMPORTANT]
> Linhas da tabela não virtualizadas que estão em iOS. Tente limitar o número de linhas (Apple recomenda menos de 20).

Depois que as linhas foram criadas, você precisará preencher cada célula (como `GetCell` faria no iOS). Este trecho de código a partir de [WatchTables exemplo](https://developer.xamarin.com/samples/monotouch/watchOS/WatchTables/) atualiza o rótulo em cada linha

```csharp
for (var i = 0; i < rows.Count; i++) {
    var elementRow = (RowController)myTable.GetRowController (i);
    elementRow.myRowLabel.SetText (rows [i]);
}
```

> [!IMPORTANT]
> Usando `SetNumberOfRows` e, em seguida, um loop por meio do usando `GetRowController` faz com que toda a tabela a ser enviado para o relógio. Em exibições subsequentes da tabela, se você precisar adicionar ou remover linhas específicas usam `InsertRowsAt` e `RemoveRowsAt` para melhorar o desempenho.


## <a name="respond-to-taps"></a>Responder a toques

Você pode responder a seleção de linhas de duas maneiras diferentes:

- implementar o `DidSelectRow` método no controlador de interface, ou
- criar um segue no storyboard e implementar `GetContextForSegue` se você deseja que a seleção de linha para abrir outra cena.

### <a name="didselectrow"></a>DidSelectRow

Para tratar programaticamente a seleção de linha, implemente o `DidSelectRow` método. Para abrir uma nova cena, use `PushController` e passar o identificador da cena e o contexto de dados a ser usado:

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

Arraste um segue no storyboard de sua linha de tabela para outra cena (mantenha pressionada a **controle** chave ao arrastar).
Certifique-se de selecionar o segue e dê a ele um identificador **propriedades** teclado (como `secondLevel` no exemplo abaixo).

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

Esses dados são passados para a cena de storyboard de destino em seu `Awake` método.

## <a name="multiple-row-types"></a>Vários tipos de linha

Por padrão, o controle de tabela tem um tipo de linha única que você pode criar. Para adicionar mais linhas 'modelos' usam o **linhas** caixa o **propriedades** pad para criar mais controladores de linha:

![](table-images/prototype-rows1.png "Definindo o número de linhas de protótipo")

Definindo o **linhas** propriedade **3** criará espaços reservados de linha adicional para você arraste os controles no. Para cada linha, defina a **classe** nome o **propriedades** pad para assegurar que a classe do controlador de linha é criada.

![](table-images/prototype-rows2.png "As linhas de protótipo no designer")

Para popular uma tabela com o uso de tipos diferentes de linha de `SetRowTypes` método para especificar o tipo de controlador de linha a ser usado para cada linha na tabela. Use identificadores da linha para especificar qual controlador de linha deve ser usado para cada linha.

O número de elementos nesta matriz deve corresponder ao número de linhas que você pretende estar na tabela:

```csharp
myTable.SetRowTypes (new [] {"type1", "default", "default", "type2", "default"});
```

Ao popular uma tabela com vários controladores de linha, será necessário controlar o tipo esperado como popular a interface do usuário:

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

watchOS 3 introduziu um novo recurso para tabelas: a capacidade de percorrer as páginas de detalhes relacionados para cada linha, sem a necessidade de voltar à tabela e escolha outra linha. As telas de detalhes podem ser roladas passar o dedo para cima e para baixo, ou usando o coroa Digital.

![](table-images/table-scroll-sml.png "Exemplo de detalhes de paginação vertical") ![](table-images/table-detail-sml.png)

> [!IMPORTANT]
> Este recurso está apenas disponível ao editar o storyboard no construtor de Interface do Xcode.

Para habilitar esse recurso, selecione o `WKInterfaceTable` na superfície de design e escala de **paginação Vertical de detalhes** opção:

![](table-images/vertical-detail-paging-sml.png "A seleção da opção de paginação Vertical de detalhes")

Como [explicado pela Apple](https://developer.apple.com/reference/watchkit/wkinterfacetable#1682023) a navegação de tabela deve usar segues para o recurso de paginação trabalhar. Grave novamente qualquer código existente que usa `PushController` usar segues em vez disso.

<a name="add_row_controller" />

## <a name="appendix-row-controller-code-example"></a>Apêndice: Exemplo de código do controlador de linha

O IDE criará dois arquivos de código automaticamente quando um controlador de linha é criado no designer. O código nesses arquivos gerado é mostrado abaixo para referência.

O primeiro será nomeado para a classe, por exemplo **RowController.cs**, assim:

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

O outro **. designer.cs** arquivo é uma definição de classe parcial que contém as saídas e as ações que são criadas na superfície do designer, como neste exemplo com um `WKInterfaceLabel` controle:

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

As saídas e as ações declaradas aqui podem ser referenciadas no código, no entanto, a **. designer.cs** arquivo não deve ser editado diretamente.



## <a name="related-links"></a>Links relacionados

- [WatchTables (exemplo)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchTables/)
- [WatchKitCatalog (exemplo)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Doc de tabela da Apple](https://developer.apple.com/reference/watchkit/wkinterfacetable)
