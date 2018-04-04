---
title: Trabalhando com tabelas no Designer de iOS
description: Nas seções anteriores, podemos explorados desenvolvendo com tabelas. Nesse ponto, a seção quinta e último, vamos agregar o que aprendeu até o momento e criar um aplicativo de lista básico usando um Storyboard.
ms.prod: xamarin
ms.assetid: D8416E10-481A-0B6E-4081-B146E6358004
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: 869719b1d3514fee4b45f61dcdb34a0bcf54f7b2
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="working-with-tables-in-the-ios-designer"></a>Trabalhando com tabelas no Designer de iOS

Storyboards são uma maneira WYSIWYG para criar aplicativos do iOS e têm suporte dentro do Visual Studio no Mac e Windows. Para obter mais informações sobre Storyboards, consulte o [Introdução a Storyboards](~/ios/user-interface/storyboards/index.md) documento. Storyboards também permitem que você edite os layouts de célula *em* a tabela, o que simplifica o desenvolvimento com tabelas e células

Ao configurar as propriedades de uma exibição de tabela no Designer do iOS, há dois tipos de conteúdo de célula, você pode escolher entre: **dinâmico** ou **estático** conteúdo de protótipo.

<a name="Prototype_Content" />

## <a name="dynamic-prototype-content"></a>Conteúdo de protótipo dinâmico

Um `UITableView` com protótipo conteúdo normalmente destina-se para exibir uma lista de dados onde a célula de protótipo (ou células, como você podem definir mais de um) são reutilizadas para cada item na lista. As células não precisam ser instanciado, eles são obtidos no `GetView` método chamando o `DequeueReusableCell` método de sua `UITableViewSource`.

 <a name="Static_Content" />


## <a name="static-content"></a>Conteúdo Estático

`UITableView`s com conteúdo estático permitir que as tabelas a ser criado diretamente na superfície de design. As células podem ser arrastadas para a tabela e personalizadas alterando propriedades e adicionar controles.

 <a name="Creating_a_Storyboard-driven_app" />


## <a name="creating-a-storyboard-driven-app"></a>Criar um aplicativo orientado para o Storyboard

