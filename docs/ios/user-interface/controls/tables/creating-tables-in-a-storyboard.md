---
title: Como trabalhar com tabelas no iOS Designer
description: Nas seções anteriores exploramos o desenvolvimento usando tabelas. Nesse ponto, a quinta e última seção, vamos agregar o que aprendemos até aqui e criar um aplicativo de lista do fardo básico usando um Storyboard.
ms.prod: xamarin
ms.assetid: D8416E10-481A-0B6E-4081-B146E6358004
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 303c96ae6cdbc9f5b327c971f962d6eac75a6fa1
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61227316"
---
# <a name="working-with-tables-in-the-ios-designer"></a>Como trabalhar com tabelas no iOS Designer

Storyboards são uma maneira WYSIWYG para criar aplicativos iOS e têm suporte dentro do Visual Studio no Mac e Windows. Para obter mais informações sobre Storyboards, consulte o [Introdução ao Storyboards](~/ios/user-interface/storyboards/index.md) documento. Storyboards também permitem que você edite os layouts de célula *em* a tabela, que simplifica o desenvolvimento com tabelas e células

Ao configurar propriedades de uma exibição de tabela no Designer do iOS, há dois tipos de conteúdo da célula que podem ser escolhidos: **Dinâmico** ou **estático** conteúdo de protótipo.

<a name="Prototype_Content" />

## <a name="dynamic-prototype-content"></a>Conteúdo dinâmico de protótipo

Um `UITableView` com protótipo conteúdo normalmente deve exibir uma lista de dados em que a célula de protótipo (ou células, como você podem definir mais de um) são reutilizadas para cada item na lista. As células não precisam ser instanciado, eles são obtidos a `GetView` método chamando o `DequeueReusableCell` método do seu `UITableViewSource`.

 <a name="Static_Content" />


## <a name="static-content"></a>Conteúdo Estático

`UITableView`s com conteúdo estático permite que as tabelas a ser criado diretamente na superfície de design. Células podem ser arrastadas para a tabela e personalizadas alterando propriedades e adicionando controles.

 <a name="Creating_a_Storyboard-driven_app" />


## <a name="creating-a-storyboard-driven-app"></a>Criando um aplicativo baseado no Storyboard

