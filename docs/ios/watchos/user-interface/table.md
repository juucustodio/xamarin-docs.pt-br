---
title: Controles de tabela watchOS no Xamarin
description: Este documento descreve como usar os controles de tabela watchOS no Xamarin. Ele aborda a adição de uma tabela, a adição de um controlador de linha, a criação e o preenchimento de linhas, a resposta a toques e muito mais.
ms.prod: xamarin
ms.assetid: 7C14126D-9591-4387-A588-3C4521F11C55
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 079c72ee25f3b01c02b854a8a1eb258c885f2a96
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68642799"
---
# <a name="watchos-table-controls-in-xamarin"></a>Controles de tabela watchOS no Xamarin

O controle `WKInterfaceTable` watchOS é muito mais simples do que sua contraparte do IOS, mas executa uma função semelhante. Ele cria uma lista de rolagem de linhas que pode ter layouts personalizados e que respondem a eventos de toque.

![](table-images/table-list-sml.png "Lista de tabelas de observação")![](table-images/table-detail-sml.png)
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

## <a name="adding-a-table"></a>Adicionando uma tabela

Arraste o controle **tabela** para uma cena. Por padrão, ele terá a seguinte aparência (mostrando um único layout de linha não especificado):

[![](table-images/add-table-sml.png "Adicionando uma tabela")](table-images/add-table.png#lightbox)

Dê um nome à tabela na caixa **nome** do painel de **Propriedades** para que ela possa ser referenciada no código.

## <a name="add-a-row-controller"></a>Adicionar um controlador de linha

A tabela inclui automaticamente uma única linha, representada por um controlador de linha que contém um controle de **grupo** por padrão.

Para definir a **classe** para o controlador de linha, selecione a linha na **estrutura de tópicos do documento** e digite um nome de classe no painel de **Propriedades** :

[![](table-images/add-row-controller-sml.png "Inserindo um nome de classe no painel de propriedades")](table-images/add-row-controller.png#lightbox)

Depois que a classe do controlador da linha for definida, o IDE criará um arquivo C# correspondente no projeto. Arraste os controles (como rótulos) para a linha e dê a eles nomes para que eles possam ser referenciados no código.




## <a name="create-and-populate-rows"></a>Criar e preencher linhas

`SetNumberOfRows`cria as classes de controlador de linha para cada linha, `Identifier` usando o para selecionar a correta. Se você deu ao controlador de linha um `Identifier`personalizado, altere **padrão** no trecho de código abaixo para o identificador usado. O `RowController` *para cada linha* é criado quando `SetNumberOfRows` é chamado e a tabela é exibida.

```csharp
myTable.SetNumberOfRows ((nint)rows.Count, "default");
        // loads row controller by identifier
```

> [!IMPORTANT]
> As linhas da tabela não são virtualizadas, como estão no iOS. Tente limitar o número de linhas (a Apple recomenda menos de 20).

Depois que as linhas tiverem sido criadas, você precisará preencher cada célula ( `GetCell` como faria no IOS). Este trecho de código do [exemplo WatchTables](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchtables) atualiza o rótulo em cada linha

```csharp
for (var i = 0; i < rows.Count; i++) {
    var elementRow = (RowController)myTable.GetRowController (i);
    elementRow.myRowLabel.SetText (rows [i]);
}
```

> [!IMPORTANT]
> Usar `SetNumberOfRows` e, em seguida, fazer `GetRowController` o loop por meio de using faz com que toda a tabela seja enviada para o relógio. Nas exibições subsequentes da tabela, se você precisar adicionar ou remover as linhas específicas `InsertRowsAt` , `RemoveRowsAt` use e para obter um melhor desempenho.


## <a name="respond-to-taps"></a>Responder a toques

Você pode responder à seleção de linha de duas maneiras diferentes:

- implemente `DidSelectRow` o método no controlador de interface ou
- Crie um transição no storyboard e implemente `GetContextForSegue` se você deseja que a seleção de linha abra outra cena.

### <a name="didselectrow"></a>DidSelectRow

Para manipular de forma programática a seleção de `DidSelectRow` linha, implemente o método. Para abrir uma nova cena, use `PushController` e passe o identificador da cena e o contexto de dados a ser usado:

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

Arraste um transição no storyboard da linha da tabela para outra cena (mantenha pressionada a tecla **Control** enquanto arrasta).
Certifique-se de selecionar o transição e atribuir a ele um identificador no painel de **Propriedades** ( `secondLevel` como no exemplo abaixo).

No controlador de interface, implemente `GetContextForSegue` o método e retorne o contexto de dados que deve ser fornecido para a cena que é apresentada pelo transição.

```csharp
public override NSObject GetContextForSegue (string segueIdentifier, WKInterfaceTable table, nint rowIndex)
{
    if (segueIdentifier == "secondLevel") {
        return new NSString (rows[(int)rowIndex]);
    }
    return null;
}
```

Esses dados são passados para a cena de storyboard de destino `Awake` em seu método.

## <a name="multiple-row-types"></a>Vários tipos de linha

Por padrão, o controle tabela tem um único tipo de linha que você pode criar. Para adicionar mais ' modelos ' de linha, use a caixa **linhas** no painel de **Propriedades** para criar mais controladores de linha:

![](table-images/prototype-rows1.png "Definindo o número de linhas de protótipo")

Definir a propriedade **Rows** como **3** criará espaços reservados de linha adicionais para você arrastar os controles para o. Para cada linha, defina o nome da **classe** no painel de **Propriedades** para garantir que a classe do controlador de linha seja criada.

![](table-images/prototype-rows2.png "As linhas do protótipo no designer")

Para preencher uma tabela com tipos de linha diferentes, `SetRowTypes` use o método para especificar o tipo de controlador de linha a ser usado para cada linha na tabela. Use os identificadores da linha para especificar qual controlador de linha deve ser usado para cada linha.

O número de elementos nesta matriz deve corresponder ao número de linhas que você espera que estejam na tabela:

```csharp
myTable.SetRowTypes (new [] {"type1", "default", "default", "type2", "default"});
```

Ao preencher uma tabela com vários controladores de linha, você precisará controlar qual tipo você espera ao preencher a interface do usuário:

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


## <a name="vertical-detail-paging"></a>Paginação de detalhes vertical

o watchOS 3 introduziu um novo recurso para tabelas: a capacidade de percorrer as páginas de detalhes relacionadas a cada linha, sem precisar voltar para a tabela e escolher outra linha. As telas de detalhes podem ser roladas passando o dedo para cima e para baixo, ou usando o Digital Crown.

![]Exemplo de paginação de (table-images/table-scroll-sml.png "detalhes vertical")![](table-images/table-detail-sml.png)

> [!IMPORTANT]
> Atualmente, esse recurso só está disponível com a edição do storyboard no Xcode Interface Builder.

Para habilitar esse recurso, selecione o `WKInterfaceTable` na superfície de design e marque a opção de paginação de **detalhes vertical** :

![](table-images/vertical-detail-paging-sml.png "Selecionando a opção de paginação de detalhes vertical")

Conforme [explicado pela Apple,](https://developer.apple.com/reference/watchkit/wkinterfacetable#1682023) a navegação de tabela deve usar continuações para que o recurso de paginação funcione. Reescreva qualquer código existente que use `PushController` para usar continuações em vez disso.

<a name="add_row_controller" />

## <a name="appendix-row-controller-code-example"></a>Apêndice: Exemplo de código do controlador de linha

O IDE criará automaticamente dois arquivos de código quando um controlador de linha for criado no designer. O código nesses arquivos gerados é mostrado abaixo para referência.

O primeiro será nomeado para a classe, por exemplo, **RowController.cs**, desta forma:

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

O outro arquivo **. designer.cs** é uma definição de classe parcial que contém as saídas e as ações que são criadas na superfície do designer, como este exemplo com um `WKInterfaceLabel` controle:

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

As saídas e ações declaradas aqui podem ser referenciadas no código, no entanto, o arquivo **. designer.cs** não deve ser editado diretamente.



## <a name="related-links"></a>Links relacionados

- [WatchTables (amostra)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchtables)
- [WatchKitCatalog (amostra)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Doc de tabela da Apple](https://developer.apple.com/reference/watchkit/wkinterfacetable)