O exemplo StoryboardTable contém um aplicativo mestre-detalhes simples que usa os dois tipos de UITableView em um Storyboard. O restante desta seção descreve como criar um exemplo de lista de tarefas pequeno terá esta aparência quando concluído:

 [![Telas de exemplo](creating-tables-in-a-storyboard-images/image13a.png)](creating-tables-in-a-storyboard-images/image13a.png#lightbox)

A interface do usuário será criada com um storyboard, e ambas as telas usará um UITableView. A tela principal usa *conteúdo de protótipo* para layout de linha e o detalhe da tela usa *conteúdo estático* para criar um formulário de entrada de dados usando layouts de célula personalizado.

## <a name="walkthrough"></a>Passo a passo

Criar uma nova solução no Visual Studio usando **projeto novo (criar)... > App(C#) de exibição única**e chamá-lo _StoryboardTables_.

 [![Criar uma caixa de diálogo Novo projeto](creating-tables-in-a-storyboard-images/npd.png)](creating-tables-in-a-storyboard-images/npd.png#lightbox)

A solução será aberto com alguns arquivos c# e um `Main.storyboard` arquivo já foi criado. Clique duas vezes o `Main.storyboard` arquivo para abri-lo no Designer de iOS.

<a name="Modifying_the_Storyboard" />

## <a name="modifying-the-storyboard"></a>Modificando o Storyboard

O storyboard será editado em três etapas:

-  Primeiro, layout necessário exibir controladores e definir suas propriedades.
-  Em seguida, criar sua interface do usuário arrastando e soltando objetos em seu modo de exibição
-  Finalmente, adicione a classe de UIKit necessária para cada modo de exibição e dê um nome de vários controles de forma que possam ser referenciados no código.


Quando o storyboard for concluído, o código pode ser adicionado para que tudo funcione.

<a name="Layout_The_View_Controllers" />

### <a name="layout-the-view-controllers"></a>Os controladores de exibição de layout

A primeira alteração para o storyboard é excluir o modo de exibição de detalhes existente e substituí-lo com um UITableViewController. Siga estas etapas:

1.  Selecione a barra na parte inferior do controlador de exibição e excluí-lo.
2.  Arraste um **navegação controlador** e um **controlador de exibição de tabela** para o Storyboard da caixa de ferramentas. 
3.  Crie um segue do controlador de exibição de raiz para o segundo controlador de exibição de tabela que acabou de ser adicionado. Para criar o segue, controle + arrastar *da célula de detalhe* para o UITableViewController recém-adicionada. Escolha a opção **Mostrar*** em **seleção atender**. 
4.  Selecione o novo atender você criou e dê a ele um identificador para referência isso atender no código. Clique no segue e digite `TaskSegue` para o **identificador** no **propriedades de preenchimento**, assim:    
  [![Nomenclatura atender no painel de propriedades](creating-tables-in-a-storyboard-images/image16a-sml.png)](creating-tables-in-a-storyboard-images/image16a.png#lightbox) 

5. Em seguida, configure as duas exibições de tabela selecionando-as e usando o painel de propriedades. Certifique-se de selecionar a exibição e não o controlador de exibição – você pode usar a estrutura de tópicos do documento para ajudar com a seleção.

6.  Altere o controlador de exibição de raiz para ser **conteúdo: protótipos dinâmico** (o modo de exibição na superfície de Design será denominado **protótipo conteúdo** ):

    [![Definir a propriedade de conteúdo como protótipos dinâmicos](creating-tables-in-a-storyboard-images/image17a.png)](creating-tables-in-a-storyboard-images/image17a.png#lightbox)

7.  Altere o novo **UITableViewController** ser **conteúdo: células estático**. 


8. O novo UITableViewController deve ter seu nome de classe e um identificador definido. Selecione a controlador de exibição e digite _TaskDetailViewController_ para o **classe** no **propriedades de preenchimento** – Isso criará um novo `TaskDetailViewController.cs` arquivo da solução Teclado. Insira o **StoryboardID** como _detalhes_, conforme ilustrado no exemplo a seguir. Isso será usado posteriormente para carregar este modo de exibição no código do c#:  

    [![Definir a ID do Storyboard](creating-tables-in-a-storyboard-images/image18a.png)](creating-tables-in-a-storyboard-images/image18a.png#lightbox)

9. A superfície de design de storyboard agora deve se parecer com (título do item de navegação do controlador de exibição de raiz foi alterado para "Tarefa quadro"):

    [![Superfície de design](creating-tables-in-a-storyboard-images/image20a-sml.png)](creating-tables-in-a-storyboard-images/image20a.png#lightbox)  



<a name="Create_the_UI" />

### <a name="create-the-ui"></a>Criar a interface do usuário

Agora que os modos de exibição e segues estiver configurado, os elementos de interface do usuário precisam ser adicionados.

#### <a name="root-view-controller"></a>Controlador de exibição de raiz

Primeiro, selecione a célula de protótipo no controlador de modo de exibição mestre e defina o **identificador** como _taskcell_, conforme ilustrado abaixo. Isso será usado posteriormente no código para recuperar uma instância deste UITableViewCell:

 [![definir o identificador de célula](creating-tables-in-a-storyboard-images/image22a-sml.png)](creating-tables-in-a-storyboard-images/image22a.png#lightbox)

Em seguida, você precisará criar um botão que adicionará novas tarefas, conforme ilustrado abaixo:

[![barra de item de botão na barra de navegação](creating-tables-in-a-storyboard-images/image23-sml.png)](creating-tables-in-a-storyboard-images/image23.png#lightbox)

Faça o seguinte: 

-  Arraste um **Item de botão de barra** da caixa de ferramentas para a _lado direito da barra de navegação_.
-  No **propriedades de preenchimento**, em **Item de botão de barra** selecione **identificador: adicionar** (para torná-lo um *+* botão de mais). 
-  Dê a ele um nome para que possa ser identificado no código em um estágio posterior. Observe que você precisa dar um nome de classe de controlador de exibição de raiz (por exemplo **ItemViewController**) para permitir que você defina o nome do item de botão de barra.


#### <a name="taskdetail-view-controller"></a>Exibição de TaskDetail controlador

O modo de exibição de detalhes requer muito mais trabalho. Células de modo de exibição de tabela precisa ser arrastado para o modo de exibição e, em seguida, preenchida com os botões, modos de exibição de texto e rótulos. Captura de tela abaixo mostra a interface do usuário terminado com duas seções. Uma seção tem três células, três rótulos, um e dois campos alternar, enquanto a segunda seção tem uma célula com dois botões:

 [![layout do modo de exibição de detalhes](creating-tables-in-a-storyboard-images/image24a-sml.png)](creating-tables-in-a-storyboard-images/image24a.png#lightbox)

As etapas para criar o layout de conclusão são:

Selecione o modo de exibição de tabela e abra o **propriedade preenchimento**. Atualize as propriedades a seguir:

-  **Seções**: _2_ 
-  **Estilo**: _agrupados_
-  **Separador**: _None_
-  **Seleção de**: _nenhuma seleção_

Selecione a seção superior e, em **Propriedades > seção de exibição de tabela** alterar **linhas** para _3_, conforme ilustrado abaixo:


 [![definir a seção superior a três linhas](creating-tables-in-a-storyboard-images/image29-sml.png)](creating-tables-in-a-storyboard-images/image29.png#lightbox)

Para cada célula abrir o **propriedades de preenchimento** e defina:

-  **Estilo**: _personalizado_
-  **Identificador**: escolha um identificador exclusivo para cada célula (por exemplo. "_título_","_notas_","_feito_").
-  Arraste os controles necessários para produzir o layout mostrado na captura de tela (colocar **UILabel**, **UITextField** e **UISwitch** nas células corretas e defina os rótulos adequadamente, ou seja. Título, observações e feito).


Na segunda seção, defina **linhas** para _1_ e arraste a alça de redimensionamento inferior da célula para torná-lo mais alta.

-  **O identificador do conjunto de**: para um valor exclusivo (por exemplo. "salve"). 
-  **Definir o plano de fundo**: _Limpar cores_ .
-  Arraste dois botões para a célula e defina seus títulos corretamente (ou seja, _salvar_ e _excluir_), conforme ilustrado abaixo:

   [![configuração de dois botões na seção inferior](creating-tables-in-a-storyboard-images/image30-sml.png)](creating-tables-in-a-storyboard-images/image30.png#lightbox)

Neste momento você talvez queira definir restrições em seus controles para garantir um layout adaptável e células.

### <a name="adding-uikit-class-and-naming-controls"></a>Adicionando classe UIKit e nomeação de controles

Há algumas etapas finais na criação de nosso Storyboard. Primeiro deve damos a cada um dos nossos controles um nome de **identidade > nome** para que possam ser usados no código posteriormente. Nomeie esses da seguinte maneira:

-  **Título UITextField** : _TitleText_
-  **Notas de UITextField** : _NotesText_
-  **UISwitch** : _DoneSwitch_
-  **Excluir UIButton** : _DeleteButton_
-  **Salvar UIButton** : _SaveButton_


<a name="Adding_Code" />

## <a name="adding-code"></a>Adicionando código

O restante do trabalho será feito no Visual Studio no Mac ou Windows com c#. Observe que os nomes de propriedade usados no código refletem aquelas definidas no passo a passo acima.

Primeiro, desejamos criar um `Chores` classe, que fornecerá uma maneira de obter e definir o valor de ID, nome, observações e feito booleano, para que podemos usar esses valores em todo o aplicativo.

No seu `Chores` classe adicione o seguinte código:

```csharp
public class Chores {
    public int Id { get; set; }
    public string Name { get; set; }
    public string Notes { get; set; }
    public bool Done { get; set; }
  }
```

Em seguida, crie um `RootTableSource` classe que herda de `UITableViewSource`. 

A diferença entre isto e um modo de exibição de tabela de Storyboard não é que o `GetView` método não precisa criar uma instância de todas as células – `theDequeueReusableCell` método sempre retornará uma instância da célula protótipo (com o identificador de correspondência).

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

Para usar o `RootTableSource` classe, crie uma nova coleção no `ItemViewController`do construtor:

```csharp
chores = new List<Chore> {
      new Chore {Name="Groceries", Notes="Buy bread, cheese, apples", Done=false},
      new Chore {Name="Devices", Notes="Buy Nexus, Galaxy, Droid", Done=false}
    };
```

Em `ViewWillAppear` passa a coleção para a fonte e atribuir a exibição de tabela:

```csharp
public override void ViewWillAppear(bool animated)
{
    base.ViewWillAppear(animated);

    TableView.Source = new RootTableSource(chores.ToArray());
}
```

Se você executar o aplicativo agora, a tela principal agora carregar e exibir uma lista de duas tarefas. Quando uma tarefa é tocada o segue definido pelo storyboard fará com que a tela de detalhes exibidos, mas não exibirá nenhum dado no momento.

Para 'enviar um parâmetro', uma segue, substituir o `PrepareForSegue` método e definir propriedades no `DestinationViewController` (o `TaskDetailViewController` neste exemplo). A classe do controlador de exibição de destino será instanciada, mas não é ainda exibido para o usuário – isso significa que você pode definir propriedades na classe, mas não modifique os controles de interface do usuário:

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

Em `TaskDetailViewController` o `SetTask` método atribui seus parâmetros para propriedades, para que eles podem ser referenciados em ViewWillAppear. Não não possível modificar as propriedades de controle `SetTask` porque pode não existir quando `PrepareForSegue` é chamado:

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

O segue agora abrir a tela de detalhes e exibir as informações de tarefas selecionada. Infelizmente não há nenhuma implementação para o **salvar** e **excluir** botões. Antes de implementar os botões, adicionar esses métodos para **ItemViewController.cs** para atualizar os dados subjacentes e fechar a tela de detalhes:

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

Em seguida, você precisará adicionar o botão `TouchUpInside` manipulador de eventos para o `ViewDidLoad` método **TaskDetailViewController.cs**. O `Delegate` referência de propriedade para o `ItemViewController` foi criado especificamente para podemos chamar `SaveTask` e `DeleteTask`, que feche este modo de exibição como parte de sua operação:

```csharp
SaveButton.TouchUpInside += (sender, e) => {
        currentTask.Name = TitleText.Text;
        currentTask.Notes = NotesText.Text;
        currentTask.Done = DoneSwitch.On;
        Delegate.SaveTask(currentTask);
      };

DeleteButton.TouchUpInside += (sender, e) => Delegate.DeleteTask(currentTask);
```

A última parte restante da funcionalidade de criação é a criação de novas tarefas. Em **ItemViewController.cs** adicionar um método que cria novas tarefas e abre a exibição de detalhes. Para criar uma instância de um modo de exibição de um storyboard, use o `InstantiateViewController` método com o `Identifier` para essa exibição - neste exemplo que será 'detail':

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

Por fim, conectar o botão na barra de navegação do **ItemViewController.cs**do `ViewDidLoad` método chamá-lo:

```csharp
AddButton.Clicked += (sender, e) => CreateTask ();
```

Isso conclui o exemplo de Storyboard – o aplicativo concluído esta aparência:

[![Aplicativo concluído](creating-tables-in-a-storyboard-images/image28a.png)](creating-tables-in-a-storyboard-images/image28a.png#lightbox)

O exemplo demonstra:

-  Criando uma tabela com conteúdo de protótipo em que as células são definidas para usar novamente exibir listas de dados. 
-  Criando uma tabela com o conteúdo estático para criar um formulário de entrada. Isso incluído alterar o estilo de tabela e adicionar seções, células e controles de interface do usuário. 
-  Como criar um segue e substituir o `PrepareForSegue` método para notificar a exibição de destino de quaisquer parâmetros necessários. 
-  Carregando modos de exibição de storyboard diretamente com o `Storyboard.InstantiateViewController` método.



## <a name="related-links"></a>Links relacionados

- [StoryboardTable (exemplo)](https://developer.xamarin.com/samples/monotouch/StoryboardTable/)
- [Introdução ao Storyboards](~/ios/user-interface/storyboards/index.md)
