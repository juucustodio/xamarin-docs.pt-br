---
title: Caixas de diálogo no Xamarin. Mac
description: Este artigo aborda como trabalhar com caixas de diálogo e janelas modais em um aplicativo Xamarin. Mac. Ele descreve a criação de janelas modais no Xcode e no construtor de interface, trabalhando com caixas de diálogo padrão e interagindo com esses controles em código C#.
ms.prod: xamarin
ms.assetid: 55451990-B77B-4D44-B8BB-F874EC503B0C
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 631b1019313ddde6b53ffe63600be8f3fc58673a
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86931463"
---
# <a name="dialogs-in-xamarinmac"></a>Caixas de diálogo no Xamarin. Mac

Ao trabalhar com C# e .NET em um aplicativo Xamarin. Mac, você tem acesso às mesmas caixas de diálogo e janelas modais que um desenvolvedor trabalhando no *Objective-C* e no *Xcode* . Como o Xamarin. Mac se integra diretamente com o Xcode, você pode usar o _interface Builder_ do Xcode para criar e manter suas janelas modais (ou, opcionalmente, criá-las diretamente no código C#).

Uma caixa de diálogo é exibida em resposta a uma ação do usuário e normalmente fornece maneiras como os usuários podem concluir a ação. Uma caixa de diálogo requer uma resposta do usuário antes que possa ser fechada.

O Windows pode ser usado em um estado sem janela restrita (como um editor de texto que pode ter vários documentos abertos ao mesmo tempo) ou modal (como uma caixa de diálogo de exportação que deve ser ignorada antes que o aplicativo possa continuar).

