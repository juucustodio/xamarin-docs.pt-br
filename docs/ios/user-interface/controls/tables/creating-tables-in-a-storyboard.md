---
title: Como trabalhar com tabelas no iOS Designer
description: Nas seções anteriores, exploramos o desenvolvimento usando tabelas. Nesta, a quinta e última seção, agregaremos o que aprendemos até agora e criaremos um aplicativo de lista de tarefas básicas usando um Storyboard.
ms.prod: xamarin
ms.assetid: D8416E10-481A-0B6E-4081-B146E6358004
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 57347336bb91757c9c54f7279f386f15e07c9cd7
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84573587"
---
# <a name="working-with-tables-in-the-ios-designer"></a>Como trabalhar com tabelas no iOS Designer

Os storyboards são uma maneira WYSIWYG de criar aplicativos iOS e têm suporte no Visual Studio no Mac e no Windows. Para obter mais informações sobre storyboards, consulte o documento [introdução ao storyboards](~/ios/user-interface/storyboards/index.md) . Os storyboards também permitem que você edite os layouts de célula *na* tabela, o que simplifica o desenvolvimento com tabelas e células

Ao configurar as propriedades de uma exibição de tabela no designer do iOS, há dois tipos de conteúdo de célula que você pode escolher: conteúdo de protótipo **dinâmico** ou **estático** .

<a name="Prototype_Content"></a>

## <a name="dynamic-prototype-content"></a>Conteúdo de protótipo dinâmico

R `UITableView` com conteúdo de protótipo normalmente é destinado a exibir uma lista de dados em que a célula de protótipo (ou células, como você pode definir mais de um) são reutilizadas para cada item na lista. As células não precisam ser instanciadas, elas são obtidas no `GetView` método chamando o `DequeueReusableCell` método de seu `UITableViewSource` .

 <a name="Static_Content"></a>

## <a name="static-content"></a>Conteúdo estático

`UITableView`s com conteúdo estático permitem que as tabelas sejam projetadas diretamente na superfície de design. As células podem ser arrastadas para a tabela e personalizadas alterando as propriedades e adicionando controles.

 <a name="Creating_a_Storyboard-driven_app"></a>

## <a name="creating-a-storyboard-driven-app"></a>Criando um aplicativo controlado por storyboard