O exemplo de StoryboardTable contém um aplicativo mestre / detalhes simples que usa os dois tipos de UITableView em um Storyboard. O restante desta seção descreve como criar um exemplo de lista de tarefas pendentes pequena terá esta aparência ao concluir:

 [![Telas de exemplo](creating-tables-in-a-storyboard-images/image13a.png)](creating-tables-in-a-storyboard-images/image13a.png#lightbox)

A interface do usuário será criada com um storyboard, e ambas as telas usará um UITableView. Usa a tela principal *conteúdo do protótipo* para a linha de layout e o detalhe tela usa *conteúdo estático* para criar um formulário de entrada de dados usando layouts de célula personalizado.

## <a name="walkthrough"></a>Passo a passo

Criar uma nova solução no Visual Studio usando **(criar)... novo projeto > aplicativo de exibição única (C#)** e chamá-lo _StoryboardTables_.

 [![Criar uma caixa de diálogo Novo projeto](creating-tables-in-a-storyboard-images/npd.png)](creating-tables-in-a-storyboard-images/npd.png#lightbox)

A solução será aberta com alguns C# arquivos e uma `Main.storyboard` arquivo já foi criado. Clique duas vezes o `Main.storyboard` arquivo para abri-lo no Designer do iOS.

<a name="Modifying_the_Storyboard" />

## <a name="modifying-the-storyboard"></a>Modificando o Storyboard

O storyboard será editado em três etapas:

-  Primeiro, layout necessário exibir controladores e defina suas propriedades.
-  Em segundo lugar, crie sua interface do usuário, arrastando e soltando objetos em seu modo de exibição
-  Por fim, adicione a classe de UIKit necessária para cada modo de exibição e dê um nome de vários controles para que eles podem ser referenciados no código.


Quando o storyboard for concluído, o código pode ser adicionado para fazer tudo funcionar.

<a name="Layout_The_View_Controllers" />

### <a name="layout-the-view-controllers"></a>Os controladores de exibição de layout

A primeira alteração ao storyboard é excluir a exibição de detalhes existente e substituí-la por um UITableViewController. Siga estas etapas:

1.  Selecione a barra na parte inferior do controlador de exibição e excluí-lo.
2.  Arraste uma **controlador de navegação** e uma **controlador de exibição de tabela** até o Storyboard da caixa de ferramentas. 
3.  Crie um segue do controlador de exibição de raiz para o segundo controlador de exibição de tabela que acabou de ser adicionado. Para criar o segue, controle + arrastar *desde a célula de detalhe* para o UITableViewController recém-adicionado. Escolha a opção **mostram** sob **seleção Segue**. 
4.  Selecione o novo segue que você criou e dê a ele um identificador para referência Isso segue no código. Clique no segue e insira `TaskSegue` para o **identificador** no **painel de propriedades**, semelhante a esta:    
  [![Nomenclatura segue no painel de propriedade](creating-tables-in-a-storyboard-images/image16a-sml.png)](creating-tables-in-a-storyboard-images/image16a.png#lightbox) 

5. Em seguida, configure as duas exibições de tabela selecionando-os e usando o painel de propriedades. Certifique-se de selecionar o modo de exibição e controlador de exibição não – você pode usar a estrutura de tópicos do documento para ajudar com a seleção.

6.  Altere o controlador de exibição de raiz para ser **conteúdo: Protótipos dinâmicos** (o modo de exibição na superfície de Design será rotulado **protótipo conteúdo** ):

    [![Configurando a propriedade de conteúdo para protótipos dinâmicos](creating-tables-in-a-storyboard-images/image17a.png)](creating-tables-in-a-storyboard-images/image17a.png#lightbox)

7.  Alterar o novo **UITableViewController** ser **conteúdo: Células estáticas**. 


8. O novo UITableViewController deve ter seu nome de classe e o identificador definido. Selecione o controlador de exibição e digite _TaskDetailViewController_ para o **classe** no **painel de propriedades** – Isso criará um novo `TaskDetailViewController.cs` arquivo na solução Painel. Insira o **StoryboardID** como _detalhe_, conforme ilustrado no exemplo a seguir. Isso será usado posteriormente para carregar este modo de exibição no C# código:  

    [![Definir a ID do Storyboard](creating-tables-in-a-storyboard-images/image18a.png)](creating-tables-in-a-storyboard-images/image18a.png#lightbox)

9. A superfície de design do storyboard agora deve ser semelhante isso (título do item de navegação do controlador de exibição de raiz foi alterado para "Fardo Board"):

    [![Superfície de design](creating-tables-in-a-storyboard-images/image20a-sml.png)](creating-tables-in-a-storyboard-images/image20a.png#lightbox)  



<a name="Create_the_UI" />

### <a name="create-the-ui"></a>Criar a interface do usuário

Agora que os modos de exibição e segues são configurados, os elementos de interface do usuário precisarão ser adicionados.

#### <a name="root-view-controller"></a>Controlador de exibição de raiz

Primeiro, selecione a célula de protótipo no controlador de exibição mestre e defina as **identificador** como _taskcell_, conforme ilustrado abaixo. Isso será usado posteriormente no código para recuperar uma instância desse UITableViewCell:

 [![definir o identificador da célula](creating-tables-in-a-storyboard-images/image22a-sml.png)](creating-tables-in-a-storyboard-images/image22a.png#lightbox)

Em seguida, você precisará criar um botão que irá adicionar novas tarefas, conforme ilustrado abaixo:

[![item de botão na barra de navegação da](creating-tables-in-a-storyboard-images/image23-sml.png)](creating-tables-in-a-storyboard-images/image23.png#lightbox)

Faça o seguinte: 

-  Arraste uma **Item de botão de barra** da caixa de ferramentas para o _lado direito da barra de navegação_.
-  No **painel de propriedades**, em **Item de botão de barra** selecione **identificador: Adicione** (para torná-lo um *+* além de botão). 
-  Dê um nome para que possa ser identificado no código em um estágio posterior. Observe que você precisa dar um nome de classe de controlador de exibição de raiz (por exemplo **ItemViewController**) para permitir que você defina o nome do item de botão de barra.


#### <a name="taskdetail-view-controller"></a>Controlador de exibição de TaskDetail

A exibição de detalhes requer muito mais trabalho. Células de exibição de tabela precisa ser arrastado para o modo de exibição e, em seguida, preenchido com rótulos, modos de exibição de texto e botões. Captura de tela abaixo mostra a interface do usuário terminar com duas seções. Uma seção tem três células, três rótulos, um e dois campos alternam, enquanto a segunda seção tem uma célula com dois botões:

 [![layout de exibição de detalhes](creating-tables-in-a-storyboard-images/image24a-sml.png)](creating-tables-in-a-storyboard-images/image24a.png#lightbox)

As etapas para criar o layout completo são:

Selecione o modo de exibição de tabela e abra o **painel de propriedade**. Atualize as propriedades a seguir:

-  **Seções**: _2_ 
-  **Estilo**: _Agrupados_
-  **Separador**: _None_
-  **Seleção de**: _Nenhuma seleção_

Selecione a seção superior e, em **Propriedades > seção de exibição de tabela** alterar **linhas** para _3_, conforme ilustrado abaixo:


 [![definindo a seção superior a três linhas](creating-tables-in-a-storyboard-images/image29-sml.png)](creating-tables-in-a-storyboard-images/image29.png#lightbox)

Para cada célula aberta a **painel de propriedades** e defina:

-  **Estilo**:  _Personalizado_
-  **Identificador**: Escolha um identificador exclusivo para cada célula (por exemplo. "_title_","_notas_","_feito_").
-  Arraste os controles necessários para produzir o layout mostrado na captura de tela (Coloque **UILabel**, **UITextField** e **UISwitch** nas células corretas e defina os rótulos apropriadamente, ie. Título, anotações e feito).


A segunda seção, defina **linhas** à _1_ e arraste a alça de redimensionamento inferior da célula para torná-lo o mais alto.

-  **O identificador do conjunto de**: para um valor exclusivo (por exemplo. "salve"). 
-  **Definir o plano de fundo**:  _Limpar cores_ .
-  Arraste dois botões para a célula e defina seus títulos adequadamente (ou seja, _salve_ e _excluir_), conforme ilustrado abaixo:

   [![configuração de dois botões na seção inferior](creating-tables-in-a-storyboard-images/image30-sml.png)](creating-tables-in-a-storyboard-images/image30.png#lightbox)

Neste ponto você talvez queira definir restrições em suas células e controles para garantir um layout adaptável.

### <a name="adding-uikit-class-and-naming-controls"></a>Adicionando classe UIKit e controles de nomenclatura

Há algumas etapas finais na criação de nosso Storyboard. Primeiro, deve atribuir cada um dos nossos controles um nome em **identidade > nome** para que possam ser usados no código posteriormente. Nomeie esses da seguinte maneira:

-  **Título UITextField** : _TitleText_
-  **Notas de UITextField** : _NotesText_
-  **UISwitch** : _DoneSwitch_
-  **Excluir UIButton** : _DeleteButton_
-  **Salvar UIButton** : _SaveButton_


<a name="Adding_Code" />

## <a name="adding-code"></a>Adição de código

O restante do trabalho será feito no Visual Studio no Mac ou Windows com o C#. Observe que os nomes de propriedade usados no código de refletem aquelas definidas no passo a passo acima.

Primeiro vamos criar um `Chores` classe, que fornecerá uma maneira de obter e definir o valor de ID, nome, observações e feito booleano, para que podemos usar esses valores em todo o aplicativo.

No seu `Chores` classe adicione o seguinte código:

```csharp
public class Chores {
    public int Id { get; set; }
    public string Name { get; set; }
    public string Notes { get; set; }
    public bool Done { get; set; }
  }
```

Em seguida, crie uma `RootTableSource` classe que herda de `UITableViewSource`. 

A diferença entre isto e uma exibição de tabela não são do Storyboard é que o `GetView` método não precisa criar uma instância de todas as células – `theDequeueReusableCell` método sempre retornará uma instância da célula protótipo (com identificador correspondente).

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

No `ViewWillAppear` passe a coleção para a fonte e atribuir a exibição de tabela:

```csharp
public override void ViewWillAppear(bool animated)
{
    base.ViewWillAppear(animated);

    TableView.Source = new RootTableSource(chores.ToArray());
}
```

Se você executar o aplicativo agora, a tela principal agora carregar e exibir uma lista de duas tarefas. Quando uma tarefa é tocada o segue definido pelo storyboard fará com que a tela de detalhes são exibidos, mas não exibirá nenhum dado no momento.

Para 'enviar um parâmetro', um segue, substituir os `PrepareForSegue` método e definir propriedades em de `DestinationViewController` (o `TaskDetailViewController` neste exemplo). A classe de controlador de exibição de destino será instanciada, mas não é ainda for exibido para o usuário – isso significa que você pode definir propriedades na classe, mas não modificar todos os controles da interface do usuário:

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

Na `TaskDetailViewController` o `SetTask` método atribui seus parâmetros para propriedades, para que eles podem ser referenciados em ViewWillAppear. As propriedades de controle não podem ser modificadas `SetTask` porque pode não existir quando `PrepareForSegue` é chamado:

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

O segue Agora abra a tela de detalhes e exibir as informações da tarefa selecionada. Infelizmente não há nenhuma implementação para o **salve** e **excluir** botões. Antes de implementar os botões, adicionar esses métodos para **ItemViewController.cs** para atualizar os dados subjacentes e fechar a tela de detalhes:

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

Em seguida, você precisará adicionar o botão `TouchUpInside` manipulador de eventos para o `ViewDidLoad` método **TaskDetailViewController.cs**. O `Delegate` referência de propriedade para o `ItemViewController` foi criado especificamente para que podemos chamar `SaveTask` e `DeleteTask`, que fechar este modo de exibição como parte de suas operações:

```csharp
SaveButton.TouchUpInside += (sender, e) => {
        currentTask.Name = TitleText.Text;
        currentTask.Notes = NotesText.Text;
        currentTask.Done = DoneSwitch.On;
        Delegate.SaveTask(currentTask);
      };

DeleteButton.TouchUpInside += (sender, e) => Delegate.DeleteTask(currentTask);
```

A última parte restante da funcionalidade de criação é a criação de novas tarefas. Na **ItemViewController.cs** adicionar um método que cria novas tarefas e abre a exibição de detalhes. Para criar uma instância de um modo de exibição de um storyboard, use o `InstantiateViewController` método com o `Identifier` dessa exibição - neste exemplo que será 'Detalhes':

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

Por fim, ativar o botão na barra de navegação **ItemViewController.cs**do `ViewDidLoad` método chamá-lo:

```csharp
AddButton.Clicked += (sender, e) => CreateTask ();
```

Isso conclui o exemplo de Storyboard – o aplicativo concluído semelhante ao seguinte:

[![Aplicativo concluído](creating-tables-in-a-storyboard-images/image28a.png)](creating-tables-in-a-storyboard-images/image28a.png#lightbox)

O exemplo demonstra:

-  Criando uma tabela com o conteúdo de protótipo em que as células são definidas para reutilização exibir listas de dados. 
-  Criando uma tabela com o conteúdo estático para criar um formulário de entrada. Isso incluiu alterando o estilo de tabela e adicionando seções, células e controles de interface do usuário. 
-  Como criar um segue e substituir o `PrepareForSegue` método para notificar a exibição de destino de quaisquer parâmetros que ele exige. 
-  Carregando os modos de exibição do storyboard diretamente com o `Storyboard.InstantiateViewController` método.



## <a name="related-links"></a>Links relacionados

- [StoryboardTable (amostra)](https://developer.xamarin.com/samples/monotouch/StoryboardTable/)
- [Introdução ao Storyboards](~/ios/user-interface/storyboards/index.md)