[![Uma caixa de diálogo aberta](dialog-images/dialog03.png)](dialog-images/dialog03.png#lightbox)

Neste artigo, abordaremos as noções básicas de como trabalhar com caixas de diálogo e janelas modais em um aplicativo Xamarin. Mac. É altamente recomendável que você trabalhe pelo artigo [Hello, Mac](~/mac/get-started/hello-mac.md) primeiro, especificamente a [introdução às seções Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e [ações](~/mac/get-started/hello-mac.md#outlets-and-actions) , pois ela aborda os principais conceitos e técnicas que usaremos neste artigo.

Talvez você queira dar uma olhada na seção [expondo classes/métodos C# para Objective-C](~/mac/internals/how-it-works.md) do documento [interno do Xamarin. Mac](~/mac/internals/how-it-works.md) também, explica os `Register` `Export` comandos e usados para conectar suas classes C# a objetos Objective-C e elementos de interface do usuário.

<a name="Introduction_to_Dialogs"></a>

## <a name="introduction-to-dialogs"></a>Introdução às caixas de diálogo

Uma caixa de diálogo é exibida em resposta a uma ação do usuário (como salvar um arquivo) e fornece uma maneira para os usuários concluírem essa ação. Uma caixa de diálogo requer uma resposta do usuário antes que possa ser fechada.

De acordo com a Apple, há três maneiras de apresentar uma caixa de diálogo:

- **Janela restrita de documento** -uma caixa de diálogo modal do documento impede que o usuário faça mais nada dentro de um determinado documento até que seja Descartado.
- **Janela modal do aplicativo** – uma caixa de diálogo modal do aplicativo impede que o usuário interaja com o aplicativo até que seja Descartado.
- **Sem janela restrita** Uma caixa de diálogo sem janela restrita permite que os usuários alterem as configurações na caixa de diálogo enquanto ainda interage com a janela do documento.

### <a name="modal-window"></a>Janela Modal

Qualquer padrão `NSWindow` pode ser usado como uma caixa de diálogo personalizada exibindo-o de forma modal:

[![Uma janela modal de exemplo](dialog-images/modal01.png)](dialog-images/modal01.png#lightbox)

### <a name="document-modal-dialog-sheets"></a>Planilhas de diálogo modais do documento

Uma _planilha_ é uma caixa de diálogo modal anexada a uma determinada janela de documento, impedindo que os usuários interajam com a janela até ignorarem a caixa de diálogo. Uma planilha é anexada à janela da qual surge e apenas uma planilha pode ser aberta para uma janela de cada vez.

[![Um exemplo de planilha modal](dialog-images/sheet08.png)](dialog-images/sheet08.png#lightbox)

### <a name="preferences-windows"></a>Janelas de preferências

Uma janela de preferências é uma caixa de diálogo sem janela restrita que contém as configurações do aplicativo que o usuário altera com pouca frequência. As janelas de preferências geralmente incluem uma barra de ferramentas que permite ao usuário alternar entre diferentes grupos de configurações:

[![Uma janela de preferência de exemplo](dialog-images/dialog02.png)](dialog-images/dialog02.png#lightbox)

### <a name="open-dialog"></a>Abrir caixa de diálogo

A caixa de diálogo abrir fornece aos usuários uma maneira consistente de localizar e abrir um item em um aplicativo:

[![Uma caixa de diálogo aberta](dialog-images/dialog03.png)](dialog-images/dialog03.png#lightbox)

### <a name="print-and-page-setup-dialogs"></a>Caixas de diálogo de configuração de impressão e página

o macOS fornece caixas de diálogo de impressão e configuração de página padrão que seu aplicativo pode exibir para que os usuários possam ter uma experiência de impressão consistente em todos os aplicativos que usam.

O diálogo Imprimir pode ser exibido como uma caixa de diálogo flutuante livre:

[![Uma caixa de diálogo de impressão](dialog-images/print01.png)](dialog-images/print01.png#lightbox)

Ou pode ser exibido como uma planilha:

[![Uma planilha de impressão](dialog-images/print02.png)](dialog-images/print02.png#lightbox)

O diálogo Configurar página pode ser exibido como uma caixa de diálogo flutuante livre:

[![Uma caixa de diálogo de configuração de página](dialog-images/print03.png)](dialog-images/print03.png#lightbox)

Ou pode ser exibido como uma planilha:

[![Uma planilha de configuração de página](dialog-images/print04.png)](dialog-images/print04.png#lightbox)

### <a name="save-dialogs"></a>Salvar caixas de diálogo

A caixa de diálogo Salvar fornece aos usuários uma maneira consistente de salvar um item em um aplicativo. A caixa de diálogo Salvar tem dois Estados: **mínimo** (também conhecido como recolhido):

[![Uma caixa de diálogo salvar](dialog-images/save01.png)](dialog-images/save01.png#lightbox)

E o estado **expandido** :

[![Uma caixa de diálogo Salvar expandida](dialog-images/save02.png)](dialog-images/save02.png#lightbox)

A caixa de diálogo Salvar **mínimo** também pode ser exibida como uma planilha:

[![Uma folha de salvamento mínima](dialog-images/save03.png)](dialog-images/save03.png#lightbox)

Como pode a caixa de diálogo Salvar **expandida** :

[![Uma planilha de salvamento expandida](dialog-images/save04.png)](dialog-images/save04.png#lightbox)

Para obter mais informações, consulte a seção [caixas de diálogo](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowDialogs.html#//apple_ref/doc/uid/20000957-CH43-SW1) das [diretrizes de interface humana do os X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) da Apple

<a name="Adding_a_Modal_Window_to_a_Project"></a>

## <a name="adding-a-modal-window-to-a-project"></a>Adicionando uma janela modal a um projeto

Além da janela principal do documento, um aplicativo Xamarin. Mac pode precisar exibir outros tipos de janelas para o usuário, como preferências ou painéis de inspetores.

Para adicionar uma nova janela, faça o seguinte:

1. No **Gerenciador de soluções**, abra o `Main.storyboard` arquivo para edição no interface Builder do Xcode.
2. Arraste um novo **controlador de exibição** para a design Surface:

    [![Selecionando um controlador de exibição da biblioteca](dialog-images/new01.png)](dialog-images/new01.png#lightbox)
3. No **Inspetor de identidade**, digite `CustomDialogController` para o **nome da classe**: 

    [![Definindo o nome da classe](dialog-images/new02.png)](dialog-images/new02.png#lightbox)
4. Volte para Visual Studio para Mac, permita que ele seja sincronizado com o Xcode e crie o `CustomDialogController.h` arquivo.
5. Retorne ao Xcode e projete sua interface: 

    [![Criando a interface do usuário no Xcode](dialog-images/new03.png)](dialog-images/new03.png#lightbox)
6. Crie um **transição modal** na janela principal do seu aplicativo para o novo controlador de exibição por meio de controle-arrastando do elemento da interface do usuário que abrirá a caixa de diálogo na janela da caixa de diálogo. Atribuir o **identificador** `ModalSegue` : 

    [![Um transição modal](dialog-images/new06.png)](dialog-images/new06.png#lightbox)
7. Conectar qualquer **ação** e **saídas**: 

    [![Configurando uma ação](dialog-images/new04.png)](dialog-images/new04.png#lightbox)
8. Salve as alterações e retorne ao Visual Studio para Mac para sincronizar com o Xcode.

Faça com que o `CustomDialogController.cs` arquivo se pareça com o seguinte:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacDialog
{
    public partial class CustomDialogController : NSViewController
    {
        #region Private Variables
        private string _dialogTitle = "Title";
        private string _dialogDescription = "Description";
        private NSViewController _presentor;
        #endregion

        #region Computed Properties
        public string DialogTitle {
            get { return _dialogTitle; }
            set { _dialogTitle = value; }
        }

        public string DialogDescription {
            get { return _dialogDescription; }
            set { _dialogDescription = value; }
        }

        public NSViewController Presentor {
            get { return _presentor; }
            set { _presentor = value; }
        }
        #endregion

        #region Constructors
        public CustomDialogController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewWillAppear ()
        {
            base.ViewWillAppear ();

            // Set initial title and description
            Title.StringValue = DialogTitle;
            Description.StringValue = DialogDescription;
        }
        #endregion

        #region Private Methods
        private void CloseDialog() {
            Presentor.DismissViewController (this);
        }
        #endregion

        #region Custom Actions
        partial void AcceptDialog (Foundation.NSObject sender) {
            RaiseDialogAccepted();
            CloseDialog();
        }

        partial void CancelDialog (Foundation.NSObject sender) {
            RaiseDialogCanceled();
            CloseDialog();
        }
        #endregion

        #region Events
        public EventHandler DialogAccepted;

        internal void RaiseDialogAccepted() {
            if (this.DialogAccepted != null)
                this.DialogAccepted (this, EventArgs.Empty);
        }

        public EventHandler DialogCanceled;

        internal void RaiseDialogCanceled() {
            if (this.DialogCanceled != null)
                this.DialogCanceled (this, EventArgs.Empty);
        }
        #endregion
    }
}
```

Esse código expõe algumas propriedades para definir o título e a descrição da caixa de diálogo e alguns eventos para reagir à caixa de diálogo que está sendo cancelada ou aceita.

Em seguida, edite o `ViewController.cs` arquivo, substitua o `PrepareForSegue` método e faça com que ele se pareça com o seguinte:

```csharp
public override void PrepareForSegue (NSStoryboardSegue segue, NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    // Take action based on the segue name
    switch (segue.Identifier) {
    case "ModalSegue":
        var dialog = segue.DestinationController as CustomDialogController;
        dialog.DialogTitle = "MacDialog";
        dialog.DialogDescription = "This is a sample dialog.";
        dialog.DialogAccepted += (s, e) => {
            Console.WriteLine ("Dialog accepted");
            DismissViewController (dialog);
        };
        dialog.Presentor = this;
        break;
    }
}
```

Esse código inicializa o transição que definimos no Interface Builder do Xcode para nossa caixa de diálogo e configura o título e a descrição. Ele também manipula a opção que o usuário faz na caixa de diálogo.

Podemos executar nosso aplicativo e exibir a caixa de diálogo personalizada:

[![Um diálogo de exemplo](dialog-images/new05.png)](dialog-images/new05.png#lightbox)

Para obter mais informações sobre como usar o Windows em um aplicativo Xamarin. Mac, consulte [a documentação trabalhando com o Windows](~/mac/user-interface/window.md) .

<a name="Creating_a_Custom_Sheet"></a>

## <a name="creating-a-custom-sheet"></a>Criando uma planilha personalizada

Uma _planilha_ é uma caixa de diálogo modal anexada a uma determinada janela de documento, impedindo que os usuários interajam com a janela até ignorarem a caixa de diálogo. Uma planilha é anexada à janela da qual surge e apenas uma planilha pode ser aberta para uma janela de cada vez. 

Para criar uma planilha personalizada no Xamarin. Mac, vamos fazer o seguinte:

1. No **Gerenciador de soluções**, abra o `Main.storyboard` arquivo para edição no interface Builder do Xcode.
2. Arraste um novo **controlador de exibição** para a design Surface:

    [![Selecionando um controlador de exibição da biblioteca](dialog-images/new01.png)](dialog-images/new01.png#lightbox)
3. Crie sua interface do usuário:

    [![O design da interface do usuário](dialog-images/sheet01.png)](dialog-images/sheet01.png#lightbox)
4. Crie um **transição de planilha** de sua janela principal para o novo controlador de exibição: 

    [![Selecionando o tipo de transição de planilha](dialog-images/sheet02.png)](dialog-images/sheet02.png#lightbox)
5. No **Inspetor de identidade**, nomeie a **classe** do controlador de exibição `SheetViewController` : 

    [![Definindo o nome da classe](dialog-images/sheet03.png)](dialog-images/sheet03.png#lightbox)
6. Defina as **ações**e **saídas** necessárias: 

    [![Definindo as ações e saídas necessárias](dialog-images/sheet04.png)](dialog-images/sheet04.png#lightbox)
7. Salve as alterações e retorne ao Visual Studio para Mac para sincronização.

Em seguida, edite o `SheetViewController.cs` arquivo e faça com que ele se pareça com o seguinte:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacDialog
{
    public partial class SheetViewController : NSViewController
    {
        #region Private Variables
        private string _userName = "";
        private string _password = "";
        private NSViewController _presentor;
        #endregion

        #region Computed Properties
        public string UserName {
            get { return _userName; }
            set { _userName = value; }
        }

        public string Password {
            get { return _password;}
            set { _password = value;}
        }

        public NSViewController Presentor {
            get { return _presentor; }
            set { _presentor = value; }
        }
        #endregion

        #region Constructors
        public SheetViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewWillAppear ()
        {
            base.ViewWillAppear ();

            // Set initial values
            NameField.StringValue = UserName;
            PasswordField.StringValue = Password;

            // Wireup events
            NameField.Changed += (sender, e) => {
                UserName = NameField.StringValue;
            };
            PasswordField.Changed += (sender, e) => {
                Password = PasswordField.StringValue;
            };
        }
        #endregion

        #region Private Methods
        private void CloseSheet() {
            Presentor.DismissViewController (this);
        }
        #endregion

        #region Custom Actions
        partial void AcceptSheet (Foundation.NSObject sender) {
            RaiseSheetAccepted();
            CloseSheet();
        }

        partial void CancelSheet (Foundation.NSObject sender) {
            RaiseSheetCanceled();
            CloseSheet();
        }
        #endregion

        #region Events
        public EventHandler SheetAccepted;

        internal void RaiseSheetAccepted() {
            if (this.SheetAccepted != null)
                this.SheetAccepted (this, EventArgs.Empty);
        }

        public EventHandler SheetCanceled;

        internal void RaiseSheetCanceled() {
            if (this.SheetCanceled != null)
                this.SheetCanceled (this, EventArgs.Empty);
        }
        #endregion
    }
}
```

Em seguida, edite o `ViewController.cs` arquivo, edite o `PrepareForSegue` método e faça com que ele se pareça com o seguinte:

```csharp
public override void PrepareForSegue (NSStoryboardSegue segue, NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    // Take action based on the segue name
    switch (segue.Identifier) {
    case "ModalSegue":
        var dialog = segue.DestinationController as CustomDialogController;
        dialog.DialogTitle = "MacDialog";
        dialog.DialogDescription = "This is a sample dialog.";
        dialog.DialogAccepted += (s, e) => {
            Console.WriteLine ("Dialog accepted");
            DismissViewController (dialog);
        };
        dialog.Presentor = this;
        break;
    case "SheetSegue":
        var sheet = segue.DestinationController as SheetViewController;
        sheet.SheetAccepted += (s, e) => {
            Console.WriteLine ("User Name: {0} Password: {1}", sheet.UserName, sheet.Password);
        };
        sheet.Presentor = this;
        break;
    }
}
```

Se executarmos nosso aplicativo e abrirmos a planilha, ele será anexado à janela:

[![Uma planilha de exemplo](dialog-images/sheet08.png)](dialog-images/sheet08.png#lightbox)

<a name="Creating_a_Preferences_Dialog"></a>

## <a name="creating-a-preferences-dialog"></a>Criando uma caixa de diálogo Preferências

Antes de criarmos o modo de exibição de preferência no Interface Builder, precisaremos adicionar um tipo de transição personalizado para lidar com a alternância das preferências. Adicione uma nova classe ao seu projeto e chame-a `ReplaceViewSeque` . Edite a classe e faça com que ela fique parecida com a seguinte:

```csharp
using System;
using AppKit;
using Foundation;

namespace MacWindows
{
    [Register("ReplaceViewSeque")]
    public class ReplaceViewSeque : NSStoryboardSegue
    {
        #region Constructors
        public ReplaceViewSeque() {

        }

        public ReplaceViewSeque (string identifier, NSObject sourceController, NSObject destinationController) : base(identifier,sourceController,destinationController) {

        }

        public ReplaceViewSeque (IntPtr handle) : base(handle) {
        }

        public ReplaceViewSeque (NSObjectFlag x) : base(x) {
        }
        #endregion

        #region Override Methods
        public override void Perform ()
        {
            // Cast the source and destination controllers
            var source = SourceController as NSViewController;
            var destination = DestinationController as NSViewController;

            // Is there a source?
            if (source == null) {
                // No, get the current key window
                var window = NSApplication.SharedApplication.KeyWindow;

                // Swap the controllers
                window.ContentViewController = destination;

                // Release memory
                window.ContentViewController?.RemoveFromParentViewController ();
            } else {
                // Swap the controllers
                source.View.Window.ContentViewController = destination;

                // Release memory
                source.RemoveFromParentViewController ();
            }
        
        }
        #endregion

    }

}
```

Com o transição personalizado criado, podemos adicionar uma nova janela na Interface Builder do Xcode para lidar com nossas preferências.

Para adicionar uma nova janela, faça o seguinte:

1. No **Gerenciador de soluções**, abra o `Main.storyboard` arquivo para edição no interface Builder do Xcode.
2. Arraste um novo **controlador de janela** para a design Surface:

    [![Selecionar um controlador de janela na biblioteca](dialog-images/pref01.png)](dialog-images/pref01.png#lightbox)
3. Organize a janela ao lado do designer de **barra de menus** :

    [![Adicionando a nova janela](dialog-images/pref02.png)](dialog-images/pref02.png#lightbox)
4. Crie cópias do controlador de exibição anexado, pois haverá guias no modo de exibição de preferência:

    [![Adicionando os controladores de exibição necessários](dialog-images/pref03.png)](dialog-images/pref03.png#lightbox)
5. Arraste um novo **controlador de barra de ferramentas** da **biblioteca**:

    [![Selecione um controlador de barra de ferramentas na biblioteca](dialog-images/pref04.png)](dialog-images/pref04.png#lightbox)
6. E solte-o na janela na Design Surface:

    [![Adicionando um novo controlador de barra de ferramentas](dialog-images/pref05.png)](dialog-images/pref05.png#lightbox)
7. Layout o design da sua barra de ferramentas:

    [![Layout da barra de ferramentas](dialog-images/pref06.png)](dialog-images/pref06.png#lightbox)
8. Controle-clique e arraste de cada **botão da barra de ferramentas** para os modos de exibição que você criou acima. Selecione um tipo de transição **personalizado** :

    [![Configurando o tipo transição](dialog-images/pref07.png)](dialog-images/pref07.png#lightbox)
9. Selecione o novo transição e defina a **classe** como `ReplaceViewSegue` :

    [![Definindo a classe transição](dialog-images/pref08.png)](dialog-images/pref08.png#lightbox)
10. No **Designer de BarraDeMenu** na design Surface, no menu do aplicativo, selecione **preferências...**, clique no botão de controle e arraste para a janela preferências para criar um transição de **exibição** :

    [![Configurando o tipo transição](dialog-images/pref09.png)](dialog-images/pref09.png#lightbox)
11. Salve as alterações e retorne ao Visual Studio para Mac para sincronização.

Se executarmos o código e selecionarmos as **preferências...** no **menu do aplicativo**, a janela será exibida:

[![Uma janela de preferências de exemplo](dialog-images/pref10.png)](dialog-images/pref10.png#lightbox)

Para obter mais informações sobre como trabalhar com janelas e barras de ferramentas, consulte nossa documentação sobre [janelas](~/mac/user-interface/window.md) e [barras de ferramentas](~/mac/user-interface/toolbar.md) .

<a name="Saving-and-Loading-Preferences"></a>

### <a name="saving-and-loading-preferences"></a>Salvando e carregando preferências

Em um aplicativo macOS típico, quando o usuário faz alterações em qualquer uma das preferências do usuário do aplicativo, essas alterações são salvas automaticamente. A maneira mais fácil de lidar com isso em um aplicativo Xamarin. Mac é criar uma única classe para gerenciar todas as preferências do usuário e compartilhá-las em todo o sistema.

Primeiro, adicione uma nova `AppPreferences` classe ao projeto e herde de `NSObject` . As preferências serão projetadas para usar a [vinculação de dados e a codificação de chave-valor](~/mac/app-fundamentals/databinding.md) , o que tornará o processo de criação e manutenção dos formulários de preferência muito mais simples. Como as preferências consistirão em uma pequena quantidade de tipos de dado simples, use o interno `NSUserDefaults` para armazenar e recuperar valores.

Edite o `AppPreferences.cs` arquivo e faça com que ele se pareça com o seguinte:

```csharp
using System;
using Foundation;
using AppKit;

namespace SourceWriter
{
    [Register("AppPreferences")]
    public class AppPreferences : NSObject
    {
        #region Computed Properties
        [Export("DefaultLanguage")]
        public int DefaultLanguage {
            get { 
                var value = LoadInt ("DefaultLanguage", 0);
                return value; 
            }
            set {
                WillChangeValue ("DefaultLanguage");
                SaveInt ("DefaultLanguage", value, true);
                DidChangeValue ("DefaultLanguage");
            }
        }

        [Export("SmartLinks")]
        public bool SmartLinks {
            get { return LoadBool ("SmartLinks", true); }
            set {
                WillChangeValue ("SmartLinks");
                SaveBool ("SmartLinks", value, true);
                DidChangeValue ("SmartLinks");
            }
        }

        // Define any other required user preferences in the same fashion
        ...

        [Export("EditorBackgroundColor")]
        public NSColor EditorBackgroundColor {
            get { return LoadColor("EditorBackgroundColor", NSColor.White); }
            set {
                WillChangeValue ("EditorBackgroundColor");
                SaveColor ("EditorBackgroundColor", value, true);
                DidChangeValue ("EditorBackgroundColor");
            }
        }
        #endregion

        #region Constructors
        public AppPreferences ()
        {
        }
        #endregion

        #region Public Methods
        public int LoadInt(string key, int defaultValue) {
            // Attempt to read int
            var number = NSUserDefaults.StandardUserDefaults.IntForKey(key);

            // Take action based on value
            if (number == null) {
                return defaultValue;
            } else {
                return (int)number;
            }
        }
            
        public void SaveInt(string key, int value, bool sync) {
            NSUserDefaults.StandardUserDefaults.SetInt(value, key);
            if (sync) NSUserDefaults.StandardUserDefaults.Synchronize ();
        }

        public bool LoadBool(string key, bool defaultValue) {
            // Attempt to read int
            var value = NSUserDefaults.StandardUserDefaults.BoolForKey(key);

            // Take action based on value
            if (value == null) {
                return defaultValue;
            } else {
                return value;
            }
        }

        public void SaveBool(string key, bool value, bool sync) {
            NSUserDefaults.StandardUserDefaults.SetBool(value, key);
            if (sync) NSUserDefaults.StandardUserDefaults.Synchronize ();
        }

        public string NSColorToHexString(NSColor color, bool withAlpha) {
            //Break color into pieces
            nfloat red=0, green=0, blue=0, alpha=0;
            color.GetRgba (out red, out green, out blue, out alpha);

            // Adjust to byte
            alpha *= 255;
            red *= 255;
            green *= 255;
            blue *= 255;

            //With the alpha value?
            if (withAlpha) {
                return String.Format ("#{0:X2}{1:X2}{2:X2}{3:X2}", (int)alpha, (int)red, (int)green, (int)blue);
            } else {
                return String.Format ("#{0:X2}{1:X2}{2:X2}", (int)red, (int)green, (int)blue);
            }
        }

        public NSColor NSColorFromHexString (string hexValue)
        {
            var colorString = hexValue.Replace ("#", "");
            float red, green, blue, alpha;

            // Convert color based on length
            switch (colorString.Length) {
            case 3 : // #RGB
                red = Convert.ToInt32(string.Format("{0}{0}", colorString.Substring(0, 1)), 16) / 255f;
                green = Convert.ToInt32(string.Format("{0}{0}", colorString.Substring(1, 1)), 16) / 255f;
                blue = Convert.ToInt32(string.Format("{0}{0}", colorString.Substring(2, 1)), 16) / 255f;
                return NSColor.FromRgba(red, green, blue, 1.0f);
            case 6 : // #RRGGBB
                red = Convert.ToInt32(colorString.Substring(0, 2), 16) / 255f;
                green = Convert.ToInt32(colorString.Substring(2, 2), 16) / 255f;
                blue = Convert.ToInt32(colorString.Substring(4, 2), 16) / 255f;
                return NSColor.FromRgba(red, green, blue, 1.0f);
            case 8 : // #AARRGGBB
                alpha = Convert.ToInt32(colorString.Substring(0, 2), 16) / 255f;
                red = Convert.ToInt32(colorString.Substring(2, 2), 16) / 255f;
                green = Convert.ToInt32(colorString.Substring(4, 2), 16) / 255f;
                blue = Convert.ToInt32(colorString.Substring(6, 2), 16) / 255f;
                return NSColor.FromRgba(red, green, blue, alpha);
            default :
                throw new ArgumentOutOfRangeException(string.Format("Invalid color value '{0}'. It should be a hex value of the form #RBG, #RRGGBB or #AARRGGBB", hexValue));
            }
        }

        public NSColor LoadColor(string key, NSColor defaultValue) {

            // Attempt to read color
            var hex = NSUserDefaults.StandardUserDefaults.StringForKey(key);

            // Take action based on value
            if (hex == null) {
                return defaultValue;
            } else {
                return NSColorFromHexString (hex);
            }
        }

        public void SaveColor(string key, NSColor color, bool sync) {
            // Save to default
            NSUserDefaults.StandardUserDefaults.SetString(NSColorToHexString(color,true), key);
            if (sync) NSUserDefaults.StandardUserDefaults.Synchronize ();
        }
        #endregion
    }
}
```

Essa classe contém algumas rotinas auxiliares, como,,, `SaveInt` `LoadInt` `SaveColor` `LoadColor` etc., para facilitar o trabalho com `NSUserDefaults` mais facilidade. Além disso, como o `NSUserDefaults` não tem uma maneira interna de manipular `NSColors` , os `NSColorToHexString` métodos e `NSColorFromHexString` são usados para converter cores em cadeias de caracteres hexadecimais baseadas na Web ( `#RRGGBBAA` onde `AA` é a transparência alfa) que pode ser facilmente armazenada e recuperada.

No `AppDelegate.cs` arquivo, crie uma instância do objeto **AppPreferences** que será usada em todo o aplicativo:

```csharp
using AppKit;
using Foundation;
using System.IO;
using System;

namespace SourceWriter
{
    [Register ("AppDelegate")]
    public class AppDelegate : NSApplicationDelegate
    {
        #region Computed Properties
        public int NewWindowNumber { get; set;} = -1;

        public AppPreferences Preferences { get; set; } = new AppPreferences();
        #endregion

        #region Constructors
        public AppDelegate ()
        {
        }
        #endregion
        
        ...
```

<a name="Wiring-Preferences-to-Preference-Views"></a>

### <a name="wiring-preferences-to-preference-views"></a>Preferências de fiação para exibições de preferência

Em seguida, conecte a classe de preferência aos elementos da interface do usuário na janela de preferência e exibições criadas acima. Em Interface Builder, selecione um controlador de exibição de preferência e alterne para o **Inspetor de identidade**, crie uma classe personalizada para o controlador: 

[![O Inspetor de identidade](dialog-images/prefs12.png)](dialog-images/prefs12.png#lightbox)

Volte para Visual Studio para Mac para sincronizar as alterações e abrir a classe recém-criada para edição. Faça com que a classe fique parecida com a seguinte:

```csharp
using System;
using Foundation;
using AppKit;

namespace SourceWriter
{
    public partial class EditorPrefsController : NSViewController
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)NSApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Computed Properties
        [Export("Preferences")]
        public AppPreferences Preferences {
            get { return App.Preferences; }
        }
        #endregion

        #region Constructors
        public EditorPrefsController (IntPtr handle) : base (handle)
        {
        }
        #endregion
    }
}
```

Observe que essa classe fez duas coisas aqui: primeiro, há uma `App` propriedade auxiliar para facilitar o acesso ao **AppDelegate** . Em segundo lugar, a `Preferences` Propriedade expõe a classe global **AppPreferences** para a vinculação de dados com qualquer controle de interface do usuário colocado nessa exibição.

Em seguida, clique duas vezes no arquivo de storyboard para abri-lo novamente no Interface Builder (e veja as alterações feitas acima). Arraste os controles da interface do usuário necessários para criar a interface de preferências na exibição. Para cada controle, alterne para o **Inspetor de associação** e associe às propriedades individuais da classe **AppPreference** :

[![O Inspetor de associação](dialog-images/prefs13.png)](dialog-images/prefs13.png#lightbox)

Repita as etapas acima para todos os painéis (Exibir controladores) e propriedades de preferência necessários.

<a name="Applying-Preference-Changes-to-All-Open-Windows"></a>

### <a name="applying-preference-changes-to-all-open-windows"></a>Aplicando alterações de preferência a todas as janelas abertas

Conforme mencionado acima, em um aplicativo macOS típico, quando o usuário faz alterações em qualquer uma das preferências do usuário do aplicativo, essas alterações são salvas automaticamente e aplicadas a qualquer janela que o usuário possa ter aberto no aplicativo.

O planejamento cuidadoso e o design das preferências do seu aplicativo e do Windows permitirão que esse processo ocorra de forma tranqüila e transparente para o usuário final, com uma quantidade mínima de trabalho de codificação.

Para qualquer janela que consumirá as preferências do aplicativo, adicione a seguinte propriedade auxiliar ao seu controlador de exibição de conteúdo para tornar o acesso ao nosso **AppDelegate** mais fácil:

```csharp
#region Application Access
public static AppDelegate App {
    get { return (AppDelegate)NSApplication.SharedApplication.Delegate; }
}
#endregion
```

Em seguida, adicione uma classe para configurar o conteúdo ou o comportamento com base nas preferências do usuário:

```csharp
public void ConfigureEditor() {

    // General Preferences
    TextEditor.AutomaticLinkDetectionEnabled = App.Preferences.SmartLinks;
    TextEditor.AutomaticQuoteSubstitutionEnabled = App.Preferences.SmartQuotes;
    ...

}
``` 

Você precisa chamar o método de configuração quando a janela é aberta pela primeira vez para garantir que ela esteja de acordo com as preferências do usuário:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Configure editor from user preferences
    ConfigureEditor ();
    ...
}
```

Em seguida, edite o `AppDelegate.cs` arquivo e adicione o seguinte método para aplicar qualquer alteração de preferência a todas as janelas abertas:

```csharp
public void UpdateWindowPreferences() {

    // Process all open windows
    for(int n=0; n<NSApplication.SharedApplication.Windows.Length; ++n) {
        var content = NSApplication.SharedApplication.Windows[n].ContentViewController as ViewController;
        if (content != null ) {
            // Reformat all text
            content.ConfigureEditor ();
        }
    }

}
```

Em seguida, adicione uma `PreferenceWindowDelegate` classe ao projeto e faça com que ele se pareça com o seguinte:

```csharp
using System;
using AppKit;
using System.IO;
using Foundation;

namespace SourceWriter
{
    public class PreferenceWindowDelegate : NSWindowDelegate
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)NSApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Computed Properties
        public NSWindow Window { get; set;}
        #endregion

        #region constructors
        public PreferenceWindowDelegate (NSWindow window)
        {
            // Initialize
            this.Window = window;

        }
        #endregion

        #region Override Methods
        public override bool WindowShouldClose (Foundation.NSObject sender)
        {
            
            // Apply any changes to open windows
            App.UpdateWindowPreferences();

            return true;
        }
        #endregion
    }
}
```

Isso fará com que todas as alterações de preferência sejam enviadas a todas as janelas abertas quando a janela de preferência fechar.

Por fim, edite o controlador de janela de preferência e adicione o delegado criado acima:

```csharp
using System;
using Foundation;
using AppKit;

namespace SourceWriter
{
    public partial class PreferenceWindowController : NSWindowController
    {
        #region Constructors
        public PreferenceWindowController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void WindowDidLoad ()
        {
            base.WindowDidLoad ();

            // Initialize
            Window.Delegate = new PreferenceWindowDelegate(Window);
            Toolbar.SelectedItemIdentifier = "General";
        }
        #endregion
    }
}
```

Com todas essas alterações em vigor, se o usuário editar as preferências do aplicativo e fechar a janela de preferência, as alterações serão aplicadas a todas as janelas abertas:

[![Uma janela de preferências de exemplo](dialog-images/prefs14.png)](dialog-images/prefs14.png#lightbox)

<a name="The_Open_Dialog"></a>

## <a name="the-open-dialog"></a>A caixa de diálogo abrir

A caixa de diálogo abrir fornece aos usuários uma maneira consistente de localizar e abrir um item em um aplicativo. Para exibir uma caixa de diálogo aberta em um aplicativo Xamarin. Mac, use o seguinte código:

```csharp
var dlg = NSOpenPanel.OpenPanel;
dlg.CanChooseFiles = true;
dlg.CanChooseDirectories = false;
dlg.AllowedFileTypes = new string[] { "txt", "html", "md", "css" };

if (dlg.RunModal () == 1) {
    // Nab the first file
    var url = dlg.Urls [0];

    if (url != null) {
        var path = url.Path;

        // Create a new window to hold the text
        var newWindowController = new MainWindowController ();
        newWindowController.Window.MakeKeyAndOrderFront (this);

        // Load the text into the window
        var window = newWindowController.Window as MainWindow;
        window.Text = File.ReadAllText(path);
        window.SetTitleWithRepresentedFilename (Path.GetFileName(path));
        window.RepresentedUrl = url;

    }
}
```

No código acima, estamos abrindo uma nova janela de documento para exibir o conteúdo do arquivo. Você precisará substituir esse código com a funcionalidade necessária para seu aplicativo.

As propriedades a seguir estão disponíveis ao trabalhar com um `NSOpenPanel` :

- **CanChooseFiles** – se `true` o usuário puder selecionar arquivos.
- **CanChooseDirectories** – se `true` o usuário puder selecionar diretórios.
- **AllowsMultipleSelection** – se `true` o usuário puder selecionar mais de um arquivo por vez.
- **ResolveAliases** -se `true` selecionar e alias, o o resolverá para o caminho do arquivo original.
- **AllowedFileTypes** -é uma matriz de cadeia de caracteres de tipos de arquivos que o usuário pode selecionar como uma extensão ou _UTI_. O valor padrão é `null` , que permite que qualquer arquivo seja aberto.

O `RunModal ()` método exibe a caixa de diálogo abrir e permite que o usuário selecione arquivos ou diretórios (conforme especificado pelas propriedades) e retorna `1` se o usuário clica no botão **abrir** .

A caixa de diálogo abrir retorna os arquivos ou diretórios selecionados do usuário como uma matriz de URLs na `URL` propriedade.

Se executarmos o programa e selecionarmos o item **abrir...** no menu **arquivo** , o seguinte será exibido: 

[![Uma caixa de diálogo aberta](dialog-images/dialog03.png)](dialog-images/dialog03.png#lightbox)

<a name="The_Print_and_Page_Setup_Dialogs"></a>

## <a name="the-print-and-page-setup-dialogs"></a>As caixas de diálogo de configuração de impressão e de página

o macOS fornece caixas de diálogo de impressão e configuração de página padrão que seu aplicativo pode exibir para que os usuários possam ter uma experiência de impressão consistente em todos os aplicativos que usam.

O código a seguir mostrará a caixa de diálogo de impressão padrão:

```csharp
public bool ShowPrintAsSheet { get; set;} = true;
...

[Export ("showPrinter:")]
void ShowDocument (NSObject sender) {
    var dlg = new NSPrintPanel();

    // Display the print dialog as dialog box
    if (ShowPrintAsSheet) {
        dlg.BeginSheet(new NSPrintInfo(),this,this,null,new IntPtr());
    } else {
        if (dlg.RunModalWithPrintInfo(new NSPrintInfo()) == 1) {
            var alert = new NSAlert () {
                AlertStyle = NSAlertStyle.Critical,
                InformativeText = "We need to print the document here...",
                MessageText = "Print Document",
            };
            alert.RunModal ();
        }
    }
}

```

Se definirmos a `ShowPrintAsSheet` propriedade como `false` , executar o aplicativo e exibir a caixa de diálogo de impressão, o seguinte será exibido:

[![Uma caixa de diálogo de impressão](dialog-images/print01.png)](dialog-images/print01.png#lightbox)

Se definir a `ShowPrintAsSheet` propriedade como `true` , execute o aplicativo e exiba a caixa de diálogo Imprimir, o seguinte será exibido:

[![Uma planilha de impressão](dialog-images/print02.png)](dialog-images/print02.png#lightbox)

O código a seguir exibirá a caixa de diálogo layout da página:

```csharp
[Export ("showLayout:")]
void ShowLayout (NSObject sender) {
    var dlg = new NSPageLayout();

    // Display the print dialog as dialog box
    if (ShowPrintAsSheet) {
        dlg.BeginSheet (new NSPrintInfo (), this);
    } else {
        if (dlg.RunModal () == 1) {
            var alert = new NSAlert () {
                AlertStyle = NSAlertStyle.Critical,
                InformativeText = "We need to print the document here...",
                MessageText = "Print Document",
            };
            alert.RunModal ();
        }
    }
}
```

Se definirmos a `ShowPrintAsSheet` propriedade como `false` , executar o aplicativo e exibir a caixa de diálogo layout de impressão, o seguinte será exibido:

[![Uma caixa de diálogo de configuração de página](dialog-images/print03.png)](dialog-images/print03.png#lightbox)

Se definir a `ShowPrintAsSheet` propriedade como `true` , execute o aplicativo e exiba a caixa de diálogo layout de impressão, o seguinte será exibido:

[![Uma planilha de configuração de página](dialog-images/print04.png)](dialog-images/print04.png#lightbox)

Para obter mais informações sobre como trabalhar com as caixas de diálogo de impressão e configuração de página, consulte a documentação do [NSPrintPanel](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSPrintPanel_Class/index.html#//apple_ref/doc/uid/TP40004092) e do [NSPageLayout](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSPageLayout_Class/index.html#//apple_ref/doc/uid/TP40004080) da Apple.

<a name="The_Save_Dialog"></a>

## <a name="the-save-dialog"></a>A caixa de diálogo salvar

A caixa de diálogo Salvar fornece aos usuários uma maneira consistente de salvar um item em um aplicativo.

O código a seguir mostrará a caixa de diálogo Salvar padrão:

```csharp
public bool ShowSaveAsSheet { get; set;} = true;
...

[Export("saveDocumentAs:")]
void ShowSaveAs (NSObject sender)
{
    var dlg = new NSSavePanel ();
    dlg.Title = "Save Text File";
    dlg.AllowedFileTypes = new string[] { "txt", "html", "md", "css" };

    if (ShowSaveAsSheet) {
        dlg.BeginSheet(mainWindowController.Window,(result) => {
            var alert = new NSAlert () {
                AlertStyle = NSAlertStyle.Critical,
                InformativeText = "We need to save the document here...",
                MessageText = "Save Document",
            };
            alert.RunModal ();
        });
    } else {
        if (dlg.RunModal () == 1) {
            var alert = new NSAlert () {
                AlertStyle = NSAlertStyle.Critical,
                InformativeText = "We need to save the document here...",
                MessageText = "Save Document",
            };
            alert.RunModal ();
        }
    }

}
```

A `AllowedFileTypes` propriedade é uma matriz de cadeia de caracteres de tipos de arquivo que o usuário pode selecionar para salvar o arquivo como. O tipo de arquivo pode ser especificado como uma extensão ou _UTI_. O valor padrão é `null` , que permite que qualquer tipo de arquivo seja usado.

Se definirmos a `ShowSaveAsSheet` propriedade como `false` , executar o aplicativo e selecionar **salvar como...** no menu **arquivo** , o seguinte será exibido:

[![Uma caixa de diálogo salvar](dialog-images/save01.png)](dialog-images/save01.png#lightbox)

O usuário pode expandir a caixa de diálogo:

[![Uma caixa de diálogo Salvar expandida](dialog-images/save02.png)](dialog-images/save02.png#lightbox)

Se definirmos a `ShowSaveAsSheet` propriedade como `true` , executar o aplicativo e selecionar **salvar como...** no menu **arquivo** , o seguinte será exibido:

[![Uma planilha de salvamento](dialog-images/save03.png)](dialog-images/save03.png#lightbox)

O usuário pode expandir a caixa de diálogo:

[![Uma planilha de salvamento expandida](dialog-images/save04.png)](dialog-images/save04.png#lightbox)

Para obter mais informações sobre como trabalhar com a caixa de diálogo salvar, consulte a documentação do [NSSavePanel](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSSavePanel_Class/index.html#//apple_ref/doc/uid/TP40004098) da Apple.

<a name="Summary"></a>

## <a name="summary"></a>Resumo

Este artigo adotou uma visão detalhada de como trabalhar com janelas modais, folhas e as caixas de diálogo do sistema padrão em um aplicativo Xamarin. Mac. Vimos os diferentes tipos e usos de janelas modais, planilhas e caixas de diálogo, como criar e manter janelas restritas e folhas no Interface Builder do Xcode e como trabalhar com janelas modais, folhas e caixas de diálogo no código C#.

## <a name="related-links"></a>Links Relacionados

- [MacWindows (exemplo)](https://docs.microsoft.com/samples/xamarin/mac-samples/macwindows)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Menus](~/mac/user-interface/menu.md)
- [Windows](~/mac/user-interface/window.md)
- [Barras de ferramentas](~/mac/user-interface/toolbar.md)
- [Diretrizes de interface humana do OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introdução ao Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
- [Introdução às planilhas](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/Sheets/Sheets.html#//apple_ref/doc/uid/10000002i)
- [Introdução à impressão](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/Printing/osxp_aboutprinting/osxp_aboutprt.html)