O exemplo de Storyboardtable contém um aplicativo mestre-Detail simples que usa os dois tipos de UITableView em um Storyboard. O restante desta seção descreve como criar um exemplo de lista de tarefas pendentes que terá a seguinte aparência quando for concluído:

 [![Exemplos de telas](creating-tables-in-a-storyboard-images/image13a.png)](creating-tables-in-a-storyboard-images/image13a.png#lightbox)

A interface do usuário será criada com um storyboard e as duas telas usarão um UITableView. A tela principal usa *conteúdo de protótipo* para fazer layout da linha e a tela de detalhes usa *conteúdo estático* para criar um formulário de entrada de dados usando layouts de célula personalizados.

## <a name="walkthrough"></a>Passo a passo

Criar uma nova solução no Visual Studio usando **(criar) novo projeto... > aplicativo de exibição única (C#)** e chamá-lo de _StoryboardTables_.

 [![Caixa de diálogo criar um novo projeto](creating-tables-in-a-storyboard-images/npd.png)](creating-tables-in-a-storyboard-images/npd.png#lightbox)

A solução será aberta com alguns arquivos C# e um `Main.storyboard` arquivo já criado. Clique duas vezes no `Main.storyboard` arquivo para abri-lo no designer do Ios.

<a name="Modifying_the_Storyboard"></a>

## <a name="modifying-the-storyboard"></a>Modificando o storyboard

O storyboard será editado em três etapas:

- Primeiro, o layout dos controladores de exibição necessários e a definição de suas propriedades.
- Em segundo lugar, crie sua interface do usuário arrastando e soltando objetos no modo de exibição
- Por fim, adicione a classe UIKit necessária a cada exibição e dê um nome a vários controles para que eles possam ser referenciados no código.

Quando o storyboard estiver completo, o código poderá ser adicionado para fazer tudo funcionar.

<a name="Layout_The_View_Controllers"></a>

### <a name="layout-the-view-controllers"></a>Layout dos controladores de exibição

A primeira alteração no storyboard é excluir a exibição de detalhes existente e substituí-la por um UITableViewController. Siga estas etapas:

1. Selecione a barra na parte inferior do controlador de exibição e a exclua.
2. Arraste um **controlador de navegação** e um **controlador de exibição de tabela** para o storyboard da caixa de ferramentas. 
3. Crie um transição do controlador de exibição raiz para o segundo controlador de exibição de tabela que acabou de ser adicionado. Para criar o transição, controle + arraste *da célula de detalhes* para o UITableViewController recém-adicionado. Escolha a opção **Mostrar** em **transição seleção**. 
4. Selecione o novo transição que você criou e dê a ele um identificador para fazer referência a esse transição no código. Clique em transição e insira `TaskSegue` para o **identificador** no **painel de propriedades**, da seguinte maneira:    
  [![Nomeando transição no painel de propriedades](creating-tables-in-a-storyboard-images/image16a-sml.png)](creating-tables-in-a-storyboard-images/image16a.png#lightbox) 

5. Em seguida, configure os dois modos de exibição de tabela selecionando-os e usando o Painel de Propriedades. Certifique-se de selecionar Exibir e não exibir controlador – você pode usar a estrutura de tópicos do documento para ajudar com a seleção.

6. Altere o controlador de exibição raiz para ser **conteúdo: protótipos dinâmicos** (a exibição no design Surface será rotulada como **conteúdo de protótipo** ):

    [![Definindo a propriedade Content como protótipos dinâmicos](creating-tables-in-a-storyboard-images/image17a.png)](creating-tables-in-a-storyboard-images/image17a.png#lightbox)

7. Altere o novo **UITableViewController** para ser **Content: células estáticas**. 

8. O novo UITableViewController deve ter seu nome de classe e o conjunto de identificadores. Selecione o controlador de exibição e digite _TaskDetailViewController_ para a **classe** no **painel de propriedades** – isso criará um novo `TaskDetailViewController.cs` arquivo na painel de soluções. Insira o **storyboardid** como _detalhe_, conforme ilustrado no exemplo a seguir. Isso será usado posteriormente para carregar essa exibição em código C#:  

    [![Definindo a ID do storyboard](creating-tables-in-a-storyboard-images/image18a.png)](creating-tables-in-a-storyboard-images/image18a.png#lightbox)

9. A superfície de design do storyboard agora deve ser parecida com esta (o título do item de navegação do controlador de exibição raiz foi alterado para "painel de tarefas"):

    [![Superfície de design](creating-tables-in-a-storyboard-images/image20a-sml.png)](creating-tables-in-a-storyboard-images/image20a.png#lightbox)  

<a name="Create_the_UI"></a>

### <a name="create-the-ui"></a>Criar a interface do usuário

Agora que os modos de exibição e continuações estão configurados, os elementos da interface do usuário precisam ser adicionados.

#### <a name="root-view-controller"></a>Controlador de exibição de raiz

Primeiro, selecione a célula de protótipo no controlador de modo de exibição mestre e defina o **identificador** como _taskcell_, conforme ilustrado abaixo. Isso será usado posteriormente no código para recuperar uma instância desse UITableViewCell:

 [![definindo o identificador de célula](creating-tables-in-a-storyboard-images/image22a-sml.png)](creating-tables-in-a-storyboard-images/image22a.png#lightbox)

Em seguida, você precisará criar um botão que adicionará novas tarefas, conforme ilustrado abaixo:

[![item do botão de barra na barra de navegação](creating-tables-in-a-storyboard-images/image23-sml.png)](creating-tables-in-a-storyboard-images/image23.png#lightbox)

Faça o seguinte: 

- Arraste um **item de botão de barra** da caixa de ferramentas para o _lado direito da barra de navegação_.
- Na **painel de propriedades**, em **item do botão de barra** , selecione **identificador: Adicionar** (para torná-lo um *+* botão de adição). 
- Dê um nome a ele para que ele possa ser identificado no código em um estágio posterior. Observe que você precisará dar ao controlador de exibição raiz um nome de classe (por exemplo, **ItemViewController**) para permitir que você defina o nome do item do botão de barra.

#### <a name="taskdetail-view-controller"></a>Controlador de exibição TaskDetail

A exibição de detalhes requer muito mais trabalho. As células da exibição de tabela precisam ser arrastadas para a exibição e, em seguida, preenchidas com rótulos, exibições de texto e botões. A captura de tela abaixo mostra a interface do usuário concluída com duas seções. Uma seção tem três células, três rótulos, dois campos de texto e um comutador, enquanto a segunda seção tem uma célula com dois botões:

 [![layout de exibição de detalhes](creating-tables-in-a-storyboard-images/image24a-sml.png)](creating-tables-in-a-storyboard-images/image24a.png#lightbox)

As etapas para criar o layout completo são:

Selecione o modo de exibição de tabela e abra o **painel de propriedades**. Atualize as seguintes propriedades:

- **Seções**: _2_ 
- **Estilo**: _agrupado_
- **Separador**: _nenhum_
- **Seleção**: _nenhuma seleção_

Selecione a seção superior e, em **propriedades > seção exibição de tabela** , altere as **linhas** para _3_, conforme ilustrado abaixo:

 [![definindo a seção superior como três linhas](creating-tables-in-a-storyboard-images/image29-sml.png)](creating-tables-in-a-storyboard-images/image29.png#lightbox)

Para cada célula, abra o **painel de propriedades** e defina:

- **Estilo**: _personalizado_
- **Identificador**: escolha um identificador exclusivo para cada célula (por exemplo, "_título_", "_notas_", "_concluído_").
- Arraste os controles necessários para produzir o layout mostrado na captura de tela (Coloque **UILabel**, **UITextField** e **UISwitch** nas células corretas e defina os rótulos adequadamente, ou seja, o IE. Título, observações e concluídos).

Na segunda seção, defina **linhas** como _1_ e pegue a alça de redimensionamento inferior da célula para torná-la mais alta.

- **Defina o identificador**: como um valor exclusivo (por exemplo, "salvar"). 
- **Defina o plano de fundo**: _limpar cor_ .
- Arraste dois botões para a célula e defina seus títulos adequadamente (ou seja, _salvar_ e _excluir_), conforme ilustrado abaixo:

   [![definindo dois botões na seção inferior](creating-tables-in-a-storyboard-images/image30-sml.png)](creating-tables-in-a-storyboard-images/image30.png#lightbox)

Neste ponto, você também pode querer definir restrições em suas células e controles para garantir um layout adaptável.

### <a name="adding-uikit-class-and-naming-controls"></a>Adicionando a classe UIKit e os controles de nomenclatura

Há algumas etapas finais na criação de nosso Storyboard. Primeiro, devemos dar a cada um de nossos controles um nome com **identidade > nome** para que possam ser usados em código posteriormente. Nomeie-os da seguinte maneira:

- **Título UITextField** : _TitleText_
- **Observações UITextField** : _NotesText_
- **UISwitch** : _DoneSwitch_
- **Excluir UIButton** : _DeleteButton_
- **Salvar UIButton** : _SaveButton_

<a name="Adding_Code"></a>

## <a name="adding-code"></a>Adicionando código

O restante do trabalho será feito no Visual Studio no Mac ou no Windows com C#. Observe que os nomes de propriedade usados no código refletem aqueles definidos no passo a passos acima.

Primeiro, desejamos criar uma `Chores` classe, que fornecerá uma maneira de obter e definir o valor de ID, nome, observações e o booliano concluído, para que possamos usar esses valores em todo o aplicativo.

Em sua `Chores` classe, adicione o seguinte código:

```csharp
public class Chores {
    public int Id { get; set; }
    public string Name { get; set; }
    public string Notes { get; set; }
    public bool Done { get; set; }
  }
```

Em seguida, crie uma `RootTableSource` classe que herde de `UITableViewSource` . 

A diferença entre isso e uma exibição de tabela não-Storyboard é que o `GetView` método não precisa instanciar nenhuma célula – o `theDequeueReusableCell` método sempre retornará uma instância da célula de protótipo (com identificador correspondente).

O código a seguir é do `RootTableSource.cs` arquivo:

```csharp
public class RootTableSource : UITableViewSource
{
// there is NO database or storage of Tasks in this example, just an in-memory List<>
Chores[] tableItems;
string cellIdentifier = "taskcell"; // set in the Storyboard

    public RootTableSource(Chores[] items)
    {
        tableItems = items;
    }

public override nint RowsInSection(UITableView tableview, nint section)
{
  return tableItems.Length;
}

public override UITableViewCell GetCell(UITableView tableView, NSIndexPath indexPath)
{
  // in a Storyboard, Dequeue will ALWAYS return a cell, 
  var cell = tableView.DequeueReusableCell(cellIdentifier);
  // now set the properties as normal
  cell.TextLabel.Text = tableItems[indexPath.Row].Name;
  if (tableItems[indexPath.Row].Done)
    cell.Accessory = UITableViewCellAccessory.Checkmark;
  else
    cell.Accessory = UITableViewCellAccessory.None;
  return cell;
}
public Chores GetItem(int id)
{
  return tableItems[id];
}
```

Para usar a `RootTableSource` classe, crie uma nova coleção no `ItemViewController` Construtor de:

```csharp
chores = new List<Chore> {
      new Chore {Name="Groceries", Notes="Buy bread, cheese, apples", Done=false},
      new Chore {Name="Devices", Notes="Buy Nexus, Galaxy, Droid", Done=false}
    };
```

Em `ViewWillAppear` passe a coleção para a origem e atribua à exibição de tabela:

```csharp
public override void ViewWillAppear(bool animated)
{
    base.ViewWillAppear(animated);

    TableView.Source = new RootTableSource(chores.ToArray());
}
```

Se você executar o aplicativo agora, a tela principal agora será carregada e exibirá uma lista de duas tarefas. Quando uma tarefa é tocada, o transição definido pelo storyboard fará com que a tela de detalhes apareça, mas não exibirá nenhum dado no momento.

Para ' enviar um parâmetro ' em um transição, substitua o `PrepareForSegue` método e defina as propriedades em `DestinationViewController` (o `TaskDetailViewController` neste exemplo). A classe do controlador de exibição de destino terá sido instanciada, mas ainda não foi exibida para o usuário – isso significa que você pode definir propriedades na classe, mas não modificar nenhum controle de interface do usuário:

```csharp
public override void PrepareForSegue (UIStoryboardSegue segue, NSObject sender)
    {
      if (segue.Identifier == "TaskSegue") { // set in Storyboard
        var navctlr = segue.DestinationViewController as TaskDetailViewController;
        if (navctlr != null) {
          var source = TableView.Source as RootTableSource;
          var rowPath = TableView.IndexPathForSelectedRow;
          var item = source.GetItem(rowPath.Row);
          navctlr.SetTask (this, item); // to be defined on the TaskDetailViewController
        }
      }
    }
```

No `TaskDetailViewController` `SetTask` método, atribui seus parâmetros às propriedades para que eles possam ser referenciados em ViewWillAppear. As propriedades de controle não podem ser modificadas em `SetTask` porque pode não existir quando `PrepareForSegue` é chamada:

```csharp
Chore currentTask {get;set;}
    public ItemViewController Delegate {get;set;} // will be used to Save, Delete later

public override void ViewWillAppear (bool animated)
    {
      base.ViewWillAppear (animated);
      TitleText.Text = currentTask.Name;
      NotesText.Text = currentTask.Notes;
      DoneSwitch.On = currentTask.Done;
    }

    // this will be called before the view is displayed
    public void SetTask (ItemViewController d, Chore task) {
      Delegate = d;
      currentTask = task;
    }
```

Agora, o transição abrirá a tela de detalhes e exibirá as informações da tarefa selecionada. Infelizmente, não há nenhuma implementação para os botões **salvar** e **excluir** . Antes de implementar os botões, adicione esses métodos ao **ItemViewController.cs** para atualizar os dados subjacentes e feche a tela de detalhes:

```csharp
public void SaveTask(Chores chore)
{
  var oldTask = chores.Find(t => t.Id == chore.Id);
        NavigationController.PopViewController(true);
}

public void DeleteTask(Chores chore)
{
  var oldTask = chores.Find(t => t.Id == chore.Id);
  chores.Remove(oldTask);
        NavigationController.PopViewController(true);
}
```

Em seguida, você precisará adicionar o manipulador de `TouchUpInside` eventos do botão ao `ViewDidLoad` método de **TaskDetailViewController.cs**. A `Delegate` referência de propriedade para o `ItemViewController` foi criada especificamente para que possamos chamar `SaveTask` e `DeleteTask` , que fecha esta exibição como parte de sua operação:

```csharp
SaveButton.TouchUpInside += (sender, e) => {
        currentTask.Name = TitleText.Text;
        currentTask.Notes = NotesText.Text;
        currentTask.Done = DoneSwitch.On;
        Delegate.SaveTask(currentTask);
      };

DeleteButton.TouchUpInside += (sender, e) => Delegate.DeleteTask(currentTask);
```

A última parte da funcionalidade restante a ser compilada é a criação de novas tarefas. Em **ItemViewController.cs** , adicione um método que cria novas tarefas e abre a exibição de detalhes. Para instanciar uma exibição de um storyboard, use o `InstantiateViewController` método com o `Identifier` para essa exibição-neste exemplo que será ' Detail ':

```csharp
public void CreateTask () 
    {
      // first, add the task to the underlying data
      var newId = chores[chores.Count - 1].Id + 1;
      var newChore = new Chore{Id = newId};
      chores.Add (newChore);

      // then open the detail view to edit it
      var detail = Storyboard.InstantiateViewController("detail") as TaskDetailViewController;
      detail.SetTask (this, newChore);
      NavigationController.PushViewController (detail, true);
    }
```

Por fim, conecte o botão na barra de navegação no método do **ItemViewController.cs** `ViewDidLoad` para chamá-lo:

```csharp
AddButton.Clicked += (sender, e) => CreateTask ();
```

Isso conclui o exemplo de storyboard – o aplicativo concluído tem a seguinte aparência:

[![Aplicativo concluído](creating-tables-in-a-storyboard-images/image28a.png)](creating-tables-in-a-storyboard-images/image28a.png#lightbox)

O exemplo demonstra:

- Criando uma tabela com conteúdo de protótipo, em que as células são definidas para reutilização para exibir listas de dados. 
- Criar uma tabela com conteúdo estático para criar um formulário de entrada. Isso inclui a alteração do estilo de tabela e a adição de seções, células e controles de interface do usuário. 
- Como criar um transição e substituir o `PrepareForSegue` método para notificar a exibição de destino de todos os parâmetros necessários. 
- Carregando exibições de storyboard diretamente com o `Storyboard.InstantiateViewController` método.

## <a name="related-links"></a>Links relacionados

- [Storyboardtable (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/storyboardtable)
- [Introdução ao Storyboards](~/ios/user-interface/storyboards/index.md)
